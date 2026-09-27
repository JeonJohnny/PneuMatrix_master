# PneuMatrix

PneuMatrix 장비용 펌웨어와 Windows 제어 프로그램 배포 저장소입니다.

## 다운로드

| 구분 | 최신 버전 | 다운로드 |
| --- | --- | --- |
| 펌웨어 업데이터 설치파일 | 0.3.3 | [Setup EXE](https://github.com/JeonJohnny/PneuMatrix_master/releases/download/updater-v0.3.3/TKMS_Updater_Setup_0.3.3.exe) |
| 장비 펌웨어 파일 | 2.8.1 (HW v002 이상 호환) | [TKFW](https://github.com/JeonJohnny/PneuMatrix_master/releases/download/firmware-v2.8.1/TKMS_v002_v2.8.1_update.tkfw) |
| 윈도우 제어 소프트웨어 설치파일 (PneuMatrix Control) | 2.0.0 | [Windows 설치본](https://github.com/JeonJohnny/PneuMatrix_master/releases/download/control-v2.0.0/PneuMatrix-Setup-2.0.0.exe) |
| USB 드라이버 (CH340) | 제조사 제공 | [설치 안내](drivers/ch340/README.md) |

</br>

## 펌웨어와 PneuMatrix Control 호환성

| 장비 메인보드 버전 | 장비 펌웨어 버전 | PneuMatrix Control GUI | 설치·업데이트 안내 |
| --- | --- | --- | --- |
| **v002** | **2.8.1 이상** | **2.0.0** | 펌웨어 업데이트 가능 |
| v002 | 2.7.0–2.7.x | 2.0.0 | 펌웨어 업데이트 가능 |
| v002 | 2.5.1–2.6.x | 2.0.0 | 본사에 호환성 및 업데이트 지원 문의 |
| v002 | 2.5.0-2.5.x | — | 본사에 호환성 및 업데이트 지원 문의 |
| v001 | 2.1.0-2.4.x | — | 2025년 이전 제품 |

</br>

### 설치·업데이트 안내

- 위 표를 통해 현재 배포 프로그램의 통신 규격에 따라 호환 가능한 장비/GUI/펌웨어를 확인할 수 있습니다.
- `—`는 이 표에서 호환 버전을 제공하지 않는 경우입니다. 다른 HW 및 표에 없는 FW는 적용 전 본사에 문의 부탁드립니다.
- 펌웨어 업데이터는 **최소 v2.7.0 이상의 펌웨어**를 요구하며, 선택한 패키지보다 높은 버전에서 내리는 작업은 지원하지 않습니다.

</br>

## 빠른 설치

1. [상세 설치 안내](docs/INSTALL.md)를 확인합니다.
2. USB 연결 후 포트가 나타나지 않으면 CH340 드라이버를 설치합니다.
3. PneuMatrix Control 설치 파일을 실행합니다. 별도의 환경 세팅은 필요하지 않습니다.
4. 장비를 연결하고 모델·메인보드 HW·펌웨어 버전을 확인합니다.
5. 펌웨어 업데이트가 필요하면 제어 프로그램을 종료하고 **펌웨어 업데이터**에서 최신 버전의 펌웨어 파일(`.tkfw`)을 선택합니다.

</br>

## 폴더 안내

- [firmware](firmware/README.md): 업데이터·펌웨어 다운로드 및 사용 방법
- [Window Control Software](Window%20Control%20Software/README.md): PneuMatrix Control 설치·사용 안내
- [drivers/ch340](drivers/ch340/README.md): 드라이버 공식 다운로드와 포트 확인
- [문제 해결](docs/TROUBLESHOOTING.md)
- 개발자 매뉴얼 v1.3: [한국어](docs/DEVELOPER_MANUAL_KO.md) | [English](docs/DEVELOPER_MANUAL_EN.md) — FW v2.8.1 기준 명령·응답 및 연동 개발 안내
- [변경 이력](CHANGELOG.md) / [이전 버전](https://github.com/JeonJohnny/PneuMatrix_master/releases)

설치 파일은 각 Release의 **Assets**에서 받으세요. GitHub의 `Source code (zip)`은 안내 문서이며 설치 묶음이 아닙니다. 실제 EXE와 TKFW는 Releases에서 관리합니다.
