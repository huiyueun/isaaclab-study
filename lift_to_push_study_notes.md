# Lift에서 Push로 확장하는 셀프 교과서

## 0. 이 문서의 목표
- Lift 예제를 이해한 사람이 Push 태스크를 직접 설계/구현할 수 있게 안내
- "무엇을 왜 바꾸는지"를 코드 기준으로 설명
- 학습(train)과 실행(play)까지 한 번에 연결

---

## 1. Lift와 Push의 본질 차이

Lift:
- 물체를 집어서 들어올리고 목표 위치로 옮기는 태스크
- 그리퍼 제어 필요
- 높이(z) 개념이 보상의 핵심

Push:
- 물체를 밀어서 목표 위치로 이동시키는 태스크
- 그리퍼 제어 불필요
- XY 평면 이동이 보상의 핵심

결론:
- Push는 Lift보다 단순한 동작 구조
- 코드 변경 핵심은 "그리퍼 제거 + 보상 재정의"

---

## 2. 새 태스크가 어디에 생겼는지

기준 경로:
- `source/isaaclab_tasks/isaaclab_tasks/manager_based/manipulation/push/`

핵심 파일:
1. `config/franka/__init__.py`  
- Push task ID 등록

2. `push_env_cfg.py`  
- Push MDP 골격

3. `config/franka/joint_pos_env_cfg.py`  
- Franka 전용 Push 설정

4. `mdp/rewards.py`  
- Push 보상 함수 구현

5. `config/franka/agents/rsl_rl_ppo_cfg.py`  
- PPO 학습 설정

---

## 3. 환경 등록 비교 (Lift -> Push)

코드 원문 (Push, `config/franka/__init__.py`):
```python
gym.register(
    id="Isaac-Push-Cube-Franka-v0",
    entry_point="isaaclab.envs:ManagerBasedRLEnv",
    kwargs={
        "env_cfg_entry_point": f"{__name__}.joint_pos_env_cfg:FrankaCubePushEnvCfg",
        "rsl_rl_cfg_entry_point": f"{agents.__name__}.rsl_rl_ppo_cfg:PushCubePPORunnerCfg",
    },
    disable_env_checker=True,
)

gym.register(
    id="Isaac-Push-Cube-Franka-Play-v0",
    entry_point="isaaclab.envs:ManagerBasedRLEnv",
    kwargs={
        "env_cfg_entry_point": f"{__name__}.joint_pos_env_cfg:FrankaCubePushEnvCfg_PLAY",
        "rsl_rl_cfg_entry_point": f"{agents.__name__}.rsl_rl_ppo_cfg:PushCubePPORunnerCfg",
    },
    disable_env_checker=True,
)
```

핵심:
- task ID만 바뀐 것이 아니라, env cfg entry point 자체가 Push 클래스로 연결

---

## 4. 가장 중요한 변경 1: Action 구조 단순화

Lift에서는 arm + gripper 사용.
Push에서는 arm only 사용.

코드 원문 (Push, `push_env_cfg.py`):
```python
class ActionsCfg:
    """Action specifications for the MDP."""

    # Arm only (no gripper for pushing)
    arm_action: mdp.JointPositionActionCfg = MISSING
```

코드 원문 (Push Franka, `config/franka/joint_pos_env_cfg.py`):
```python
# Arm only (gripper stays at default = open)
self.actions.arm_action = mdp.JointPositionActionCfg(
    asset_name="robot", joint_names=["panda_joint.*"], scale=0.5, use_default_offset=True
)
```

핵심:
- grasp/close 동작 자체를 제거
- 구현 복잡도와 실패 케이스 대폭 감소

---

## 5. 가장 중요한 변경 2: Reward 재설계

Lift 보상 중심:
- 접근
- 들어올림(z)
- 공중 목표 추적

Push 보상 중심:
- 접근
- 실제 물체 이동(velocity)
- XY 목표 추적

코드 원문 (Push, `push_env_cfg.py`):
```python
class RewardsCfg:
    reaching_object = RewTerm(func=mdp.object_ee_distance, params={"std": 0.1}, weight=1.0)

    object_moved = RewTerm(func=mdp.object_moved, params={"min_velocity": 0.01}, weight=1.0)

    pushing_object = RewTerm(
        func=mdp.object_goal_distance,
        params={"std": 0.3, "command_name": "object_pose"},
        weight=2.0,
    )

    pushing_object_fine = RewTerm(
        func=mdp.object_goal_distance,
        params={"std": 0.05, "command_name": "object_pose"},
        weight=1.0,
    )
```

코드 원문 (Push, `mdp/rewards.py`):
```python
def object_goal_distance(env, std, command_name, robot_cfg=SceneEntityCfg("robot"), object_cfg=SceneEntityCfg("object")):
    """Reward the agent for pushing the object to the goal position (XY only, no height constraint)."""
    robot: RigidObject = env.scene[robot_cfg.name]
    object: RigidObject = env.scene[object_cfg.name]
    command = env.command_manager.get_command(command_name)
    des_pos_b = command[:, :3]
    des_pos_w, _ = combine_frame_transforms(robot.data.root_pos_w, robot.data.root_quat_w, des_pos_b)
    # XY only - no height constraint for pushing
    distance = torch.norm(des_pos_w[:, :2] - object.data.root_pos_w[:, :2], dim=1)
    return 1 - torch.tanh(distance / std)
```

