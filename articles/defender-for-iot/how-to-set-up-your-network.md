---
title: 네트워크 설정
description: IoT 어플라이언스 용 Azure Defender를 사용 하도록 네트워크를 성공적으로 설정 하는 데 필요한 솔루션 아키텍처, 네트워크 준비, 필수 구성 요소 및 기타 정보에 대해 알아봅니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 02/18/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 0f85eebbfa8fcdfd9ad6e31a564f27b5d9bfbdfc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101733247"
---
# <a name="about-azure-defender-for-iot-network-setup"></a>Azure Defender for IoT 네트워크 설정 정보

IoT 용 Azure Defender는 연속 ICS 위협 모니터링 및 장치 검색을 제공 합니다. 플랫폼에는 다음 구성 요소가 포함 됩니다.

**IoT 용 Defender 센서:** 센서는 수동 (에이전트 없는) 모니터링을 사용 하 여 ICS 네트워크 트래픽을 수집 합니다. 수동 및 비-간섭의 경우,이 센서는 OT 및 IoT 네트워크 및 장치에 대 한 성능에 영향을 주지 않습니다. 센서는 범위 포트나 네트워크 탭에 연결 하 고 즉시 네트워크 모니터링을 시작 합니다. 감지는 센서 콘솔에 표시 됩니다. 여기에서 네트워크 맵, 장치 인벤토리 및 광범위 한 보고서에서 보고, 조사 하 고, 분석할 수 있습니다. 예를 들면 위험 평가 보고서, 데이터 마이닝 쿼리 및 공격 벡터가 있습니다. 

**Defender For IoT 온-프레미스 관리 콘솔**: 온-프레미스 관리 콘솔은 모든 네트워크 장치에 대 한 통합 보기를 제공 합니다. 모든 시설에서 핵심 OT 및 IoT 위험 표시기와 경고에 대 한 실시간 보기를 제공 합니다. SOC 워크플로 및 플레이 북과 긴밀 하 게 통합 되어 있으며, 완화 작업 및 위협에 대 한 사이트 간 상관 관계를 쉽게 확인할 수 있습니다. 

**IoT 용 Defender 포털:** IoT 용 Defender 응용 프로그램은 솔루션 어플라이언스를 구매 하 고, 소프트웨어를 설치 및 업데이트 하 고, TI 패키지를 업데이트 하는 데 도움이 됩니다. 

이 문서에서는 IoT 어플라이언스 용 Defender를 사용 하도록 네트워크를 설정 하는 데 도움이 되는 솔루션 아키텍처, 네트워크 준비, 필수 구성 요소 등에 대 한 정보를 제공 합니다. 이 문서의 정보를 사용 하는 독자는 OT 및 IoT 네트워크를 운영 및 관리 하는 데 문제가 발생 합니다. 예를 들면 automation 엔지니어, 공장 관리자, OT 네트워크 인프라 서비스 공급자, 사이버 보안 팀, CISOs 또는 Cio가 있습니다.

지원 또는 지원을 받으려면 [Microsoft 지원](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)에 문의 하세요.

## <a name="on-site-deployment-tasks"></a>사이트 간 배포 작업

사이트 배포 작업에는 다음이 포함 됩니다.

