# IsaacLab `scripts/` 정리
## 목차
- [1) 폴더 트리 (폴더만)](#sec-tree)
- [2) 폴더별 상세 정리](#sec-details)
  - [튜토리얼](#sec-tutorials)
  - [데모](#sec-demos)
  - [환경 유틸](#sec-env)
  - [강화학습](#sec-rl)
  - [모방학습](#sec-il)
  - [벤치마크](#sec-bench)
  - [Sim-to-Sim 전이](#sec-sim2sim)
  - [툴링](#sec-tools)
---
<a id="sec-tree"></a>
## 1) 폴더 트리 (폴더만)
```text
scripts
├── benchmarks
├── demos
│   └── sensors
├── environments
│   ├── state_machine
│   └── teleoperation
├── imitation_learning
│   ├── isaaclab_mimic
│   ├── locomanipulation_sdg
│   └── robomimic
├── reinforcement_learning
│   ├── ray
│   │   ├── cluster_configs
│   │   │   └── google_cloud
│   │   └── hyperparameter_tuning
│   ├── rl_games
│   ├── rsl_rl
│   ├── sb3
│   └── skrl
├── sim2sim_transfer
│   └── config
├── tools
│   ├── cosmos
│   └── test
└── tutorials
    ├── 00_sim
    ├── 01_assets
    ├── 02_scene
    ├── 03_envs
    ├── 04_sensors
    └── 05_controllers
```
---
<a id="sec-details"></a>
## 2) 폴더별 상세 정리
<a id="sec-tutorials"></a>
## 튜토리얼
`scripts/tutorials` (23개 스크립트)
- 순서 : 시뮬레이션 기초 → 로봇/자산 조작 → 환경 구성 → 센서 조작 → 제어기(컨트롤)
### 00_sim (시뮬레이션 기초)
1. `create_empty.py` : `AppLauncher`
2. `spawn_prims.py` : Ground/Light/Cone/Cuboid/USD 등 Prim Spawn 방식 학습
3. `launch_app.py` : CLI 인자
4. `set_rendering_mode.py` : 렌더링 프리셋
5. `log_time.py` : 시뮬레이션 시간 로그를 파일로 저장하는 예제
### 01_assets (자산/로봇 다루기)
1. `run_rigid_object.py` : 강체 생성/리셋/상태쓰기/업데이트 루프
2. `run_deformable_object.py` : 연성체 오브젝트 생성/업데이트
3. `run_articulation.py` : 관절 로봇
4. `run_surface_gripper.py` : Surface Gripper 열기/닫기/상태 읽기
5. `add_new_robot.py` : 새 로봇 USD를 ArticulationCfg로 등록하고 actuator 설정하는 방법
### 02_scene (씬 구성)
1. `create_scene.py` : `InteractiveSceneCfg` 기반으로 Ground/Light/Robot을 묶어 씬 구성
### 03_envs (환경 구성/실행)
1. `create_cartpole_base_env.py` : `ManagerBasedEnv`의 action/observation/event 구성 방법
2. `run_cartpole_rl_env.py` : `ManagerBasedRLEnv` 실행
3. `create_cube_base_env.py` : 커스텀 `ActionTerm`/이벤트 구성
4. `create_quadruped_base_env.py` : ANYmal-C + height scanner 기반 base env와 policy 추론
5. `policy_inference_in_usd.py` : JIT 정책 USD 월드 추론
### 04_sensors (센서 조작)
1. `add_sensors_on_robot.py` : 로봇에 Camera/RayCaster/Contact 센서를 통합 구성
2. `run_frame_transformer.py` : FrameTransformer로 프레임 변환/시각화 확인
3. `run_ray_caster.py` : RayCaster 그리드 스캔으로 지형 높이 정보 획득
4. `run_ray_caster_camera.py` : Warp 기반 Ray-cast camera
5. `run_usd_camera.py` : USD camera + Replicator 기반 데이터 획득/저장/포인트클라우드 처리
### 05_controllers (제어기)
1. `run_diff_ik.py` : Differential IK 기반 task-space 제어
2. `run_osc.py` : OSC 기반 동역학 제어
---
<a id="sec-demos"></a>
## 데모
`scripts/demos` (21개 스크립트)
### 기본 데모 (`scripts/demos`)
1. `arms.py` : 단일 매니퓰레이터 데모
2. `bipeds.py` : 이족 로봇 시뮬레이션 데모
3. `quadrupeds.py` : 사족 로봇 시뮬레이션 데모
4. `hands.py` : 덱스터러스 핸드 데모
5. `quadcopter.py` : 쿼드콥터 데모
6. `h1_locomotion.py` : H1 보행 정책 인터랙티브 데모
7. `pick_and_place.py` : 키보드 조작 기반 Pick-and-Place 데모
8. `bin_packing.py` : 다중 오브젝트 랜덤 적재
9. `multi_asset.py` : 다중 환경에서 다중 자산 스폰 데모
10. `deformables.py` : 변형체 스폰 데모
11. `procedural_terrain.py` : 절차적 지형 생성/시각화 데모
12. `markers.py` : 마커 시각화 데모
13. `haply_teleoperation.py` : Haply 장치 기반 원격조작 데모
### 센서 데모 (`scripts/demos/sensors`)
1. `cameras.py` : 카메라 센서 데모
2. `contact_sensor.py` : 접촉 센서 데모
3. `frame_transformer_sensor.py` : Frame Transformer 센서 데모
4. `imu_sensor.py` : IMU 센서 데모
5. `raycaster_sensor.py` : Raycaster 센서 데모
6. `multi_mesh_raycaster.py` : 다중 메시 레이캐스터 데모
7. `multi_mesh_raycaster_camera.py` : 다중 메시 레이캐스터 카메라 데모
8. `tacsl_sensor.py` : TacSL 촉각 센서 데모
---
<a id="sec-env"></a>
## 환경 유틸
`scripts/environments` (8개 스크립트)
1. `list_envs.py` : 등록된 Isaac 환경 목록 출력
2. `random_agent.py` : 랜덤 액션 에이전트로 환경 step
3. `zero_agent.py` : 0 액션 에이전트로 환경 step
4. `export_IODescriptors.py` : IO descriptor 내보내기
5. `state_machine/lift_cube_sm.py` : Lift cube 상태기계 데모
6. `state_machine/lift_teddy_bear.py` : Teddy bear lift 상태기계 데모
7. `state_machine/open_cabinet_sm.py` : 캐비닛 열기 상태기계 데모
8. `teleoperation/teleop_se3_agent.py` : SE3 텔레오퍼레이션 에이전트
---
<a id="sec-rl"></a>
## 강화학습
`scripts/reinforcement_learning` (22개 스크립트, 커스텀 수집 스크립트 제외)
### `rsl_rl`
1. `train.py` : RSL-RL 학습
2. `play.py` : 학습 정책 플레이
3. `cli_args.py` : RSL-RL CLI 인자 모듈
### `rl_games`
1. `train.py` : RL-Games 학습
2. `play.py` : RL-Games 플레이
### `sb3`
1. `train.py` : Stable-Baselines3 학습
2. `play.py` : Stable-Baselines3 플레이
### `skrl`
1. `train.py` : SKRL 학습
2. `play.py` : SKRL 플레이
### `ray`
1. `launch.py` : Ray 클러스터/실험 실행
2. `tuner.py` : 하이퍼파라미터 튜닝
3. `task_runner.py` : 학습 작업 실행 래퍼
4. `submit_job.py` : Ray job 제출
5. `wrap_resources.py` : 리소스 설정 래퍼
6. `util.py` : 유틸 함수
7. `mlflow_to_local_tensorboard.py` : MLflow 로그 변환
8. `grok_cluster_with_kubectl.py` : kubectl 기반 클러스터 점검
9. `hyperparameter_tuning/vision_cfg.py` : 비전 튜닝 설정
10. `hyperparameter_tuning/vision_cartpole_cfg.py` : 비전-cartpole 튜닝 설정
11. `cluster_configs/Dockerfile` : Ray 컨테이너 빌드 파일
12. `cluster_configs/google_cloud/kuberay.yaml.jinja` : GCP용 KubeRay 템플릿
---
<a id="sec-il"></a>
## 모방학습
`scripts/imitation_learning` (8개 스크립트)
### `robomimic`
1. `train.py` : robomimic 학습
2. `play.py` : robomimic 플레이
3. `robust_eval.py` : 강건성 평가
### `isaaclab_mimic`
1. `annotate_demos.py` : 데모 주석 작업
2. `generate_dataset.py` : 데이터셋 생성
3. `consolidated_demo.py` : 데모 통합 처리
### `locomanipulation_sdg`
1. `generate_data.py` : loco-manipulation 데이터 생성
2. `plot_navigation_trajectory.py` : 내비게이션 궤적 시각화
---
<a id="sec-bench"></a>
## 벤치마크
`scripts/benchmarks` (8개 스크립트)
1. `benchmark_cameras.py` : 카메라 성능 벤치마크
2. `benchmark_load_robot.py` : 로봇 로딩 성능 벤치마크
3. `benchmark_non_rl.py` : 비-RL 작업 벤치마크
4. `benchmark_rlgames.py` : RL-Games 학습 성능 벤치마크
5. `benchmark_rsl_rl.py` : RSL-RL 학습 성능 벤치마크
6. `benchmark_view_comparison.py` : View API 비교 벤치마크
7. `benchmark_xform_prim_view.py` : XformPrimView 벤치마크
8. `utils.py` : 벤치마크 공통 유틸
---
<a id="sec-sim2sim"></a>
## Sim-to-Sim 전이
`scripts/sim2sim_transfer` (5개 파일)
1. `rsl_rl_transfer.py` : RSL-RL 정책 sim2sim 전이 실행
2. `config/newton_to_physx_anymal_d.yaml` : ANYmal-D 전이 설정
3. `config/newton_to_physx_g1.yaml` : G1 전이 설정
4. `config/newton_to_physx_go2.yaml` : GO2 전이 설정
5. `config/newton_to_physx_h1.yaml` : H1 전이 설정
---
<a id="sec-tools"></a>
## 툴링
`scripts/tools` (18개 파일)
1. `convert_urdf.py` : URDF 변환
2. `convert_mjcf.py` : MJCF 변환
3. `convert_mesh.py` : 메쉬 변환
4. `convert_instanceable.py` : instanceable 변환
5. `check_instanceable.py` : instanceable 상태 점검
6. `process_meshes_to_obj.py` : 메쉬를 OBJ로 전처리
7. `blender_obj.py` : Blender OBJ 관련 유틸
8. `record_demos.py` : 데모 기록
9. `replay_demos.py` : 데모 재생
10. `merge_hdf5_datasets.py` : HDF5 데이터셋 병합
11. `hdf5_to_mp4.py` : HDF5 -> MP4 변환
12. `mp4_to_hdf5.py` : MP4 -> HDF5 변환
13. `train_and_publish_checkpoints.py` : 학습/체크포인트 배포 자동화
14. `cosmos/cosmos_prompt_gen.py` : Cosmos 프롬프트 생성
15. `cosmos/transfer1_templates.json` : Cosmos 템플릿
16. `test/test_cosmos_prompt_gen.py` : cosmos 유틸 테스트
17. `test/test_hdf5_to_mp4.py` : hdf5_to_mp4 테스트
18. `test/test_mp4_to_hdf5.py` : mp4_to_hdf5 테스트