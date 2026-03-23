# Franka Lift 셀프 교과서

## 사전 용어

- `MDP` : 강화학습 문제를 정의하는 틀(상태, 행동, 보상, 종료).
- `Policy` : 상태를 입력받아 행동을 출력하는 함수(보통 신경망).
- `PPO` : Policy를 안정적으로 조금씩 업데이트하는 대표 RL 알고리즘.
- `Observation` : 에이전트가 매 스텝 보는 입력값(관절각, 물체 위치 등).
- `Action` : 에이전트가 내리는 명령(관절 목표값, 그리퍼 열기/닫기 등).
- `Reward` : 행동 결과에 대한 점수 신호(학습 방향 결정).
- `Episode` : 초기화부터 종료까지 한 번의 시도.
- `train` : 정책을 학습하는 단계.
- `play` : 학습된 정책을 불러 동작만 확인하는 단계.

## 0. 문서 목적
- 강화학습(RL)을 모르는 상태에서도 `Franka Lift` 예제의 구조를 이해
- Isaac Lab에서 실제로 어떤 파일을 읽고 수정해야 하는지 파악
- `train -> play` 흐름으로 직접 실행하고 결과를 해석

---

## 1. 개요

Franka Lift 태스크 핵심 목표:
- 로봇 팔(Franka)이 큐브를 집고 들어서 목표 위치로 옮기도록 정책(Policy) 학습

강화학습 최소 개념:
- 상태(State): 로봇이 보는 정보
- 행동(Action): 로봇이 내리는 명령
- 보상(Reward): 동작을 잘했는지 점수
- 종료(Termination): 한 에피소드 끝나는 조건
- 정책(Policy): 상태를 입력받아 행동을 출력하는 신경망

Isaac Lab의 역할:
- Isaac Sim: 물리 계산
- Isaac Lab 환경 코드: 상태/행동/보상/종료 정의
- RL 알고리즘(PPO): 정책 가중치 업데이트

---

## 2. 파일 구성

기준 경로:
- `source/isaaclab_tasks/isaaclab_tasks/manager_based/manipulation/lift/`

핵심 파일 5개는 "기능"이 아니라 "역할"로 구분해서 보면 바로 이해 가능.

1. `config/franka/__init__.py`  
역할: 실행 문자열(`--task`)을 실제 환경/학습 설정에 연결하는 라우터

2. `lift_env_cfg.py`  
역할: Lift의 MDP 구성요소를 한 곳에서 선언 (관측(무엇을 볼지), 행동(무엇을 제어할지), 보상(무엇을 칭찬할지), 종료(언제 끝낼지), 이벤트/커리큘럼(학습 난이도 조절))

3. `config/franka/joint_pos_env_cfg.py`  
역할: 뼈대를 Franka 로봇 기준으로 채우는 로봇 특화 설정

4. `mdp/rewards.py`  
역할: `lift_env_cfg.py`에서 선언한 보상 항목의 실제 계산식 구현

5. `config/franka/agents/rsl_rl_ppo_cfg.py`  
역할: PPO 학습 하이퍼파라미터 정의

### 2.1 비유로 이해하기

- `lift_env_cfg.py`: 문제집 목차 (무엇을 평가할지 정의)
- `mdp/rewards.py`: 채점 기준표 (점수 계산식)
- `joint_pos_env_cfg.py`: 시험 응시자 스펙 (Franka 몸체/관절/그리퍼)
- `rsl_rl_ppo_cfg.py`: 공부 방법 (PPO 학습 전략)
- `__init__.py`: 시험장 안내판 (`--task`가 어디로 연결되는지)

### 2.2 파일 관계도 (그림)

```text
[CLI]
  ./isaaclab.sh ... --task Isaac-Lift-Cube-Franka-v0
            |
            v
[config/franka/__init__.py]
  gym.register(id="Isaac-Lift-Cube-Franka-v0", ...)
            |
            +--> env_cfg_entry_point -> FrankaCubeLiftEnvCfg
            |                         (config/franka/joint_pos_env_cfg.py)
            |
            |      inherits
            |         from
            |          v
            +------> LiftEnvCfg (lift_env_cfg.py)
            |          |- ObservationsCfg
            |          |- ActionsCfg
            |          |- RewardsCfg  ----calls----> mdp/rewards.py
            |          |- TerminationsCfg
            |          |- Events/Curriculum
            |
            +--> rsl_rl_cfg_entry_point -> LiftCubePPORunnerCfg
                                      (config/franka/agents/rsl_rl_ppo_cfg.py)
```

