---
title: Azure에서 Ethereum Proof-of-Authority 컨소시엄 솔루션 템플릿 배포
description: Etherereum Proof-of-Authority 컨소시엄 솔루션을 사용하여 Azure에서 다중 멤버 컨소시엄 Ethereum 네트워크 배포 및 구성
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3, devx-track-azurepowershell
ms.openlocfilehash: 58615f1d57c5b97da555e894bcc33dcf98dee204
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110705237"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Azure에서 Ethereum Proof-of-Authority 컨소시엄 솔루션 템플릿 배포

[Ethereum Proof-of-Authority 컨소시엄 미리 보기 Azure 솔루션 템플릿](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum)을 사용하여 최소한의 Azure 및 Ethereum 지식으로 다중 멤버 컨소시엄 Proof-of-Authority Ethereum 네트워크를 배포, 구성 및 관리합니다.

솔루션 템플릿은 각 컨소시엄 멤버가 Microsoft Azure 컴퓨팅, 네트워킹 및 스토리지 서비스를 사용하여 블록 체인 네트워크 공간을 프로비전하는 데 사용할 수 있습니다. 각 컨소시엄 멤버의 네트워크 공간은 애플리케이션 또는 사용자가 Ethereum 트랜잭션을 제출하기 위해 상호 작용할 수 있는 일단의 부하 분산된 유효성 검사기 노드로 구성됩니다.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Azure Blockchain 솔루션 선택

Ethereum Proof-of-Authority 컨소시엄 솔루션 템플릿을 사용하도록 선택하기 전에 시나리오를 사용 가능한 Azure Blockchain 옵션의 일반적인 사용 사례와 비교해보세요.

