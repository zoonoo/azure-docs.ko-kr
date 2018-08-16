---
title: Ethereum 인증 증명 컨소시엄
description: Etherereum 인증 증명 컨소시엄 솔루션을 사용하여 다중 멤버 컨소시엄 Ethereum 네트워크 배포 및 구성
services: azure-blockchain
keywords: ''
author: CodyBorn
ms.author: coborn
ms.date: 8/2/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: vamelech
ms.openlocfilehash: fcb35f23be384b3625e4e17e5dc2285b7eeb341a
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39530059"
---
# <a name="ethereum-proof-of-authority-consortium"></a>Ethereum 인증 증명 컨소시엄

[이 솔루션](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium)은 최소한의 Azure 및 Ethereum 지식으로 다중 멤버 컨소시엄 인증 증명 Ethereum 네트워크를 더 쉽게 배포, 구성 및 관리할 수 있도록 설계되었습니다.

Azure Portal을 통해 약간의 사용자 입력과 한 번의 클릭으로 배포하면 각 멤버가 전 세계의 Microsoft Azure Compute, 네트워킹 및 저장소 서비스를 사용하여 네트워크 공간을 프로비전할 수 있습니다. 각 멤버의 네트워크 공간은 응용 프로그램 또는 사용자가 Ethereum 트랜잭션을 제출하기 위해 상호 작용할 수 있는 일단의 부하 분산된 유효성 검사기 노드로 구성됩니다.

## <a name="concepts"></a>개념

### <a name="terminology"></a>용어

-   **합의** - 블록 유효성 검사 및 만들기를 통해 분산 네트워크에서 데이터를 동기화하는 행위입니다.

-   **컨소시엄 멤버** - 블록체인 네트워크에 대한 합의에 참여하는 엔터티입니다.

-   **관리자** - 지정된 컨소시엄 멤버의 참여를 관리하는 데 사용되는 Ethereum 계정입니다.

-   **유효성 검사기** - 관리자를 대신하여 합의에 참여하는 Ethereum 계정과 관련된 머신입니다.

### <a name="proof-of-authority"></a>인증 증명

블록체인 커뮤니티에 처음 들어온 경우, 이 솔루션의 릴리스는 Azure에서 이 기술을 쉽고 구성 가능한 방법으로 배울 좋은 기회입니다. 작업 증명은 계산 비용을 활용하여 네트워크를 자체적으로 규제하고 공정한 참여를 허용하는 Sybil 차단 메커니즘입니다. 이는 암호 화폐 경쟁이 네트워크의 보안을 촉진하는 익명의 개방형 블록체인 네트워크에서 효과적으로 작동합니다. 그러나 사설/컨소시엄 네트워크에서 기본 Ether는 가치가 없습니다. 대체 프로토콜로서 인증 증명은 모든 합의 참가자가 알려져 있으며, 권한이 부여되고 신뢰할 수 있는 네트워크에 더 적합합니다. 채굴할 필요가 없으면 비잔틴 내결함성을 유지하면서도 인증 증명이 더 효율적입니다.

### <a name="consortium-governance"></a>컨소시엄 거버넌스

인증 증명은 네트워크를 정상으로 유지하기 위해 권한이 부여된 네트워크 기관 목록을 사용하므로 이 권한 목록을 수정하기 위한 공정한 메커니즘을 제공해야 합니다. 각 배포에는 권한이 부여된 이 목록의 온체인(on-chain) 거버넌스를 위한 일단의 스마트 계약과 포털이 제공됩니다. 제안된 변경이 컨소시엄 멤버의 과반수 표결에 도달하면 해당 변경이 시행됩니다. 이렇게 하면 정직한 네트워크를 장려하는 투명한 방식으로 새 합의 참가자가 추가되거나 타협한 참가자가 제거될 수 있습니다.

### <a name="admin-account"></a>관리자 계정

인증 증명 노드를 배포하는 동안 관리자 Ethereum 주소를 묻는 메시지가 표시됩니다. 이 Ethereum 계정을 생성하고 보호하기 위해 여러 가지 메커니즘을 사용할 수 있습니다. 이 주소가 네트워크의 기관으로 추가되면 이 계정을 사용하여 거버넌스에 참여할 수 있습니다. 또한 이 관리자 계정은 이 배포의 일부로 만들어진 유효성 검사기 노드에 합의 참여를 위임하는 데 사용됩니다. 공용 Ethereum 주소만 사용되므로 각 관리자는 원하는 보안 모델을 준수하는 방식으로 개인 키를 유연하게 보호할 수 있습니다.

### <a name="validator-node"></a>유효성 검사기 노드