### 2.3 실제 호출 흐름 (학습 시점)

1. `--task Isaac-Lift-Cube-Franka-v0` 입력
2. `config/franka/__init__.py`가 해당 ID를 `FrankaCubeLiftEnvCfg`와 PPO 설정에 연결
3. `FrankaCubeLiftEnvCfg`가 `LiftEnvCfg`를 상속해 Franka 로봇/그리퍼/큐브 설정 적용
4. 학습 루프에서 `LiftEnvCfg.RewardsCfg` 항목이 `mdp/rewards.py` 함수 호출
5. PPO는 `rsl_rl_ppo_cfg.py` 설정으로 정책 업데이트 반복

### 2.4 어디를 수정해야 무엇이 바뀌는지

- 태스크 규칙(관측/보상/종료)을 바꾸고 싶다:
  - `lift_env_cfg.py`, `mdp/rewards.py`
- 로봇 동작 방식(그리퍼, 관절 스케일, 오브젝트 초기값)을 바꾸고 싶다:
  - `config/franka/joint_pos_env_cfg.py`
- 학습 속도/안정성(PPO)을 튜닝하고 싶다:
  - `config/franka/agents/rsl_rl_ppo_cfg.py`
- `--task` 이름/등록 자체를 바꾸고 싶다:
  - `config/franka/__init__.py`

### 2.5 초보자용 권장 읽기 순서

1. `config/franka/__init__.py` (연결점 이해)
2. `lift_env_cfg.py` (문제 정의 이해)
3. `mdp/rewards.py` (점수 계산 이해)
4. `config/franka/joint_pos_env_cfg.py` (Franka 특화 이해)
5. `config/franka/agents/rsl_rl_ppo_cfg.py` (학습 설정 이해)

---

## 3. 실행

처음에는 보통 `train` 먼저 실행, 그 다음 `play` 실행.

학습:
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Lift-Cube-Franka-v0
```

재생(학습된 정책 확인):
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Lift-Cube-Franka-Play-v0
```

왜 train 먼저 필요한가:
- `play`는 보통 이미 학습된 체크포인트를 불러서 동작 확인하는 단계
- 체크포인트가 없으면 볼 수 있는 결과가 제한

---

## 4. 환경 등록

### 4.1 task 문자열이 어디서 연결되는지

코드 원문 (`config/franka/__init__.py`):
```python
gym.register(
    id="Isaac-Lift-Cube-Franka-v0",
    entry_point="isaaclab.envs:ManagerBasedRLEnv",
    kwargs={
        "env_cfg_entry_point": f"{__name__}.joint_pos_env_cfg:FrankaCubeLiftEnvCfg",
        "rsl_rl_cfg_entry_point": f"{agents.__name__}.rsl_rl_ppo_cfg:LiftCubePPORunnerCfg",
        "skrl_cfg_entry_point": f"{agents.__name__}:skrl_ppo_cfg.yaml",
        "rl_games_cfg_entry_point": f"{agents.__name__}:rl_games_ppo_cfg.yaml",
        "sb3_cfg_entry_point": f"{agents.__name__}:sb3_ppo_cfg.yaml",
    },
    disable_env_checker=True,
)
```

해석:
- `--task Isaac-Lift-Cube-Franka-v0` 입력 시 `FrankaCubeLiftEnvCfg` 환경 구성 사용
- RSL-RL PPO 설정도 함께 연결

### 4.2 태스크 파라미터 요약 (간단 버전)

`Isaac-Lift-Cube-Franka-v0` 기준으로 먼저 보면 되는 파라미터:

- `id`  
  역할: CLI에서 호출하는 태스크 이름.

- `entry_point`  
  역할: 어떤 env 실행 클래스를 쓸지 지정 (`ManagerBasedRLEnv`).

