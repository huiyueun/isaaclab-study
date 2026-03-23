# IsaacLab `source/` 정리

## 목차
- [1) 폴더 트리 (폴더만)](#sec-tree)
- [2) 폴더별 상세 정리](#sec-details)
  - [isaaclab (코어)](#sec-isaaclab)
  - [isaaclab_tasks (태스크)](#sec-tasks)
  - [isaaclab_rl (RL 래퍼)](#sec-rl)
  - [isaaclab_mimic (IL/Mimic)](#sec-mimic)
  - [isaaclab_assets (에셋)](#sec-assets)
  - [isaaclab_contrib (외부 기여)](#sec-contrib)
- [3) 관계도](#sec-map)

---

<a id="sec-tree"></a>
## 1) 폴더 트리 (폴더만)

```text
source
├── isaaclab
│   ├── config
│   ├── docs
│   ├── isaaclab
│   └── test
├── isaaclab_tasks
│   ├── config
│   ├── docs
│   ├── isaaclab_tasks
│   └── test
├── isaaclab_rl
│   ├── config
│   ├── docs
│   ├── isaaclab_rl
│   └── test
├── isaaclab_mimic
│   ├── config
│   ├── docs
│   ├── isaaclab_mimic
│   └── test
├── isaaclab_assets
│   ├── config
│   ├── data
│   ├── docs
│   ├── isaaclab_assets
│   └── test
└── isaaclab_contrib
    ├── config
    ├── docs
    ├── isaaclab_contrib
    └── test
```

---

<a id="sec-details"></a>
## 2) 폴더별 상세 정리

<a id="sec-isaaclab"></a>
## isaaclab (코어)

`source/isaaclab` (722 files)

- 목적: 시뮬레이션/환경/센서/매니저/유틸 등 프레임워크 코어 제공
- 핵심 코드 루트: `source/isaaclab/isaaclab`

주요 하위 모듈:
1. `app` : `AppLauncher` 등 실행 진입 모듈
2. `sim` : 시뮬레이션 컨텍스트, 스키마, 스포너, 뷰
3. `envs` : `ManagerBasedEnv`, `ManagerBasedRLEnv`, `DirectRLEnv` 계열
4. `managers` : action/observation/reward/event/termination 매니저
5. `scene` : `InteractiveScene` 계열
6. `assets` : rigid/deformable/articulation/surface gripper
7. `sensors` : camera/contact/imu/frame_transformer/ray_caster
8. `controllers` : IK/OSC 등 제어기
9. `terrains` : 지형 생성/임포트
10. `utils` : config/dataset/io/noise/warp 등 공통 유틸
11. `devices` : keyboard/gamepad/openxr/haply/spacemouse

<a id="sec-tasks"></a>
## isaaclab_tasks (태스크)

`source/isaaclab_tasks` (728 files)

- 목적: 실제 학습 환경(태스크) 정의 및 Gym 등록
- 핵심 코드 루트: `source/isaaclab_tasks/isaaclab_tasks`

주요 하위 모듈:
1. `direct` : Direct workflow 태스크
2. `manager_based` : Manager-based 태스크
3. `utils` : 태스크 로딩/등록/파싱 유틸

`manager_based` 주요 도메인:
1. `classic` : cartpole/ant/humanoid
2. `manipulation` : reach/lift/push/stack/cabinet/pick_place
3. `locomotion` : 속도추종 보행 태스크
4. `navigation` : 이동/내비게이션 태스크
5. `drone_arl` : 드론 ARL 태스크
6. `locomanipulation` : 보행+조작 결합 태스크

참고 (Franka Lift 위치):
1. `source/isaaclab_tasks/isaaclab_tasks/manager_based/manipulation/lift/config/franka/__init__.py`
2. `source/isaaclab_tasks/isaaclab_tasks/manager_based/manipulation/lift/config/franka/joint_pos_env_cfg.py`
3. `source/isaaclab_tasks/isaaclab_tasks/manager_based/manipulation/lift/lift_env_cfg.py`
4. `source/isaaclab_tasks/isaaclab_tasks/manager_based/manipulation/lift/mdp/rewards.py`

<a id="sec-rl"></a>
## isaaclab_rl (RL 래퍼)

`source/isaaclab_rl` (43 files)

- 목적: RL 프레임워크 연동 래퍼/설정 제공
- 핵심 코드 루트: `source/isaaclab_rl/isaaclab_rl`

주요 하위 모듈:
1. `rsl_rl` : RSL-RL 래퍼
2. `rl_games` : RL-Games 래퍼/PBT 유틸
3. `utils` : RL 공통 유틸

<a id="sec-mimic"></a>
## isaaclab_mimic (IL/Mimic)

`source/isaaclab_mimic` (65 files)

- 목적: 모방학습 데이터 생성/재생/플래닝 보조
- 핵심 코드 루트: `source/isaaclab_mimic/isaaclab_mimic`

주요 하위 모듈:
1. `datagen` : 데이터 생성 파이프라인
2. `envs` : mimic용 환경 확장
3. `motion_planners/curobo` : 모션 플래닝 연동
4. `locomanipulation_sdg` : loco-manipulation 데이터 생성
5. `ui` : mimic 보조 UI

<a id="sec-assets"></a>
## isaaclab_assets (에셋)

`source/isaaclab_assets` (70 files)

- 목적: 로봇/센서/기본 에셋 설정 제공
- 핵심 코드 루트: `source/isaaclab_assets/isaaclab_assets`

주요 하위 모듈:
1. `robots` : Franka, ANYmal, Unitree, H1 등 로봇 cfg
2. `sensors` : 센서 cfg

<a id="sec-contrib"></a>
## isaaclab_contrib (외부 기여)

`source/isaaclab_contrib` (41 files)

- 목적: 외부 기여 기능/실험 기능 스테이징
- 핵심 코드 루트: `source/isaaclab_contrib/isaaclab_contrib`

주요 하위 모듈:
1. `assets/multirotor` : 멀티로터 관련 에셋
2. `sensors/tacsl_sensor` : TacSL 촉각 센서
3. `actuators` : 액추에이터 확장
4. `mdp/actions` : 액션 확장
5. `utils` : 보조 유틸

---

<a id="sec-map"></a>
## 3) 관계도

```text
[source/isaaclab] (코어 프레임워크)
    ├─ envs/managers/scene/sim/sensors/controllers/assets/utils 제공
    │
    ├──> [source/isaaclab_assets] (로봇/센서 cfg)
    │
    ├──> [source/isaaclab_tasks] (환경/태스크 정의 + gym 등록)
    │        ├─ direct 태스크
    │        └─ manager_based 태스크 (lift/push/locomotion/...)
    │
    ├──> [source/isaaclab_rl] (RL 프레임워크 래퍼)
    │        ├─ rsl_rl
    │        ├─ rl_games
    │        └─ utils
    │
    ├──> [source/isaaclab_mimic] (IL/mimic, datagen, planner)
    │
    └──> [source/isaaclab_contrib] (외부 기여 확장)

실행 계층 연결:
[scripts/*] -> task id 선택 -> source/isaaclab_tasks 환경 cfg 로드
            -> source/isaaclab(코어)로 시뮬레이션 실행
            -> 필요 시 source/isaaclab_rl 또는 source/isaaclab_mimic 래퍼 사용
```
