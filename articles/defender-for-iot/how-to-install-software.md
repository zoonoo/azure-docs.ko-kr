---
title: IoT 설치용 Defender
description: IoT 용 Azure Defender에 대 한 센서 및 온-프레미스 관리 콘솔을 설치 하는 방법에 대해 알아봅니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/2/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 2bd994f14863715274e137bce2dd6873eeec1135
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208763"
---
# <a name="defender-for-iot-installation"></a>IoT 설치용 Defender

이 문서에서는 IoT 용 Azure Defender의 다음 요소를 설치 하는 방법을 설명 합니다.

- **센서**: IoT 센서에 대 한 Defender는 수동 (에이전트 없는) 모니터링을 사용 하 여 ICS 네트워크 트래픽을 수집 합니다. 수동 및 비-간섭, 센서는 OT 및 IoT 네트워크 및 장치에 대 한 영향을 0으로 설정 합니다. 센서는 범위 포트나 네트워크 탭에 연결 하 고 즉시 네트워크 모니터링을 시작 합니다. 감지는 센서 콘솔에 표시 됩니다. 여기에서 네트워크 맵, 장치 인벤토리 및 광범위 한 보고서에서이를 보고 조사 하 고 분석할 수 있습니다. 예를 들면 위험 평가 보고서, 데이터 마이닝 쿼리 및 공격 벡터가 있습니다. [IoT 용 Defender 센서 사용자 가이드](https://aka.ms/AzureDefenderforIoTUserGuide)의 센서 기능에 대해 자세히 알아보세요 (직접 다운로드).

- 온 **-프레미스 관리 콘솔**: 온-프레미스 관리 콘솔을 사용 하 여 장치 관리, 위험 관리 및 취약성 관리를 수행할 수 있습니다. 또한 엔터프라이즈 전체에서 위협 모니터링 및 인시던트 대응을 수행 하는 데 사용할 수 있습니다. 센서를 배포한 시설에서 감지 된 모든 네트워크 장치, 키 IoT 및 OT 위험 표시기와 경고에 대 한 통합 보기를 제공 합니다. 온-프레미스 관리 콘솔을 사용 하 여 gapped 네트워크에서 센서를 보고 관리 합니다.

이 문서에서는 다음과 같은 설치 정보를 다룹니다.

  - **하드웨어:** Dell 및 HPE 실제 어플라이언스 세부 정보

  - **소프트웨어:** 센서 및 온-프레미스 관리 콘솔 소프트웨어 설치.

  - **가상 어플라이언스:** 가상 컴퓨터 세부 정보 및 소프트웨어 설치.

설치 후 센서를 네트워크에 연결 합니다.

## <a name="about-defender-for-iot-appliances"></a>IoT 어플라이언스에 대 한 Defender 정보 

다음 섹션에서는 iot 센서 어플라이언스 용 Defender와 IoT 온-프레미스 관리 콘솔용 Defender에 대 한 정보를 제공 합니다.

### <a name="physical-appliances"></a>물리적 어플라이언스

IoT 어플라이언스 센서 용 Defender는 범위 포트 또는 네트워크 탭에 연결 하 고, 수동 (에이전트 없는) 모니터링을 사용 하 여 ICS 네트워크 트래픽 수집을 즉시 시작 합니다. 이 프로세스는 데이터 경로에 배치 되지 않고 장치를 적극적으로 검색 하지 않으므로 네트워크 및 장치에 대 한 영향을 0으로 설정 합니다.

다음 랙 탑재 어플라이언스를 사용할 수 있습니다.

| **배포 유형** | **회사** | **엔터프라이즈** | **SMB** |  |
|--|--|--|--|--|
| **모델** | HPE 프로라이언트 DL360 | Dell PowerEdge R340 XL | HPE 프로라이언트 DL20 | HPE 프로라이언트 DL20 |
| **포트 모니터링** | 최대 15 개의 RJ45 또는 8 옵트인 | 최대 9 개 RJ45 또는 6 옵트인 | 최대 8 개 RJ45 또는 6 옵트인 | 4 RJ45 |
| **최대 대역폭 \** _ | 3 g b/초 | 1 g b/초 | 1 g b/초 | 100 m b/초 |
| _ *최대 보호 장치** | 30,000 | 10000 | 15,000 | 1,000 |

* 최대 대역폭 용량은 프로토콜 배포에 따라 다를 수 있습니다.

### <a name="virtual-appliances"></a>가상 어플라이언스

다음 가상 어플라이언스를 사용할 수 있습니다.

| **배포 유형** | **엔터프라이즈** | **SMB** | **Line** |
|--|--|--|--|
| **설명** | 엔터프라이즈 배포용 가상 어플라이언스 | SMB 배포용 가상 어플라이언스 | 회선 배포를 위한 가상 어플라이언스 |
| **최대 대역폭 \** _ | 150 m b/초 | 15 m b/초 | 3 m b/초 |
| _ *최대 보호 장치** | 3,000 | 300 | 100 |
| **배포 유형** | Enterprise | SMB | 줄 |
| **설명** | 엔터프라이즈 배포용 가상 어플라이언스 | SMB 배포용 가상 어플라이언스 | 회선 배포를 위한 가상 어플라이언스 |

* 최대 대역폭 용량은 프로토콜 배포에 따라 다를 수 있습니다.

### <a name="hardware-specifications-for-the-on-premises-management-console"></a>온-프레미스 관리 콘솔에 대 한 하드웨어 사양

 | 항목 | 설명 |
 |----|--|
 **설명** | 다중 계층 아키텍처에서 온-프레미스 관리 콘솔은 지리적으로 분산 된 사이트에 대 한 가시성 및 제어를 제공 합니다. SIEMs, 티켓 시스템, 차세대 방화벽, 보안 원격 액세스 플랫폼, IoT for IoT ICS 맬웨어 샌드박스에 포함 된 SOC 보안 스택과 통합 됩니다. |
 **배포 유형** | Enterprise |
 **어플라이언스 유형**  | Dell R340, VM |
 **관리 센서 수** | 제한 없음 |

## <a name="prepare-for-the-installation"></a>설치 준비

### <a name="access-the-iso-installation-image"></a>ISO 설치 이미지 액세스

설치 이미지는 IoT 용 Defender 포털에서 액세스할 수 있습니다.

파일에 액세스 하려면:

1. IoT 계정에 대 한 Defender에 로그인 합니다.

2. **네트워크 센서** 또는 **온-프레미스 관리 콘솔** 페이지로 이동 하 여 다운로드할 버전을 선택 합니다.

### <a name="install-from-dvd"></a>DVD에서 설치

설치 하기 전에 다음이 있는지 확인 합니다.

- USB 커넥터를 사용 하는 휴대용 DVD 드라이브입니다.

- ISO 설치 관리자 이미지

설치하려면 다음을 수행합니다.

1. 이미지를 DVD로 굽거나 키에서 디스크를 준비 합니다. 휴대용 DVD 드라이브를 컴퓨터에 연결 하 고, ISO 이미지를 마우스 오른쪽 단추로 클릭 하 고, **디스크에 굽기** 를 선택 합니다.

1. 키에 DVD 또는 디스크를 연결 하 고 키에 대 한 DVD 또는 디스크에서 부팅 하도록 어플라이언스를 구성 합니다.

### <a name="install-from-disk-on-a-key"></a>키에 디스크에서 설치

설치 하기 전에 다음이 있는지 확인 합니다.

  - Rufus가 설치 되었습니다.
  
  - USB 버전 3.0 이상을 사용 하는 키의 디스크입니다. 최소 크기는 4gb입니다.

  - ISO 설치 관리자 이미지 파일입니다.

키의 디스크는이 프로세스에서 지워집니다.

키를 통해 디스크를 준비 하려면:

1. Rufus를 실행 하 고 **센서 ISO** 를 선택 합니다.

2. 키의 디스크를 전면 패널에 연결 합니다.

3. USB에서 부팅할 서버의 BIOS를 설정 합니다.

## <a name="dell-poweredger340xl-installation"></a>Dell PowerEdgeR340XL 설치

Dell 어플라이언스에 소프트웨어를 설치 하기 전에 어플라이언스의 BIOS 구성을 조정 해야 합니다.

  - [Dell Poweredge R340 전면 패널](#dell-poweredge-r340-front-panel) 및 [Dell Poweredge R340 Back 패널](#dell-poweredge-r340-back-panel) 에는 드라이버 및 포트와 같은 설치에 필요한 정보와 함께 Front 및 back 패널에 대 한 설명이 포함 되어 있습니다.

  - [DELL Bios 구성은](#dell-bios-configuration) dell 어플라이언스 관리 인터페이스에 연결 하 고 BIOS를 구성 하는 방법에 대 한 정보를 제공 합니다.

  - [소프트웨어 설치 (Dell R340)](#software-installation-dell-r340) 는 IoT 센서 소프트웨어용 Defender를 설치 하는 데 필요한 절차를 설명 합니다.

### <a name="dell-poweredge-r340xl-requirements"></a>Dell PowerEdge R340XL 요구 사항 

Dell PowerEdge R340XL 어플라이언스를 설치 하려면 다음이 필요 합니다.

- Dell 원격 액세스 컨트롤러에 대 한 엔터프라이즈 라이선스 (iDrac)

- BIOS 구성 XML

- 서버 펌웨어 버전:

  - BIOS 버전 2.1.6

  - iDrac 버전 3.23.23.23

### <a name="dell-poweredge-r340-front-panel"></a>Dell PowerEdge R340 전면 패널

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-front-panel.jpg" alt-text="Dell PowerEdge R340 전면 패널.":::

 1. 왼쪽 제어판 
 2. 광학 드라이브 (선택 사항) 
 3. 오른쪽 제어판 
 4. 정보 태그 
 5. 드라이브  

### <a name="dell-poweredge-r340-back-panel"></a>Dell PowerEdge R340 뒤로 패널

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-back-panel.jpg" alt-text="Dell PowerEdge R340 뒤로 패널.":::

1. 직렬 포트 
2. NIC 포트 (Gb 1) 
3. NIC 포트 (Gb 1) 
4. 절반 높이 PCIe 
5. 전체 높이 PCIe 확장 카드 슬롯 
6. 전원 공급 장치 1 
7. 전원 공급 장치 2 
8. 시스템 id 
9. 시스템 상태 표시기 케이블 포트 (CMA) 단추 
10. USB 3.0 포트 (2) 
11. iDRAC9 전용 네트워크 포트 
12. VGA 포트 

### <a name="dell-bios-configuration"></a>Dell BIOS 구성

Dell 어플라이언스를 조정 하 여 소프트웨어를 사용 하려면 dell BIOS를 구성 해야 합니다.

BIOS 구성은 미리 정의 된 구성을 통해 수행 됩니다. [도움말 센터](https://help.cyberx-labs.com/)에서 파일에 액세스할 수 있습니다.

Dell 어플라이언스로 구성 파일을 가져옵니다. 구성 파일을 사용 하기 전에 Dell 기기와 관리 컴퓨터 간의 통신을 설정 해야 합니다.

Dell 어플라이언스는 수명 주기 컨트롤러 (LC)와 함께 통합 iDRAC 관리 됩니다. LC는 모든 Dell PowerEdge 서버에 포함 되어 있으며, Dell PowerEdge 어플라이언스를 배포, 업데이트, 모니터링 및 유지 관리 하는 데 도움이 되는 기능을 제공 합니다.

Dell 어플라이언스와 관리 컴퓨터 간의 통신을 설정 하려면 동일한 서브넷에서 iDRAC IP 주소와 관리 컴퓨터의 IP 주소를 정의 해야 합니다.

연결이 설정 되 면 BIOS를 구성할 수 있습니다.

Dell BIOS를 구성 하려면:

1. [IDRAC IP 주소 구성](#configure-idrac-ip-address)

2. [BIOS 구성 파일 가져오기](#import-the-bios-configuration-file)

#### <a name="configure-idrac-ip-address"></a>IDRAC IP 주소 구성

1. 센서를 거듭제곱 합니다.

2. OS가 이미 설치 되어 있는 경우 F2 키를 선택 하 여 BIOS 구성을 입력 합니다.

3. **IDRAC 설정** 을 선택 합니다.

4. **네트워크** 를 선택 합니다.

   > [!NOTE]
   > 설치 하는 동안 다음 단계에서 설명 하는 기본 iDRAC IP 주소와 암호를 구성 해야 합니다. 설치 후에 이러한 정의를 변경 합니다.

5. 고정 IPv4 주소를 **10.100.100.250** 로 변경 합니다.

6. 정적 서브넷 마스크를 **255.255.255.0** 으로 변경 합니다.

   :::image type="content" source="media/tutorial-install-components/idrac-network-settings-screen-v2.png" alt-text="정적 서브넷 마스크를 보여 주는 스크린샷":::

7. **백**  >  **마침** 을 선택 합니다.

#### <a name="import-the-bios-configuration-file"></a>BIOS 구성 파일 가져오기

이 문서에서는 구성 파일을 사용 하 여 BIOS를 구성 하는 방법을 설명 합니다.

1. 미리 구성 된 정적 IP 주소 **10.100.100.200** 를 사용 하 여 PC를 **iDRAC** 포트에 연결 합니다.

   :::image type="content" source="media/tutorial-install-components/idrac-port.png" alt-text="미리 구성 된 IP 주소 포트의 스크린샷":::

2. 브라우저를 열고 **10.100.100.250** 를 입력 하 여 iDRAC 웹 인터페이스에 연결 합니다.

3. Dell 기본 관리자 권한으로 로그인 합니다.

   - 사용자 이름: **root**

   - 암호: **calvin**

4. 어플라이언스의 자격 증명은 다음과 같습니다.

   - 사용자 이름: **XXX**

   - 암호: **XXX**

     서버 프로필 가져오기 작업이 시작 되었습니다.

     > [!NOTE]
     > 파일을 가져오기 전에 다음을 확인 합니다.
     > - 현재 iDRAC에 연결 된 유일한 사용자입니다.
     > - 시스템이 BIOS 메뉴에 없습니다.

5. **구성**  >  **서버 구성 프로필** 로 이동 합니다. 다음 매개 변수를 설정합니다.

   :::image type="content" source="media/tutorial-install-components/configuration-screen.png" alt-text="서버 프로필의 구성을 보여 주는 스크린샷":::

   | 매개 변수 | 구성 |
   |--|--|
   | 위치 유형 | **로컬** 을 선택 합니다. |
   | 파일 경로 | **파일 선택** 을 선택 하 고 구성 XML 파일을 추가 합니다. |
   | 구성 요소 가져오기 | **BIOS, NIC, RAID를** 선택 합니다. |
   | 최대 대기 시간 | **20 분** 을 선택 합니다. |

6. **가져오기** 를 선택합니다.

7. 프로세스를 모니터링 하려면 **유지 관리**  >  **작업 큐** 로 이동 합니다.

   :::image type="content" source="media/tutorial-install-components/view-the-job-queue.png" alt-text="작업 큐를 보여 주는 스크린샷":::

#### <a name="manually-configuring-bios"></a>수동으로 BIOS 구성 

다음과 같은 경우에는 어플라이언스 BIOS를 수동으로 구성 해야 합니다.

- 화살표를 통해 어플라이언스를 구입 하지 않았습니다.

- 기기가 있지만 XML 구성 파일에 대 한 액세스 권한이 없습니다.

BIOS에 액세스 한 후 **장치 설정** 으로 이동 합니다.

수동으로 구성 하려면:

1. 키보드 및 화면을 사용 하 여 직접 어플라이언스 BIOS에 액세스 하거나 iDRAC를 사용 합니다.

   - 기기가 IoT 어플라이언스 용 Defender가 아닌 경우 브라우저를 열고 이전에 구성한 IP 주소로 이동 합니다. Dell 기본 관리자 권한으로 로그인 합니다. Username 및 **calvin** 에 대 한 **root** 를 사용 하 여 암호를 입력 합니다.

   - 기기가 IoT 어플라이언스 용 Defender 인 경우에는 **xxx** 를 사용 하 여 로그인 하 고 암호에 **xxx** 를 사용 하 여 로그인 합니다.

2. BIOS에 액세스 한 후 **장치 설정** 으로 이동 합니다.

3. **통합 된 raid 컨트롤러 1: DELL PERC \<PERC H330 Adapter\> 구성 유틸리티** 를 선택 하 여 raid 제어 구성을 선택 합니다.

4. **구성 관리** 를 선택 합니다.

5. **가상 디스크 만들기** 를 선택 합니다.

6. **RAID 수준 선택** 필드에서 **RAID5** 를 선택 합니다. **가상 디스크 이름** 필드에 **ROOT** 를 입력 하 고 **실제 디스크** 를 선택 합니다.

7. **모두 확인** 을 선택 하 고 **변경 내용 적용** 을 선택 합니다.

8. **확인** 을 선택합니다.

9. 아래로 스크롤하여 **가상 디스크 만들기** 를 선택 합니다.

10. **확인** 확인란을 선택 하 고 **예** 를 선택 합니다.

11. **확인** 을 선택합니다.

12. 주 화면으로 돌아가서 **시스템 BIOS** 를 선택 합니다.

13. **부팅 설정** 을 선택 합니다.

14. **부팅 모드** 옵션에서 **BIOS** 를 선택 합니다.

15. **뒤로** 를 선택 하 고 **마침** 을 선택 하 여 BIOS 설정을 종료 합니다.

### <a name="software-installation-dell-r340"></a>소프트웨어 설치 (Dell R340)

설치 프로세스는 약 20 분이 걸립니다. 설치 후 시스템이 여러 번 다시 시작 됩니다.

설치하려면 다음을 수행합니다.

1. 다음 방법 중 하나로 버전 미디어가 어플라이언스에 탑재 되었는지 확인 합니다.

   - 키의 외부 CD 또는 디스크를 릴리스와 연결 합니다.

   - IDRAC를 사용 하 여 ISO 이미지를 탑재 합니다. IDRAC에 로그인 한 후 가상 콘솔을 선택 하 고 **가상 미디어** 를 선택 합니다.

2. **CD/DVD 매핑** 섹션에서 **파일 선택** 을 선택 합니다.

3. 열리는 대화 상자에서이 버전에 대 한 버전 ISO 이미지 파일을 선택 합니다.

4. **장치 매핑** 단추를 선택 합니다.

   :::image type="content" source="media/tutorial-install-components/mapped-device-on-virtual-media-screen-v2.png" alt-text="매핑된 장치를 보여 주는 스크린샷":::

5. 미디어를 탑재 합니다. **닫기** 를 선택합니다.

6. 어플라이언스를 시작 합니다. IDRAC를 사용 하는 경우 **Consul 컨트롤** 단추를 선택 하 여 서버를 다시 시작할 수 있습니다. 그런 다음 **키보드 매크로** 에서 **적용** 단추를 선택 하면 Ctrl + Alt + Delete 시퀀스를 시작 합니다.

7. **영어** 를 선택 합니다.

8. **센서-릴리스- \<version\> 엔터프라이즈** 를 선택 합니다.

   :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="버전 선택 항목을 보여 주는 스크린샷":::   

9. 어플라이언스 프로필 및 네트워크 속성을 정의 합니다.

   :::image type="content" source="media/tutorial-install-components/appliance-profile-screen-v2.png" alt-text="어플라이언스 프로필을 보여 주는 스크린샷":::   

   | 매개 변수 | 구성 |
   |--|--|
   | **하드웨어 프로필** | **사내** |
   | **관리 인터페이스** | **eno1** |
   | **네트워크 매개 변수 (고객에서 제공)** | - |
   |**관리 네트워크 IP 주소:** | - |
   | **서브넷 마스크:** | - |
   | **어플라이언스 호스트 이름:** | - |
   | **DNS** | - |
   | **기본 게이트웨이 IP 주소:** | - |
   | **입력 인터페이스:** |  시스템에서 입력 인터페이스 목록을 생성 합니다. 입력 인터페이스를 미러링하려면 쉼표 구분 기호를 사용 하 여 목록에 표시 된 모든 항목을 복사 합니다. 브리지 인터페이스를 구성할 필요가 없습니다. 이 옵션은 특별 한 사용 사례로만 사용 됩니다. |

10. 약 10 분 후에 두 개의 자격 증명 집합이 표시 됩니다. 하나는 **CyberX** 사용자를 위한 것이 고 다른 하나는 **지원** 사용자를 위한 것입니다.  

11. 어플라이언스 ID와 암호를 저장 합니다. 플랫폼을 처음 사용할 때 플랫폼에 액세스 하려면 이러한 자격 증명이 필요 합니다.

12. **Enter 키** 를 선택 하 여 계속 합니다.

## <a name="hpe-proliant-dl20-installation"></a>HPE 프로라이언트 DL20 설치

이 문서에서는 다음 단계를 포함 하 여 HPE 프로라이언트 DL20 설치 프로세스에 대해 설명 합니다.

  - 원격 액세스를 사용 하도록 설정 하 고 기본 관리자 암호를 업데이트 합니다.
  - BIOS 및 RAID 설정을 구성 합니다.
  - 소프트웨어를 설치 합니다.

### <a name="about-the-installation"></a>설치 정보

  - 엔터프라이즈 및 SMB 어플라이언스를 설치할 수 있습니다. 설치 프로세스는 배열 구성을 제외 하 고 두 어플라이언스 유형 모두에 대해 동일 합니다.
  - 기본 관리자가 제공 됩니다. 네트워크 구성 프로세스 중에 암호를 변경 하는 것이 좋습니다.
  - 네트워크 구성 프로세스 중에 네트워크 포트 1에서 iLO 포트를 구성 합니다.
  - 설치 프로세스는 약 20 분이 걸립니다. 설치 후 시스템이 여러 번 다시 시작 됩니다.

### <a name="hpe-proliant-dl20-front-panel"></a>HPE 프로라이언트 DL20 전면 패널

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-front-panel-v2.png" alt-text="HPE 프로라이언트 DL20 전면 패널.":::

### <a name="hpe-proliant-dl20-back-panel"></a>HPE 프로라이언트 DL20 back 패널

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-back-panel-v2.png" alt-text="HPE 프로라이언트 DL20의 후면 패널":::

### <a name="enable-remote-access-and-update-the-password"></a>원격 액세스를 사용 하도록 설정 하 고 암호를 업데이트 합니다.

다음 절차를 사용 하 여 네트워크 옵션을 설정 하 고 기본 암호를 업데이트 합니다.

암호를 사용 하도록 설정 하 고 업데이트 하려면:

1. HP 어플라이언스에 화면과 키보드를 연결 하 고, 어플라이언스를 켜고, **F9** 키를 누릅니다.

    :::image type="content" source="media/tutorial-install-components/hpe-proliant-screen-v2.png" alt-text="HPE 프로라이언트 창을 보여 주는 스크린샷":::

2. **시스템 유틸리티**  >  **시스템 구성**  >  **iLO 5 구성 유틸리티**  >  **네트워크 옵션** 으로 이동 합니다.

    :::image type="content" source="media/tutorial-install-components/system-configuration-window-v2.png" alt-text="시스템 구성 창을 보여 주는 스크린샷":::

    1.  **네트워크 인터페이스 어댑터** 필드에서 **공유 네트워크 포트-LOM** 을 선택 합니다.
    
    1.  DHCP를 사용 하지 않습니다.
    
    1.  IP 주소, 서브넷 마스크 및 게이트웨이 IP 주소를 입력 합니다.

3. **F10: 저장** 을 선택 합니다.

4. **Esc 키** 를 선택 하 여 **ILO 5 구성 유틸리티로** 돌아가 **사용자 관리** 를 선택 합니다.

5. **사용자 편집/제거** 를 선택 합니다. 관리자는 기본 사용자만 정의 합니다. 

6. 기본 암호를 변경 하 고 **F10: 저장** 을 선택 합니다.

### <a name="configure-the-hpe-bios"></a>HPE BIOS 구성

다음 절차에서는 엔터프라이즈 및 SMB 어플라이언스에 대해 HPE BIOS를 구성 하는 방법을 설명 합니다.

HPE BIOS를 구성 하려면:

1. **시스템 유틸리티**  >  **시스템 구성**  >  **BIOS/플랫폼 구성 (RBSU)** 을 선택 합니다.

2. **BIOS/플랫폼 구성 (RBSU)** 양식에서 **부팅 옵션** 을 선택 합니다.

3. **부팅 모드** 를 **레거시 BIOS 모드로** 변경한 다음 **F10: 저장** 을 선택 합니다.

4. **Esc 키** 를 두 번 선택 하 여 **시스템 구성** 양식을 닫습니다.

#### <a name="for-the-enterprise-appliance"></a>엔터프라이즈 어플라이언스의 경우

1. **포함 된 RAID 1: HPE 스마트 배열 P408i-a SR Gen 10**  >  **배열 구성**  >  **배열 만들기** 를 선택 합니다.

2. **배열 만들기** 양식에서 모든 옵션을 선택 합니다. **엔터프라이즈** 어플라이언스에 대 한 세 가지 옵션을 사용할 수 있습니다.

#### <a name="for-the-smb-appliance"></a>SMB 어플라이언스의 경우

1. **포함 된 RAID 1: HPE 스마트 배열 P208i-a SR Gen 10**  >  **배열 구성**  >  **배열 만들기** 를 선택 합니다.

2. **다음 양식으로 이동을** 선택 합니다.

3. **Raid 수준 설정** 양식에서 엔터프라이즈 배포에 대해이 수준을 **raid 5** 로, SMB 배포용 **raid 1** 로 설정 합니다.

4. **다음 양식으로 이동을** 선택 합니다.

5. **논리 드라이브 레이블** 폼에 **논리 드라이브 1** 을 입력 합니다.

6. **변경 내용 전송** 을 선택 합니다.

7. **전송** 양식에서 **주 메뉴로 돌아가기** 를 선택 합니다.

8. **F10: 저장** 을 선택 하 고 **Esc** 키를 두 번 누릅니다.

9. **시스템 유틸리티** 창에서 **일회성 부팅 메뉴** 를 선택 합니다.

10. **일회성 부팅 메뉴** 양식에서 **레거시 BIOS One-Time 부팅 메뉴** 를 선택 합니다.

11. 레거시 및 **부팅 재정의** 창 **에서 부팅** 하는 것이 나타납니다. 부팅 재정의 옵션을 선택 합니다. 예를 들면 cd-rom, USB, HDD 또는 UEFI 셸에 있습니다.

    :::image type="content" source="media/tutorial-install-components/boot-override-window-one-v2.png" alt-text="첫 번째 부팅 재정의 창을 보여 주는 스크린샷":::

    :::image type="content" source="media/tutorial-install-components/boot-override-window-two-v2.png" alt-text="두 번째 부팅 재정의 창을 보여 주는 스크린샷":::
### <a name="software-installation-hpe-proliant-dl20-appliance"></a>소프트웨어 설치 (HPE 프로라이언트 DL20 어플라이언스)

설치 프로세스는 약 20 분이 걸립니다. 설치 후 시스템이 여러 번 다시 시작 됩니다.

소프트웨어를 설치 하려면:

1. 화면과 키보드를 어플라이언스에 연결한 다음 CLI에 연결 합니다.

2. IoT 포털 용 Defender의 **업데이트** 페이지에서 다운로드 한 ISO 이미지를 사용 하 여 키의 외부 CD 또는 디스크를 연결 합니다.

3. 어플라이언스를 시작 합니다.

4. **영어** 를 선택 합니다.

    :::image type="content" source="media/tutorial-install-components/select-english-screen.png" alt-text="CLI 창에서 영어를 선택 합니다.":::

5. **센서-릴리스- <version> 엔터프라이즈** 를 선택 합니다.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="버전을 선택 하는 화면의 스크린샷":::

6. 설치 마법사에서 어플라이언스 프로필 및 네트워크 속성을 정의 합니다.

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="설치 마법사를 보여 주는 스크린샷":::

    | 매개 변수 | 구성 |
    | ----------| ------------- |
    | **하드웨어 프로필** | SMB 배포용 **Enterprise** 또는 **Office** 를 선택 합니다. |
    | **관리 인터페이스** | **eno2** |
    | **기본 네트워크 매개 변수 (일반적으로 고객은 매개 변수를 제공)** | **관리 네트워크 IP 주소:** <br/> <br/>**어플라이언스 호스트 이름:** <br/>**DNS** <br/>**기본 게이트웨이 IP 주소:**|
    | **입력 인터페이스:** | 시스템에서 입력 인터페이스 목록을 생성 합니다.<br/><br/>입력 인터페이스를 미러링하려면 쉼표 구분 기호를 사용 하 여 목록에 표시 된 모든 항목을 복사 합니다. **eno5, eno3, eno1, eno6, eno4**<br/><br/>**HPE DL20의 경우: eno1, enp1s0f4u4 (iLo 인터페이스)를 나열 하지 않습니다.**<br/><br/>연결: 브리지 인터페이스를 구성할 필요가 **없습니다.** 이 옵션은 특별 한 사용 사례로만 사용 됩니다. **Enter** 키를 눌러 계속 진행합니다. |

7. 약 10 분 후에 두 개의 자격 증명 집합이 표시 됩니다. 하나는 **CyberX** 사용자를 위한 것이 고 다른 하나는 **지원** 사용자를 위한 것입니다.

8. 어플라이언스의 ID와 암호를 저장 합니다. 플랫폼에 처음으로 액세스 하려면 자격 증명이 필요 합니다.

9. **Enter 키** 를 선택 하 여 계속 합니다.

## <a name="hpe-proliant-dl360-installation"></a>HPE 프로라이언트 DL360 설치

  - 기본 관리자가 제공 됩니다. 네트워크 구성 중에 암호를 변경 하는 것이 좋습니다.

  - 네트워크를 구성 하는 동안 iLO 포트를 구성 합니다.

  - 설치 프로세스는 약 20 분이 걸립니다. 설치 후 시스템이 여러 번 다시 시작 됩니다.

### <a name="hpe-proliant-dl360-front-panel"></a>HPE 프로라이언트 DL360 전면 패널

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-front-panel.png" alt-text="HPE 프로라이언트 DL360 전면 패널.":::

### <a name="hpe-proliant-dl360-back-panel"></a>HPE 프로라이언트 DL360 back 패널

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-back-panel.png" alt-text="HPE 프로라이언트 DL360 back 패널.":::

### <a name="enable-remote-access-and-update-the-password"></a>원격 액세스를 사용 하도록 설정 하 고 암호를 업데이트 합니다.

HPE 프로라이언트 DL20 설치에 대 한 이전 섹션을 참조 하세요.

  - "원격 액세스를 사용 하도록 설정 하 고 암호 업데이트"

  - "HPE BIOS 구성"

엔터프라이즈 구성이 동일 합니다.

> [!Note]
> 배열 형식에서 모든 옵션을 선택 했는지 확인 합니다.

### <a name="ilo-remote-installation-from-a-virtual-drive"></a>iLO 원격 설치 (가상 드라이브에서)

이 절차에서는 가상 드라이브에서의 iLO 설치에 대해 설명 합니다.

설치하려면 다음을 수행합니다.

1. ILO 콘솔에 로그인 한 다음 서버 화면을 마우스 오른쪽 단추로 클릭 합니다.

2. **HTML5 콘솔** 을 선택 합니다.

3. 콘솔에서 CD 아이콘을 선택 하 고 CD/DVD 옵션을 선택 합니다.

4. **로컬 ISO 파일** 을 선택 합니다.

5. 대화 상자에서 관련 ISO 파일을 선택 합니다.

6. 왼쪽 아이콘으로 이동 하 여 **전원** 을 선택 하 고 **다시 설정** 을 선택 합니다.

7. 기기가 다시 시작 되 고 센서 설치 프로세스를 실행 합니다.

### <a name="software-installation-hpe-dl360"></a>소프트웨어 설치 (HPE DL360)

설치 프로세스는 약 20 분이 걸립니다. 설치 후 시스템이 여러 번 다시 시작 됩니다.

설치하려면 다음을 수행합니다.

1. 화면과 키보드를 어플라이언스에 연결한 다음 CLI에 연결 합니다.

2. IoT 포털 용 Defender의 **업데이트** 페이지에서 다운로드 한 ISO 이미지를 사용 하 여 키의 외부 CD 또는 디스크를 연결 합니다.

3. 어플라이언스를 시작 합니다.

4. **영어** 를 선택 합니다.

5. **센서-릴리스- <version> 엔터프라이즈** 를 선택 합니다.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="버전을 선택 하는 것을 보여 주는 스크린샷":::

6. 설치 마법사에서 어플라이언스 프로필 및 네트워크 속성을 정의 합니다.

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="설치 마법사를 보여 주는 스크린샷":::

    | 매개 변수 | 구성 |
    | ----------| ------------- |
    | **하드웨어 프로필** | **회사** 를 선택 합니다. |
    | **관리 인터페이스** | **eno2** |
    | **기본 네트워크 매개 변수 (고객에서 제공)** | **관리 네트워크 IP 주소:** <br/>**서브넷 마스크:** <br/>**어플라이언스 호스트 이름:** <br/>**DNS** <br/>**기본 게이트웨이 IP 주소:**|
    | **입력 인터페이스:**  | 시스템에서 입력 인터페이스 목록을 생성 합니다.<br/><br/>입력 인터페이스를 미러링하려면 쉼표 구분 기호를 사용 하 여 목록에 표시 된 모든 항목을 복사 합니다.<br/><br/>브리지 인터페이스를 구성할 필요가 없습니다. 이 옵션은 특별 한 사용 사례로만 사용 됩니다. |

7. 약 10 분 후에 두 개의 자격 증명 집합이 표시 됩니다. 하나는 **CyberX** 사용자를 위한 것이 고 다른 하나는 **지원** 사용자를 위한 것입니다.

8. 어플라이언스의 ID와 암호를 저장 합니다. 플랫폼에 처음으로 액세스 하려면 이러한 자격 증명이 필요 합니다.

9. **Enter 키** 를 선택 하 여 계속 합니다.

## <a name="sensor-installation-for-the-virtual-appliance"></a>가상 어플라이언스에 대 한 센서 설치

다음 아키텍처에서 IoT 용 Defender 센서에 대 한 가상 머신을 배포할 수 있습니다.


| 아키텍처 | 사양 | 사용량 | 의견 |
|---|---|---|---|
| **엔터프라이즈** | CPU: 8<br/>메모리: 32G RAM<br/>HDD: 1800 G B | 프로덕션 환경 | 기본 및 가장 일반적인 |
| **중소기업** | CPU: 4 <br/>메모리: 8G RAM<br/>HDD: 500 G B | 테스트 또는 소규모 프로덕션 환경 | -  |
| **Office** | CPU: 4<br/>메모리: 8G RAM<br/>HDD: 100 G B | 소규모 테스트 환경 | -  |

### <a name="prerequisites"></a>필수 조건

온-프레미스 관리 콘솔은 VMware 및 Hyper-v 배포 옵션을 모두 지원 합니다. 설치를 시작 하기 전에 다음 항목이 있는지 확인 합니다.

  - VMware (ESXi 5.5 이상) 또는 Hyper-v 하이퍼바이저 (Windows 10 Pro 또는 Enterprise) 설치 및 작동

  - 가상 컴퓨터에 대 한 사용 가능한 하드웨어 리소스

  - IoT 용 Azure Defender 센서에 대 한 ISO 설치 파일

하이퍼바이저가 실행 중인지 확인 합니다.

### <a name="create-the-virtual-machine-esxi"></a>가상 컴퓨터 만들기 (ESXi)

1. ESXi에 로그인 하 고, 관련 **데이터 저장소** 를 선택 하 고, **데이터 저장소 브라우저** 를 선택 합니다.

2. 이미지를 **업로드** 하 고 **닫기** 를 선택 합니다.

3. **Virtual Machines** 으로 이동한 다음 **VM 만들기/등록** 을 선택 합니다.

4. **새 가상 컴퓨터 만들기** 를 선택 하 고 **다음** 을 선택 합니다.

5. 센서 이름을 추가 하 고 다음을 선택 합니다.

   - 호환성: **&lt; 최신 ESXi 버전 &gt;**

   - 게스트 OS 제품군: **Linux**

   - 게스트 OS 버전: **Ubuntu Linux (64 비트)**

6. **다음** 을 선택합니다.

7. 관련 데이터 저장소를 선택 하 고 **다음** 을 선택 합니다.

8. 필요한 아키텍처에 따라 가상 하드웨어 매개 변수를 변경 합니다.

9. **CD/DVD 드라이브 1** 의 경우 **데이터 저장소 iso 파일** 을 선택 하 고 이전에 업로드 한 ISO 파일을 선택 합니다.

10. **다음** > **마침** 을 선택합니다.

### <a name="create-the-virtual-machine-hyper-v"></a>가상 컴퓨터 만들기 (Hyper-v)

이 절차에서는 Hyper-v를 사용 하 여 가상 컴퓨터를 만드는 방법을 설명 합니다.

가상 컴퓨터를 만들려면 다음을 수행 합니다.

1. Hyper-v 관리자에서 가상 디스크를 만듭니다.

2. **형식 = VHDX** 를 선택 합니다.

3. **유형 = 동적 확장** 을 선택 합니다.

4. VHD의 이름과 위치를 입력 합니다.

5. 아키텍처에 따라 필요한 크기를 입력 합니다.   

6. 요약을 검토하고 **마침** 을 선택합니다.

7. **작업** 메뉴에서 새 가상 컴퓨터를 만듭니다.

8. 가상 컴퓨터의 이름을 입력합니다.

9. **세대**  >  **1** 세대 지정을 선택 합니다.

10. 아키텍처에 따라 메모리 할당을 지정 하 고 동적 메모리의 확인란을 선택 합니다.

11. 서버 네트워크 토폴로지에 따라 네트워크 어댑터를 구성 합니다.

12. 이전에 만든 VHDX를 가상 머신에 연결 합니다.

13. 요약을 검토하고 **마침** 을 선택합니다.

14. 새 가상 컴퓨터를 마우스 오른쪽 단추로 클릭 하 고 **설정** 을 선택 합니다.

15. **하드웨어 추가** 를 선택 하 고 새 네트워크 어댑터를 추가 합니다.

16. 센서 관리 네트워크에 연결 되는 가상 스위치를 선택 합니다.

17. CPU 리소스를 할당 합니다 (아키텍처에 따라).

18. 관리 콘솔의 ISO 이미지를 가상 DVD 드라이브에 연결 합니다.

19. 가상 머신을 시작합니다.

20. **작업** 메뉴에서 **연결** 을 선택 하 여 소프트웨어 설치를 계속 합니다.

### <a name="software-installation-esxi-and-hyper-v"></a>소프트웨어 설치 (ESXi 및 Hyper-v)

이 섹션에서는 ESXi 및 Hyper-v 소프트웨어 설치에 대해 설명 합니다.

설치하려면 다음을 수행합니다.

1. 가상 컴퓨터 콘솔을 엽니다.

2. ISO 이미지에서 VM이 시작 되 고 언어 선택 화면이 표시 됩니다. **영어** 를 선택 합니다.

3. 필요한 아키텍처를 선택 합니다.

4. 어플라이언스 프로필 및 네트워크 속성을 정의 합니다.

    | 매개 변수 | 구성 |
    | ----------| ------------- |
    | **하드웨어 프로필** | &lt;필수 아키텍처&gt; |
    | **관리 인터페이스** | **ens192** |
    | **네트워크 매개 변수 (고객에서 제공)** | **관리 네트워크 IP 주소:** <br/>**서브넷 마스크:** <br/>**어플라이언스 호스트 이름:** <br/>**DNS** <br/>**기본 게이트웨이:** <br/>**입력 인터페이스:**|
    | **브리지 인터페이스:** | 브리지 인터페이스를 구성할 필요가 없습니다. 이 옵션은 특별 한 사용 사례로만 사용 됩니다. |

5. 설정을 적용 하려면 **Y** 를 입력 합니다.

6. 로그인 자격 증명은 자동으로 생성 되 고 표시 됩니다. 로그인 및 관리에 필요한 사용자 이름 및 암호를 안전한 장소에 복사 합니다.

   - **지원**: 사용자 관리에 대 한 관리자입니다.

   - **CyberX**: 어플라이언스에 액세스 하기 위한 루트와 동일 합니다.

7. 기기가 다시 시작 됩니다.

8. 이전에 구성한 IP 주소를 통해 관리 콘솔에 액세스 `https://ip_address` 합니다.

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="관리 콘솔에 대 한 액세스를 보여 주는 스크린샷":::

## <a name="virtual-appliance-on-premises-management-console-installation"></a>가상 어플라이언스: 온-프레미스 관리 콘솔 설치

온-프레미스 관리 콘솔 VM은 다음 아키텍처를 지원 합니다.

| 아키텍처 | 사양 | 사용량 | 
|--|--|--|
| Enterprise <br/>(기본 및 가장 일반적) | CPU: 8 <br/>메모리: 32G RAM<br/> HDD: 1.8 TB | 대량 프로덕션 환경 | 
| Enterprise | CPU: 4 <br/> 메모리: 8G RAM<br/> HDD: 500 G B | 대량 프로덕션 환경 |
| Enterprise | CPU: 4 <br/>메모리: 8G RAM <br/> HDD: 100 G B | 소규모 테스트 환경 | 
   
### <a name="prerequisites"></a>필수 조건

온-프레미스 관리 콘솔은 VMware 및 Hyper-v 배포 옵션을 모두 지원 합니다. 설치를 시작 하기 전에 다음을 확인 합니다.

- VMware (ESXi 5.5 이상) 또는 Hyper-v 하이퍼바이저 (Windows 10 Pro 또는 Enterprise)가 설치 되 고 작동 합니다.

- 하드웨어 리소스는 가상 컴퓨터에 사용할 수 있습니다.

- 온-프레미스 관리 콘솔용 ISO 설치 파일이 있습니다.
    
- 하이퍼바이저가 실행 중입니다.

### <a name="create-the-virtual-machine-esxi"></a>가상 컴퓨터 만들기 (ESXi)

가상 컴퓨터 만들기 (ESXi):

1. ESXi에 로그인 하 고, 관련 **데이터 저장소** 를 선택 하 고, **데이터 저장소 브라우저** 를 선택 합니다.

2. 이미지를 업로드 하 고 **닫기** 를 선택 합니다.

3. **Virtual Machines** 로 이동 합니다.

4. **VM 만들기/등록** 을 선택 합니다.

5. **새 가상 컴퓨터 만들기** 를 선택 하 고 **다음** 을 선택 합니다.

6. 센서 이름을 추가 하 고 다음을 선택 합니다.

   - 호환성 \<latest ESXi version>

   - 게스트 OS 제품군: Linux

   - 게스트 OS 버전: Ubuntu Linux (64 비트)

7. **다음** 을 선택합니다.

8. 관련 데이터 저장소를 선택 하 고 **다음** 을 선택 합니다.

9. 필요한 아키텍처에 따라 가상 하드웨어 매개 변수를 변경 합니다.

10. **CD/DVD 드라이브 1** 의 경우 **데이터 저장소 iso 파일** 을 선택 하 고 이전에 업로드 한 ISO 파일을 선택 합니다.

11. **다음** > **마침** 을 선택합니다.

### <a name="create-the-virtual-machine-hyper-v"></a>가상 컴퓨터 만들기 (Hyper-v)

Hyper-v를 사용 하 여 가상 컴퓨터를 만들려면 다음을 수행 합니다.

1. Hyper-v 관리자에서 가상 디스크를 만듭니다.

2. **VHDX** 형식을 선택 합니다.

3. **다음** 을 선택합니다.

4. **동적 확장** 유형을 선택 합니다.

5. **다음** 을 선택합니다.

6. VHD의 이름과 위치를 입력 합니다.

7. **다음** 을 선택합니다.

8. 아키텍처에 따라 필요한 크기를 입력 합니다.

9. **다음** 을 선택합니다.

10. 요약을 검토하고 **마침** 을 선택합니다.

11. **작업** 메뉴에서 새 가상 컴퓨터를 만듭니다.

12. **다음** 을 선택합니다.

13. 가상 컴퓨터의 이름을 입력합니다.

14. **다음** 을 선택합니다.

15. **세대** 를 선택 하 고 **1 세대로** 설정 합니다.

16. **다음** 을 선택합니다.

17. 아키텍처에 따라 메모리 할당을 지정 하 고 동적 메모리의 확인란을 선택 합니다.

18. **다음** 을 선택합니다.

19. 서버 네트워크 토폴로지에 따라 네트워크 어댑터를 구성 합니다.

20. **다음** 을 선택합니다.

21. 이전에 만든 VHDX를 가상 머신에 연결 합니다.

22. **다음** 을 선택합니다.

23. 요약을 검토하고 **마침** 을 선택합니다.

24. 새 가상 컴퓨터를 마우스 오른쪽 단추로 클릭 한 다음 **설정** 을 선택 합니다.

25. **하드웨어 추가** 를 선택 하 고 **네트워크 어댑터용** 새 어댑터를 추가 합니다.

26. **가상 스위치** 에 대해 센서 관리 네트워크에 연결 하는 스위치를 선택 합니다.

27. CPU 리소스를 할당 합니다 (아키텍처에 따라).

28. 관리 콘솔의 ISO 이미지를 가상 DVD 드라이브에 연결 합니다.

29. 가상 머신을 시작합니다.

30. **작업** 메뉴에서 **연결** 을 선택 하 여 소프트웨어 설치를 계속 합니다.

### <a name="software-installation-esxi-and-hyper-v"></a>소프트웨어 설치 (ESXi 및 Hyper-v)

가상 컴퓨터를 시작 하면 ISO 이미지에서 설치 프로세스가 시작 됩니다.

소프트웨어를 설치 하려면:

1. **영어** 를 선택 합니다.

2. 배포에 필요한 아키텍처를 선택 합니다.

3. 센서 관리 네트워크에 대 한 네트워크 인터페이스 (인터페이스, IP, 서브넷, DNS 서버 및 기본 게이트웨이)를 정의 합니다.

4. 로그인 자격 증명은 자동으로 생성 되 고 표시 됩니다. 이러한 자격 증명은 로그인 및 관리에 필요 하므로 안전한 장소에 보관 하세요.

  - **지원**: 사용자 관리에 대 한 관리자입니다.

  - **CyberX**: 어플라이언스에 액세스 하기 위한 루트와 동일 합니다.

5. 기기가 다시 시작 됩니다.

6. 이전에 구성한 IP 주소를 통해 관리 콘솔에 액세스 `<https://ip_address>` 합니다.

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-management-console-sign-in-screen.png" alt-text="관리 콘솔의 로그인 화면을 보여 주는 스크린샷":::

## <a name="post-installation-validation"></a>사후 설치 유효성 검사

물리적 어플라이언스의 설치를 확인 하려면 여러 테스트를 수행 해야 합니다. 모든 어플라이언스 유형에 동일한 유효성 검사 프로세스가 적용 됩니다.

GUI 또는 CLI를 사용 하 여 유효성 검사를 수행 합니다. 사용자 **지원** 및 사용자 **CyberX** 유효성 검사를 수행할 수 있습니다.

사후 설치 유효성 검사에는 다음 테스트가 포함 되어야 합니다.

  - **온전성 테스트**: 시스템이 실행 중인지 확인 합니다.

  - **버전**: 버전이 올바른지 확인 합니다.

  - **ifconfig**: 설치 프로세스 중에 구성 된 모든 입력 인터페이스가 실행 중인지 확인 합니다.

### <a name="checking-system-health-by-using-the-gui"></a>GUI를 사용 하 여 시스템 상태 확인

:::image type="content" source="media/tutorial-install-components/system-health-check-screen.png" alt-text="시스템 상태 검사를 보여 주는 스크린샷":::

#### <a name="sanity"></a>검사

- **어플라이언스**: 어플라이언스 온전성 검사를 실행 합니다. CLI 명령을 사용 하 여 동일한 검사를 수행할 수 있습니다 `system-sanity` .

- **버전**: 어플라이언스 버전을 표시 합니다.

- **네트워크 속성**: 센서 네트워크 매개 변수를 표시 합니다.

#### <a name="redis"></a>Redis

- **Memory**: 사용 된 메모리 양 및 남은 메모리와 같은 메모리 사용에 대 한 전반적인 그림을 제공 합니다.

- **가장 긴 키**: 광범위 한 메모리 사용을 유발할 수 있는 가장 긴 키를 표시 합니다.

#### <a name="system"></a>System

- **핵심 로그**: 전체 시스템 로그를 내보내지 않고 최근 로그 행을 볼 수 있도록 핵심 로그의 마지막 500 행을 제공 합니다.

- **작업 관리자**: 프로세스의 테이블에 표시 되는 작업을 다음 계층으로 변환 합니다. 
  
  - 영구 계층 (Redis) 
  - 현금 계층 (SQL)

- **네트워크 통계**: 네트워크 통계를 표시 합니다.

- **TOP**: 프로세스의 테이블을 표시 합니다. 실행 중인 시스템의 동적 실시간 뷰를 제공 하는 Linux 명령입니다.

- **백업 메모리 확인**: 백업 메모리의 상태를 제공 하 고 다음을 확인 합니다.
  - 백업 폴더의 위치입니다. 
  - 백업 폴더의 크기입니다.
  - 백업 폴더의 제한 사항
  - 마지막 백업이 발생 한 시간
  - 추가 백업 파일에 대 한 공간의 크기

- **ifconfig**: 어플라이언스의 실제 인터페이스에 대 한 매개 변수를 표시 합니다.

- **CyberX 체제용**: 6 초 테스트를 사용 하 여 네트워크 트래픽 및 대역폭을 표시 합니다.

- **Core의 오류, 로그**: 핵심 로그 파일의 오류를 표시 합니다.

도구에 액세스 하려면:

1. **지원** 사용자 자격 증명을 사용 하 여 센서에 로그인 합니다.

2. **시스템 설정** 창에서 **시스템 통계** 를 선택 합니다.

    :::image type="icon" source="media/tutorial-install-components/system-statistics-icon.png" border="false":::

### <a name="checking-system-health-by-using-the-cli"></a>CLI를 사용 하 여 시스템 상태 확인

**테스트 1: 온전성**

시스템이 실행 중인지 확인 합니다.

1. Linux 터미널 (예: PuTTY)을 사용 하 여 CLI에 연결 하 고 사용자 **지원을 제공** 합니다.

2. `system sanity`를 입력합니다.

3. 모든 서비스가 녹색 (실행 중) 인지 확인 합니다.

    :::image type="content" source="media/tutorial-install-components/support-screen.png" alt-text="실행 중인 서비스를 보여 주는 스크린샷":::

4. 시스템이 작동 하는지 확인 합니다 **. (prod)** 아래쪽에 표시 됩니다.

**테스트 2: 버전 확인**

올바른 버전이 사용 되는지 확인 합니다.

1. Linux 터미널 (예: PuTTY)을 사용 하 여 CLI에 연결 하 고 사용자 **지원을 제공** 합니다.

2. `system version`를 입력합니다.

3. 올바른 버전이 표시 되는지 확인 합니다.

**테스트 3: 네트워크 유효성 검사**

설치 프로세스 중에 구성 된 모든 입력 인터페이스가 실행 중인지 확인 합니다.

1. Linux 터미널 (예: PuTTY)을 사용 하 여 CLI에 연결 하 고 사용자 **지원을 제공** 합니다.

2. 을 `network list` (를) 입력 합니다 (Linux 명령에 해당 `ifconfig` ).

3. 필요한 입력 인터페이스가 나타나는지 확인 합니다. 예를 들어 4 개의 쿼드 구리 Nic가 설치 된 경우 목록에 10 개의 인터페이스가 있어야 합니다.

    :::image type="content" source="media/tutorial-install-components/interface-list-screen.png" alt-text="인터페이스 목록을 보여 주는 스크린샷":::

**테스트 4: UI에 대 한 관리 액세스**

콘솔 웹 GUI에 액세스할 수 있는지 확인 합니다.

1. 이더넷 케이블을 사용 하 여 노트북을 관리 포트 (**Gb1**)에 연결 합니다.

2. 기기와 동일한 범위에 있는 랩톱 NIC 주소를 정의 합니다.

    :::image type="content" source="media/tutorial-install-components/access-to-ui.png" alt-text="UI에 대 한 관리 액세스를 보여 주는 스크린샷":::

3. 랩톱에서 어플라이언스의 IP 주소를 Ping 하 여 연결을 확인 합니다 (기본값: 10.100.10.1).

4. 노트북에서 Chrome 브라우저를 열고 어플라이언스의 IP 주소를 입력 합니다.

5. **비공개 사용자의 연결 창이 아닌** 경우 **고급** 을 선택 하 고 계속 합니다.

6. IoT 용 Defender 로그인 화면이 나타날 때 테스트가 성공 합니다.

   :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="관리 콘솔에 대 한 액세스를 보여 주는 스크린샷":::

## <a name="troubleshooting"></a>문제 해결

### <a name="you-cant-connect-by-using-a-web-interface"></a>웹 인터페이스를 사용 하 여 연결할 수 없습니다.

1. 연결 하려는 컴퓨터가 어플라이언스와 동일한 네트워크에 있는지 확인 합니다.

2. GUI 네트워크가 관리 포트에 연결 되어 있는지 확인 합니다.

3. 어플라이언스의 IP 주소를 Ping 합니다. Ping이 없으면 다음을 수행 합니다.

   1. 모니터와 키보드를 어플라이언스에 연결 합니다.

   1. **지원** 사용자 및 암호를 사용 하 여 로그인 합니다.

   1. 명령을 사용 `network list` 하 여 현재 IP 주소를 확인 합니다.

      :::image type="content" source="media/tutorial-install-components/network-list.png" alt-text="네트워크 목록을 보여 주는 스크린샷":::

4. 네트워크 매개 변수가 잘못 구성 된 경우 다음 절차를 사용 하 여 해당 매개 변수를 변경 합니다.

   1. 명령을 사용 `network edit-settings` 합니다.

   1. 관리 네트워크 IP 주소를 변경 하려면 **Y** 를 선택 합니다.

   1. 서브넷 마스크를 변경 하려면 **Y** 를 선택 합니다.

   1. DNS를 변경 하려면 **Y** 를 선택 합니다.

   1. 기본 게이트웨이 IP 주소를 변경 하려면 **Y** 를 선택 합니다.

   1. 입력 인터페이스 변경 (센서 전용)의 경우 **N** 을 선택 합니다.

   1. 설정을 적용 하려면 **Y** 를 선택 합니다.

5. 다시 시작한 후 지원 사용자 자격 증명을 사용 하 여 연결 하 고 `network list` 명령을 사용 하 여 매개 변수가 변경 되었는지 확인 합니다.

6. GUI를 다시 ping 하 고 연결 해 보세요.

### <a name="the-appliance-isnt-responding"></a>기기가 응답 하지 않습니다.

1. 모니터와 키보드를 어플라이언스에 연결 하거나 PuTTY를 사용 하 여 CLI에 원격으로 연결 합니다.

2. **지원** 사용자의 자격 증명을 사용 하 여 로그인 합니다.

3. 명령을 사용 하 여 `system sanity` 모든 프로세스가 실행 중인지 확인 합니다.

    :::image type="content" source="media/tutorial-install-components/system-sanity-screen.png" alt-text="시스템 온전성 명령을 보여 주는 스크린샷":::

다른 문제는 [Microsoft 지원](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)에 문의 하세요.

## <a name="appendix-a-mirroring-port-on-vswitch-esxi"></a>부록 A: vSwitch의 미러링 포트 (ESXi)

### <a name="configure-a-span-port-on-an-existing-vswitch"></a>기존 vSwitch에서 범위 포트 구성

VSwitch에는 미러링 기능이 없지만 간단한 해결 방법을 사용 하 여 범위 포트를 구현할 수 있습니다.

범위 포트를 구성 하려면:

1. VSwitch 속성을 엽니다.

2. **추가** 를 선택합니다.

3. **가상 컴퓨터**  >  **다음** 을 선택 합니다.

4. 네트워크 레이블 **범위 네트워크** 를 삽입 하 고 **VLAN ID**  >  **모두** 를 선택한 후 **다음** 을 선택 합니다.

5. **마침** 을 선택합니다.

6. **범위 네트워크** > **편집* 을 선택 합니다.

7. **보안** 을 선택 하 고 **무차별 모드** 정책이 **수락** 모드로 설정 되어 있는지 확인 합니다.

8. **확인** 을 선택 하 고 **닫기** 를 선택 하 여 vSwitch 속성을 닫습니다.

9. **Xsense VM** 속성을 엽니다.

10. **네트워크 어댑터 2** 의 경우 **범위** 네트워크를 선택 합니다.

11. **확인** 을 선택합니다.

12. 센서에 연결 하 고 미러링이 작동 하는지 확인 합니다.

## <a name="appendix-b-access-sensors-from-the-on-premises-management-console"></a>부록 B: 온-프레미스 관리 콘솔에서 센서 액세스

사용자가 센서에 직접 액세스 하지 못하도록 하 여 시스템 보안을 향상 시킬 수 있습니다. 대신, 사용자가 단일 방화벽 규칙을 사용 하 여 온-프레미스 관리 콘솔에서 센서에 액세스할 수 있도록 프록시 터널링을 사용 합니다. 이 기술은 센서를 벗어나는 네트워크 환경에 대 한 무단 액세스 가능성을 좁힙니다. 센서에 로그인 할 때의 사용자 환경은 동일 하 게 유지 됩니다.

:::image type="content" source="media/tutorial-install-components/sensor-system-graph.png" alt-text="센서에 대 한 액세스를 보여 주는 스크린샷":::

터널링을 사용 하려면:

1. **CyberX** 를 사용 하 여 온-프레미스 관리 콘솔의 CLI에 로그인 하거나 사용자 자격 증명을 **지원** 합니다.

2. `sudo cyberx-management-tunnel-enable`를 입력합니다.

3. **Enter** 키를 선택합니다.

4. `--port 10000`를 입력합니다.

### <a name="next-steps"></a>다음 단계

[네트워크 설정](how-to-set-up-your-network.md)