- `env_cfg_entry_point`  
  역할: 환경 설정 클래스 지정 (`FrankaCubeLiftEnvCfg`).

- `rsl_rl_cfg_entry_point`  
  역할: PPO 학습 설정 클래스 지정 (`LiftCubePPORunnerCfg`).

- `scene.num_envs` (`lift_env_cfg.py`)  
  역할: 병렬 환경 수. 클수록 데이터 수집 빠르지만 GPU 메모리 사용 증가.

- `episode_length_s` (`lift_env_cfg.py`)  
  역할: 한 에피소드 길이(초). 시간 초과 종료 기준.

- `sim.dt`, `decimation` (`lift_env_cfg.py`)  
  역할: 시뮬레이션 시간 해상도/제어 주기 결정.

- `minimal_height` (`RewardsCfg.lifting_object`)  
  역할: "들었다"고 인정하는 높이 임계값.

- `weight` (각 Reward 항목)  
  역할: 어떤 행동을 더 강하게 학습할지 결정하는 중요도.

- `learning_rate`, `clip_param`, `num_learning_epochs` (`rsl_rl_ppo_cfg.py`)  
  역할: PPO 업데이트 속도/안정성/학습 강도 결정.

실무에서 처음 조정 순서:
1. 보상 가중치(`weight`)와 목표 범위
2. `scene.num_envs` / `episode_length_s`
3. 마지막에 PPO 파라미터

---

## 5. MDP 구조

### 5.1 관측(Observation)

코드 원문 (`lift_env_cfg.py`):
```python
class ObservationsCfg:
    @configclass
    class PolicyCfg(ObsGroup):
        joint_pos = ObsTerm(func=mdp.joint_pos_rel)
        joint_vel = ObsTerm(func=mdp.joint_vel_rel)
        object_position = ObsTerm(func=mdp.object_position_in_robot_root_frame)
        target_object_position = ObsTerm(func=mdp.generated_commands, params={"command_name": "object_pose"})
        actions = ObsTerm(func=mdp.last_action)

        def __post_init__(self):
            self.enable_corruption = True
            self.concatenate_terms = True
```

해석:
- 정책 입력은 관절/속도/물체 위치/목표 위치/직전 행동
- `enable_corruption = True`: 학습 시 관측 노이즈 사용

### 5.2 행동(Action)

코드 원문 (`lift_env_cfg.py`):
```python
class ActionsCfg:
    arm_action: mdp.JointPositionActionCfg | mdp.DifferentialInverseKinematicsActionCfg = MISSING
    gripper_action: mdp.BinaryJointPositionActionCfg = MISSING
```

해석:
- Lift는 팔 + 그리퍼 둘 다 제어

### 5.3 보상(Reward)

코드 원문 (`lift_env_cfg.py`):
```python
class RewardsCfg:
    reaching_object = RewTerm(func=mdp.object_ee_distance, params={"std": 0.1}, weight=1.0)

    lifting_object = RewTerm(func=mdp.object_is_lifted, params={"minimal_height": 0.04}, weight=15.0)

    object_goal_tracking = RewTerm(
        func=mdp.object_goal_distance,
        params={"std": 0.3, "minimal_height": 0.04, "command_name": "object_pose"},
        weight=16.0,
    )
```

해석:
- 가까이 가기(+), 들어올리기(++), 목표 위치 추적(+++)의 단계형 보상 구조

### 5.4 종료(Termination)

코드 원문 (`lift_env_cfg.py`):
```python
class TerminationsCfg:
    time_out = DoneTerm(func=mdp.time_out, time_out=True)

    object_dropping = DoneTerm(
        func=mdp.root_height_below_minimum, params={"minimum_height": -0.05, "asset_cfg": SceneEntityCfg("object")}
    )
```

해석:
- 시간 초과 종료
- 큐브를 너무 떨어뜨리면 실패 종료

---

## 6. Franka 설정

코드 원문 (`config/franka/joint_pos_env_cfg.py`):
```python
self.actions.arm_action = mdp.JointPositionActionCfg(
    asset_name="robot", joint_names=["panda_joint.*"], scale=0.5, use_default_offset=True
)
self.actions.gripper_action = mdp.BinaryJointPositionActionCfg(
    asset_name="robot",
    joint_names=["panda_finger.*"],
    open_command_expr={"panda_finger_.*": 0.04},
    close_command_expr={"panda_finger_.*": 0.0},
)
```

