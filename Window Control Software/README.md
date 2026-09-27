# PneuMatrix Control — Windows

밸브·압력 제어, 시퀀스 실행 및 실시간 모니터링 프로그램입니다.

| 프로그램 | 버전 | 운영체제 | 다운로드 |
| --- | --- | --- | --- |
| PneuMatrix Control | 2.0.0 | Windows x64 | [설치 EXE](https://github.com/JeonJohnny/PneuMatrix_master/releases/download/control-v2.0.0/PneuMatrix-Setup-2.0.0.exe) |

메인보드 HW·펌웨어·프로그램 버전 조합은 [메인 호환성 표](../README.md#펌웨어와-pneumatrix-control-호환성)를 확인하세요.

## 설치 및 연결

1. 설치 EXE를 다운로드하여 실행합니다. Python이나 개발 도구는 필요하지 않습니다.
2. 장비 USB 포트가 인식되지 않으면 [CH340 드라이버](../drivers/ch340/README.md)를 설치합니다.
3. 프로그램에서 포트와 장비 주소를 선택해 연결합니다.
4. 장비 모델·메인보드 HW·펌웨어 버전을 확인한 후 사용합니다.

펌웨어 업데이터와 동시에 같은 COM 포트를 사용하지 마세요.
펌웨어를 변경할 때는 제어 프로그램을 종료하고 [업데이트 안내](../firmware/README.md)를 따릅니다.

프로그램 창에는 `PneuMatrix 사용자 GUI`로 표시될 수 있습니다.
이전 배포 여부는 [Releases](https://github.com/JeonJohnny/PneuMatrix_master/releases)에서 확인하세요.
