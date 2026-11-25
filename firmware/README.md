# 펌웨어 및 업데이터

| 구성품 | 버전 | 대상 메인보드 HW | 다운로드 |
| --- | --- | --- | --- |
| Firmware Updater | 0.4.2 | v002 | [설치 EXE](https://github.com/JeonJohnny/PneuMatrix_master/releases/download/updater-v0.4.2/TKMS_Updater_Setup_0.4.2.exe) |
| 사용자용 펌웨어 | 3.1.0 | v002 | [TKMS_v002_v3.1.0_update.tkfw](https://github.com/JeonJohnny/PneuMatrix_master/releases/download/firmware-v3.1.0/TKMS_v002_v3.1.0_update.tkfw) |

프로그램·장비 버전 조합은 [메인 호환성 표](../README.md#펌웨어와-pneumatrix-control-호환성)를 확인하세요.
</br>

## 설치·업데이트 조건

- Windows x64, 장비 한 대와 USB 직결.
- TKPM 계열 모델, 메인보드 HW v002, 현재 FW 3.0.0 이상(3.1.0 파일 기준).
- 선택한 패키지보다 높은 버전에서 낮은 버전으로 내리기는 미지원.
- 사용자용 업데이트는 기존 장비 정보와 장비 내 calibration table을 유지한 채로 업데이트 됩니다.

**FW 2.x 장비는 3.x로 업데이트할 수 없습니다.** 3.0.0부터 압력 보정 방식이 바뀌어 장비에 저장된 보정값을 쓸 수 없고 본사 재보정이 필요합니다. 업데이터 0.4.2는 이 경우 장비에 아무것도 쓰지 않고 멈춥니다. 2.x 장비이거나 장비 정보 확인에 실패하면 본사에 문의하세요.
</br>

## 업데이트 순서

1. 업데이터 설치 EXE와 장비에 맞는 `.tkfw`를 다운로드합니다.
2. 장비 운전을 중지하고 `RS485 케이블`를 분리합니다.(여러 장비 동시 제어용 데이지체인 형태로 사용하지 않는 경우 무시)
3. PneuMatrix 제어 프로그램 및 장비와 연결된 모든 프로그램(UART)을 종료합니다.
4. 장비를 USB로 연결하고 업데이터에서 COM 포트를 선택합니다.
5. 장비 식별 정보의 모델·HW·FW를 확인합니다.
6. **펌웨어 파일 선택 (.tkfw)**에서 파일을 선택하고 업데이트를 시작합니다.
7. 완료가 표시될 때까지 USB와 전원을 유지합니다.
포트 목록은 CH340K(USB VID 1A86 / PID 7522)를 대상으로 하며 기본 통신 속도는 115200입니다.
</br>

## 재설치 및 제거

설치본 0.4.2를 다시 실행하면 재설치 / 제거 / 클린 삭제를 선택할 수 있습니다.
일반 제거는 백업·작업 기록을 유지합니다. 클린 삭제는 확인 후 해당 기록까지 삭제합니다.
프로그램 제거로 장비 내부 펌웨어가 삭제되지는 않습니다.
</br>

## 배포 범위

현재 개별 배포 펌웨어는 사용자용 3.1.0입니다. 2.x 장비용 2.8.1은 [Releases](https://github.com/JeonJohnny/PneuMatrix_master/releases/tag/firmware-v2.8.1)에 남아 있습니다. 이전 설치 파일은 [Releases](https://github.com/JeonJohnny/PneuMatrix_master/releases)에서 확인할 수 있습니다.

통신 연동은 개발자 매뉴얼 [한국어](../docs/DEVELOPER_MANUAL_KO.md) / [English](../docs/DEVELOPER_MANUAL_EN.md)를 참고하세요.
