---
title: Ethereum 인증 증명 컨소시엄 - Azure
description: Etherereum 인증 증명 컨소시엄 솔루션을 사용하여 다중 멤버 컨소시엄 Ethereum 네트워크 배포 및 구성
services: azure-blockchain
keywords: ''
author: CodyBorn
ms.author: coborn
ms.date: 04/08/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: vamelech
ms.openlocfilehash: 3531b43e6aee1eedef811e81e192873c5b5ed561
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66126802"
---
# <a name="ethereum-proof-of-authority-consortium"></a>Ethereum 인증 증명 컨소시엄

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>개요
[이 솔루션](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium)은 최소한의 Azure 및 Ethereum 지식으로 다중 멤버 컨소시엄 인증 증명 Ethereum 네트워크를 더 쉽게 배포, 구성 및 관리할 수 있도록 설계되었습니다.

Azure Portal을 통해 약간의 사용자 입력과 한 번의 클릭으로 배포하면 각 멤버가 글로벌의 Microsoft Azure Compute, 네트워킹 및 스토리지 서비스를 사용하여 네트워크 공간을 프로비전할 수 있습니다. 각 멤버의 네트워크 공간은 애플리케이션 또는 사용자가 Ethereum 트랜잭션을 제출하기 위해 상호 작용할 수 있는 일단의 부하 분산된 유효성 검사기 노드로 구성됩니다.

## <a name="concepts"></a>개념

### <a name="terminology"></a>용어

-   **합의** - 블록 유효성 검사 및 만들기를 통해 분산 네트워크에서 데이터를 동기화하는 행위입니다.

-   **컨소시엄 멤버** - 블록체인 네트워크에 대한 합의에 참여하는 엔터티입니다.

-   **관리자** - 지정된 컨소시엄 멤버의 참여를 관리하는 데 사용되는 Ethereum 계정입니다.

-   **유효성 검사기** - 관리자를 대신하여 합의에 참여하는 Ethereum 계정과 관련된 머신입니다.

### <a name="proof-of-authority"></a>인증 증명

블록체인 커뮤니티에 처음 들어온 경우, 이 솔루션의 릴리스는 Azure에서 이 기술을 쉽고 구성 가능한 방법으로 배울 좋은 기회입니다. 작업 증명은 계산 비용을 활용하여 네트워크를 자체적으로 규제하고 공정한 참여를 허용하는 Sybil 차단 메커니즘입니다. 이는 암호 화폐 경쟁이 네트워크의 보안을 촉진하는 익명의 개방형 블록체인 네트워크에서 효과적으로 작동합니다. 그러나 프라이빗/컨소시엄 네트워크에서 기본 Ether는 가치가 없습니다. 기관 증명 대체 프로토콜을 더 모든 합의 참가자가 알려져 있는 경우 허용 된 네트워크에 적합 하 고 신뢰할 수 있는 경우 채굴할 필요가 없으면 비잔틴 내결함성을 유지하면서도 인증 증명이 더 효율적입니다.

### <a name="consortium-governance"></a>컨소시엄 거버넌스

증명 권한 네트워크를 정상으로 유지 하는 네트워크 인증 기관 허용된 목록에 의존 합니다 이므로이 사용 권한 목록 수정 공정 메커니즘을 제공 하는 것이 중요 합니다. 각 배포는 스마트 계약 및이 허용 목록의 온 체인 거 버 넌 스에 대 한 포털 집합이 함께 제공 됩니다. 제안된 변경이 컨소시엄 멤버의 과반수 표결에 도달하면 해당 변경이 시행됩니다. 이렇게 하면 정직한 네트워크를 장려하는 투명한 방식으로 새 합의 참가자가 추가되거나 타협한 참가자가 제거될 수 있습니다.

### <a name="admin-account"></a>관리자 계정

개념 증명의 인증 기관 노드를 배포 하는 동안 관리자 Ethereum 주소 라는 나타납니다. 이 Ethereum 계정을 생성하고 보호하기 위해 여러 가지 메커니즘을 사용할 수 있습니다. 이 주소가 네트워크의 기관으로 추가되면 이 계정을 사용하여 거버넌스에 참여할 수 있습니다. 또한 이 관리자 계정은 이 배포의 일부로 만들어진 유효성 검사기 노드에 합의 참여를 위임하는 데 사용됩니다. 공용 Ethereum 주소만 사용 되므로 각 관리자가 원하는 보안 모델을 따르는 방식으로 해당 개인 키를 보호 하는 유연성을 있습니다.

### <a name="validator-node"></a>유효성 검사기 노드