- [사이트 정보 수집](#collect-site-information)

- [구성 워크스테이션 준비](#prepare-a-configuration-workstation)

- [랙 설치 계획](#planning-rack-installation)

### <a name="collect-site-information"></a>사이트 정보 수집

다음과 같은 사이트 정보를 기록 합니다.

- 센서 관리 네트워크 정보입니다.

- 사이트 네트워크 아키텍처.

- 물리적 환경.

- 시스템 통합.

- 계획 된 사용자 자격 증명

- 구성 워크스테이션.

- SSL 인증서 (선택 사항 이지만 권장 됨).

- SMTP 인증 (옵션). 인증에 SMTP 서버를 사용 하려면 서버에 필요한 자격 증명을 준비 하십시오.

- DNS 서버 (옵션). DNS 서버의 IP 및 호스트 이름을 준비 합니다.

중요 한 사이트 정보에 대 한 자세한 목록과 설명은 [예제 사이트 설명서](#example-site-book)를 참조 하십시오.

#### <a name="successful-monitoring-guidelines"></a>성공적인 모니터링 지침

각 프로덕션 네트워크에서 어플라이언스를 연결 하는 최적의 위치를 찾으려면 다음 절차를 수행 하는 것이 좋습니다.

:::image type="content" source="media/how-to-set-up-your-network/production-network-diagram.png" alt-text="프로덕션 네트워크 설정의 다이어그램 예제":::

### <a name="prepare-a-configuration-workstation"></a>구성 워크스테이션 준비

다음을 포함 하 여 Windows 워크스테이션을 준비 합니다.

- 센서 관리 인터페이스에 연결 합니다.

- 지원 되는 브라우저

- PuTTY와 같은 터미널 소프트웨어

필요한 방화벽 규칙이 워크스테이션에서 열려 있는지 확인 합니다. 자세한 내용은 [네트워크 액세스 요구 사항](#network-access-requirements) 을 참조 하세요.

#### <a name="supported-browsers"></a>지원되는 브라우저

센서 및 온-프레미스 관리 콘솔 웹 응용 프로그램에 대해 지원 되는 브라우저는 다음과 같습니다.

- Chrome 32 이상

- Microsoft Edge 86 이상

- Internet Explorer 10+

### <a name="network-access-requirements"></a>네트워크 액세스 요구 사항

조직 보안 정책에서 다음에 대 한 액세스를 허용 하는지 확인 합니다.

| 프로토콜 | 전송 | 입/출력 | 포트 | 사용됨 | 용도 | 원본 | 대상 |
|--|--|--|--|--|--|--|--|
| HTTPS | TCP | IN/OUT | 443 | 센서 및 온-프레미스 관리 콘솔 웹 콘솔 | 웹 콘솔에 대 한 액세스 | 클라이언트 | 센서 및 온-프레미스 관리 콘솔 |
| SSH | TCP | IN/OUT | 22 | CLI | CLI에 대 한 액세스 | 클라이언트 | 센서 및 온-프레미스 관리 콘솔 |
| SSL | TCP | IN/OUT | 443 | 센서 및 온-프레미스 관리 콘솔 | CyberX 플랫폼과 중앙 관리 플랫폼 간 연결 | 센서 | 온-프레미스 관리 콘솔 |
| NTP | UDP | IN | 123 | 시간 동기화 | 온-프레미스 관리 콘솔을 사용 하 여 센서에 NTP | 센서 | 온-프레미스 관리 콘솔 |
| NTP | UDP | IN/OUT | 123 | 시간 동기화 | 온-프레미스 관리 콘솔이 설치 되어 있지 않은 경우 외부 NTP 서버에 연결 된 센서 | 센서 | NTP |
| SMTP | TCP | OUT | 25 | 메일 | CyberX 플랫폼과 관리 플랫폼과 메일 서버 간의 연결 | 센서 및 온-프레미스 관리 콘솔 | 메일 서버 |
| Syslog | UDP | OUT | 514 | LEEF | 온-프레미스 관리 콘솔에서 Syslog 서버로 전송 하는 로그 | 온-프레미스 관리 콘솔 및 센서 | Syslog 서버 |
| DNS |  | IN/OUT | 53 | DNS | DNS 서버 포트 | 온-프레미스 관리 콘솔 및 센서 | DNS 서버 |
| LDAP | TCP | IN/OUT | 389 | Active Directory | CyberX 플랫폼과 관리 플랫폼 간의 연결 Active Directory | 온-프레미스 관리 콘솔 및 센서 | LDAP 서버 |
| LDAPS | TCP | IN/OUT | 636 | Active Directory | CyberX 플랫폼과 관리 플랫폼 간의 연결 Active Directory | 온-프레미스 관리 콘솔 및 센서 | LDAPS 서버 |
| SNMP | UDP | OUT | 161 | 모니터링 | 원격 SNMP 수집기. | 온-프레미스 관리 콘솔 및 센서 | SNMP 서버 |
| WMI | UDP | OUT | 135 | 모니터링 | Windows 끝점 모니터링 | 센서 | 관련 네트워크 요소 |
| 터널링 | TCP | IN | 9000 <br /><br />-포트 443 위에 <br /><br />최종 사용자에서 온-프레미스 관리 콘솔로 <br /><br />-포트 22를 센서에서 온-프레미스 관리 콘솔로  | 모니터링 | 터널링 | 센서 | 온-프레미스 관리 콘솔 |

### <a name="planning-rack-installation"></a>랙 설치 계획

랙 설치를 계획 하려면 다음을 수행 합니다.

1. 어플라이언스 네트워크 설정에 대 한 모니터와 키보드를 준비 합니다.

1. 어플라이언스에 대 한 랙 공간을 할당 합니다.

1. 어플라이언스에 대 한 AC 전원을 사용할 수 있습니다.
1. 관리를 네트워크 스위치에 연결 하기 위한 LAN 케이블을 준비 합니다.
1. 스위치 범위 (미러) 포트를 연결 하기 위한 LAN 케이블을 준비 하 고 IoT 어플라이언스에 대 한 네트워크 탭을 Defender에 연결 합니다. 
1. 아키텍처 검토 세션에 설명 된 대로 미러된 스위치의 범위 포트를 구성, 연결 및 유효성을 검사 합니다.
1. Wireshark를 실행 하는 컴퓨터에 구성 된 범위 포트를 연결 하 고 포트가 올바르게 구성 되었는지 확인 합니다.
1. 모든 관련 방화벽 포트를 엽니다.

## <a name="about-passive-network-monitoring"></a>수동 네트워크 모니터링 정보

어플라이언스는 스위치 미러 포트 (스팬 포트) 또는 네트워크 탭을 통해 여러 원본에서 트래픽을 수신 합니다. 관리 포트는 온-프레미스 관리 콘솔 또는 Defender for IoT 포털에 대 한 연결을 통해 비즈니스, 회사 또는 센서 관리 네트워크에 연결 됩니다.

:::image type="content" source="media/how-to-set-up-your-network/switch-with-port-mirroring.png" alt-text="포트 미러링이 포함 된 관리 되는 스위치의 다이어그램입니다.":::

### <a name="purdue-model"></a>Purdue 모델

다음 섹션에서는 Purdue 수준에 대해 설명 합니다.

:::image type="content" source="media/how-to-set-up-your-network/purdue-model.png" alt-text="Purdue 모델의 다이어그램입니다.":::

####  <a name="level-0-cell-and-area"></a>수준 0: 셀 및 영역  

수준 0은 기본 제조 프로세스에 관련 된 다양 한 센서, 발동기 및 장치로 구성 됩니다. 이러한 장치는 다음과 같은 산업용 자동화 및 제어 시스템의 기본 기능을 수행 합니다.

- 모터를 구동 합니다.

- 변수 측정.
- 출력 설정
- 그리기, 용접, 벤딩 등의 주요 기능을 수행 합니다.

#### <a name="level-1-process-control"></a>수준 1: 컨트롤 처리

수준 1은 해당 키 함수가 수준 0 장치와 통신 하는 제조 프로세스를 제어 하 고 조작 하는 포함 된 컨트롤러로 구성 됩니다. 불연속 제조에서 해당 장치는 PLCs (프로그래밍 가능한 논리 컨트롤러) 또는 RTUs (원격 원격 분석 단위)입니다. 프로세스 제조에서 기본 컨트롤러는 DC (분산 제어 시스템) 라고 합니다.

#### <a name="level-2-supervisory"></a>수준 2: 감독 기관과 이러한

수준 2는 프로덕션 기능 영역에 대 한 런타임 감독 및 작업과 관련 된 시스템 및 함수를 나타냅니다. 이러한 작업에는 일반적으로 다음이 포함 됩니다. 

- Operator 인터페이스 또는 HMIs

- 경보 또는 경고 시스템

- 프로세스 historian 및 일괄 처리 관리 시스템

- 대화방 워크스테이션 제어

이러한 시스템은 수준 1의 PLCs 및 RTUs와 통신 합니다. 일부 경우에는 사이트 또는 엔터프라이즈 (수준 4 및 수준 5) 시스템 및 응용 프로그램과 통신 하거나 데이터를 공유 합니다. 이러한 시스템은 주로 표준 컴퓨팅 장비 및 운영 체제 (Unix 또는 Microsoft Windows)를 기반으로 합니다.

#### <a name="levels-3-and-35-site-level-and-industrial-perimeter-network"></a>수준 3 및 3.5: 사이트 수준 및 산업용 경계 네트워크

사이트 수준은 산업 automation 및 제어 시스템의 최고 수준을 나타냅니다. 이 수준에 존재 하는 시스템 및 응용 프로그램은 사이트 전체의 산업용 자동화 및 제어 기능을 관리 합니다. 수준 0 ~ 3은 사이트 작업에 중요 한 것으로 간주 됩니다. 이 수준에 존재 하는 시스템 및 함수에는 다음이 포함 될 수 있습니다.

- 프로덕션 보고 (예: 주기 시간, 품질 인덱스, 예측 유지 관리)

- 공장 historian

- 자세한 프로덕션 일정

- 사이트 수준 작업 관리

- 장치 및 자재 관리

- 패치 시작 서버

- 파일 서버

- 산업용 도메인, Active Directory, 터미널 서버

이러한 시스템은 프로덕션 영역과 통신 하 고 엔터프라이즈 (수준 4 및 수준 5) 시스템 및 응용 프로그램과 데이터를 공유 합니다.  

#### <a name="levels-4-and-5-business-and-enterprise-networks"></a>수준 4 및 5: 비즈니스 및 엔터프라이즈 네트워크

수준 4 및 수준 5는 중앙 집중식 IT 시스템과 기능이 있는 사이트 또는 엔터프라이즈 네트워크를 나타냅니다. IT 조직은 이러한 수준에서 서비스, 시스템 및 응용 프로그램을 직접 관리 합니다.

## <a name="planning-for-network-monitoring"></a>네트워크 모니터링 계획

다음 예에서는 센서의 최적의 모니터링 및 배치에 대 한 고려 사항과 함께 산업 제어 네트워크에 대 한 다양 한 유형의 토폴로지를 제공 합니다.

### <a name="what-should-be-monitored"></a>무엇을 모니터링 해야 하나요?

계층 1과 2를 통과 하는 트래픽을 모니터링 해야 합니다.

### <a name="what-should-the-defender-for-iot-appliance-connect-to"></a>IoT 어플라이언스 용 Defender는 어떻게 연결 되나요?

IoT 어플라이언스 용 Defender는 계층 1과 2 사이의 산업용 통신을 표시 하는 관리 되는 스위치 (일부 경우에는 계층 3)에 연결 해야 합니다.

다음 다이어그램은 일반적으로 SOC 및 MSSP에서 운영 하는 광범위 한 사이버 보안 에코 시스템을 사용 하는 다중 테 넌 트 네트워크를 추상화 한 것입니다.

일반적으로 NTA 센서는 OSI 모델의 0 ~ 3 계층에 배포 됩니다.

:::image type="content" source="media/how-to-set-up-your-network/osi-model.png" alt-text="OSI 모델의 다이어그램입니다.":::

#### <a name="example-ring-topology"></a>예: 링 토폴로지

링 네트워크는 각 스위치 또는 노드가 정확히 두 개의 다른 스위치에 연결 하 여 트래픽에 대해 단일 연속 경로를 형성 하는 토폴로지입니다.

:::image type="content" source="media/how-to-set-up-your-network/ring-topology.PNG" alt-text="링 토폴로지의 다이어그램입니다.":::

#### <a name="example-linear-bus-and-star-topology"></a>예: 선형 버스 및 별모양 토폴로지

스타 네트워크에서 모든 호스트는 중앙 허브에 연결 됩니다. 가장 간단한 형태의 중앙 허브 하나는 메시지를 전송 하기 위한 통로 역할을 합니다. 다음 예제에서 낮은 스위치는 모니터링 되지 않으며 이러한 스위치에 로컬 상태로 유지 되는 트래픽은 표시 되지 않습니다. ARP 메시지에 따라 장치를 식별할 수 있지만 연결 정보가 누락 됩니다.

:::image type="content" source="media/how-to-set-up-your-network/linear-bus-star-topology.PNG" alt-text="선형 버스 및 별모양 토폴로지의 다이어그램입니다.":::

#### <a name="multisensor-deployment"></a>Multisensor 배포

다음은 여러 센서를 배포 하기 위한 몇 가지 권장 사항입니다.

| **Number** | **단위로** | **종속성** | **센서 수** |
|--|--|--|--|
| 스위치 간 최대 거리입니다. | 80 미터 | 준비 된 이더넷 케이블 | 1 개 이상 |
| 네트워크 수 | 1 개 이상 | 물리적 연결 없음 | 1 개 이상 |
| 스위치 수 | RSPAN 구성을 사용할 수 있습니다. | 케이블 거리가 센서에 가까운 로컬 범위를 포함 하는 최대 8 개의 스위치 | 1 개 이상 |

#### <a name="traffic-mirroring"></a>트래픽 미러링  

트래픽 분석에 대 한 관련 정보만 보려면 Defender 용 Defender 플랫폼을 스위치의 미러링 포트 또는 산업용 ICS 및 SCADA 트래픽만 포함 하는 탭에 연결 해야 합니다. 

:::image type="content" source="media/how-to-set-up-your-network/switch.jpg" alt-text="설정에이 스위치를 사용 합니다.":::

다음 방법을 사용 하 여 스위치 트래픽을 모니터링할 수 있습니다.

- [스위치 범위 포트](#switch-span-port)

- [원격 범위 (RSPAN)](#remote-span-rspan)

- [활성 및 수동 집계 탭](#active-and-passive-aggregation-tap)

SPAN 및 RSPAN은 Cisco 용어입니다. 다른 브랜드의 스위치는 비슷한 기능을 갖지만 다른 용어를 사용할 수도 있습니다.

#### <a name="switch-span-port"></a>스위치 범위 포트

스위치 포트 분석기는 스위치의 인터페이스에서 동일한 스위치의 인터페이스로 로컬 트래픽을 미러링합니다. 몇 가지 고려 사항은 다음과 같습니다.

- 관련 스위치가 포트 미러링 기능을 지원 하는지 확인 합니다.  

- 미러링 옵션은 기본적으로 해제 되어 있습니다.

- 연결 된 데이터가 없는 경우에도 모든 스위치의 포트를 구성 하는 것이 좋습니다. 그렇지 않으면 rogue 장치는 모니터링 되지 않은 포트에 연결 될 수 있으며 센서에 대 한 경고가 표시 되지 않습니다.

- 브로드캐스트 또는 멀티 캐스트 메시징을 활용 하는 네트워크에서 미러 서버 (수신) 전송만 미러로 구성 합니다. 그렇게 하지 않으면 멀티 캐스트 메시지가 활성 포트 수에 대해 반복 되 고 대역폭을 곱합니다.

다음 구성 예제는 참조용 으로만 사용 되며 IOS를 실행 하는 Cisco 2960 스위치 (24 개 포트)를 기반으로 합니다. 일반적인 예일 뿐 이므로 지침으로 사용 하지 마세요. 다른 Cisco 운영 체제의 미러 포트와 기타 스위치 브랜드는 다르게 구성 됩니다.

:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-terminal-v2.png" alt-text="범위 포트 구성 터미널의 다이어그램입니다.":::
:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-mode-v2.png" alt-text="범위 포트 구성 모드의 다이어그램입니다.":::

##### <a name="monitoring-multiple-vlans"></a>여러 Vlan 모니터링

IoT 용 Defender를 사용 하면 네트워크에 구성 된 Vlan을 모니터링할 수 있습니다. IoT 시스템용 Defender의 구성이 필요 하지 않습니다. 사용자는 네트워크의 스위치가 IoT 용 Defender에 VLAN 태그를 보내도록 구성 되어 있는지 확인 해야 합니다.

다음 예제에서는 IoT 용 Defender에서 Vlan 모니터링을 사용 하도록 Cisco 스위치에 구성 해야 하는 필수 명령을 보여 줍니다.

**세션 모니터링**:이 명령은 VLAN을 범위 포트로 보내는 프로세스를 담당 합니다.

- 세션 1 원본 인터페이스 Gi1/2 모니터링

- 세션 1 필터 패킷 유형 양호한 Rx 모니터

- 모니터 세션 1 대상 인터페이스 fastEthernet1/1 캡슐화 dot1q

**트렁크 포트 F.E. Gi1/1 모니터링**: vlan이 트렁크 포트에서 구성 됩니다.

- 인터페이스 GigabitEthernet1/1

- switchport 트렁크 캡슐화 dot1q

- switchport 모드 트렁크

#### <a name="remote-span-rspan"></a>원격 범위 (RSPAN)

원격 범위 세션은 여러 분산 된 원본 포트에서 전용 원격 VLAN으로 트래픽을 미러링합니다.  

:::image type="content" source="media/how-to-set-up-your-network/remote-span.png" alt-text="원격 범위의 다이어그램입니다.":::

그런 다음 VLAN의 데이터는 여러 스위치의 트렁크 포트를 통해 실제 대상 포트를 포함 하는 특정 스위치로 배달 됩니다. 이 포트는 IoT 플랫폼용 Defender에 연결 됩니다.  

##### <a name="more-about-rspan"></a>RSPAN에 대 한 자세한 정보

- RSPAN은 스위치 사이에 모니터를 확장 하는 트래픽을 전달 하기 위해 특수 VLAN이 필요한 고급 기능입니다. RSPAN은 모든 스위치에서 지원 되지 않습니다. 스위치가 RSPAN 함수를 지원 하는지 확인 합니다.

- 미러링 옵션은 기본적으로 해제 되어 있습니다.

- 트렁크 포트에서 원본 및 대상 스위치 사이에 원격 VLAN을 허용 해야 합니다.

- 동일한 RSPAN 세션을 연결 하는 모든 스위치는 동일한 공급 업체에서 제공 되어야 합니다.

> [!NOTE]
> 스위치 사이에 원격 VLAN을 공유 하는 트렁크 포트가 미러 세션 원본 포트로 정의 되어 있지 않은지 확인 합니다.
>
> 원격 VLAN은 미러된 세션 대역폭의 크기에 따라 트렁크 포트의 대역폭을 늘립니다. 스위치의 트렁크 포트가이를 지원 하는지 확인 합니다.  

:::image type="content" source="media/how-to-set-up-your-network/remote-vlan.jpg" alt-text="원격 VLAN의 다이어그램입니다.":::

#### <a name="rspan-configuration-examples"></a>RSPAN 구성 예제

RSPAN: Cisco catalyst 2960 (24 포트)을 기반으로 합니다.

**원본 스위치 구성 예제:**

:::image type="content" source="media/how-to-set-up-your-network/rspan-configuration.png" alt-text="RSPAN 구성의 스크린샷":::

1. 전역 구성 모드를 입력 합니다.

1. 전용 VLAN을 만듭니다.

1. VLAN을 RSPAN VLAN으로 식별 합니다.

1. "터미널 구성" 모드로 돌아갑니다.

1. 모든 24 포트를 세션 원본으로 구성 합니다.

1. RSPAN VLAN을 세션 대상으로 구성 합니다.

1. 권한 있는 EXEC 모드로 돌아갑니다.

1. 포트 미러링 구성을 확인 합니다.

**대상 스위치 구성 예제:**

1. 전역 구성 모드를 입력 합니다.

1. RSPAN VLAN을 세션 원본으로 구성 합니다.

1. 실제 포트 24를 세션 대상으로 구성 합니다.

1. 권한 있는 EXEC 모드로 돌아갑니다.

1. 포트 미러링 구성을 확인 합니다.

1. 구성을 저장합니다.

#### <a name="active-and-passive-aggregation-tap"></a>활성 및 수동 집계 탭

활성 또는 수동 집계 탭은 네트워크 케이블에 인라인으로 설치 됩니다. 이는 RX와 TX를 모두 모니터링 센서에 복제 합니다.

터미널 액세스 포인트 (탭)는 네트워크 트래픽이 포트 A에서 포트 B로 이동 하 고, 포트 B에서 포트 A로 이동 하는 것을 허용 하는 하드웨어 장치입니다. 네트워크 무결성을 손상 시 키 지 않고 계속 해 서 트래픽 흐름 양쪽의 정확한 복사본을 만듭니다. 필요한 경우 스위치 설정을 사용 하 여 전송 및 수신 트래픽을 집계 하는 탭도 있습니다. 집계가 지원 되지 않는 경우 각 탭에서 두 개의 센서 포트를 사용 하 여 송신 및 수신 트래픽을 모니터링 합니다.

탭은 다양 한 이유로 유용 합니다. 하드웨어 기반 이며 손상 될 수 없습니다. 이러한 메시지는 모두 삭제 하는 경우에도 손상 된 메시지를 포함 하 여 모든 트래픽을 전달 합니다. 프로세서를 인식 하지 않으므로 패킷 타이밍은 스위치가 미러 패킷의 타이밍에 영향을 줄 수 있는 낮은 우선 순위의 작업으로 미러 함수를 처리 하는 정확한 위치입니다. 법적 용도의 경우 탭이 가장 적합 한 장치입니다.

탭 집계는 포트 모니터링에도 사용할 수 있습니다. 이러한 장치는 프로세서 기반 이며 하드웨어 탭으로 본질적으로 안전 하지 않습니다. 정확한 패킷 타이밍을 반영 하지 않을 수 있습니다.

:::image type="content" source="media/how-to-set-up-your-network/active-passive-tap-v2.PNG" alt-text="활성 및 수동 탭의 다이어그램입니다.":::

##### <a name="common-tap-models"></a>일반적인 탭 모델

이러한 모델은 호환성 테스트를 거쳤습니다. 다른 공급 업체 및 모델이 호환 될 수도 있습니다.

| 이미지 | 모델 |
|--|--|
| :::image type="content" source="media/how-to-set-up-your-network/garland-p1gccas-v2.png" alt-text="Garland P1GCCAS의 스크린샷"::: | Garland P1GCCAS |
| :::image type="content" source="media/how-to-set-up-your-network/ixia-tpa2-cu3-v2.png" alt-text="IXIA TPA2-CU3의 스크린샷"::: | IXIA TPA2-CU3 |
| :::image type="content" source="media/how-to-set-up-your-network/us-robotics-usr-4503-v2.png" alt-text="MICROSOFT USR 4503의 스크린샷"::: | US 로봇 USR 4503 |

##### <a name="special-tap-configuration"></a>특수 탭 구성

| Garland 탭 | US 로봇 탭 |
| ----------- | --------------- |
| 점퍼가 다음과 같이 설정 되어 있는지 확인 합니다.<br />:::image type="content" source="media/how-to-set-up-your-network/jumper-setup-v2.jpg" alt-text="US 로봇 공학 스위치 스크린샷.":::| 집계 모드가 활성 상태 인지 확인 합니다. |

## <a name="deployment-validation"></a>배포 유효성 검사

### <a name="engineering-self-review"></a>엔지니어링 자체 검토  

가장 효율적인 모니터링 트래픽을 얻을 수 있는 가장 효율적인 연결 위치를 정의 하는 가장 효율적인 방법으로, OT 및 ICS 네트워크 다이어그램을 검토 하는 것이 가장 효율적입니다.

사이트 엔지니어는 네트워크의 모습을 알고 있습니다. 로컬 네트워크 및 운영 팀에 대 한 검토 세션이 있는 경우 일반적으로 기대치를 명확 하 게 설명 하 고 어플라이언스를 연결 하는 가장 좋은 장소를 정의 합니다.

관련 정보:

- 알려진 장치 목록 (스프레드시트)  

- 예상 장치 수  

- 공급 업체 및 산업 프로토콜

- 포트 미러링 옵션을 사용할 수 있는지 확인 하기 위한 스위치 모델

- 스위치 (예: IT)를 관리 하는 사용자 및 외부 리소스 인지 여부에 대 한 정보

- 사이트의 네트워크 목록

#### <a name="common-questions"></a>일반적인 질문

- 구현의 전반적인 목표는 어떻습니까? 전체 재고와 정확한 네트워크 맵이 중요 한가요?

- ICS에 다중 또는 중복 네트워크가 있나요? 모든 네트워크를 모니터링 하 고 있습니까?

- ICS와 enterprise (business) 네트워크 간에 통신이 있나요? 이러한 통신을 모니터링 하 고 있습니까?

- Vlan이 네트워크 디자인에서 구성 되었습니까?

- 고정 또는 임시 장치를 사용 하 여 ICS의 유지 관리를 수행 하는 방법

- 방화벽이 모니터링 되는 네트워크에 설치 되는 위치는 어디 인가요?

- 모니터링 되는 네트워크에 라우팅이 있나요?

- 모니터링 되는 네트워크에서 어떤 OT 프로토콜이 활성 상태 인가요?

- 이 스위치에 연결 하는 경우 HMI와 PLCs 간의 통신이 표시 되나요?

- ICS 스위치와 엔터프라이즈 방화벽 간의 실제 거리는 어떻게 되나요? 

- 관리 되지 않는 스위치를 관리 되는 스위치로 바꾸거나 네트워크 탭을 사용 하 여 옵션을 사용할 수 있습니까?

- 네트워크에 직렬 통신이 있나요? 그렇다면 네트워크 다이어그램에 표시 합니다.

- IoT 어플라이언스 용 Defender를 해당 스위치에 연결 해야 하는 경우 해당 캐비닛에 사용 가능한 실제 랙 공간이 있나요?

#### <a name="other-considerations"></a>기타 고려 사항

IoT 어플라이언스의 Defender는 계층 1과 2의 트래픽을 모니터링 하는 용도로 사용할 수 있습니다.

일부 아키텍처의 경우, IoT 어플라이언스 용 Defender는이 계층에 있는 트래픽이 있는 경우 계층 3도 모니터링 합니다. 사이트 아키텍처를 검토 하 고 스위치를 모니터링할 지 여부를 결정 하는 동안 다음 변수를 고려 합니다.

- 이 스위치 모니터링의 중요도와 비용/혜택은 무엇 인가요?

- 스위치가 관리 되지 않는 경우에는 더 높은 수준의 스위치에서 트래픽을 모니터링할 수 있나요?

  ICS 아키텍처가 링 토폴로지 인 경우이 링에서 하나의 스위치만 모니터링 해야 합니다.

- 이 네트워크의 보안 또는 운영 위험은 무엇입니까?

- 스위치의 VLAN을 모니터링할 수 있나요? VLAN이 모니터링할 수 있는 다른 스위치에 표시 되나요?

#### <a name="technical-validation"></a>기술 유효성 검사

스위치 범위 (또는 미러) 포트에서 기록 된 트래픽 (PCAP 파일) 샘플을 수신 하면 다음 작업에 도움이 될 수 있습니다.

- 스위치가 올바르게 구성 되었는지 확인 합니다.

- 스위치를 통과 하는 트래픽이 모니터링 (OT 트래픽)과 관련이 있는지 확인 합니다.

- 이 스위치에서 대역폭 및 예상 장치 수를 확인 합니다.

Wireshark 응용 프로그램을 통해 이미 구성 된 범위 포트에 노트북을 연결 하 여 샘플 PCAP 파일 (몇 분)을 기록할 수 있습니다.

:::image type="content" source="media/how-to-set-up-your-network/laptop-connected-to-span.jpg" alt-text="범위 포트에 연결 된 랩톱의 스크린샷":::
:::image type="content" source="media/how-to-set-up-your-network/sample-pcap-file.PNG" alt-text="샘플 PCAP 파일 기록의 스크린샷":::

#### <a name="wireshark-validation"></a>Wireshark 유효성 검사

- *유니캐스트 패킷이* 기록 트래픽에 있는지 확인 합니다. 유니캐스트가 한 주소에서 다른 주소에 있습니다. 대부분의 트래픽이 ARP 메시지 인 경우 스위치 설정이 올바르지 않습니다.

- **통계**  >  **프로토콜 계층 구조** 로 이동 합니다. 산업 OT 프로토콜이 있는지 확인 합니다.

:::image type="content" source="media/how-to-set-up-your-network/wireshark-validation.png" alt-text="Wireshark 유효성 검사의 스크린샷":::

## <a name="troubleshooting"></a>문제 해결

문제 해결을 위해 다음 섹션을 사용 합니다.

- [웹 인터페이스를 사용 하 여 연결할 수 없습니다.](#cant-connect-by-using-a-web-interface)

- [기기가 응답 하지 않습니다.](#appliance-is-not-responding)

### <a name="cant-connect-by-using-a-web-interface"></a>웹 인터페이스를 사용 하 여 연결할 수 없습니다.

1. 연결 하려는 컴퓨터가 어플라이언스와 동일한 네트워크에 있는지 확인 합니다.

2. GUI 네트워크가 센서의 관리 포트에 연결 되어 있는지 확인 합니다.

3. 어플라이언스 IP 주소를 Ping 합니다. Ping에 대 한 응답이 없는 경우:

    1. 모니터와 키보드를 어플라이언스에 연결 합니다.

    1. **지원** 사용자 및 암호를 사용 하 여 로그인 합니다.

    1. 명령 **네트워크 목록** 을 사용 하 여 현재 IP 주소를 확인 합니다.

    :::image type="content" source="media/how-to-set-up-your-network/list-of-network-commands.png" alt-text="네트워크 목록 명령의 스크린샷":::

4. 네트워크 매개 변수가 잘못 구성 된 경우 다음 절차를 사용 하 여 변경 합니다.

    1. **네트워크 편집-설정** 명령을 사용 합니다.

    1. 관리 네트워크 IP 주소를 변경 하려면 **Y** 를 선택 합니다.

    1. 서브넷 마스크를 변경 하려면 **Y** 를 선택 합니다.

    1. DNS를 변경 하려면 **Y** 를 선택 합니다.

    1. 기본 게이트웨이 IP 주소를 변경 하려면 **Y** 를 선택 합니다.

    1. 입력 인터페이스 변경 (센서 전용)의 경우 **Y** 를 선택 합니다.

    1. 브리지 인터페이스에서 **N** 을 선택 합니다.

    1. 설정을 적용 하려면 **Y** 를 선택 합니다.

5. 를 다시 시작한 후 사용자 지원에 연결 하 고 **네트워크 목록** 명령을 사용 하 여 매개 변수가 변경 되었는지 확인 합니다.

6. GUI를 다시 ping 하 고 연결 해 보세요.

### <a name="appliance-is-not-responding"></a>기기가 응답 하지 않습니다.

1. 모니터와 키보드를 사용 하 여 장비에 연결 하거나 PuTTY를 사용 하 여 CLI에 원격으로 연결 합니다.

2. 지원 자격 증명을 사용 하 여 로그인 합니다.

3. **시스템 온전성** 명령을 사용 하 여 모든 프로세스가 실행 중인지 확인 합니다.

    :::image type="content" source="media/how-to-set-up-your-network/system-sanity-command.png" alt-text="시스템 온전성 명령의 스크린샷":::

다른 문제는 [Microsoft 지원](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)에 문의 하세요.

## <a name="example-site-book"></a>예제 사이트 서적

예제 사이트를 사용 하 여 네트워크 설정에 필요한 중요 한 정보를 검색 하 고 검토할 수 있습니다.

### <a name="site-checklist"></a>사이트 검사 목록

사이트 배포 전에이 목록을 검토 합니다.

| **#** | **작업 또는 작업** | **상태** | **설명** |
|--|--|--|--|
| 1 | 주문 어플라이언스. | ☐ |  |
| 2 | 네트워크에서 서브넷 목록을 준비 합니다. | ☐ |  |
| 3 | 프로덕션 네트워크의 VLAN 목록을 제공 합니다. | ☐ |  |
| 4 | 네트워크의 스위치 모델 목록을 제공 합니다. | ☐ |  |
| 5 | 산업 장비의 공급 업체 및 프로토콜 목록을 제공 합니다. | ☐ |  |
| 6 | 센서에 대 한 네트워크 세부 정보 (IP 주소, 서브넷, D-GW, DNS)를 제공 합니다. | ☐ |  |
| 7 | 필요한 방화벽 규칙 및 액세스 목록을 만듭니다. | ☐ |  |
| 8 | 포트 모니터링에 대 한 스위치에 스패닝 포트를 만들거나 원하는 대로 네트워크 탭을 구성 합니다. | ☐ |  |
| 9 | 센서 어플라이언스에 대 한 랙 공간을 준비 합니다. | ☐ |  |
| 10 | 직원을 위한 워크스테이션을 준비 합니다. | ☐ |  |
| 11 | IoT 랙 장치용 Defender에 대 한 키보드, 모니터 및 마우스를 제공 합니다. | ☐ |  |
| 12 | 어플라이언스를 랙 및 케이블로 연결 합니다. | ☐ |  |
| 13 | 사이트 리소스를 할당 하 여 배포를 지원 합니다. | ☐ |  |
| 14 | Active Directory 그룹 또는 로컬 사용자를 만듭니다. | ☐ |  |
| 15 | 설정 교육 (자가 학습) | ☐ |  |
| 16 | Go 또는 no go. | ☐ |  |
| 17 | 배포 날짜를 예약 합니다. | ☐ |  |


| **날짜** | **참고** | **배포 날짜** | **참고** |
|--|--|--|--|
| Defender for IoT |  | 사이트 이름 * |  |
| 속성 |  | 속성 |  |
| 위치 |  | 위치 |  |

#### <a name="architecture-review"></a>아키텍처 검토

산업용 네트워크 다이어그램의 개요를 통해 IoT 장비에 대 한 Defender의 적절 한 위치를 정의할 수 있습니다.

1.  산업 OT 환경의 글로벌 네트워크 다이어그램을 봅니다. 예를 들면 다음과 같습니다.

    :::image type="content" source="media/how-to-set-up-your-network/ot-global-network-diagram.png" alt-text="글로벌 네트워크에 대 한 산업 OT 환경의 다이어그램입니다.":::

    > [!NOTE]
    > IoT 어플라이언스 용 Defender는 스위치의 포트 간 트래픽을 확인 하는 하위 수준 스위치에 연결 되어야 합니다.  

2. 모니터링할 네트워크 장치의 대략적인 수를 제공 합니다. IoT 포털에 대 한 Azure Defender에 구독을 온 보 딩 하는 경우이 정보가 필요 합니다. 온 보 딩 프로세스 중에 장치 수를 1000 단위로 입력 하 라는 메시지가 표시 됩니다.

3. 프로덕션 네트워크에 대 한 서브넷 목록 및 설명 (선택 사항)을 제공 합니다. 

    |  **#**  | **서브넷 이름** | **설명** |
    |--| --------------- | --------------- |
    | 1  | |
    | 2  | |
    | 3  | |
    | 4  | |

4. 프로덕션 네트워크의 VLAN 목록을 제공 합니다.

    | **#** | **VLAN 이름** | **설명** |
    |--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

5. 스위치에 포트 미러링 기능이 있는지 확인 하려면 IoT 플랫폼용 Defender가 연결 해야 하는 스위치 모델 번호를 제공 합니다.

    | **#** | **스위치** | **모델** | **트래픽 미러링 지원 (범위, RSPAN 또는 없음)** |
    |--|--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

    제 3 자가 스위치를 관리 하나요? Y 또는 N 

    그렇다면 누구 인가요? __________________________________ 

    해당 정책 이란? __________________________________ 

    예를 들면 다음과 같습니다.

    - Siemens

    - Rockwell automation – 이더넷 또는 IP

    - 곳인 emerson – DeltaV, Ovation
    
6. 네트워크의 직렬 연결을 통해 통신 하는 장치가 있나요? Yes 또는 No 

    그렇다면 직렬 통신 프로토콜을 지정 합니다. ________________ 

    예 인 경우 네트워크 다이어그램에 표시 되는 장치는 직렬 프로토콜과 통신 하 고 어디에 있는지를 표시 합니다. 
 
    <Add your network diagram with marked serial connection> 

7. QoS의 경우 센서의 기본 설정은 1.5 Mbps입니다. 변경 여부를 지정 합니다. ________________ 

   부서 (BU): ________________ 

### <a name="specifications-for-site-equipment"></a>사이트 장비에 대 한 사양

#### <a name="network"></a>네트워크  

센서 어플라이언스는 네트워크 어댑터를 통해 스위치 범위 포트에 연결 됩니다. 다른 전용 네트워크 어댑터를 통해 관리할 수 있도록 고객의 회사 네트워크에 연결 되어 있습니다.

회사 네트워크에 연결 될 센서 NIC의 주소 정보를 제공 합니다. 

|  항목               | 어플라이언스 1 | 어플라이언스 2 | 어플라이언스 3 |
| --------------- | ------------- | ------------- | ------------- |
| 어플라이언스 IP 주소    |               |               |               |
| 서브넷          |               |               |               |
| 기본 게이트웨이 |               |               |               |
| DNS             |               |               |               |
| 호스트 이름       |               |               |               |

#### <a name="idraciloserver-management"></a>iDRAC/iLO/서버 관리

|       항목          | 어플라이언스 1 | 어플라이언스 2 | 어플라이언스 3 |
| --------------- | ------------- | ------------- | ------------- |
| 어플라이언스 IP 주소     |               |               |               |
| 서브넷          |               |               |               |
| 기본 게이트웨이 |               |               |               |
| DNS             |               |               |               |

#### <a name="on-premises-management-console"></a>온-프레미스 관리 콘솔  

|       항목          | 활성 | Passive (HA를 사용 하는 경우) |
| --------------- | ------ | ----------------------- |
| IP 주소             |        |                         |
| 서브넷          |        |                         |
| 기본 게이트웨이 |        |                         |
| DNS             |        |                         |

#### <a name="snmp"></a>SNMP  

|   항목              | 세부 정보 |
| --------------- | ------ |
| IP              |        |
| IP 주소 | |
| 사용자 이름 | |
| 암호 | |
| 인증 유형 | MD5 또는 SHA |
| 암호화 | DES 또는 AES |
| 비밀 키 | |
| SNMP v2 커뮤니티 문자열 |

### <a name="on-premises-management-console-ssl-certificate"></a>온-프레미스 관리 콘솔 SSL 인증서

SSL 인증서를 사용 하려고 계획 하 고 있습니까? Yes 또는 No

그렇다면이를 생성 하는 데 사용할 서비스는 무엇 인가요? 인증서에 포함할 특성 (예: 도메인 또는 IP 주소)은 무엇 인가요?

### <a name="smtp-authentication"></a>SMTP 인증

SMTP를 사용 하 여 전자 메일 서버에 경고를 전달할 계획 입니까? Yes 또는 No

그렇다면 어떤 인증 방법을 사용 해야 하나요?  

### <a name="active-directory-or-local-users"></a>Active Directory 또는 로컬 사용자

Active Directory 관리자에 게 문의 하 여 Active Directory 사이트 사용자 그룹을 만들거나 로컬 사용자를 만듭니다. 사용자에 게 배포를 준비 해야 합니다. 

### <a name="iot-device-types-in-the-network"></a>네트워크의 IoT 장치 유형

| 디바이스 유형 | 네트워크의 장치 수 | 평균 대역폭 |
| --------------- | ------ | ----------------------- |
| 카메라 | |
| X 광선 컴퓨터 | |

## <a name="see-also"></a>참고 항목

[IoT 설치용 Defender 정보](how-to-install-software.md)