```python
def object_moved(env, min_velocity: float = 0.01, object_cfg: SceneEntityCfg = SceneEntityCfg("object")):
    """Reward when the object is actually moving (velocity-based)."""
    object: RigidObject = env.scene[object_cfg.name]
    velocity = torch.norm(object.data.root_lin_vel_w[:, :2], dim=1)
    return torch.where(velocity > min_velocity, 1.0, 0.0)
```

핵심:
- z축 기반 lift 조건 제거
- 물체가 실제로 움직였는지 별도 보상 추가

---

## 6. 가장 중요한 변경 3: 목표/초기조건을 테이블 평면 기준으로 고정

코드 원문 (`push_env_cfg.py`):
```python
object_pose = mdp.UniformPoseCommandCfg(
    ...
    ranges=mdp.UniformPoseCommandCfg.Ranges(
        pos_x=(0.35, 0.65), pos_y=(-0.25, 0.25), pos_z=(0.05, 0.05),
        roll=(0.0, 0.0), pitch=(0.0, 0.0), yaw=(0.0, 0.0)
    ),
)
```

핵심:
- 목표 z를 고정해서 "밀기"에 집중
- lift 같은 수직 동작을 학습 목표에서 제외

---

## 7. 가장 중요한 변경 4: Domain Randomization 도입

코드 원문 (`push_env_cfg.py`):
```python
randomize_object_mass = EventTerm(
    func=mdp.randomize_rigid_body_mass,
    mode="startup",
    params={
        "asset_cfg": SceneEntityCfg("object"),
        "mass_distribution_params": (0.10, 0.30),
        "operation": "abs",
    },
)

randomize_object_friction = EventTerm(
    func=mdp.randomize_rigid_body_material,
    mode="startup",
    params={
        "asset_cfg": SceneEntityCfg("object", body_names=".*"),
        "static_friction_range": (0.2, 0.7),
        "dynamic_friction_range": (0.2, 0.7),
        "restitution_range": (0.0, 0.0),
        "num_buckets": 64,
    },
)
```

핵심:
- 큐브 질량/마찰 랜덤화로 정책 강건성 강화
- 같은 push 전략이 다양한 물리 조건에서도 동작하도록 유도

---

## 8. Play 모드 실전 셋업

코드 원문 (`config/franka/joint_pos_env_cfg.py`):
```python
class FrankaCubePushEnvCfg_PLAY(FrankaCubePushEnvCfg):
    def __post_init__(self):
        super().__post_init__()
        self.scene.num_envs = 4
        self.scene.env_spacing = 3.0
        self.observations.policy.enable_corruption = False

        self.scene.object.init_state.pos = [0.5, -0.15, 0.08]
        self.events.reset_object_position.params["pose_range"] = {
            "x": (0.0, 0.0), "y": (0.0, 0.0), "z": (0.0, 0.0)
        }

        self.commands.object_pose.ranges.pos_x = (0.5, 0.5)
        self.commands.object_pose.ranges.pos_y = (0.15, 0.15)
        self.commands.object_pose.ranges.pos_z = (0.08, 0.08)
```

핵심:
- play에서 초기조건과 목표를 고정하면 디버깅이 쉬움
- 학습이 잘 됐는지 반복 재현성 높은 방식으로 검증 가능

---

## 9. PPO 설정 관점

코드 원문 (Push, `config/franka/agents/rsl_rl_ppo_cfg.py`):
```python
class PushCubePPORunnerCfg(RslRlOnPolicyRunnerCfg):
    num_steps_per_env = 24
    max_iterations = 1500
    save_interval = 50
    experiment_name = "franka_push"
```

```python
algorithm = RslRlPpoAlgorithmCfg(
    value_loss_coef=1.0,
    use_clipped_value_loss=True,
    clip_param=0.2,
    entropy_coef=0.006,
    num_learning_epochs=5,
    num_mini_batches=4,
    learning_rate=1.0e-4,
    schedule="adaptive",
    gamma=0.98,
    lam=0.95,
    desired_kl=0.01,
    max_grad_norm=1.0,
)
```

핵심:
- Lift와 거의 동일한 PPO 설정 유지
- 이번 변경의 본질은 알고리즘이 아니라 환경/보상 재설계

---

## 10. 실행 가이드

학습:
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Push-Cube-Franka-v0
```

재생:
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Push-Cube-Franka-Play-v0
```

권장 순서:
1. 먼저 Lift를 이해
2. Push train 실행
3. Push play에서 목표 도달 패턴 확인
4. 실패 시 `RewardsCfg`와 `commands.object_pose.ranges`부터 점검

---

## 11. Lift -> Push 전환 체크리스트

1. task 등록 (`__init__.py`) 완료
2. `ActionsCfg`에서 gripper 제거 완료
3. Lift 보상 항목(lifting) 제거 및 Push 보상 추가 완료
4. 목표 z 고정 확인
5. Play 고정 시나리오 설정 완료
6. train/play 커맨드로 실제 검증 완료

---

## 12. 다음 실습 과제

1. `object_moved` 임계속도(`min_velocity`)를 바꿔 학습 안정성 비교
2. `pushing_object` 가중치(2.0)를 조정해 목표 지향성 변화 확인
3. 질량/마찰 랜덤화 범위를 줄이거나 늘려 일반화 성능 비교