인증 증명 프로토콜에서 유효성 검사기 노드는 기존의 채굴기 노드를 대신합니다. 각 유효성 검사기에는 스마트 계약 권한 목록에 추가되는 고유한 Ethereum ID가 있습니다. 유효성 검사기가 이 목록에 있으면 블록 만들기 프로세스에 참여할 수 있습니다. 이 프로세스에 대한 자세한 내용은 [Authority Round 합의](https://wiki.parity.io/Aura)에 대한 Parity의 설명서를 참조하세요. 각 컨소시엄 멤버는 지리적 중복을 위해 5개 지역에 걸쳐 둘 이상의 유효성 검사기 노드를 프로비전할 수 있습니다. 유효성 검사기 노드는 다른 유효성 검사기 노드와 통신하여 기본 분산 원장의 상태에 관한 합의에 도달합니다.
네트워크에서 공정한 참여를 보장하기 위해 각 컨소시엄 멤버는 네트워크의 첫 번째 멤버보다 더 많은 유효성 검사기를 사용하지 못하도록 금지됩니다. 첫 번째 멤버가 세 개의 유효성 검사기를 배포하는 경우 각 멤버는 유효성 검사기를 최대 세 개까지만 가질 수 있습니다.

### <a name="identity-store"></a>ID 저장소

각 멤버마다 동시에 실행되는 여러 유효성 검사기 노드가 있고 각 노드마다 권한이 부여된 ID가 있어야 하므로 유효성 검사기가 네트워크에서 고유한 활성 ID를 안전하게 얻을 수 있어야 합니다. 이를 용이하게 하기 위해 생성된 Ethereum ID를 안전하게 보유하고 있는 각 멤버의 구독에 배포되는 ID 저장소를 구축했습니다. 배포 시 오케스트레이션 컨테이너는 각 유효성 검사기에 대한 Ethereum 개인 키를 생성하고 Azure Key Vault에 저장합니다. 패리티 노드가 시작되기 전에 먼저 사용되지 않은 ID에 대한 임대를 획득하여 ID가 다른 노드에서 선택되지 않았는지 확인합니다. ID는 블록을 만들기 시작하는 권한을 부여하는 클라이언트에 제공됩니다. 호스팅 VM이 중단되면 ID 임대가 해제되어 나중에 대체 노드에서 해당 ID를 다시 시작할 수 있습니다.

### <a name="bootnode-registrar"></a>부트노드 등록자

용이하게 연결하기 위해 각 멤버는 [데이터 API 엔드포인트](#data-api)에서 일단의 연결 정보를 호스팅합니다. 이 데이터에는 조인 멤버에 대한 피어링 노드로 제공되는 부트노드 목록이 포함됩니다. 이 데이터 API의 일환으로 이 부트노드 목록을 최신 상태로 유지합니다

### <a name="bring-your-own-operator"></a>사용자 고유 연산자 가져오기

컨소시엄 멤버는 종종 네트워크 관리에 참여하려고 하지만 인프라를 운영 및 유지 관리하지 않으려고 합니다. 기존 시스템과는 달리 네트워크 전체에 걸쳐 단일 운영자를 배치하면 분산 모델의 블록체인 시스템이 작동합니다. 네트워크를 운영하기 위해 중앙 집중식 중개자를 고용하는 대신, 각 컨소시엄 멤버는 자신이 선택한 운영자에게 인프라 관리를 위임할 수 있습니다. 이렇게 하면 각 멤버가 자신의 인프라를 운영하거나 다른 파트너에게 작업을 위임할 수 있는 하이브리드 모델을 허용합니다. 위임된 작업 흐름은 다음과 같이 작동합니다.

1.  **컨소시엄 멤버**가 Ethereum 주소를 생성합니다(개인 키 보유).

2.  **컨소시엄 멤버**가 **운영자**에게 공개 Ethereum 주소를 제공합니다.

3.  **운영자**는 Azure Resource Manager 솔루션을 사용하여 PoA 유효성 검사기 노드를 배포하고 구성합니다

4.  **운영자**가 **컨소시엄 멤버**에게 RPC 및 관리 엔드포인트를 제공합니다.

5.  **컨소시엄 멤버**가 개인 키를 사용하여 **운영자**가 자신을 대신하여 참여하도록 배포한 유효성 검사기 노드를 수락하는 요청에 서명합니다.

![사용자 고유 연산자 가져오기](./media/ethereum-poa-deployment-guide/bring-you-own-operator.png)

### <a name="azure-monitor"></a>Azure Monitor

이 솔루션은 노드 및 네트워크 통계를 추적하기 위해 Azure Monitor도 제공합니다. 응용 프로그램 개발자를 위해 기본 블록체인에 대한 가시성을 제공하여 블록 생성 통계를 추적합니다. 네트워크 운영자는 Azure Monitor를 사용하여 인프라 통계 및 쿼리 가능한 로그를 통해 네트워크 중단을 빠르게 감지하고 방지할 수 있습니다. 자세한 내용은 [서비스 모니터링](#service-monitoring)을 참조하세요.

### <a name="deployment-architecture"></a>배포 아키텍처

#### <a name="description"></a>설명

이 솔루션은 단일 또는 다중 지역 기반 다중 멤버 Ethereum 컨소시엄 네트워크를 배포할 수 있습니다. 기본적으로 RPC 및 피어링 엔드포인트는 공용 IP를 통해 액세스할 수 있으므로 구독 및 클라우드 간 연결을 간소화할 수 있습니다. 응용 프로그램 수준 액세스 제어에 [Parity의 권한 부여 계약](https://wiki.parity.io/Permissioning)을 활용하는 것이 좋습니다. 또한 구독 간 연결에 VNet 게이트웨이를 활용하는 VPN 뒤에 배포되는 네트워크도 지원합니다. 이러한 배포는 더 복잡하므로 먼저 공용 IP 모델부터 시작하는 것이 좋습니다.

#### <a name="consortium-member-overview"></a>컨소시엄 멤버 개요

각 컨소시엄 멤버 배포에는 다음이 포함됩니다.

-   PoA 유효성 검사기를 실행하기 위한 VMSS(Virtual Machine Scale Sets)

-   RPC, 피어링 및 거버넌스 DApp 요청을 배포하기 위한 Azure Load Balancer

-   유효성 검사기 ID 보안을 위한 Azure Key Vault

-   지속적인 네트워크 정보 호스팅 및 임대 조정을 위한 Azure Storage

-   로그 및 성능 통계를 집계하기 위한 Azure Monitor

-   사설 VNet 간 VPN 연결을 허용하기 위한 VNet 게이트웨이(선택 사항)

![배포 아키텍처](./media/ethereum-poa-deployment-guide/deployment-architecture.png)

안정성과 모듈화를 위해 Docker 컨테이너를 활용합니다. Azure Container Registry를 사용하여 각 배포의 일환으로 버전이 있는 이미지를 호스팅하고 제공합니다. 컨테이너 이미지는 다음과 같이 구성됩니다.

-   오케스트레이터

    -   배포하는 동안 한 번 실행됩니다.

    -   ID 및 거버넌스 계약을 생성합니다.

    -   ID 저장소에 ID를 저장합니다.

-   Parity 클라이언트

    -   ID 저장소에서 ID를 임대합니다.

    -   피어를 검색하고 연결합니다.

-   EthStats 에이전트

    -   RPC를 통해 로컬 로그와 통계를 수집하고 Azure Monitor에 푸시합니다.

-   거버넌스 DApp

    -   거버넌스 계약과 상호 작용하기 위한 웹 인터페이스

## <a name="governance-dapp"></a>거버넌스 DApp

인증 증명의 중심에는 분산 거버넌스가 있습니다. 거버넌스 DApp는 네트워크에서 기관을 관리하는 데 사용되는 일단의 미리 배포된 스마트 계약 및 웹 응용 프로그램입니다.
기관은 관리자 ID 및 유효성 검사기 노드로 분할됩니다.
관리자는 일단의 검사기 노드에 합의 참여를 위임할 수 있습니다. 관리자는 네트워크 내부 또는 외부에서 다른 관리자에게 투표할 수도 있습니다.

![거버넌스 DApp](./media/ethereum-poa-deployment-guide/governance-dapp.png)

-   **분산 거버넌스 -**  네트워크 기관의 변경은 선택한 관리자의 온체인 투표를 통해 관리됩니다.

-   **유효성 검사기 위임 -**  기관은 각 PoA 배포에서 설정된 유효성 검사기 노드를 관리할 수 있습니다.

-   **감사 가능한 변경 기록 -**  각 변경은 투명성과 감사 가능성을 제공하는 블록체인에 기록됩니다.

## <a name="how-to-guides"></a>방법 가이드

### <a name="deploy-ethereum-proof-of-authority"></a>Ethereum 인증 증명 배포

다자간 배포 흐름의 예는 다음과 같습니다.

1.  세 명의 멤버가 각각 MetaMask를 사용하여 Ethereum 계정을 생성합니다.

2.  *멤버 A*가 Ethereum PoA를 배포하여 해당 Ethereum 공용 주소를 제공합니다.

3.  *멤버 A*가 *멤버 B* 및 *멤버 C*에게 컨소시엄 URL을 제공합니다.

4.  *멤버 B* 및 *멤버 C*가 Ethereum PoA를 배포하여 해당 Ethereum 공용 주소 및 *멤버 A*의 컨소시엄 URL을 제공합니다.

5.  *멤버 A*가 *멤버 B*에게 관리자로 투표합니다.

6.  *멤버 A* 및 *멤버 B*가 모두 *멤버 C*에게 관리자로 투표합니다.

이 프로세스를 실행하려면 여러 가상 머신 확장 집합 및 관리 디스크의 배포를 지원할 수 있는 Azure 구독이 필요합니다. 필요한 경우 [체험 Azure 계정을 만들어](https://azure.microsoft.com/free/) 시작합니다.

구독이 보호된 후 Azure Portal로 이동합니다. '+', Marketplace('모두 보기')를 선택하고 Ethereum PoA 컨소시엄을 검색합니다.

다음 섹션에서는 네트워크에서 첫 번째 멤버의 공간을 구성하는 과정을 안내합니다. 배포 흐름은 기본 사항, 배포 지역, 네트워크 크기 및 성능, Ethereum 설정, Azure Monitor의 5단계로 구분됩니다.

#### <a name="basics"></a>기본 사항

**기본 사항** 아래에서 구독, 리소스 그룹 및 기본 가상 머신 속성과 같은 배포에 대한 표준 매개 변수의 값을 지정합니다.

각 매개 변수에 대한 자세한 설명은 다음과 같습니다.

매개 변수 이름|설명|허용되는 값|기본값    
---|---|---|---
새 네트워크를 만드시겠습니까, 아니면 기존 네트워크를 조인하시겠습니까?|새 네트워크를 만들거나 기존 컨소시엄 네트워크를 조인합니다.|새로 만들기 기존 조인|새로 만들기
이메일 주소(선택 사항)|배포에 대한 정보를 사용하여 배포가 완료되면 이메일 알림을 받게 됩니다.|유효한 이메일 주소|해당 없음
VM 사용자 이름|각 배포된 VM의 관리자 사용자 이름(영숫자 문자만)|1-64자|해당 없음
인증 유형|가상 머신을 인증하는 방법.|암호 또는 SSH 공개 키|암호
암호(인증 형식 = 암호)|배포된 각 가상 머신의 관리자 계정 암호.  암호에는 대문자 1자, 소문자 1자, 숫자 1개 및 특수 문자 1자 중 3가지가 포함되어야 합니다. 처음에는 모든 VM의 암호가 동일하지만, 프로비전 후 암호를 변경할 수 있습니다.|12-72자|해당 없음
SSH 키(인증 유형 = 공개 키)|원격 로그인에 사용하는 보안 셸 키.||해당 없음
구독|컨소시엄 네트워크를 배포하는 구독||해당 없음
리소스 그룹|컨소시엄 네트워크를 배포하는 리소스 그룹.||해당 없음
위치|리소스 그룹에 대한 Azure 지역입니다.||해당 없음

샘플 배포는 다음과 같습니다. ![기본 사항 블레이드](./media/ethereum-poa-deployment-guide/basic-blade.png)

#### <a name="deployment-regions"></a>배포 지역

다음으로, [배포 지역] 아래에서 컨소시엄 네트워크를 배포할 지역 수를 입력하고 지정한 지역 수에 따라 Azure 지역을 선택합니다. 사용자는 최대 5개 지역에 배포할 수 있습니다. [기본 사항] 섹션의 리소스 그룹 위치와 일치하는 첫 번째 지역을 선택하는 것이 좋습니다. 개발 또는 테스트 네트워크의 경우 멤버당 하나의 지역을 사용하는 것이 좋습니다. 프로덕션 환경의 경우 고가용성을 위해 둘 이상의 지역에 걸쳐 배포하는 것이 좋습니다.

각 매개 변수에 대한 자세한 설명은 다음과 같습니다.

  매개 변수 이름|설명|허용되는 값|기본값
  ---|---|---|---
  지역 수|컨소시엄 네트워크를 배포하는 지역 수|1, 2, 3, 4, 5|1
  첫 번째 지역|컨소시엄 네트워크를 배포하는 첫 번째 지역|모든 허용된 Azure 지역|해당 없음
  두 번째 지역|컨소시엄 네트워크를 배포하는 두 번째 지역(지역 수를 2로 선택한 경우에만 볼 수 있음)|모든 허용된 Azure 지역|해당 없음
  세 번째 지역|컨소시엄 네트워크를 배포하는 세 번째 지역(지역 수를 3으로 선택한 경우에만 볼 수 있음)|모든 허용된 Azure 지역|해당 없음
  네 번째 지역|컨소시엄 네트워크를 배포하는 네 번째 지역(지역 수를 4로 선택한 경우에만 볼 수 있음)|모든 허용된 Azure 지역|해당 없음
  다섯 번째 지역|컨소시엄 네트워크를 배포하는 다섯 번째 지역(지역 수를 5로 선택한 경우에만 볼 수 있음)|모든 허용된 Azure 지역|해당 없음

샘플 배포는 다음과 같습니다. ![배포 지역](./media/ethereum-poa-deployment-guide/deployment-regions.png)

#### <a name="network-size-and-performance"></a>네트워크 크기 및 성능 

다음으로, '네트워크 크기 및 성능' 아래에서 컨소시엄 네트워크의 크기(예: 유효성 검사기 노드의 수와 크기)를 입력합니다.
유효성 검사기 노드 저장소 크기에 따라 블록체인의 잠재적 크기가 결정됩니다. 이는 배포 후에 변경할 수 있습니다.

각 매개 변수에 대한 자세한 설명은 다음과 같습니다.

  매개 변수 이름|설명|허용되는 값|기본값
  ---|---|---|---
  부하가 분산된 유효성 검사기 노드 수|네트워크의 일부로 프로비전하는 유효성 검사기 노드의 수입니다.|2-15|2
  유효성 검사기 노드 저장소 성능|배포한 유효성 검사기 노드 각각을 지원하는 관리 디스크의 유형입니다.|Standard 또는 Premium|Standard
  유효성 검사기 노드 가상 머신 크기|유효성 검사기 노드에 사용되는 가상 머신 크기입니다.|표준 A, 표준 D, 표준 D-v2, 표준 F 시리즈, 표준 DS 및 표준 FS|표준 D1 v2

샘플 배포는 다음과 같습니다: ![네트워크 크기 및 성능](./media/ethereum-poa-deployment-guide/network-size-and-performance.png)

#### <a name="ethereum-settings"></a>Ethereum 설정

다음으로, [Ethereum 설정] 아래에서 Ethereum 관련 구성 설정(예: 네트워크 ID 및 Ethereum 계정 암호 또는 제네시스 블록)을 지정합니다.

각 매개 변수에 대한 자세한 설명은 다음과 같습니다.

  매개 변수 이름|설명|허용되는 값|기본값
  ---|---|---|---
컨소시엄 멤버 ID|충돌을 방지하기 위해 IP 주소 공간을 구성하는 데 사용하는 컨소시엄 네트워크에 참가하는 각 멤버와 연결된 ID입니다. 사설망의 경우 멤버 ID는 동일한 네트워크의 여러 조직에서 고유해야 합니다.  고유 멤버 ID는 같은 조직이 여러 지역에 배포하는 경우에도 필요합니다. 충돌이 없는지 확인하기 위해 다른 조인 멤버와 공유해야 하므로 이 매개 변수의 값을 적어 둡니다.|0-255|해당 없음
네트워크 ID|배포하는 컨소시엄 Ethereum 네트워크의 네트워크 ID입니다.  각 Ethereum 네트워크에는 공용 네트워크에 대한 ID를 1로 하는 자체의 네트워크 ID가 있습니다.|5 ~ 999,999,999|10101010
관리자 Ethereum 주소|PoA 거버넌스 참여에 사용되는 Ethereum 계정 주소입니다.  Ethereum 주소를 생성하는 경우 MetaMask를 사용하는 것이 좋습니다.|0x로 시작하는 42자의 영숫자 문자|해당 없음
고급 옵션|Ethereum 설정에 대한 고급 옵션|사용 또는 사용 안 함|사용 안 함
공용 IP(고급 옵션 = 사용)|VNet 게이트웨이 뒤에 네트워크를 배포하고 피어링 액세스를 제거합니다. 이 옵션을 선택하면 모든 멤버가 VNet 게이트웨이를 사용해야 연결이 호환됩니다.|공용 IP 사설 VNet|공용 IP
트랜잭션 권한 계약(고급 옵션 = 사용)|트랜잭션 권한 지정 계약에 대한 바이트 코드입니다. 스마트 계약 배포 및 실행을 권한이 부여된 Ethereum 계정 목록으로 제한합니다.|계약 바이트 코드|해당 없음

샘플 배포는 다음과 같습니다: ![Ethereum 설정](./media/ethereum-poa-deployment-guide/ethereum-settings.png)

#### <a name="azure-monitor"></a>Azure Monitor

Azure Monitor 블레이드를 사용하면 네트워크에 Azure Monitor 리소스를 구성할 수 있습니다. Azure Monitor는 네트워크에서 유용한 메트릭과 로그를 수집하고 노출하여 네트워크 상태 또는 디버그 문제를 빨리 검사하는 기능을 제공합니다.

  매개 변수 이름|설명|허용되는 값|기본값
  ---|---|---|---
모니터링|Azure Monitor를 사용하도록 설정하는 옵션입니다.|사용 또는 사용 안 함|사용
기존 Log Analytics에 연결|Azure Monitor의 일부로 새 Log Analytics 인스턴스를 만들거나 기존 인스턴스에 조인합니다.|새로 만들기 또는 기존 조인|새로 만들기
Azure Monitor 위치(기존 Azure Monitor에 연결 = 새로 만들기)|새 Azure Monitor가 배포될 지역입니다.|모든 Azure Monitor 지역|해당 없음
기존 Log Analytics 작업 영역 ID(기존 Azure Monitor에 연결 = 기존 조인)|기존 Log Analytics 인스턴스의 작업 영역 ID입니다.||해당 없음
기존 Log Analytics 기본 키(기존 Azure Monitor에 연결 = 기존 조인)|기존 Azure Monitor 인스턴스에 연결하는 데 사용되는 기본 키입니다.||해당 없음


샘플 배포는 다음과 같습니다. ![Azure Monitor](./media/ethereum-poa-deployment-guide/azure-monitor.png)

#### <a name="summary"></a>요약

요약 블레이드를 클릭하여 지정한 입력을 검토하고 기본 배포 전 유효성 검사를 실행합니다. 배포하기 전에 템플릿과 매개 변수를 다운로드할 수 있습니다.

법률 정보 및 개인정보처리방침을 검토하고 '구매'를 클릭하여 배포합니다. 배포에 VNet 게이트웨이가 포함된 경우 배포하는 데 45-50분이 걸립니다.

#### <a name="post-deployment"></a>배포 후

##### <a name="deployment-output"></a>배포 출력 

배포가 완료되면 확인 이메일 또는 Azure Portal을 통해 필요한 매개 변수에 액세스할 수 있습니다. 이러한 매개 변수에서 확인할 수 있는 항목은 다음과 같습니다.

-   Ethereum RPC 엔드포인트

-   거버넌스 대시보드 URL

-   Azure Monitor URL

-   데이터 URL

-   VNet 게이트웨이 리소스 ID(선택 사항)

##### <a name="confirmation-email"></a>확인 이메일 

이메일 주소([기본 사항 섹션](#basics))를 제공하면 배포 출력 정보가 포함된 이메일 주소로 이메일이 보내집니다.

![배포 이메일](./media/ethereum-poa-deployment-guide/deployment-email.png)

##### <a name="portal"></a>포털

배포가 성공적으로 완료되고 모든 리소스가 프로비전되면 리소스 그룹의 출력 매개 변수를 볼 수 있습니다.

1.  포털에서 리소스 그룹을 찾습니다.

2.  *배포*로 이동합니다.

3.  리소스 그룹과 이름이 동일한 최상위 배포를 선택합니다.

4.  *출력*을 선택합니다.

### <a name="growing-the-consortium"></a>컨소시엄 확장

컨소시엄을 확장하려면 먼저 실제 네트워크를 연결해야 합니다.
공용 IP 기반 배포를 사용하면 이 첫 번째 단계가 원활하게 수행됩니다. VPN 뒤에 배포하는 경우 [VNet 게이트웨이 연결](#connecting-vnet-gateways)을 참조하여 네트워크 연결을 [2단계](#step-2-new-admin-deployment)의 일환으로 수행합니다.

#### <a name="step-1-add-the-new-admin"></a>1단계: 새 관리자 추가

1.  새 관리자의 공용 Ethereum 주소를 수집합니다.

2.  거버넌스 DApp로 이동하고 Ethereum 주소와 적절한 별칭을 사용하여 새 관리자를 만듭니다.

3.  이 새 관리자를 추가하기 위해 투표할 수 있도록 새 요청을 다른 기존 멤버에게 알립니다.

4.  투표가 51%에 도달하면 해당 멤버가 관리자로 추가됩니다.

![관리자 추가](./media/ethereum-poa-deployment-guide/adding-admin.png)

#### <a name="step-2-new-admin-deployment"></a>2단계: 새 관리자 배포

1.  다음 정보를 조인 멤버와 공유합니다. 이 정보는 배포 후 이메일 또는 포털 배포 출력에서 찾을 수 있습니다.

    -  컨소시엄 데이터 URL

    -  배포한 노드 수

    -  VNet 게이트웨이 리소스 ID(VPN을 사용하는 경우)

2.  배포 멤버는 네트워크 참석을 배포할 때 다음 사항에 유의하여 [동일한 솔루션](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium)을 사용해야 합니다.

    -  *기존 조인*을 선택합니다.

    -  공정한 표현을 보장하기 위해 네트워크상의 나머지 멤버와 동일한 유효성 검사기 노드 수를 선택합니다

    -  [이전 단계](#step-1-add-the-new-admin)에서 제공한 것과 동일한 Ethereum 주소를 사용합니다.

    -  *Ethereum 설정* 탭에서 제공한 *컨소시엄 데이터 URL*을 전달합니다.

    -  네트워크의 나머지 부분이 VPN 뒤에 있는 경우 고급 섹션 아래에서 *사설 VNet*을 선택합니다.

#### <a name="step-3-delegate-validators"></a>3단계: 유효성 검사기 위임

새 관리자는 자신을 대신하여 참여할 유효성 검사기에 권한을 부여해야 합니다. 멤버만 키를 제어하므로 이 작업은 자동으로 수행할 수 없습니다.\*

_\*네트워크의 제네시스 블록에 유효성 검사기 노드를 미리 컴파일할 수 있으므로 네트워크에 있는 첫 번째 멤버의 노드는 자동으로 추가할 수 있습니다._

새 관리자가 수행해야 하는 단계는 다음과 같습니다.

1.  *해당* 배포의 일환으로 배포된 거버넌스 DApp를 엽니다.

2.  유효성 검사기 탭으로 이동합니다.

3.  배포된 각 유효성 검사기를 선택하고 **추가**를 클릭합니다.

![유효성 검사기 추가](./media/ethereum-poa-deployment-guide/adding-validators.png)

#### <a name="connecting-vnet-gateways"></a>VNet 게이트웨이 연결

사설망의 경우 다른 멤버는 VNet 게이트웨이 연결을 통해 연결됩니다. 멤버가 네트워크에 조인하고 트랜잭션 트래픽을 볼 수 있으려면 먼저 기존 멤버가 자신의 VPN 게이트웨이에서 최종 구성을 수행하여 해당 연결을 수락해야 합니다. 즉 연결이 설정될 때까지 조인 멤버의 Ethereum 노드는 실행되지 않습니다. 단일 실패 지점의 가능성을 줄이기 위해 컨소시엄에 중복 네트워크 연결(메시)을 만드는 것이 좋습니다.

새 멤버가 배포되면 기존 멤버는 새 멤버에 대한 VNet 게이트웨이 연결을 설정하여 양방향 연결을 완료해야 합니다. 이를 위해 기존 멤버에 필요한 항목은 다음과 같습니다.

1.  연결 멤버의 VNet 게이트웨이 ResourceId(배포 출력 참조)

2.  공유 연결 키

기존 멤버는 다음 PowerShell 스크립트를 실행하여 연결을 완료해야 합니다. 포털에서 오른쪽 위의 탐색 모음에 있는 Azure Cloud Shell을 사용하는 것이 좋습니다.

![Cloud Shell](./media/ethereum-poa-deployment-guide/cloud-shell.png)

```bash
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzureRmSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzureRmVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

### <a name="service-monitoring"></a>서비스 모니터링

배포 이메일의 링크를 따르거나 \[OMS\_PORTAL\_URL\] 배포 출력에서 매개 변수를 찾아서 Azure Monitor 포털을 찾을 수 있습니다.

포털에서 먼저 고급 네트워크 통계와 노드 개요를 표시합니다.

![모니터링 범주](./media/ethereum-poa-deployment-guide/monitor-categories.png)

**노드 개요**를 선택하면 노드별 인프라 통계를 볼 수 있는 포털로 이동합니다.

![노드 통계](./media/ethereum-poa-deployment-guide/node-stats.png)

**네트워크 통계**를 선택하면 Ethereum 네트워크 통계를 볼 수 있습니다.

![네트워크 통계](./media/ethereum-poa-deployment-guide/network-stats.png)

#### <a name="sample-log-analytics-queries"></a>Log Analytics 쿼리 샘플

이러한 대시보드 뒤에는 일단의 쿼리 가능한 원시 로그가 있습니다. 이러한 원시 로그를 사용하여 대시보드를 사용자 지정하고, 실패를 조사하거나 임계값 경고를 설정할 수 있습니다. [로그 검색] 도구에서 실행할 수 있는 일단의 쿼리 예제는 다음과 같습니다.

##### <a name="lists-blocks-that-have-been-reported-by-more-than-one-validator-useful-to-help-find-chain-forks"></a>둘 이상의 유효성 검사기에서 보고한 블록을 나열합니다. 체인 포크를 찾는 데 도움이 됩니다.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

##### <a name="get-average-peer-count-for-a-specified-validator-node-averaged-over-5-minute-buckets"></a>지정된 유효성 검사기 노드에 대한 평균 피어 수(5분 버킷에 대한 평균)를 가져옵니다.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int)) 
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

### <a name="ssh-access"></a>SSH 액세스

보안상의 이유로 SSH 포트 액세스는 기본적으로 네트워크 그룹 보안 규칙에 의해 거부됩니다. PoA 네트워크에서 가상 머신 확장 집합 인스턴스에 액세스하려면 이 규칙을 \"허용\"으로 변경해야 합니다.

1.  Azure Portal에서 배포된 리소스 그룹의 [개요] 섹션에서 시작합니다.

    ![SSH 개요](./media/ethereum-poa-deployment-guide/ssh-overview.png)

2.  네트워크 보안 그룹을 선택합니다.

    ![SSH NSG](./media/ethereum-poa-deployment-guide/ssh-nsg.png)

3.  \"allow-ssh\" 규칙을 선택합니다.

    ![allow-ssh](./media/ethereum-poa-deployment-guide/ssh-allow.png)

4.  \"작업\"을 [허용]으로 변경합니다.

    ![SSH 사용 허용](./media/ethereum-poa-deployment-guide/ssh-enable-allow.png)

5.  \"저장\"을 클릭합니다(변경 내용을 적용하는 데 몇 분이 걸릴 수 있음).

이제 제공된 관리자 사용자 이름 및 암호/SSH 키를 사용하여 SSH를 통해 유효성 검사기 노드에 대한 가상 머신에 원격으로 연결할 수 있습니다.
첫 번째 유효성 검사기 노드에 액세스하기 위해 실행하는 SSH 명령(샘플 배포의 경우: ssh - p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com)은 템플릿 배포 출력 매개 변수에서 'SSH\_TO\_FIRST\_VL\_NODE\_REGION1'로 나열됩니다. 추가 트랜잭션 노드를 가져오려면 포트 번호를 1만큼 증가합니다(예를 들어 첫 번째 트랜잭션 노드는 포트 4000에 있음).

둘 이상의 지역에 배포한 경우 위의 명령을 해당 지역의 부하 분산 장치에 대한 DNS 이름 또는 IP 주소로 변경합니다. 다른 지역의 DNS 이름이나 IP 주소를 찾으려면 \*\*\*\*\*-lbpip-reg\# 명명 규칙을 사용하여 리소스를 찾아서 해당 DNS 이름과 IP 주소 속성을 확인합니다.

### <a name="azure-traffic-manager-load-balancing"></a>Azure Traffic Manager 부하 분산

Azure Traffic Manager는 들어오는 트래픽을 여러 지역의 여러 배포로 라우팅하여 가동 중지 시간을 줄이고 PoA 네트워크의 응답성을 향상시킬 수 있습니다. 기본 제공 상태 확인 및 자동 다시 라우팅은 RPC 엔드포인트와 거버넌스 DApp의 고가용성을 보장하는 데 도움이 됩니다. 이 기능은 여러 지역에 배포하고 프로덕션을 준비하는 경우에 유용합니다.

Traffic Manager를 사용하는 목적은 다음과 같습니다.

-   자동 장애 조치를 통해 PoA 네트워크 가용성을 향상시킵니다.

-   최종 사용자를 네트워크 대기 시간이 가장 짧은 Azure 위치로 라우팅하여 네트워크 응답성을 높입니다.

Traffic Manager 프로필을 만들려는 경우 네트워크에 액세스하기 위해 프로필의 DNS 이름을 사용할 수 있습니다. 다른 컨소시엄 멤버가 네트워크에 추가되면 Traffic Manager를 사용하여 배포된 유효성 검사기에서 부하를 분산시킬 수도 있습니다.

#### <a name="creating-a-traffic-manager-profile"></a>Traffic Manager 프로필 만들기

Azure Portal에서 \"리소스 만들기\" 단추를 클릭한 후 \"Traffic Manager 프로필\"을 검색하여 선택합니다.

![Azure Traffic Manager 검색](./media/ethereum-poa-deployment-guide/traffic-manager-search.png)

프로필에 고유한 이름을 지정하고 PoA 배포 중에 만든 리소스 그룹을 선택합니다.

![Traffic Manager 만들기](./media/ethereum-poa-deployment-guide/traffic-manager-create.png)

프로필이 배포되면 리소스 그룹에서 인스턴스를 선택합니다. Traffic Manager에 액세스하기 위한 DNS 이름은 [개요] 탭에서 확인할 수 있습니다.

![Traffic Manager DNS 찾기](./media/ethereum-poa-deployment-guide/traffic-manager-dns.png)

[엔드포인트] 탭을 선택하고 [추가] 단추를 클릭합니다. 그런 다음, [대상 리소스 종류]를 [공용 IP 주소]로 변경합니다. 그런 다음, 첫 번째 지역의 부하 분산 장치에 대한 공용 IP 주소를 선택합니다.

![Traffic Manager 라우팅](./media/ethereum-poa-deployment-guide/traffic-manager-routing.png)

배포된 네트워크의 각 지역에 대해 반복합니다. 엔드포인트가 \"사용\" 상태에 있으면 해당 엔드포인트가 Traffic Manager의 DNS 이름에 자동으로 로드되고 지역적으로 분산됩니다. 이제 문서의 다른 단계에서 \[CONSORTIUM\_DATA\_URL\] 매개 변수를 대신하여 이 DNS 이름을 사용할 수 있습니다.

## <a name="data-api"></a>데이터 API

각 컨소시엄 멤버는 다른 멤버가 네트워크에 연결하는 데 필요한 정보를 호스팅합니다. 기존 멤버는 멤버를 배포하기 전에 [CONSORTIUM_DATA_URL]을 제공합니다. 배포 시 조인 멤버는 다음 엔드포인트의 JSON 인터페이스에서 정보를 검색합니다.

`<CONSORTIUM_DATA_URL>/networkinfo`

응답에는 기존 멤버에 유용한 정보(유효성 검사기 주소)뿐만 아니라 조인 멤버에 유용한 정보(제네시스 블록, ABI 유효성 검사기 집합 계약, 부트노드)도 포함됩니다. 클라우드 공급자 간에 컨소시엄을 확장하려면 이 표준화를 사용하는 것이 좋습니다. 이 API는 다음과 같은 구조의 JSON 형식 응답을 반환합니다.
```json
{
  "$id": "", 
  "type": "object", 
  "definitions": {}, 
  "$schema": "http://json-schema.org/draft-07/schema#", 
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion", 
      "type": "integer", 
      "title": "This schema’s major version", 
      "default": 0, 
      "examples": [
        0
      ]
    }, 
    "minorVersion": {
      "$id": "/properties/minorVersion", 
      "type": "integer", 
      "title": "This schema’s minor version", 
      "default": 0, 
      "examples": [
        0
      ]
    }, 
    "bootnodes": {
      "$id": "/properties/bootnodes", 
      "type": "array", 
      "items": {
        "$id": "/properties/bootnodes/items", 
        "type": "string", 
        "title": "This member’s bootnodes", 
        "default": "", 
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300", 
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    }, 
    "valSetContract": {
      "$id": "/properties/valSetContract", 
      "type": "string", 
      "title": "The ValidatorSet Contract Source", 
      "default": "", 
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    }, 
    "adminContract": {
      "$id": "/properties/adminContract", 
      "type": "string", 
      "title": "The AdminSet Contract Source", 
      "default": "", 
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    }, 
    "adminContractABI": {
      "$id": "/properties/adminContractABI", 
      "type": "string", 
      "title": "The Admin Contract ABI", 
      "default": "", 
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    }, 
    "paritySpec": {
      "$id": "/properties/paritySpec", 
      "type": "string", 
      "title": "The Parity client spec file", 
      "default": "", 
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    }, 
    "errorMessage": {
      "$id": "/properties/errorMessage", 
      "type": "string", 
      "title": "Error message", 
      "default": "", 
      "examples": [
        ""
      ]
    }, 
    "addressList": {
      "$id": "/properties/addressList", 
      "type": "object", 
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses", 
          "type": "array", 
          "items": {
            "$id": "/properties/addressList/properties/addresses/items", 
            "type": "string", 
            "title": "This member’s validator addresses", 
            "default": "", 
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f", 
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

```
## <a name="development"></a>개발

### <a name="programmatically-interacting-with-a-smart-contract"></a>프로그래밍 방식으로 스마트 계약과 상호 작용

> [!WARNING]
> Ethereum 개인 키는 네트워크를 통해 보내지 마세요! 각 트랜잭션을 로컬에서 먼저 서명한 다음, 네트워크를 통해 서명된 트랜잭션을 보내도록 합니다.

다음 예제에서는 *ethereumjs-wallet*을 사용하여 Ethereum 주소를 생성하고, *ethereumjs-tx*를 사용하여 로컬에서 서명하고, *web3*을 사용하여 원시 트랜잭션을 Ethereum RPC 엔드포인트로 보냅니다.

다음 예제에서는 간단한 Hello-World 스마트 계약이 사용됩니다.

```javascript
pragma solidity ^0.4.11;
contract postBox {
    string message;
    function postMsg(string text) public {
        message = text; 
    }
    function getMsg() public view returns (string) {
        return message;
    }
}
```

다음 예제에서는 계약이 이미 배포되었다고 가정합니다. *solc* 및*web3*을 사용하여 프로그래밍 방식으로 계약을 배포할 수 있습니다. 먼저 다음 노드 모듈을 설치합니다.
```
sudo npm install web3@0.20.2
sudo npm install ethereumjs-tx@1.3.6
sudo npm install ethereumjs-wallet@0.6.1
```
이 NodeJS 스크립트에서 수행하는 작업은 다음과 같습니다.

-   원시 트랜잭션 생성: postMsg

-   생성된 개인 키를 사용하여 트랜잭션에 서명

-   Ethereum 네트워크에 서명된 트랜잭션 제출

```javascript
var ethereumjs = require('ethereumjs-tx')
var wallet = require('ethereumjs-wallet')
var Web3 = require('web3')

// TODO Replace with your contract address
var address = "0xfe53559f5f7a77125039a993e8d5d9c2901edc58";
var abi = [{"constant": false,"inputs": [{"name": "text","type": "string"}],"name": "postMsg","outputs": [],"payable": false,"stateMutability": "nonpayable","type": "function"},{"constant": true,"inputs": [],"name": "getMsg","outputs": [{"name": "","type": "string"}],"payable": false,"stateMutability": "view","type": "function"}];

// Generate a new Ethereum account
var account = wallet.generate();
var accountAddress = account.getAddressString()
var privateKey = account.getPrivateKey();

 // TODO Replace with your RPC endpoint
 var web3 = new Web3(new Web3.providers.HttpProvider(
    "http://testzvdky-dns-reg1.eastus.cloudapp.azure.com:8545"));
 
// Get the current nonce of the account
 web3.eth.getTransactionCount(accountAddress, function (err, nonce) {
   var data = web3.eth.contract(abi).at(address).postMsg.getData("Hello World");
   var rawTx = {
     nonce: nonce,
     gasPrice: '0x00', 
     gasLimit: '0x2FAF080',
     to: address, 
     value: '0x00', 
     data: data
   }
   var tx = new ethereumjs(rawTx);
   
   tx.sign(privateKey);

   var raw = '0x' + tx.serialize().toString('hex');
   web3.eth.sendRawTransaction(raw, function (txErr, transactionHash) {
     console.log("TX Hash: " + transactionHash);
     console.log("Error: " + txErr);
   });
 });
```

### <a name="debug-a-smart-contract-truffle-develop"></a>스마트 계약 디버그(Truffle Develop)

Truffle은 스마트 계약을 디버그하는 데 사용할 수 있는 로컬 개발 네트워크를 보유하고 있습니다. 전체 자습서는 [여기서](http://truffleframework.com/tutorials/debugging-a-smart-contract) 찾을 수 있습니다.

### <a name="webassembly-wasm-support"></a>WebAssembly(WASM) 지원

WebAssembly 지원은 새로 배포된 PoA 네트워크에서 이미 사용하도록 설정되어 있습니다. 이를 통해 스마트 계약을 웹 어셈블리로 변환 컴파일되는 모든 언어(Rust, C, C++)로 개발할 수 있습니다. 자세한 내용은 아래 링크를 참조하세요.

-   WebAssembly에 대한 Parity 개요 - <https://wiki.parity.io/WebAssembly-Home>

-   Parity Tech의 자습서 - <https://github.com/paritytech/pwasm-tutorial>

## <a name="faq"></a>FAQ

### <a name="i-notice-a-bunch-of-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>네트워크에서 내가 보내지 않은 많은 트랜잭션을 발견했습니다. 이러한 트랜잭션은 어디서 오는 것인가요?

[개인 API](https://web3js.readthedocs.io/en/1.0/web3-eth-personal.html)를 잠금 해제하는 것은 안전하지 않습니다. 봇 계정에서는 잠금 해제된 Ethereum 계정을 수신 대기하고 자금을 고갈시키려고 시도합니다. 봇은 이러한 계정에 실제 Ether가 있다고 가정하고 잔액을 가장 먼저 빼돌리려고 시도합니다. 개인 API는 네트워크에서 사용하도록 설정하지 않습니다. 대신, '프로그래밍 방식으로 스마트 계약과 상호 작용' 섹션에서 설명한 대로 MetaMask와 같은 지갑을 수동으로 사용하거나 프로그래밍 방식으로 트랜잭션을 미리 서명합니다.

### <a name="how-to-ssh-onto-a-vm"></a>VM에 SSH를 연결하려면 어떻게 할까요?

SSH 포트는 보안상의 이유로 잠겨 있습니다. [이 가이드](#_Accessing_Nodes_via)에 따라 SSH 포트를 사용하도록 설정합니다.

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>멤버 또는 트랜잭션 노드를 설정하고 감사하려면 어떻게 할까요?

트랜잭션 노드는 네트워크와 피어링되지만 합의에는 참여하지 않는 일단의 패리티 클라이언트입니다. 이러한 노드는 여전히 Ethereum 트랜잭션을 제출하고 스마트 계약 상태를 읽는 데 사용할 수 있습니다.
이는 네트워크의 규제자에게 감사 가능성을 제공하는 메커니즘으로 잘 작동합니다. 이렇게 하려면 '컨소시엄 확장 2단계'를 수행하기만 하면 됩니다.

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>MetaMask 트랜잭션이 오래 걸리는 이유는 무엇인가요?

트랜잭션이 올바른 순서로 수신되도록 하기 위해 각 Ethereum 트랜잭션에는 증분 논스(nonce)가 제공됩니다. 다른 네트워크에 있는 MetaMask의 계정을 사용한 경우 논스 값을 다시 설정해야 합니다. 설정 아이콘(3개 막대), 설정, [계정], [계정 원래 대로]를 차례로 클릭합니다. 트랜잭션 기록이 지워지고, 이제 트랜잭션을 다시 제출할 수 있습니다.

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>공용 IP 배포는 사설망 배포와 호환되나요?

아니요, 피어링하려면 양방향 통신이 필요하므로 전체 네트워크를 공용 또는 사설로 설정해야 합니다.

## <a name="next-steps"></a>다음 단계

[Ethereum 인증 증명 컨소시엄](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) 솔루션을 사용하여 시작합니다.
