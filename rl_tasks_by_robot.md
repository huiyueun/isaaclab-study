# Isaac Lab 강화학습 예제 정리 (로봇+태스크)

## 목차
- [0) 공통 실행 패턴](#sec-common)
- [1) 매니퓰레이터 계열](#sec-manipulators)
- [2) 보행 로봇 계열](#sec-legged)
- [3) 손/고난도 조작 계열](#sec-hands)
- [4) 특수 태스크 계열](#sec-special)
- [5) 전체 환경 목록 확인](#sec-list)

---

<a id="sec-common"></a>
## 0) 공통 실행 패턴

1. 학습 (RSL-RL)
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task <TASK_ID>
```

2. 실행/재생 (RSL-RL)
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task <TASK_ID_OR_PLAY_ID>
```

3. 체크포인트 지정 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py \
  --task <TASK_ID_OR_PLAY_ID> \
  --checkpoint <CHECKPOINT_PATH>
```

---

<a id="sec-manipulators"></a>
## 1) 매니퓰레이터 계열

### Franka Panda
- 로봇 설명: 7자유도 산업용 매니퓰레이터, 그리퍼 기반 pick/reach/lift 학습에 표준적으로 사용
- 대표 태스크

1. Reach
- 태스크: 목표 자세(혹은 위치)로 엔드이펙터 이동
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Reach-Franka-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Reach-Franka-Play-v0
```

2. Lift
- 태스크: 큐브 grasp 후 지정 높이/목표 지점으로 들어올리기
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Lift-Cube-Franka-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Lift-Cube-Franka-Play-v0
```

3. Open Drawer
- 태스크: 서랍 손잡이 접근/접촉 후 서랍 개방
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Open-Drawer-Franka-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Open-Drawer-Franka-Play-v0
```

### OpenArm
- 로봇 설명: 비교적 경량/단순 매니퓰레이터 계열, Reach/Lift/Cabinet 기본 조작 실험에 적합
- 대표 태스크

1. Reach
- 태스크: 목표 위치 추종
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Reach-OpenArm-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Reach-OpenArm-Play-v0
```

2. Lift
- 태스크: 큐브 집어서 들어올리기
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Lift-Cube-OpenArm-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Lift-Cube-OpenArm-Play-v0
```

### UR10 / UR10e
- 로봇 설명: 산업 현장형 6축 매니퓰레이터, 조립/배치/배포형 태스크에 자주 사용
- 대표 태스크

1. UR10 Reach
- 태스크: 엔드이펙터 목표 도달
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Reach-UR10-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Reach-UR10-Play-v0
```

2. UR10e Deploy Reach
- 태스크: 배포(실기기 연동 고려) 전제 reach
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Deploy-Reach-UR10e-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Deploy-Reach-UR10e-Play-v0
```

3. UR10e Gear Assembly
- 태스크: 기어 어셈블리 조립 동작
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Deploy-GearAssembly-UR10e-2F140-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Deploy-GearAssembly-UR10e-2F140-Play-v0
```

---

<a id="sec-legged"></a>
## 2) 보행 로봇 계열

### ANYmal (B/C/D)
- 로봇 설명: 사족 보행 연구 표준 플랫폼, 속도 추종/지형 적응 학습에 널리 사용
- 대표 태스크

1. Flat terrain velocity
- 태스크: 평지에서 목표 선속/각속 추종
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Velocity-Flat-Anymal-C-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Velocity-Flat-Anymal-C-Play-v0
```

2. Rough terrain velocity
- 태스크: 험지에서 속도 추종 + 안정 보행
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Velocity-Rough-Anymal-C-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Velocity-Rough-Anymal-C-Play-v0
```

### Unitree (A1/Go1/Go2)
- 로봇 설명: 경량 사족 플랫폼, RL 보행 벤치마크에서 자주 사용
- 대표 태스크

1. Go2 Flat
- 태스크: 평지 속도 추종
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Velocity-Flat-Unitree-Go2-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Velocity-Flat-Unitree-Go2-Play-v0
```

2. Go2 Rough
- 태스크: 험지 속도 추종
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Velocity-Rough-Unitree-Go2-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Velocity-Rough-Unitree-Go2-Play-v0
```

### Humanoid (H1 / G1 / Cassie / Digit)
- 로봇 설명: 고자유도 휴머노이드/준휴머노이드, 균형/보행/지형 적응 연구 중심
- 대표 태스크

1. H1 Rough
- 태스크: 험지 보행 속도 추종
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Velocity-Rough-H1-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Velocity-Rough-H1-Play-v0
```

2. G1 Flat
- 태스크: 평지 보행 속도 추종
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Velocity-Flat-G1-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Velocity-Flat-G1-Play-v0
```

3. Cassie Rough
- 태스크: 험지 보행 속도 추종
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Velocity-Rough-Cassie-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Velocity-Rough-Cassie-Play-v0
```

4. Digit Flat
- 태스크: 평지 보행 속도 추종
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Velocity-Flat-Digit-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Velocity-Flat-Digit-Play-v0
```

---

<a id="sec-hands"></a>
## 3) 손/고난도 조작 계열

### Allegro Hand
- 로봇 설명: 다관절 손 조작 플랫폼, in-hand reorientation/접촉 조작 학습 중심
- 대표 태스크

1. Repose Cube
- 태스크: 손 안에서 큐브 자세 재배치
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Repose-Cube-Allegro-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Repose-Cube-Allegro-Play-v0
```

### Shadow Hand
- 로봇 설명: 고자유도 anthropomorphic hand, 장기 horizon 조작 학습에 자주 사용
- 대표 태스크

1. Repose Cube Shadow (Direct)
- 태스크: in-hand 큐브 재자세화
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Repose-Cube-Shadow-Direct-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Repose-Cube-Shadow-Direct-v0
```

2. Shadow Vision
- 태스크: 시각 입력 기반 조작
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Repose-Cube-Shadow-Vision-Direct-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Repose-Cube-Shadow-Vision-Direct-Play-v0
```

### Kuka Allegro (Dexsuite)
- 로봇 설명: 암+손 결합 고난도 조작 플랫폼
- 대표 태스크

1. Lift
- 태스크: 물체 grasp 후 lift
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Dexsuite-Kuka-Allegro-Lift-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Dexsuite-Kuka-Allegro-Lift-Play-v0
```

2. Reorient
- 태스크: 물체 목표 자세 재배치
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Dexsuite-Kuka-Allegro-Reorient-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Dexsuite-Kuka-Allegro-Reorient-Play-v0
```

---

<a id="sec-special"></a>
## 4) 특수 태스크 계열

### Quadcopter
- 로봇 설명: 4축 비행 플랫폼
- 태스크: 자세/위치 안정화 중심 제어
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Quadcopter-Direct-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Quadcopter-Direct-v0
```

### Drone ARL
- 로봇 설명: 드론 경로/위치 추종 연구용 플랫폼
- 태스크: 장애물 없는 공간에서 목표 위치 추종
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-TrackPositionNoObstacles-ARL-Robot-1-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-TrackPositionNoObstacles-ARL-Robot-1-Play-v0
```

### Classic Benchmark
- 로봇 설명: RL 벤치마크 표준 태스크
- 태스크: Cartpole/Ant/Humanoid 기반 기본 제어 문제

1. Cartpole
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Cartpole-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Cartpole-v0
```

2. Ant
- 학습
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Ant-v0
```
- 실행
```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Ant-v0
```

---

<a id="sec-list"></a>
## 5) 전체 환경 목록 확인

1. 전체 환경 ID 출력
```bash
./isaaclab.sh -p scripts/environments/list_envs.py
```

2. 키워드 검색
```bash
./isaaclab.sh -p scripts/environments/list_envs.py --keyword Franka
./isaaclab.sh -p scripts/environments/list_envs.py --keyword Velocity
./isaaclab.sh -p scripts/environments/list_envs.py --keyword Allegro
```

