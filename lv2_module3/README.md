# 모듈 3 추가 과제 — 비전 토픽 수신과 설정값 비교 실험

팀 프로젝트 비전 노드로 Raw Image·Mask·검출 결과 토픽을 받아 보고, HSV S 하한과 채움률 하한을 바꿔 가며 결과를 비교했다. 자세한 내용은 [report.md](report.md)에 있다.

## 코드 출처

- 팀 프로젝트 B팀_호루스(Module 5 추적 과제) 저장소의 `perception` 패키지를 그대로 사용했다.
  - 저장소: https://github.com/cowsjh/Lv2_Horus_Assignment
  - 브랜치·커밋: `main` / `ef3c44f` (2026-10-08, PR #33 병합 시점)
- **코드는 수정하지 않았다.** 설정 파일 복사본과 기존 ROS 2 파라미터(`config_path`, `publish_debug_image`, `publish_debug_mask`)만 사용했다.
- 모터 제어 노드(`center`, `control`)는 실행하지 않았다.

## 실행 환경

| 항목 | 값 |
| --- | --- |
| PC | Ubuntu 24.04 (노트북, 카메라 USB 직결) |
| ROS 2 | Lyrical (`/opt/ros/lyrical`) |
| 카메라 | Intel RealSense D435, `realsense2_camera` (`/opt/ros/lyrical`) |
| 컬러 영상 | 640×480, `rgb8`, 수신 약 30 Hz (`ros2 topic hz` 평균 29.98) |
| 수신 도구 | `rqt_image_view`, `ros2 topic echo`, `ros2 topic info -v` |
| 입력 | D435 실시간 영상 (카메라·블록 고정) |

`rqt_graph`는 PySide6 의존성(`libpyside6-dev` 등)이 Ubuntu 24.04 저장소에 없어 설치하지 못했고, 노드·토픽 연결은 `ros2 topic info -v`로 확인했다.

## 빌드

```bash
source /opt/ros/lyrical/setup.bash
cd <팀 저장소 경로>
colcon build
source install/setup.bash
```

## 실행 명령

```bash
# 터미널 1: 카메라
ros2 launch realsense2_camera rs_launch.py

# 터미널 2: 비전 노드 (실험별 설정 파일 지정)
ros2 run perception perception_node --ros-args \
  -p config_path:=~/exp/exp1_A.yaml \
  -p publish_debug_image:=true -p publish_debug_mask:=true

# 터미널 3: 검출 수치
ros2 topic echo /target --qos-reliability best_effort

# 터미널 4: 이미지 확인 (Raw / Mask / Overlay)
ros2 run rqt_image_view rqt_image_view
```

## 설정값 변경·적용 방법

`perception_node`는 `perception.yaml`의 `detector` 섹션을 **노드 시작 시 한 번만** 읽는다(`load_config`). 실행 중 변경은 반영되지 않으므로 A↔B 전환마다 노드를 재시작했다.

팀 원본(`lv2_module5/config/perception.yaml`)은 건드리지 않고 복사본을 만들어 `config_path` 파라미터로 지정했다.

```bash
mkdir -p ~/exp
cp lv2_module5/config/perception.yaml ~/exp/base.yaml
sed -i 's/max_distance_m: 1.5 /max_distance_m: null/' ~/exp/base.yaml   # 모든 실험 depth 미사용으로 고정

cp ~/exp/base.yaml ~/exp/exp1_A.yaml                                   # S 하한 190 (원본값)
cp ~/exp/base.yaml ~/exp/exp1_B.yaml
sed -i 's/\[98, 190, 25\]/[98, 120, 25]/' ~/exp/exp1_B.yaml           # S 하한 120

cp ~/exp/exp1_A.yaml ~/exp/exp2_A.yaml                                 # min_fill_ratio 0.5 (원본값)
cp ~/exp/exp1_A.yaml ~/exp/exp2_B.yaml
sed -i 's/min_fill_ratio: 0.5 /min_fill_ratio: 0.4/' ~/exp/exp2_B.yaml # min_fill_ratio 0.4
```

적용 확인은 노드 시작 로그의 `검출 설정:` 줄로 했다. 예:

```
설정: /home/pa22/exp/exp2_B.yaml (config_path 지정)
검출 설정: HSV [98,190,25]~[118,255,230], blur 5, morph 5 (open 1, close 2), min_area 300px, bbox rotated, resize 원본, depth 끔, min_fill 0.4
```

## 결과 파일 위치

| 위치 | 내용 |
| --- | --- |
| [results/README.md](results/README.md) | 증빙 파일 목록과 표시 규칙 |
| `results/config/` | 실험별 실제 적용 설정 파일 4개 |
| `results/images/` | 실험별 A·B rqt 캡처 (Mask / Overlay / Raw) |
| `results/logs/` | 토픽 정보, 실험별 노드 로그·`/target` 출력, 목표 없음 확인 |