> [!IMPORTANT]
> Azure 솔루션 템플릿의 Ethereum 대신 [Azure Blockchain Service](../service/overview.md)를 사용하는 것이 좋습니다. Azure Blockchain 서비스는 지원되는 관리형 Azure 서비스입니다. 패리티 Ethereum은 커뮤니티 중심의 개발 및 유지 관리로 전환되었습니다. 자세한 내용은 [패리티 Ethereum에서 OpenEthereum DAO로 전환](https://www.parity.io/parity-ethereum-openethereum-dao/)을 참조하세요.

옵션 | 서비스 모델 | 일반적인 사용 사례
-------|---------------|-----------------
솔루션 템플릿 | IaaS | 솔루션 템플릿은 완전히 구성된 블록체인 네트워크 토폴로지를 준비하는 데 사용할 수 있는 Azure Resource Manager 템플릿입니다. 이 템플릿은 지정된 블록체인 네트워크 유형에 대해 Microsoft Azure 컴퓨팅, 네트워킹 및 저장소 서비스를 배포하고 구성합니다. 솔루션 템플릿은 서비스 수준 계약 없이 제공됩니다. 지원을 받으려면 [Microsoft Q&A 질문 페이지](/answers/topics/azure-blockchain-workbench.html)를 사용하세요.
[Azure Blockchain Service](../service/overview.md) | PaaS | Azure Blockchain Service Preview는 컨소시엄 블록체인 네트워크의 포맷, 관리 및 거버넌스를 간소화합니다. PaaS, 컨소시엄 관리 또는 계약 및 트랜잭션 개인 정보를 요구하는 솔루션을 위해 Azure Blockchain Service를 사용합니다.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS 및 PaaS | Azure Blockchain Workbench 미리 보기는 비즈니스 프로세스 및 데이터를 다른 조직과 공유하기 위해 블록체인 애플리케이션을 만들고 배포할 수 있도록 설계된 Azure 서비스 및 기능 컬렉션입니다. 블록체인 솔루션을 프로토타이핑하거나 블록체인 애플리케이션 개념 증명을 위해 Azure Blockchain Workbench를 사용합니다. Azure Blockchain Workbench는 서비스 수준 규약 없이 제공됩니다. 지원을 받으려면 [Microsoft Q&A 질문 페이지](/answers/topics/azure-blockchain-workbench.html)를 사용하세요.

## <a name="solution-architecture"></a>솔루션 아키텍처

Ethereum 솔루션 템플릿을 사용하여 단일 또는 다중 지역 기반 Ethereum Proof-of-Authority 컨소시엄 네트워크를 배포할 수 있습니다.

![배포 아키텍처](./media/ethereum-poa-deployment/deployment-architecture.png)

각 컨소시엄 멤버 배포에는 다음이 포함됩니다.

* PoA 유효성 검사기 실행용 가상 머신
* RPC, 피어링 및 거버넌스 DApp 요청을 배포하기 위한 Azure Load Balancer
* 유효성 검사기 ID 보안을 위한 Azure Key Vault
* 지속적인 네트워크 정보 호스팅 및 임대 조정을 위한 Azure Storage
* 로그 및 성능 통계를 집계하기 위한 Azure Monitor
* 프라이빗 VNet 간 VPN 연결을 허용하기 위한 VNet 게이트웨이(선택 사항)

기본적으로 RPC 및 피어링 엔드포인트는 공용 IP를 통해 액세스할 수 있으므로 구독 및 클라우드 간 연결을 간소화할 수 있습니다. 애플리케이션 수준 액세스 제어의 경우 [패리티의 권한 부여 계약](https://openethereum.github.io/Permissioning.html)을 사용할 수 있습니다. 구독 간 연결에 VNet 게이트웨이를 활용하는 VPN 뒤에 배포되는 네트워크를 지원합니다. VPN 및 VNet 배포는 더 복잡하므로 솔루션 프로토타이핑 시 공용 IP 모델로 시작하는 것이 좋습니다.

안정성과 모듈화를 위해 Docker 컨테이너가 사용됩니다. Azure Container Registry를 사용하여 각 배포의 일환으로 버전이 지정된 이미지를 호스팅하고 제공합니다. 컨테이너 이미지는 다음과 같이 구성됩니다.

* 오케스트레이터 - ID 및 거버넌스 계약을 생성합니다. ID 저장소에 ID를 저장합니다.
* 패리티 클라이언트 - ID 저장소에서 ID를 임대합니다. 피어를 검색하고 연결합니다.
* EthStats 에이전트 - RPC를 통해 로컬 로그와 통계를 수집하고 Azure Monitor에 정보를 푸시합니다.
* Governance DApp - 거버넌스 계약과 상호 작용하기 위한 웹 인터페이스입니다.

### <a name="validator-nodes"></a>유효성 검사기 노드

인증 증명 프로토콜에서 유효성 검사기 노드는 기존의 채굴기 노드를 대신합니다. 각 유효성 검사기에는 블록 생성 프로세스에 참여할 수 있도록 하는 고유한 Ethereum ID가 있습니다. 각 컨소시엄 멤버는 지리적 중복을 위해 5개 지역에 걸쳐 둘 이상의 유효성 검사기 노드를 프로비전할 수 있습니다. 유효성 검사기 노드는 다른 유효성 검사기 노드와 통신하여 기본 분산 원장의 상태에 관한 합의에 도달합니다. 네트워크에 대한 참여를 보장하기 위해 각 컨소시엄 멤버는 네트워크의 첫 번째 멤버보다 많은 유효성 검사기를 사용할 수 없습니다. 예를 들어 첫 번째 멤버가 세 가지 유효성 검사기를 배포하는 경우 각 멤버는 유효성 검사기를 3개까지만 가질 수 있습니다.

### <a name="identity-store"></a>ID 저장소

생성된 Ethereum ID를 안전하게 보관하는 ID 저장소가 각 멤버의 구독에 배포됩니다. 각 유효성 검사기에 대해 오케스트레이션 컨테이너는 Ethereum 개인 키를 생성하고 Azure Key Vault에 저장합니다.

## <a name="deploy-ethereum-consortium-network"></a>Ethereum 컨소시엄 네트워크 배포

이 연습에서는 다중 파티 Ethereum 컨소시엄 네트워크를 만드는 경우를 가정하겠습니다. 다음 흐름은 다중 파티 배포의 예입니다.

1. 세 명의 멤버가 각각 MetaMask를 사용하여 Ethereum 계정을 생성합니다.
1. *멤버 A* 가 Ethereum PoA를 배포하여 해당 Ethereum 공용 주소를 제공합니다.
1. *멤버 A* 가 *멤버 B* 및 *멤버 C* 에게 컨소시엄 URL을 제공합니다.
1. *멤버 B* 및 *멤버 C* 가 Ethereum PoA를 배포하여 해당 Ethereum 공용 주소 및 *멤버 A* 의 컨소시엄 URL을 제공합니다.
1. *멤버 A* 가 *멤버 B* 에게 관리자로 투표합니다.
1. *멤버 A* 및 *멤버 B* 가 모두 *멤버 C* 에게 관리자로 투표합니다.

다음 섹션에서는 네트워크에서 첫 번째 멤버의 공간을 구성하는 방법을 보여줍니다.

### <a name="create-resource"></a>리소스 만들기

[Azure Portal](https://portal.azure.com)의 왼쪽 상단에서 **리소스 만들기** 를 선택합니다.

**블록체인** > **Ethereum Proof-of-Authority 컨소시엄(미리 보기)** 을 선택합니다.

### <a name="basics"></a>기본 사항

**기본 사항** 에서 모든 배포에 대해 표준 매개 변수 값을 지정합니다.

![기본 사항](./media/ethereum-poa-deployment/basic-blade.png)

매개 변수 | 설명 | 예제 값
----------|-------------|--------------
새 네트워크 만들기 또는 기존 네트워크 가입 | 새 컨소시엄 네트워크를 만들거나 기존 컨소시엄 네트워크에 가입할 수 있습니다. 기존 네트워크를 가입하려면 추가 매개 변수가 필요합니다. | 새로 만들기
메일 주소 | 배포에 대한 정보를 사용하여 배포가 완료되면 이메일 알림을 받게 됩니다. | 유효한 이메일 주소
VM 사용자 이름 | 배포된 각 VM의 관리자 사용자 이름 | 1~64자의 영숫자 문자
인증 유형 | 가상 머신을 인증하는 방법. | 암호
암호 | 배포된 각 가상 머신의 관리자 계정 암호. 모든 VM은 처음에 동일한 암호를 가집니다. 암호는 프로비전 후에 변경할 수 있습니다. | 12-72자 
Subscription | 컨소시엄 네트워크를 배포하는 구독 |
리소스 그룹| 컨소시엄 네트워크를 배포하는 리소스 그룹. | myResourceGroup
Location | 리소스 그룹에 대한 Azure 지역입니다. | 미국 서부 2

**확인** 을 선택합니다.

### <a name="deployment-regions"></a>배포 지역

*배포 지역* 에서 각 항목에 대한 지역 및 위치 수를 지정합니다. 최대 5개의 지역에서 배포할 수 있습니다. 첫 번째 지역은 *기본 사항* 섹션의 리소스 그룹 위치와 일치해야 합니다. 개발 또는 테스트 네트워크의 경우 멤버당 하나의 지역을 사용할 수 있습니다. 프로덕션 환경의 경우 고가용성을 위해 둘 이상의 지역에 걸쳐 배포합니다.

![배포 지역](./media/ethereum-poa-deployment/deployment-regions.png)

매개 변수 | 설명 | 예제 값
----------|-------------|--------------
지역 수|컨소시엄 네트워크를 배포하는 지역 수| 2
첫 번째 지역 | 컨소시엄 네트워크를 배포하는 첫 번째 지역 | 미국 서부 2
두 번째 지역 | 컨소시엄 네트워크를 배포할 두 번째 지역입니다. 영역 수가 둘 이상인 경우에는 추가 지역이 표시 됩니다. | 미국 동부 2

**확인** 을 선택합니다.

### <a name="network-size-and-performance"></a>네트워크 크기 및 성능

*네트워크 크기 및 성능* 아래에 컨소시엄 네트워크의 크기를 입력합니다. 유효성 검사기 노드 스토리지 크기에 따라 블록체인의 잠재적 크기가 결정됩니다. 배포 후에 이 크기를 변경할 수 있습니다.

![네트워크 크기 및 성능](./media/ethereum-poa-deployment/network-size-and-performance.png)

매개 변수 | 설명 | 예제 값
----------|-------------|--------------
부하가 분산된 유효성 검사기 노드 수 | 네트워크의 일부로 프로비전하는 유효성 검사기 노드의 수입니다. | 2
유효성 검사기 노드 스토리지 성능 | 배포된 각 유효성 검사기 노드의 관리 디스크 유형입니다. 가격 책정에 대한 자세한 내용은 [스토리지 가격](https://azure.microsoft.com/pricing/details/managed-disks/)을 참조하세요. | 표준 SSD
유효성 검사기 노드 가상 머신 크기 | 유효성 검사기 노드에 사용되는 가상 머신 크기입니다. 가격 책정에 대한 자세한 내용은 [가상 머신 가격](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)을 참조하세요. | 표준 D2 v3

가상 머신 및 스토리지 계층은 네트워크 성능에 영향을 줍니다.  다음 표를 사용하여 비용 효율성을 선택할 수 있습니다.

가상 머신 SKU|스토리지 계층|가격|처리량|대기 시간
---|---|---|---|---
F1|표준 SSD|low|low|high
D2_v3|표준 SSD|중간|중간|중간
F16s|프리미엄 SSD|high|high|low

**확인** 을 선택합니다.

### <a name="ethereum-settings"></a>Ethereum 설정

*Ethereum 설정* 에서 Ethereum 관련 구성 설정을 지정합니다.

![Ethereum 설정](./media/ethereum-poa-deployment/ethereum-settings.png)

매개 변수 | 설명 | 예제 값
----------|-------------|--------------
컨소시엄 멤버 ID | 컨소시엄 네트워크에 참여하는 각 멤버와 관련된 ID입니다. 이는 충돌을 방지하기 위해 IP 주소 공간을 구성하는 데 사용됩니다. 개인 네트워크의 경우 멤버 ID는 동일한 네트워크의 여러 조직에서 고유해야 합니다.  고유 멤버 ID는 같은 조직이 여러 지역에 배포하는 경우에도 필요합니다. 충돌이 없는지 확인하기 위해 다른 조인 멤버와 공유해야 하므로 이 매개 변수의 값을 기록해 두세요. 유효한 범위는 0~255입니다. | 0
네트워크 ID | 배포하는 컨소시엄 Ethereum 네트워크의 네트워크 ID입니다. 각 Ethereum 네트워크에는 공용 네트워크에 대한 ID를 1로 하는 자체의 네트워크 ID가 있습니다. 유효한 범위는 5~999,999,999입니다. | 10101010
관리자 Ethereum 주소 | PoA 거버넌스 참여에 사용되는 Ethereum 계정 주소입니다. MetaMask를 사용하여 Ethereum 주소를 생성할 수 있습니다. |
고급 옵션 | Ethereum 설정에 대한 고급 옵션 | 사용
공용 IP를 사용하여 배포 | 개인 VNet을 선택하면 네트워크는 VNet 게이트웨이 뒤에 배포되고 피어링 액세스를 제거합니다. 개인 VNet의 경우 모든 멤버가 VNet 게이트웨이를 사용해야 연결이 호환됩니다. | 공용 IP
블록 가스 제한 | 네트워크의 시작 블록 가스 제한입니다. | 50000000
블록 다시 봉인 기간(초) | 네트워크에 트랜잭션이 없을 때 빈 블록이 작성되는 빈도입니다. 빈도가 높으면 블록이 더 빨리 작성되지만 스토리지 비용이 증가합니다. | 15
트랜잭션 권한 계약 | 트랜잭션 권한 지정 계약에 대한 바이트 코드입니다. 스마트 계약 배포 및 실행을 권한이 부여된 Ethereum 계정 목록으로 제한합니다. |

**확인** 을 선택합니다.

### <a name="monitoring"></a>모니터링

모니터링을 통해 네트워크에 대한 로그 리소스를 구성할 수 있습니다. 모니터링 에이전트는 네트워크에서 유용한 메트릭과 로그를 수집하고 표시하여 네트워크 상태 또는 디버그 문제를 빠르게 확인하는 기능을 제공합니다.

![Azure Monitor](./media/ethereum-poa-deployment/azure-monitor.png)

매개 변수 | 설명 | 예제 값
----------|-------------|--------------
모니터링 | 모니터링을 사용하도록 설정하는 옵션 | 사용
기존 Azure Monitor 로그에 연결 | 새 Azure Monitor 로그 인스턴스를 만들거나 기존 인스턴스에 조인하는 옵션 | 새로 만들기
Location | 새 리소스가 배포되는 지역 | 미국 동부
기존 Log Analytics 작업 영역 ID(기존 Azure Monitor에 연결 = 기존 조인)|기존 Azure Monitor 로그 인스턴스의 작업 영역 ID|해당 없음
기존 Log Analytics 기본 키(기존 Azure Monitor 로그에 연결 = 기존 조인)|기존 Azure Monitor 로그 인스턴스에 연결하는 데 사용되는 기본 키입니다.|해당 없음

**확인** 을 선택합니다.

### <a name="summary"></a>요약

요약을 클릭하여 지정된 입력을 검토하고 기본 배포 전 유효성 검사를 실행합니다. 배포하기 전에 템플릿과 매개 변수를 다운로드할 수 있습니다.

**만들기** 를 선택하여 배포합니다.

배포에 VNet 게이트웨이가 포함된 경우 배포하는 데 45~50분이 걸릴 수 있습니다.

## <a name="deployment-output"></a>배포 출력

배포가 완료되면 Azure Portal을 사용하여 필요한 매개 변수에 액세스할 수 있습니다.

### <a name="confirmation-email"></a>확인 이메일

이메일 주소([기본 사항 섹션](#basics))를 제공하면 배포 정보 및 이 설명서에 대한 링크가 포함된 이메일이 전송됩니다.

![배포 이메일](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>포털

배포가 성공적으로 완료되고 모든 리소스가 프로비전되면 리소스 그룹의 출력 매개 변수를 볼 수 있습니다.

1. 포털의 리소스 그룹으로 이동합니다.
1. **개요 > 배포** 를 선택합니다.

    ![리소스 그룹 개요](./media/ethereum-poa-deployment/resource-group-overview.png)

1. **microsoft-azure-blockchain.azure-blockchain-ether-...** 배포를 선택합니다.
1. **출력** 섹션을 선택합니다.

    ![배포 출력](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>컨소시엄 확장

컨소시엄을 확장하려면 먼저 실제 네트워크를 연결해야 합니다. VPN 뒤에 배포하는 경우 [VNet 게이트웨이 연결](#connecting-vnet-gateways) 섹션을 참조하여 새 멤버 배포의 일환으로 네트워크 연결을 구성합니다. 배포가 완료되면 [거버넌스 DApp](#governance-dapp)를 사용하여 자신을 네트워크 관리자로 설정합니다.

### <a name="new-member-deployment"></a>새 멤버 배포

다음 정보를 조인 멤버와 공유합니다. 이 정보는 배포 후 이메일 또는 포털 배포 출력에서 찾을 수 있습니다.

* 컨소시엄 데이터 URL
* 배포한 노드 수
* VNet 게이트웨이 리소스 ID(VPN을 사용하는 경우)

배포 멤버는 다음 지침을 사용하여 네트워크 상태를 배포할 때 동일한 Ethereum Proof-of-Authority 컨소시엄 솔루션 템플릿을 사용해야 합니다.

* **기존 조인** 을 선택합니다.
* 공정한 표현을 보장하기 위해 네트워크상의 나머지 멤버와 동일한 유효성 검사기 노드 수를 선택합니다
* 동일한 관리자 Ethereum 주소 사용
* *Ethereum 설정* 에서 제공한 *컨소시엄 데이터 URL* 을 사용합니다.
* 네트워크의 나머지 부분이 VPN 뒤에 있는 경우 고급 섹션 아래에서 **개인 VNet** 을 선택합니다.

### <a name="connecting-vnet-gateways"></a>VNet 게이트웨이 연결

이 섹션은 개인 VNet을 사용하여 배포하는 경우에만 필요합니다. 공용 IP 주소를 사용하는 경우에는 이 섹션을 건너뛰면 됩니다.

개인 네트워크의 경우 다른 멤버는 VNet 게이트웨이 연결을 통해 연결됩니다. 멤버가 네트워크에 조인하고 트랜잭션 트래픽을 볼 수 있으려면 먼저 기존 멤버가 자신의 VPN 게이트웨이에서 최종 구성을 수행하여 해당 연결을 수락해야 합니다. 조인 멤버의 Ethereum 노드는 연결이 설정될 때까지 실행되지 않습니다. 단일 실패 지점이 발생하는 가능성을 줄이려면 컨소시엄에서 중복 네트워크 연결을 만듭니다.

새 멤버가 배포되면 기존 멤버는 새 멤버에 대한 VNet 게이트웨이 연결을 설정하여 양방향 연결을 완료해야 합니다. 기존 멤버에는 다음 항목이 필요합니다.

* 연결 멤버의 VNet 게이트웨이 ResourceID [배포 출력](#deployment-output)을 참조하세요.
* 공유 연결 키

기존 멤버는 다음 PowerShell 스크립트를 실행하여 연결을 완료해야 합니다. 포털에서 오른쪽 위의 탐색 모음에 있는 Azure Cloud Shell을 사용할 수 있습니다.

![Cloud Shell](./media/ethereum-poa-deployment/cloud-shell.png)

```powershell
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

## <a name="governance-dapp"></a>거버넌스 DApp

인증 증명의 중심에는 분산 거버넌스가 있습니다. Proof-of-Authority는 네트워크를 정상으로 유지하기 위해 권한이 부여된 네트워크 기관 목록을 사용하므로 이 권한 목록을 수정하기 위한 공정한 메커니즘을 제공해야 합니다. 각 배포에는 권한이 부여된 이 목록의 온체인(on-chain) 거버넌스를 위한 일단의 스마트 계약과 포털이 제공됩니다. 제안된 변경이 컨소시엄 멤버의 과반수 표결에 도달하면 해당 변경이 시행됩니다. 표결을 통해 정직한 네트워크를 장려하는 투명한 방식으로 새 합의 참가자가 추가되거나 타협한 참가자가 제거될 수 있습니다.

거버넌스 DApp는 네트워크에서 기관을 관리하는 데 사용되는 미리 배포된 [스마트 계약](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) 집합 및 웹 애플리케이션입니다. 기관은 관리자 ID 및 유효성 검사기 노드로 분할됩니다.
관리자는 일단의 유효성 검사기 노드에 합의 참여를 위임할 수 있습니다. 관리자는 네트워크 내부 또는 외부에서 다른 관리자에게 투표할 수도 있습니다.

![거버넌스 DApp](./media/ethereum-poa-deployment/governance-dapp.png)

* **분산 거버넌스:** 네트워크 기관의 변경은 선택한 관리자의 온체인 투표를 통해 관리됩니다.
* **유효성 검사기 위임:** 기관은 각 PoA 배포에서 설정된 유효성 검사기 노드를 관리할 수 있습니다.
* **감사 가능한 변경 기록:** 각 변경은 투명성과 감사 가능성을 제공하는 블록체인에 기록됩니다.

### <a name="getting-started-with-governance"></a>거버넌스 시작

거버넌스 DApp을 통해 원하는 트랜잭션을 수행하려면 Ethereum 전자지갑을 사용해야 합니다. 가장 간단한 방법은 [MetaMask](https://metamask.io) 등의 브라우저 내 전자지갑을 사용하는 것입니다. 하지만 거버넌스 DApp는 네트워크에서 배포되는 스마트 계약이므로 거버넌스 계약에 대한 상호 작용을 자동화할 수도 있습니다.

MetaMask를 설치한 후 브라우저에서 거버넌스 DApp로 이동합니다.  배포 출력에서 Azure Portal을 통해 URL을 찾을 수 있습니다.  브라우저 내 전자지갑이 설치되어 있지 않으면 작업을 수행할 수는 없지만 관리자 상태를 확인할 수는 있습니다.

### <a name="becoming-an-admin"></a>관리자 되기

네트워크에 배포된 첫 번째 멤버인 경우 자동으로 관리자가 되며, 패리티 노드가 유효성 검사기로 나열됩니다. 네트워크에 가입하는 경우에는 기존 관리자 집합 중 과반수(50% 이상)로부터 득표를 해야 관리자가 됩니다. 관리자가 되지 않더라도 노드는 계속 동기화되며 블록체인 유효성을 검사하지만 블록 작성 프로세스에 참가하지는 않습니다. 관리자가 되기 위한 투표 프로세스를 시작하려면 **지정** 을 선택하고 Ethereum 주소와 별칭을 입력합니다.

![지정](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>후보

**후보** 탭을 선택하면 현재 관리자 후보 집합이 표시됩니다.  후보자가 현재 관리자의 과반수 투표에 도달하면 후보가 관리자로 승격됩니다. 후보자에게 투표하려면 행을 선택하고 **투표** 를 선택합니다. 다른 후보에게 투표하려는 경우 후보를 선택하고 **투표 취소** 를 선택합니다.

![후보](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>관리자

현재 관리자 집합이 표시되는 **관리자** 탭에서 투표를 할 수 있습니다.  50% 이상의 지지를 받지 못한 관리자는 네트워크의 관리자 명단에서 제거됩니다. 그러면 해당 관리자가 소유한 유효성 검사기 노드의 유효성 검사기 상태가 해제되어 네트워크의 트랜잭션 노드로 설정됩니다. 관리자는 여러 가지 이유로 인해 관리자 명단에서 제거될 수 있지만 관련 정책에 대한 사전 합의 여부는 컨소시엄에서 결정합니다.

![관리자](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>유효성 검사기

**유효성 검사기** 탭을 선택하면 이 인스턴스에 대해 현재 배포된 패리티 노드와 해당 노드의 현재 상태(노드 형식)가 표시됩니다. 이 목록에는 컨소시엄 멤버별로 각기 다른 유효성 검사기 집합이 표시됩니다. 이 보기는 현재 배포된 컨소시엄 멤버를 나타내기 때문입니다. 인스턴스를 새로 배포하고 유효성 검사기를 추가하지 않은 경우에는 **유효성 검사기를 추가** 할 수 있는 옵션이 제공됩니다. 유효성 검사기를 추가하면 지역별로 분산된 패리티 노드 집합이 자동으로 선택되어 유효성 검사기 집합에 할당됩니다. 허용된 용량보다 많은 노드를 배포한 경우 나머지 노드는 네트워크의 트랜잭션 노드가 됩니다.

각 유효성 검사기의 주소는 Azure의 [ID 저장소](#identity-store)를 통해 자동으로 할당됩니다.  노드가 다운되면 해당 ID가 해제되므로 배포의 다른 노드가 해당 ID를 사용할 수 있습니다. 이 프로세스를 통해 항상 합의 과정에 참가할 수 있습니다.

![유효성 검사기](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>컨소시엄 이름

모든 관리자가 컨소시엄 이름을 업데이트할 수 있습니다.  왼쪽 위의 기어 아이콘을 선택하여 컨소시엄 이름을 업데이트합니다.

### <a name="account-menu"></a>계정 메뉴

오른쪽 위에는 Ethereum 계정 별칭과 ID 아이콘이 표시됩니다.  관리자는 별칭을 업데이트할 수 있습니다.

![계정](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="support-and-feedback"></a>지원 및 피드백<a id="tutorials"></a>

Azure Blockchain 뉴스의 경우 [Azure Blockchain 블로그](https://azure.microsoft.com/blog/topics/blockchain/)를 방문하여 Azure Blockchain 엔지니어링 팀의 블록체인 서비스 제공 및 정보를 최신 상태로 유지하세요.

제품 피드백을 제공하거나 새로운 기능을 요청하려면 [블록체인에 대한 Azure 피드백 포럼](https://aka.ms/blockchainuservoice)을 통해 아이디어를 게시하거나 투표하세요.

### <a name="community-support"></a>커뮤니티 지원

Microsoft 엔지니어 및 Azure Blockchain 커뮤니티 전문가와 소통하세요.

* [Microsoft Q&A 질문 페이지](/answers/topics/azure-blockchain-workbench.html) Azure Blockchain Workbench에 대한 엔지니어링 지원은 배포 문제로 제한됩니다.
* [Microsoft 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [스택 오버플로](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>다음 단계

더 많은 Azure Blockchain 솔루션은 [Azure Blockchain 문서](../index.yml)를 참조하세요.