해석:
- 팔: 관절 목표 위치 제어
- 그리퍼: 열기(0.04), 닫기(0.0) 이진 제어

PLAY 설정 코드 원문:
```python
class FrankaCubeLiftEnvCfg_PLAY(FrankaCubeLiftEnvCfg):
    def __post_init__(self):
        super().__post_init__()
        self.scene.num_envs = 50
        self.scene.env_spacing = 2.5
        self.observations.policy.enable_corruption = False
```

해석:
- 재생 모드에서 환경 수 축소
- 관측 노이즈 비활성화

---

## 7. 보상 함수

코드 원문 (`mdp/rewards.py`):
```python
def object_is_lifted(env, minimal_height, object_cfg=SceneEntityCfg("object")):
    object: RigidObject = env.scene[object_cfg.name]
    return torch.where(object.data.root_pos_w[:, 2] > minimal_height, 1.0, 0.0)
```

핵심:
- z축 높이가 임계값보다 높으면 1점, 아니면 0점

코드 원문:
```python
def object_ee_distance(env, std, object_cfg=SceneEntityCfg("object"), ee_frame_cfg=SceneEntityCfg("ee_frame")):
    object: RigidObject = env.scene[object_cfg.name]
    ee_frame: FrameTransformer = env.scene[ee_frame_cfg.name]
    cube_pos_w = object.data.root_pos_w
    ee_w = ee_frame.data.target_pos_w[..., 0, :]
    object_ee_distance = torch.norm(cube_pos_w - ee_w, dim=1)
    return 1 - torch.tanh(object_ee_distance / std)
```

핵심:
- 손이 큐브에 가까울수록 보상 증가

코드 원문:
```python
def object_goal_distance(env, std, minimal_height, command_name, robot_cfg=SceneEntityCfg("robot"), object_cfg=SceneEntityCfg("object")):
    robot: RigidObject = env.scene[robot_cfg.name]
    object: RigidObject = env.scene[object_cfg.name]
    command = env.command_manager.get_command(command_name)
    des_pos_b = command[:, :3]
    des_pos_w, _ = combine_frame_transforms(robot.data.root_pos_w, robot.data.root_quat_w, des_pos_b)
    distance = torch.norm(des_pos_w - object.data.root_pos_w, dim=1)
    return (object.data.root_pos_w[:, 2] > minimal_height) * (1 - torch.tanh(distance / std))
```

핵심:
- 먼저 들어올린 상태를 만족해야 목표 추적 보상이 활성화

---

## 8. PPO 설정

코드 원문 (`config/franka/agents/rsl_rl_ppo_cfg.py`):
```python
class LiftCubePPORunnerCfg(RslRlOnPolicyRunnerCfg):
    num_steps_per_env = 24
    max_iterations = 1500
    save_interval = 50
    experiment_name = "franka_lift"
```

```python
algorithm = RslRlPpoAlgorithmCfg(
    clip_param=0.2,
    entropy_coef=0.006,
    num_learning_epochs=5,
    num_mini_batches=4,
    learning_rate=1.0e-4,
    gamma=0.98,
    lam=0.95,
)
```

해석:
- PPO 기본 안정 설정 사용
- 입문 단계에서는 먼저 환경/보상 이해가 우선, PPO 수치 튜닝은 다음 단계

---

## 9. 체크리스트

1. `--task` 문자열과 env class 연결 위치를 찾을 수 있는가
2. 관측/행동/보상/종료 각각이 어느 파일에 있는지 구분 가능한가
3. Lift가 왜 `arm + gripper` 구조인지 설명 가능한가
4. `lifting_object` 보상이 왜 필요한지 설명 가능한가
5. `train -> play` 순서와 목적을 설명 가능한가

---

## 10. 학습 순서

1. Lift 기본 실행 성공 (`train`, `play`)
2. `RewardsCfg` 숫자 1개만 바꿔서 행동 변화 관찰
3. `enable_corruption` on/off 차이 관찰
4. 이후 Push 변형 문서로 진행
