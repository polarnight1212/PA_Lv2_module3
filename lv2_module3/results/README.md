# 증빙 파일 목록

모든 실험은 D435 실시간 입력, 카메라·목표 고정 상태에서 수행했다. 파일 이름은 `실험번호_A|B`로 표시한다.

## config/ — 실제 적용 설정

| 파일 | 실험 | 바뀐 값 |
| --- | --- | --- |
| `exp1_A.yaml` | 1-A | S 하한 190 |
| `exp1_B.yaml` | 1-B | S 하한 120 |
| `exp2_A.yaml` | 2-A | `min_fill_ratio` 0.5 |
| `exp2_B.yaml` | 2-B | `min_fill_ratio` 0.4 |

공통: `max_distance_m: null`, 나머지는 팀 원본 `perception.yaml`과 동일.

## images/ — rqt 캡처

한 화면에 왼쪽부터 `/target/debug_mask` | `/target/debug_image` | `/camera/camera/color/image_raw`. 각 창 상단 드롭다운에 토픽 이름이 보인다.

| 파일 | 실험 | 캡처 시각 (파일명 원본 타임스탬프) |
| --- | --- | --- |
| `exp1_A.png` | 1-A | 1791776570 |
| `exp1_B.png` | 1-B | 1791776703 |
| `exp2_A.png` | 2-A | 1791781461 |
| `exp2_B.png` | 2-B | 1791781692 |

실험 1-A 캡처는 2.1~2.3절의 Raw·Mask·Overlay 수신 증거로도 사용한다.

## logs/ — 터미널 출력 (원문 그대로 발췌)

| 파일 | 내용 |
| --- | --- |
| `topic_info.txt` | `ros2 node list`, `ros2 topic info -v`, Raw 해상도·encoding, `/target` 수신 예시, `topic hz` |
| `no_target_echo.txt` | 목표 없음 상태 `/target` (z=0) |
| `exp1_A.txt` | 1-A 노드 로그(설정 적용 줄, stats) + `/target` 출력 |
| `exp1_B.txt` | 1-B 노드 로그 + `/target` 출력 |
| `exp2_A.txt` | 2-A 노드 로그 + `/target` 출력 |
| `exp2_B.txt` | 2-B 노드 로그 + `/target` 출력 |