인증 증명 프로토콜에서 유효성 검사기 노드는 기존의 채굴기 노드를 대신합니다. 각 유효성 검사기에는 스마트 계약 권한 목록에 추가되는 고유한 Ethereum ID가 있습니다. 유효성 검사기가 이 목록에 있으면 블록 만들기 프로세스에 참여할 수 있습니다. 이 프로세스에 대한 자세한 내용은 [Authority Round 합의](https://wiki.parity.io/Aura)에 대한 Parity의 설명서를 참조하세요. 각 컨소시엄 멤버는 지리적 중복을 위해 5개 지역에 걸쳐 둘 이상의 유효성 검사기 노드를 프로비전할 수 있습니다. 유효성 검사기 노드는 다른 유효성 검사기 노드와 통신하여 기본 분산 원장의 상태에 관한 합의에 도달합니다.
네트워크에서 공정한 참여를 보장하기 위해 각 컨소시엄 멤버는 네트워크의 첫 번째 멤버보다 더 많은 유효성 검사기를 사용하지 못하도록 금지됩니다. 첫 번째 멤버가 세 개의 유효성 검사기를 배포하는 경우 각 멤버는 유효성 검사기를 최대 세 개까지만 가질 수 있습니다.

### <a name="identity-store"></a>ID 저장소

각 멤버는 동시에 실행 하는 여러 유효성 검사기 노드의 있고 각 노드의 id를 사용할 수 있어야 합니다., 이므로 중요 한 유효성 검사기는 네트워크에서 고유한 활성 id를 얻을 수 있습니다. 쉽게를 안전 하 게 생성 된 Ethereum id를 포함 하는 각 멤버의 구독에 배포 되는 Id 저장소를 빌드 했습니다. 배포 시 오케스트레이션 컨테이너 각 유효성 검사기에 대 한 Ethereum 개인 키를 생성 하 고 Azure Key Vault에 저장 됩니다. 패리티 노드가 시작되기 전에 먼저 사용되지 않은 ID에 대한 임대를 획득하여 ID가 다른 노드에서 선택되지 않았는지 확인합니다. ID는 블록을 만들기 시작하는 권한을 부여하는 클라이언트에 제공됩니다. 호스팅 VM이 중단되면 ID 임대가 해제되어 나중에 대체 노드에서 해당 ID를 다시 시작할 수 있습니다.

### <a name="bootnode-registrar"></a>부트노드 등록자

용이하게 연결하기 위해 각 멤버는 [데이터 API 엔드포인트](#data-api)에서 일단의 연결 정보를 호스팅합니다. 이 데이터는 bootnodes 조인 멤버에 대 한 피어 링 노드로 제공 되는 목록을 포함 합니다. 이 데이터 API의 일환으로 이 부트노드 목록을 최신 상태로 유지합니다

### <a name="bring-your-own-operator"></a>사용자 고유 연산자 가져오기

컨소시엄 멤버는 종종 네트워크 관리에 참여하려고 하지만 인프라를 운영 및 유지 관리하지 않으려고 합니다. 기존 시스템과는 달리 네트워크 전체에 걸쳐 단일 운영자를 배치하면 분산 모델의 블록체인 시스템이 작동합니다. 네트워크를 운영하기 위해 중앙 집중식 중개자를 고용하는 대신, 각 컨소시엄 멤버는 자신이 선택한 운영자에게 인프라 관리를 위임할 수 있습니다. 이렇게 하면 각 멤버 자체 인프라를 작동 하거나 다른 파트너에 게 작업을 위임 하도록 선택할 수는 있는 하이브리드 모델을 수 있습니다. 위임된 작업 흐름은 다음과 같이 작동합니다.

1.  **컨소시엄 멤버**가 Ethereum 주소를 생성합니다(개인 키 보유).

2.  **컨소시엄 멤버**가 **운영자**에게 공개 Ethereum 주소를 제공합니다.

3.  **운영자**는 Azure Resource Manager 솔루션을 사용하여 PoA 유효성 검사기 노드를 배포하고 구성합니다

4.  **운영자**가 **컨소시엄 멤버**에게 RPC 및 관리 엔드포인트를 제공합니다.

5.  **컨소시엄 멤버**가 개인 키를 사용하여 **운영자**가 자신을 대신하여 참여하도록 배포한 유효성 검사기 노드를 수락하는 요청에 서명합니다.

### <a name="azure-monitor"></a>Azure Monitor

이 솔루션은 노드 및 네트워크 통계를 추적하기 위해 Azure Monitor도 제공합니다. 애플리케이션 개발자를 위해 기본 블록체인에 대한 가시성을 제공하여 블록 생성 통계를 추적합니다. 네트워크 운영자는 Azure Monitor를 사용하여 인프라 통계 및 쿼리 가능한 로그를 통해 네트워크 중단을 빠르게 감지하고 방지할 수 있습니다. 자세한 내용은 [모니터링 서비스](#service-monitoring)합니다.

### <a name="deployment-architecture"></a>배포 아키텍처

#### <a name="description"></a>설명

이 솔루션은 단일 또는 다중 지역 기반 다중 멤버 Ethereum 컨소시엄 네트워크를 배포할 수 있습니다. 기본적으로 RPC 및 피어링 엔드포인트는 공용 IP를 통해 액세스할 수 있으므로 구독 및 클라우드 간 연결을 간소화할 수 있습니다. 애플리케이션 수준 액세스 제어에 [Parity의 권한 부여 계약](https://wiki.parity.io/Permissioning)을 활용하는 것이 좋습니다. 또한 구독 간 연결에 VNet 게이트웨이를 활용하는 VPN 뒤에 배포되는 네트워크도 지원합니다. 이러한 배포는 더 복잡하므로 먼저 공용 IP 모델부터 시작하는 것이 좋습니다.

#### <a name="consortium-member-overview"></a>컨소시엄 멤버 개요

각 컨소시엄 멤버 배포에는 다음이 포함됩니다.

-   PoA 유효성 검사기 실행용 가상 머신

-   RPC, 피어링 및 거버넌스 DApp 요청을 배포하기 위한 Azure Load Balancer

-   유효성 검사기 ID 보안을 위한 Azure Key Vault

-   지속적인 네트워크 정보 호스팅 및 임대 조정을 위한 Azure Storage

-   로그 및 성능 통계를 집계하기 위한 Azure Monitor

-   프라이빗 VNet 간 VPN 연결을 허용하기 위한 VNet 게이트웨이(선택 사항)

![배포 아키텍처](./media/ethereum-poa-deployment/deployment-architecture.png)

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

## <a name="how-to-guides"></a>방법 가이드
### <a name="governance-dapp"></a>거버넌스 DApp

인증 증명의 중심에는 분산 거버넌스가 있습니다. 거버넌스 DApp는 네트워크에서 기관을 관리하는 데 사용되는 미리 배포된 [스마트 계약](https://github.com/Azure-Samples/blockchain/tree/master/ethereum-on-azure/) 집합 및 웹 애플리케이션입니다.
기관은 관리자 ID 및 유효성 검사기 노드로 분할됩니다.
관리자는 일단의 검사기 노드에 합의 참여를 위임할 수 있습니다. 관리자는 네트워크 내부 또는 외부에서 다른 관리자에게 투표할 수도 있습니다.

![거버넌스 DApp](./media/ethereum-poa-deployment/governance-dapp.png)

-   **분산 거버넌스 -**  네트워크 기관의 변경은 선택한 관리자의 온체인 투표를 통해 관리됩니다.

-   **유효성 검사기 위임 -**  기관은 각 PoA 배포에서 설정된 유효성 검사기 노드를 관리할 수 있습니다.

-   **감사 가능한 변경 기록 -**  각 변경은 투명성과 감사 가능성을 제공하는 블록체인에 기록됩니다.

#### <a name="getting-started-with-governance"></a>거버넌스 시작
거 버 넌 스 DApp 통해 트랜잭션의 모든 종류를 수행 하려면 Ethereum wallet을 활용 해야 합니다.  가장 간단한 방법은 [MetaMask](https://metamask.io) 등의 브라우저 내 전자지갑을 사용하는 것입니다. 하지만 거버넌스 DApp는 네트워크에서 배포되는 스마트 계약이므로 거버넌스 계약에 대한 상호 작용을 자동화할 수도 있습니다.

MetaMask를 설치한 후 브라우저에서 거버넌스 DApp로 이동합니다.  URL은 배포 확인 이메일에 나와 있으며 Azure Portal을 통해 배포 출력에서도 확인할 수 있습니다.  설치 하는 브라우저에서 전자 지갑 필요가 없습니다; 작업을 수행할 수 있습니다 그러나 여전히 읽을 수 있습니다 관리자 상태.  

#### <a name="becoming-an-admin"></a>관리자 되기
네트워크에 배포 하는 첫 번째 구성원 인 경우 관리자를 자동으로 데이터베이스가에서는 다음 및 패리티 노드에 유효성 검사기로 나열 됩니다.  가져오기 관리자로 서 투표 하는 사람 (50% 보다 큼) 대부분 해야 네트워크를 조인 하는 경우 기존 관리자 집합입니다.  관리자가 되지 않더라도 노드는 계속 동기화되며 블록체인 유효성을 검사하지만 블록 작성 프로세스에 참가하지는 않습니다. 관리자가 되기 위한 투표 프로세스를 시작하려면 __지정__을 클릭하고 Ethereum 주소와 별칭을 입력합니다.

![지정](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

#### <a name="candidates"></a>후보
__후보__ 탭을 선택하면 현재 관리자 후보 집합이 표시됩니다.  현재 관리자 중 과반수의 표를 얻는 후보가 관리자로 승격됩니다.  특정 후보에게 투표를 하려면 해당 행을 선택하고 위쪽의 “투표하기”를 클릭합니다.  다른 후보에게 투표하려는 경우 후보를 선택하고 “투표 취소”를 클릭합니다.

![후보](./media/ethereum-poa-deployment/governance-dapp-candidates.png)


#### <a name="admins"></a>관리자
현재 관리자 집합이 표시되는 __관리자__ 탭에서 투표를 할 수 있습니다.  자세히 관리자로 손실 되 면 50% 지원 보다 제거할 예정 네트워크 관리자로 합니다.  그러면 해당 관리자가 소유한 유효성 검사기 노드의 유효성 검사기 상태가 해제되어 네트워크의 트랜잭션 노드로 설정됩니다.  관리자는 다양 한 이유로;에 대 한 제거 될 수 있습니다. 그러나에 달려 컨소시엄 미리 정책에 동의 합니다.

![관리자](./media/ethereum-poa-deployment/governance-dapp-admins.png)

#### <a name="validators"></a>유효성 검사기
왼쪽 메뉴에서 __유효성 검사기__ 탭을 선택하면 이 인스턴스에 대해 현재 배포된 패리티 노드와 해당 노드의 현재 상태(노드 형식)가 표시됩니다.  각 컨소시엄 멤버 했으므로 다른 유효성 검사기 집합을이 목록에이 뷰는 현재 배포 된 컨소시엄 멤버를 나타냅니다.  새로 배포 된 인스턴스인지에 유효성 검사기를 아직 추가 하지 않은 경우 표시 됩니다 옵션 ' 유효성 검사기 추가 '를 합니다.  이 선택 하면 자동으로 패리티 노드의 지역적으로 분산 된 집합을 선택 되며 유효성 검사기 집합에 할당 합니다.  허용된 용량보다 많은 노드를 배포한 경우 나머지 노드는 네트워크의 트랜잭션 노드가 됩니다.

각 유효성 검사기의 주소는 Azure의 [ID 저장소](#identity-store)를 통해 자동으로 할당됩니다.  노드가 다운되면 해당 ID가 해제되므로 배포의 다른 노드가 해당 ID를 사용할 수 있습니다.  그러므로 항상 합의 과정에 참가할 수 있습니다.

![유효성 검사기](./media/ethereum-poa-deployment/governance-dapp-validators.png)

#### <a name="consortium-name"></a>컨소시엄 이름
어떤 관리자든 페이지 맨 위에 표시되는 컨소시엄 이름을 업데이트할 수 있습니다.  왼쪽 위의 기어 아이콘을 선택하여 컨소시엄 이름을 업데이트합니다.

#### <a name="account-menu"></a>계정 메뉴
오른쪽 위에는 Ethereum 계정 별칭과 ID 아이콘이 표시됩니다.  관리자 인 경우에 사용자의 별칭을 업데이트 하는 기능을 해야 합니다.

![계좌](./media/ethereum-poa-deployment/governance-dapp-account.png)

### <a name="deploy-ethereum-proof-of-authority"></a>Ethereum 인증 증명 배포

다자간 배포 흐름의 예는 다음과 같습니다.

1.  세 명의 멤버가 각각 MetaMask를 사용하여 Ethereum 계정을 생성합니다.

2.  *멤버 A*가 Ethereum PoA를 배포하여 해당 Ethereum 공용 주소를 제공합니다.

3.  *멤버 A*가 *멤버 B* 및 *멤버 C*에게 컨소시엄 URL을 제공합니다.

4.  *멤버 B* 및 *멤버 C*가 Ethereum PoA를 배포하여 해당 Ethereum 공용 주소 및 *멤버 A*의 컨소시엄 URL을 제공합니다.

5.  *멤버 A*가 *멤버 B*에게 관리자로 투표합니다.

6.  *멤버 A* 및 *멤버 B*가 모두 *멤버 C*에게 관리자로 투표합니다.

이 프로세스를 실행하려면 여러 가상 머신 및 관리 디스크의 배포를 지원할 수 있는 Azure 구독이 필요합니다. 필요한 경우 [체험 Azure 계정을 만들어](https://azure.microsoft.com/free/) 시작합니다.

구독이 보호된 후 Azure Portal로 이동합니다. '+', Marketplace('모두 보기')를 선택하고 Ethereum PoA 컨소시엄을 검색합니다.

다음 섹션에서는 네트워크에서 첫 번째 멤버의 공간을 구성하는 과정을 안내합니다. 배포 흐름은 다음 5단계로 구분됩니다. 기본 사항, 배포 지역, 네트워크 크기 및 성능, Ethereum 설정, Azure Monitor.

#### <a name="basics"></a>기본 사항

**기본 사항** 아래에서 구독, 리소스 그룹 및 기본 가상 머신 속성과 같은 배포에 대한 표준 매개 변수의 값을 지정합니다.

각 매개 변수에 대한 자세한 설명은 다음과 같습니다.

매개 변수 이름|설명|허용되는 값|기본값
---|---|---|---
새 네트워크를 만드시겠습니까, 아니면 기존 네트워크를 조인하시겠습니까?|새 네트워크 만들기 또는 기존 컨소시엄 네트워크 조인|새로 만들기 기존 조인|새로 만들기
이메일 주소(선택 사항)|배포에 대한 정보를 사용하여 배포가 완료되면 이메일 알림을 받게 됩니다.|유효한 이메일 주소|해당 없음
VM 사용자 이름|각 배포된 VM의 관리자 사용자 이름(영숫자 문자만)|1-64자|해당 없음
인증 유형|가상 머신에 인증하는 메서드입니다.|암호 또는 SSH 공개 키|암호
암호(인증 형식 = 암호)|배포된 각 가상 머신의 관리자 계정 암호.  암호에는 다음 중 3가지가 반드시 포함되어야 합니다. 대문자 1개, 소문자 1개, 숫자 1개, 특수 문자 1개. 처음에는 모든 VM의 암호가 동일하지만, 프로비전 후 암호를 변경할 수 있습니다.|12-72자|해당 없음
SSH 키(인증 유형 = 공개 키)|원격 로그인에 사용하는 보안 셸 키.||해당 없음
구독|컨소시엄 네트워크를 배포하는 구독||해당 없음
리소스 그룹|컨소시엄 네트워크를 배포하는 리소스 그룹입니다.||해당 없음
위치|리소스 그룹에 대한 Azure 지역입니다.||해당 없음

샘플 배포는 다음과 같습니다. ![기본 사항 블레이드](./media/ethereum-poa-deployment/basic-blade.png)

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

샘플 배포는 다음과 같습니다. ![배포 지역](./media/ethereum-poa-deployment/deployment-regions.png)

#### <a name="network-size-and-performance"></a>네트워크 크기 및 성능

다음으로, '네트워크 크기 및 성능' 아래에서 컨소시엄 네트워크의 크기(예: 유효성 검사기 노드의 수와 크기)를 입력합니다.
유효성 검사기 노드 저장소 크기에 따라 블록체인의 잠재적 크기가 결정됩니다. 이는 배포 후에 변경할 수 있습니다.

각 매개 변수에 대한 자세한 설명은 다음과 같습니다.

  매개 변수 이름|설명|허용되는 값|기본값
  ---|---|---|---
  부하가 분산된 유효성 검사기 노드 수|네트워크의 일부로 프로비전하는 유효성 검사기 노드의 수입니다.|2-15|2
  유효성 검사기 노드 저장소 성능|배포한 유효성 검사기 노드 각각을 지원하는 관리 디스크의 유형입니다.|표준 SSD 및 프리미엄|표준 SSD
  유효성 검사기 노드 가상 머신 크기|유효성 검사기 노드에 사용되는 가상 머신 크기입니다.|표준 A, 표준 D, 표준 D-v2, 표준 F 시리즈, 표준 DS 및 표준 FS|표준 D1 v2

[저장소 가격 정보](https://azure.microsoft.com/pricing/details/managed-disks/)

[가상 머신 가격 정보](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)

가상 머신 및 저장소 계층에는 네트워크 성능은 떨어지게 됩니다.  원하는 비용 효율성에 따라 다음 SKU를 사용하는 것이 좋습니다.

  가상 머신 SKU|저장소 계층|가격|처리량|대기 시간
  ---|---|---|---|---
  F1|표준 SSD|낮음|낮음|높음
  D2_v3|표준 SSD|중간|중간|중간
  F16s|프리미엄 SSD|높음|높음|낮음

샘플 배포는 다음과 같습니다: ![네트워크 크기 및 성능](./media/ethereum-poa-deployment/network-size-and-performance.png)

#### <a name="ethereum-settings"></a>Ethereum 설정

다음으로, [Ethereum 설정] 아래에서 Ethereum 관련 구성 설정(예: 네트워크 ID 및 Ethereum 계정 암호 또는 제네시스 블록)을 지정합니다.

각 매개 변수에 대한 자세한 설명은 다음과 같습니다.

  매개 변수 이름|설명|허용되는 값|기본값
  ---|---|---|---
컨소시엄 멤버 ID|충돌을 방지하기 위해 IP 주소 공간을 구성하는 데 사용하는 컨소시엄 네트워크에 참가하는 각 멤버와 연결된 ID입니다. 사설망의 경우 멤버 ID는 동일한 네트워크의 여러 조직에서 고유해야 합니다.  고유 멤버 ID는 같은 조직이 여러 지역에 배포하는 경우에도 필요합니다. 충돌이 없습니다 있도록 조인 다른 멤버와 공유 해야 하므로이 매개 변수의 값을 확인 합니다.|0-255|해당 없음
네트워크 ID|배포하는 컨소시엄 Ethereum 네트워크의 네트워크 ID입니다.  각 Ethereum 네트워크에는 공용 네트워크에 대한 ID를 1로 하는 자체의 네트워크 ID가 있습니다.|5 ~ 999,999,999|10101010
관리자 Ethereum 주소|PoA 거버넌스 참여에 사용되는 Ethereum 계정 주소입니다.  Ethereum 주소를 생성하는 경우 MetaMask를 사용하는 것이 좋습니다.|0x로 시작하는 42자의 영숫자 문자|해당 없음
고급 옵션|Ethereum 설정에 대한 고급 옵션|사용 또는 사용 안 함|사용 안 함
공용 IP(고급 옵션 = 사용)|VNet 게이트웨이 뒤에 네트워크를 배포하고 피어링 액세스를 제거합니다. 이 옵션을 선택하면 모든 멤버가 VNet 게이트웨이를 사용해야 연결이 호환됩니다.|공용 IP 프라이빗 VNet|공용 IP
블록 가스 제한(고급 옵션 = 사용)|네트워크의 시작 블록 가스 제한입니다.|임의의 숫자|50000000
블록 다시 봉인 기간(초)|네트워크에 트랜잭션이 없을 때 빈 블록이 작성되는 빈도입니다. 빈도가 높으면 블록이 더 빨리 작성되지만 저장소 비용이 증가합니다.|임의의 숫자|15
트랜잭션 권한 계약(고급 옵션 = 사용)|트랜잭션 권한 지정 계약에 대한 바이트 코드입니다. 스마트 계약 배포 및 Ethereum 계정의 허용된 목록에 실행을 제한합니다.|계약 바이트 코드|해당 없음

샘플 배포는 다음과 같습니다: ![Ethereum 설정](./media/ethereum-poa-deployment/ethereum-settings.png)

#### <a name="monitoring"></a>모니터링

모니터링 블레이드를 사용 하면 네트워크에 대 한 Azure Monitor 로그 리소스를 구성할 수 있습니다. 모니터링 에이전트는 네트워크에서 유용한 메트릭과 로그를 수집하고 표시하여 네트워크 상태 또는 디버그 문제를 빠르게 확인하는 기능을 제공합니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

  매개 변수 이름|설명|허용되는 값|기본값
  ---|---|---|---
모니터링|모니터링을 사용하도록 설정하는 옵션|사용 또는 사용 안 함|사용
기존 Azure Monitor 로그에 연결|새 Azure Monitor 로그 인스턴스를 만들거나 기존 인스턴스를 조인 합니다.|새로 만들기 또는 기존 조인|새로 만들기
위치 모니터링 (기존 Azure Monitor 로그에 연결 = 새로 만들기)|새 Azure Monitor에서 인스턴스를 기록 하는 위치 영역을 배치할|모든 Azure Monitor 로그 지역|해당 없음
기존 log analytics 작업 영역 ID (기존 Azure Monitor 로그 연결할 기존 조인 =)|인스턴스를 기록 하는 기존 Azure Monitor의 작업 영역 ID||해당 없음
기존 log analytics에 대 한 키 정보 (기존 Azure Monitor 로그 연결할 기존 조인 =)|기존 Azure Monitor 로그 인스턴스에 연결 하는 데 기본 키||해당 없음


샘플 배포는 다음과 같습니다. ![Azure Monitor](./media/ethereum-poa-deployment/azure-monitor.png)

#### <a name="summary"></a>요약

요약 블레이드를 클릭하여 지정한 입력을 검토하고 기본 배포 전 유효성 검사를 실행합니다. 배포하기 전에 템플릿과 매개 변수를 다운로드할 수 있습니다.

법률 정보 및 개인정보처리방침을 검토하고 '구매'를 클릭하여 배포합니다. 배포에 VNet 게이트웨이가 포함된 경우 배포하는 데 45-50분이 걸립니다.

#### <a name="post-deployment"></a>배포 후

##### <a name="deployment-output"></a>배포 출력

배포가 완료 되 면 필요한 매개 변수 확인 전자 메일을 통해 또는 Azure portal을 통해 액세스할 수 있습니다. 이러한 매개 변수에서 확인할 수 있는 항목은 다음과 같습니다.

-   Ethereum RPC 엔드포인트

-   거버넌스 대시보드 URL

-   Azure Monitor URL

-   데이터 URL

-   VNet 게이트웨이 리소스 ID(선택 사항)

##### <a name="confirmation-email"></a>확인 이메일

이메일 주소([기본 사항 섹션](#basics))를 제공하면 배포 출력 정보가 포함된 이메일 주소로 이메일이 보내집니다.

![배포 이메일](./media/ethereum-poa-deployment/deployment-email.png)

##### <a name="portal"></a>포털

배포가 성공적으로 완료 된 후 모든 리소스 프로 비전 된 리소스 그룹에서 출력 매개 변수를 볼 수 있습니다.

1.  포털에서 리소스 그룹을 찾습니다.

2.  *배포*로 이동합니다.

3.  리소스 그룹과 이름이 동일한 최상위 배포를 선택합니다.

4.  *출력*을 선택합니다.

### <a name="growing-the-consortium"></a>컨소시엄 확장

컨소시엄을 확장하려면 먼저 실제 네트워크를 연결해야 합니다.
공용 IP 기반 배포를 사용하면 이 첫 번째 단계가 원활하게 수행됩니다. VPN 뒤에 배포 하는 경우 섹션을 참조 [VNet 게이트웨이에 연결](#connecting-vnet-gateways) 새 멤버 배포의 일환으로 네트워크 연결을 수행 합니다.  배포가 완료되면 [거버넌스 DApp](#governance-dapp)를 사용하여 자신을 네트워크 관리자로 설정합니다.

#### <a name="new-member-deployment"></a>새 멤버 배포

1.  다음 정보를 조인 멤버와 공유합니다. 이 정보는 배포 후 이메일 또는 포털 배포 출력에서 찾을 수 있습니다.

    -  컨소시엄 데이터 URL

    -  배포한 노드 수

    -  VNet 게이트웨이 리소스 ID(VPN을 사용하는 경우)

2.  배포 멤버는 네트워크 참석을 배포할 때 다음 사항에 유의하여 [동일한 솔루션](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium)을 사용해야 합니다.

    -  *기존 조인*을 선택합니다.

    -  공정한 표현을 보장하기 위해 네트워크상의 나머지 멤버와 동일한 유효성 검사기 노드 수를 선택합니다

    -  이전 단계에서 제공한 것과 동일한 Ethereum 주소를 사용합니다.

    -  *Ethereum 설정* 탭에서 제공한 *컨소시엄 데이터 URL*을 전달합니다.

    -  네트워크의 나머지 부분이 VPN 뒤에 있는 경우 고급 섹션 아래에서 *프라이빗 VNet*을 선택합니다.

#### <a name="connecting-vnet-gateways"></a>VNet 게이트웨이 연결

기본 공용 IP 설정을 사용하여 배포한 경우 이 단계를 무시해도 됩니다. 사설망의 경우 다른 멤버는 VNet 게이트웨이 연결을 통해 연결됩니다. 멤버는 네트워크에 연결 하 고 트랜잭션 트래픽을 볼 수 있습니다, 기존 멤버는 연결을 허용 하는 VPN gateway에서 최종 구성을 수행 해야 합니다. 즉,는 조인 멤버 Ethereum 노드의 연결 될 때까지 실행 되지 않습니다. 이 단일 실패 지점이의 가능성을 줄이려면 컨소시엄에 중복 네트워크 연결 (메시)을 만드는 것이 좋습니다.

새 멤버가 배포되면 기존 멤버는 새 멤버에 대한 VNet 게이트웨이 연결을 설정하여 양방향 연결을 완료해야 합니다. 이를 위해 기존 멤버에 필요한 항목은 다음과 같습니다.

1.  연결 멤버의 VNet 게이트웨이 ResourceID(배포 출력 참조)

2.  공유 연결 키

기존 멤버는 다음 PowerShell 스크립트를 실행하여 연결을 완료해야 합니다. 포털의 오른쪽 위 탐색 모음에 있는 Azure Cloud Shell을 사용 하는 것이 좋습니다.

![Cloud Shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
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
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

### <a name="service-monitoring"></a>서비스 모니터링

배포 이메일의 링크를 따르거나 \[OMS\_PORTAL\_URL\] 배포 출력에서 매개 변수를 찾아서 Azure Monitor 포털을 찾을 수 있습니다.

포털에서 먼저 고급 네트워크 통계와 노드 개요를 표시합니다.

![모니터링 범주](./media/ethereum-poa-deployment/monitor-categories.png)

**노드 개요**를 선택하면 노드별 인프라 통계를 볼 수 있는 포털로 이동합니다.

![노드 통계](./media/ethereum-poa-deployment/node-stats.png)

**네트워크 통계**를 선택하면 Ethereum 네트워크 통계를 볼 수 있습니다.

![네트워크 통계](./media/ethereum-poa-deployment/network-stats.png)

#### <a name="sample-kusto-queries"></a>Kusto 쿼리 예제

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

보안상의 이유로 SSH 포트 액세스는 기본적으로 네트워크 그룹 보안 규칙에 의해 거부됩니다. 이 규칙을 변경 해야 PoA 네트워크의 가상 머신 인스턴스에 액세스 하려면 \"허용\"

1.  Azure Portal에서 배포된 리소스 그룹의 [개요] 섹션에서 시작합니다.

    ![SSH 개요](./media/ethereum-poa-deployment/ssh-overview.png)

2.  액세스하려는 VM의 지역에 해당하는 네트워크 보안 그룹을 선택합니다.

    ![SSH NSG](./media/ethereum-poa-deployment/ssh-nsg.png)

3.  \"allow-ssh\" 규칙을 선택합니다.

    ![allow-ssh](./media/ethereum-poa-deployment/ssh-allow.png)

4.  \"작업\"을 [허용]으로 변경합니다.

    ![SSH 사용 허용](./media/ethereum-poa-deployment/ssh-enable-allow.png)

5.  \"저장\"을 클릭합니다(변경 내용을 적용하는 데 몇 분이 걸릴 수 있음).

이제 제공된 관리자 사용자 이름 및 암호/SSH 키를 사용하여 SSH를 통해 유효성 검사기 노드에 대한 가상 머신에 원격으로 연결할 수 있습니다.
첫 번째 유효성 검사기 노드에 액세스하기 위해 실행하는 SSH 명령은 템플릿 배포 출력 매개 변수에서 'SSH\_TO\_FIRST\_VL\_NODE\_REGION1'(샘플 배포의 경우: ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com)로 나열됩니다. 추가 트랜잭션 노드를 가져오려면 포트 번호를 1만큼 증가합니다(예를 들어 첫 번째 트랜잭션 노드는 포트 4000에 있음).

둘 이상의 지역에 배포한 경우 위의 명령을 해당 지역의 부하 분산 장치에 대한 DNS 이름 또는 IP 주소로 변경합니다. 다른 지역의 DNS 이름이나 IP 주소를 찾으려면 \*\*\*\*\*-lbpip-reg\# 명명 규칙을 사용하여 리소스를 찾아서 해당 DNS 이름과 IP 주소 속성을 확인합니다.

### <a name="azure-traffic-manager-load-balancing"></a>Azure Traffic Manager 부하 분산

Azure Traffic Manager는 들어오는 트래픽을 여러 지역의 여러 배포로 라우팅하여 가동 중지 시간을 줄이고 PoA 네트워크의 응답성을 향상시킬 수 있습니다. 기본 제공 상태 확인 및 자동 다시 라우팅은 RPC 엔드포인트와 거버넌스 DApp의 고가용성을 보장하는 데 도움이 됩니다. 이 기능은 여러 지역에 배포하고 프로덕션을 준비하는 경우에 유용합니다.

Traffic Manager를 사용하는 목적은 다음과 같습니다.

-   자동 장애 조치를 통해 PoA 네트워크 가용성을 향상시킵니다.

-   최종 사용자를 네트워크 대기 시간이 가장 짧은 Azure 위치로 라우팅하여 네트워크 응답성을 높입니다.

Traffic Manager 프로필을 만들려는 경우 네트워크에 액세스하기 위해 프로필의 DNS 이름을 사용할 수 있습니다. 다른 컨소시엄 멤버가 네트워크에 추가되면 Traffic Manager를 사용하여 배포된 유효성 검사기에서 부하를 분산시킬 수도 있습니다.

#### <a name="creating-a-traffic-manager-profile"></a>Traffic Manager 프로필 만들기

Azure Portal에서 \"리소스 만들기\" 단추를 클릭한 후 \"Traffic Manager 프로필\"을 검색하여 선택합니다.

![Azure Traffic Manager 검색](./media/ethereum-poa-deployment/traffic-manager-search.png)

프로필에 고유한 이름을 지정하고 PoA 배포 중에 만든 리소스 그룹을 선택합니다. 배포하려면 “만들기” 단추를 클릭합니다.

![Traffic Manager 만들기](./media/ethereum-poa-deployment/traffic-manager-create.png)

배포 되 면 리소스 그룹에서 인스턴스를 선택 합니다. Traffic Manager에 액세스하기 위한 DNS 이름은 [개요] 탭에서 확인할 수 있습니다.

![Traffic Manager DNS 찾기](./media/ethereum-poa-deployment/traffic-manager-dns.png)

[엔드포인트] 탭을 선택하고 [추가] 단추를 클릭합니다. 그런 다음 엔드포인트에 고유한 이름을 지정하고 대상 리소스 종류를 공용 IP 주소로 변경합니다. 그런 다음, 첫 번째 지역의 부하 분산 장치에 대한 공용 IP 주소를 선택\'합니다.

![Traffic Manager 라우팅](./media/ethereum-poa-deployment/traffic-manager-routing.png)

배포된 네트워크의 각 지역에 대해 반복합니다. 끝점 되 면 합니다 \"사용\" 상태를 자동으로 로드 될 수 및 영역 균형을 traffic manager의 DNS 이름. 이제 문서의 다른 단계에서 \[CONSORTIUM\_DATA\_URL\] 매개 변수를 대신하여 이 DNS 이름을 사용할 수 있습니다.

### <a name="data-api"></a>데이터 API

각 컨소시엄 멤버는 다른 멤버가 네트워크에 연결하는 데 필요한 정보를 호스팅합니다. 기존 멤버는 멤버의 배포 하기 전에 [CONSORTIUM_DATA_URL]를 제공 합니다. 배포 시 조인 멤버는 다음 엔드포인트의 JSON 인터페이스에서 정보를 검색합니다.

`<CONSORTIUM_DATA_URL>/networkinfo`

응답에는 유용한 정보와 멤버 (발생 블록, 유효성 검사기가 설정 계약 ABI bootnodes) 기존 멤버 (검사기 주소)에 조인 하는 데 유용한 정보가 포함 됩니다. 클라우드 공급자 간에 컨소시엄을 확장하려면 이 표준화를 사용하는 것이 좋습니다. 이 API는 다음과 같은 구조의 JSON 형식 응답을 반환합니다.
```json
{
  "$id": "",
  "type": "object",
  "definitions": {},
  "$schema": "https://json-schema.org/draft-07/schema#",
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
## <a name="tutorials"></a>자습서

### <a name="programmatically-interacting-with-a-smart-contract"></a>프로그래밍 방식으로 스마트 계약과 상호 작용

> [!WARNING]
> Ethereum 프라이빗 키는 네트워크를 통해 보내지 마세요! 각 트랜잭션을 로컬에서 먼저 서명한 다음, 네트워크를 통해 서명된 트랜잭션을 보내도록 합니다.

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

-   생성된 프라이빗 키를 사용하여 트랜잭션에 서명

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

### <a name="deploy-smart-contract-with-truffle"></a>Truffle을 사용하여 스마트 계약 배포

-   필요한 라이브러리 설치

```javascript
npm init

npm install truffle-hdwallet-provider --save
```
-   truffle.js에 다음 코드를 추가하여 MetaMask 계정을 잠금 해제하고 니모닉 구를 입력하여 PoA 노드를 진입점으로 구성합니다(MetaMask/설정/초기값 단어 표시).

```javascript
var HDWalletProvider = require("truffle-hdwallet-provider");

var rpc_endpoint = "XXXXXX";
var mnemonic = "twelve words you can find in metamask/settings/reveal seed words";

module.exports = {
  networks: {
    development: {
      host: "localhost",
      port: 8545,
      network_id: "*" // Match any network id
    },
    poa: {
      provider: new HDWalletProvider(mnemonic, rpc_endpoint),
      network_id: 3,
      gasPrice : 0
    }
  }
};

```

-   DoA 네트워크에 배포

```javascript
$ truffle migrate --network poa
```

### <a name="debug-smart-contract-with-truffle"></a>Truffle을 사용하여 스마트 계약 디버그

Truffle은 스마트 계약을 디버그하는 데 사용할 수 있는 로컬 개발 네트워크를 보유하고 있습니다. 전체 자습서는 [여기서](https://truffleframework.com/tutorials/debugging-a-smart-contract) 찾을 수 있습니다.

### <a name="webassembly-wasm-support"></a>WebAssembly(WASM) 지원

WebAssembly 지원은 새로 배포된 PoA 네트워크에서 이미 사용하도록 설정되어 있습니다. 이를 통해 스마트 계약을 웹 어셈블리로 변환 컴파일되는 모든 언어(Rust, C, C++)로 개발할 수 있습니다. 자세한 내용은 아래 링크를 참조하세요.

-   WebAssembly에 대한 Parity 개요 - <https://wiki.parity.io/WebAssembly-Home>

-   Parity Tech의 자습서 - <https://github.com/paritytech/pwasm-tutorial>

## <a name="reference"></a>참조

### <a name="faq"></a>FAQ

#### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>전송하지 않은 트랜잭션이 네트워크에\' 다수 표시됩니다. 이러한 트랜잭션은 어디서 오는 것인가요?

[개인 API](https://web3js.readthedocs.io/en/1.0/web3-eth-personal.html)를 잠금 해제하는 것은 안전하지 않습니다. 봇은 잠금 해제된 Ethereum 계정을 수신 대기하고 자금을 고갈시키려고 시도합니다. 봇은 이러한 계정에 실제 Ether가 있다고 가정하고 잔액을 가장 먼저 빼돌리려고 시도합니다. 개인 API는 네트워크에서 사용하도록 설정하지 않습니다. 대신 [프로그래밍 방식으로 스마트 계약과 상호 작용](#programmatically-interacting-with-a-smart-contract) 섹션에서 설명한 대로 MetaMask와 같은 전자지갑을 수동으로 사용하거나 프로그래밍 방식으로 트랜잭션을 미리 서명합니다.

#### <a name="how-to-ssh-onto-a-vm"></a>VM에 SSH를 연결하려면 어떻게 할까요?

SSH 포트는 보안상의 이유로 표시되지 않습니다. [이 가이드](#ssh-access)에 따라 SSH 포트를 사용하도록 설정합니다.

#### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>멤버 또는 트랜잭션 노드를 설정하고 감사하려면 어떻게 할까요?

트랜잭션 노드는 네트워크와 피어링되지만 합의에는 참여하지 않는 일단의 패리티 클라이언트입니다. 이러한 노드는 여전히 Ethereum 트랜잭션을 제출하고 스마트 계약 상태를 읽는 데 사용할 수 있습니다.
이러한 방식은 네트워크에서 권한이 없는 컨소시엄 멤버에게 감사 기능을 제공하는 메커니즘으로 활용하기에 적합합니다. 이렇게 하려면 '컨소시엄 확장 2단계'를 수행하기만 하면 됩니다.

#### <a name="why-are-metamask-transactions-taking-a-long-time"></a>MetaMask 트랜잭션이 오래 걸리는 이유는 무엇인가요?

트랜잭션이 올바른 순서로 수신되도록 하기 위해 각 Ethereum 트랜잭션에는 증분 논스(nonce)가 제공됩니다. 다른 네트워크에 있는 MetaMask의 계정을 사용한 경우 논스 값을 다시 설정해야 합니다. 설정 아이콘(3개 막대), 설정, [계정], [계정 원래 대로]를 차례로 클릭합니다. 트랜잭션 기록이 지워지고, 이제 트랜잭션을 다시 제출할 수 있습니다.

#### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>MetaMask에서 가스 요금을 지정해야 하나요?

인증 증명 컨소시엄에서는 Ethereum이 사용되지 않습니다. 따라서 MetaMask에서 트랜잭션을 제출할 때 가스 요금을 지정할 필요가 없습니다.

#### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Azure OMS로의 프로비전 실패로 인해 배포가 실패하면 어떻게 해야 하나요?

모니터링은 선택적 기능입니다. 드물지만 Azure Monitor 리소스를 정상적으로 프로비전할 수 없어 배포가 실패할 수도 있는데, 이 경우에는 Azure Monitor를 사용하지 않고 다시 배포할 수 있습니다.

#### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>공용 IP 배포는 사설망 배포와 호환되나요?

아니요, 피어링하려면 양방향 통신이 필요하므로 전체 네트워크를 공용 또는 프라이빗으로 설정해야 합니다.

#### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>인증 증명의 예상 트랜잭션 처리량은 어떻게 되나요?

트랜잭션 처리량은 네트워크 토폴로지 및 트랜잭션 유형에 따라 크게 달라집니다.  단순 트랜잭션을 사용한 벤치마크 테스트 결과 여러 지역에 걸쳐 배포된 네트워크에서 초당 평균 트랜잭션 수는 400개였습니다.

#### <a name="how-do-i-subscribe-to-smart-contract-events"></a>스마트 계약 이벤트를 구독하려면 어떻게 해야 하나요?

Ethereum 인증 증명에서는 이제 웹 소켓을 지원합니다.  배포 전자 메일 또는 배포 출력을 확인하면 웹 소켓 URL 및 포트를 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Ethereum 인증 증명 컨소시엄](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) 솔루션을 사용하여 시작합니다.
