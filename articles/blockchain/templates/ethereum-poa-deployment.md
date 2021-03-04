---
title: Azure에서 Ethereum 증명 기관 컨소시엄 솔루션 템플릿 배포
description: Ethereum Authority consortium 솔루션을 사용 하 여 Azure에서 다중 구성원 컨소시엄 Ethereum 네트워크를 배포 하 고 구성 합니다.
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3
ms.openlocfilehash: 70c9498bae9117585963e111bea4f1e127cab232
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097944"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Azure에서 Ethereum 증명 기관 컨소시엄 솔루션 템플릿 배포

[Ethereum Authority 컨소시엄 Preview Azure 솔루션 템플릿](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) 을 사용 하 여 최소 Azure 및 Ethereum 지식으로 다중 구성원 컨소시엄 증명 기관 Ethereum 네트워크를 배포, 구성 및 제어할 수 있습니다.

솔루션 템플릿은 각 consortium 멤버가 Microsoft Azure 계산, 네트워킹 및 저장소 서비스를 사용 하 여 블록 체인 네트워크 공간을 프로 비전 하는 데 사용할 수 있습니다. 각 consortium 구성원의 네트워크 공간은 응용 프로그램 또는 사용자가 Ethereum 트랜잭션을 제출 하기 위해 상호 작용할 수 있는 부하 분산 된 유효성 검사기 노드 집합으로 구성 됩니다.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Azure 블록 체인 솔루션 선택

Ethereum authority consortium 솔루션 템플릿을 사용 하도록 선택 하기 전에 시나리오를 사용 가능한 Azure Blockchain 옵션의 일반적인 사용 사례와 비교 합니다.

> [!IMPORTANT]
> Azure 솔루션 템플릿 Ethereum 대신 [Azure Blockchain 서비스](../service/overview.md) 를 사용 하는 것이 좋습니다. Azure Blockchain 서비스는 지원 되는 관리 되는 Azure 서비스입니다. 패리티 Ethereum 커뮤니티 기반 개발 및 유지 관리로 전환 되었습니다. 자세한 내용은 [패리티 Ethereum에서 OPENETHEREUM DAO로 전환](https://www.parity.io/parity-ethereum-openethereum-dao/)을 참조 하세요.

옵션 | 서비스 모델 | 일반적인 사용 사례
-------|---------------|-----------------
솔루션 템플릿 | IaaS | 솔루션 템플릿은 완전히 구성 된 blockchain 네트워크 토폴로지를 프로 비전 하는 데 사용할 수 있는 Azure Resource Manager 템플릿입니다. 템플릿은 지정 된 blockchain 네트워크 유형에 대해 Microsoft Azure 계산, 네트워킹 및 저장소 서비스를 배포 하 고 구성 합니다. 솔루션 템플릿은 서비스 수준 계약 없이 제공 됩니다. 지원을 받으려면 [Microsoft Q&A 질문 페이지](/answers/topics/azure-blockchain-workbench.html)를 사용하세요.
[Azure Blockchain Service](../service/overview.md) | PaaS | Azure Blockchain 서비스 미리 보기는 컨소시엄 Blockchain 네트워크의 대형, 관리 및 관리를 간소화 합니다. PaaS, consortium 관리 또는 계약 및 트랜잭션 개인 정보를 요구 하는 솔루션에는 Azure Blockchain 서비스를 사용 합니다.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS 및 PaaS | Azure Blockchain Workbench 미리 보기는 비즈니스 프로세스 및 데이터를 다른 조직과 공유하기 위해 블록체인 애플리케이션을 만들고 배포할 수 있도록 설계된 Azure 서비스 및 기능 컬렉션입니다. 블록 체인 솔루션 프로토타입 또는 블록 체인 응용 프로그램 개념 증명을 위해 Azure Blockchain 워크 벤치를 사용 합니다. Azure Blockchain Workbench는 서비스 수준 규약 없이 제공됩니다. 지원을 받으려면 [Microsoft Q&A 질문 페이지](/answers/topics/azure-blockchain-workbench.html)를 사용하세요.

## <a name="solution-architecture"></a>솔루션 아키텍처

Ethereum 솔루션 템플릿을 사용 하 여 단일 또는 다중 지역 기반 Ethereum (authority of authority) 컨소시엄 네트워크를 배포할 수 있습니다.

![배포 아키텍처](./media/ethereum-poa-deployment/deployment-architecture.png)

각 컨소시엄 멤버 배포에는 다음이 포함됩니다.

* PoA 유효성 검사기 실행용 가상 머신
* RPC, 피어 링 및 거 버 넌 스 요청 배포를 위한 Azure Load Balancer
* 유효성 검사기 ID 보안을 위한 Azure Key Vault
* 지속적인 네트워크 정보 호스팅 및 임대 조정을 위한 Azure Storage
* 로그 및 성능 통계를 집계하기 위한 Azure Monitor
* 프라이빗 VNet 간 VPN 연결을 허용하기 위한 VNet 게이트웨이(선택 사항)

기본적으로 RPC 및 피어링 엔드포인트는 공용 IP를 통해 액세스할 수 있으므로 구독 및 클라우드 간 연결을 간소화할 수 있습니다. 응용 프로그램 수준 액세스 제어의 경우 [패리티의 권한 부여 계약](https://openethereum.github.io/Permissioning.html)을 사용할 수 있습니다. 구독 간 연결에 VNet 게이트웨이를 활용 하는 Vpn을 통해 배포 된 네트워크를 지원 합니다. VPN 및 VNet 배포는 더 복잡 하기 때문에 솔루션을 프로토타입 할 때 공용 IP 모델을 시작 하는 것이 좋습니다.

Docker 컨테이너는 안정성 및 모듈화에 사용 됩니다. Azure Container Registry는 각 배포의 일부로 버전이 지정 된 이미지를 호스트 하 고 제공 하는 데 사용 됩니다. 컨테이너 이미지는 다음과 같이 구성됩니다.

* Orchestrator-id 및 거 버 넌 스 계약을 생성 합니다. Id 저장소에 id를 저장 합니다.
* 패리티 클라이언트-id 저장소에서 id를 임대 합니다. 피어를 검색 하 고 연결 합니다.
* EthStats Agent-RPC를 통해 로컬 로그 및 통계를 수집 하 고 Azure Monitor에 정보를 푸시합니다.
* 거 버 넌 스 계약과 상호 작용 하는 웹 인터페이스입니다.

### <a name="validator-nodes"></a>유효성 검사기 노드

인증 증명 프로토콜에서 유효성 검사기 노드는 기존의 채굴기 노드를 대신합니다. 각 유효성 검사기에는 블록 생성 프로세스에 참여할 수 있도록 하는 고유한 Ethereum id가 있습니다. 각 컨소시엄 멤버는 지리적 중복을 위해 5개 지역에 걸쳐 둘 이상의 유효성 검사기 노드를 프로비전할 수 있습니다. 유효성 검사기 노드는 다른 유효성 검사기 노드와 통신하여 기본 분산 원장의 상태에 관한 합의에 도달합니다. 네트워크에 대 한 참여를 보장 하기 위해 각 consortium 구성원은 네트워크의 첫 번째 구성원 보다 많은 유효성 검사기를 사용할 수 없습니다. 예를 들어 첫 번째 멤버가 세 가지 유효성 검사기를 배포 하는 경우 각 멤버는 유효성 검사기를 최대 3 개 까지만 포함할 수 있습니다.

### <a name="identity-store"></a>ID 저장소

Id 저장소는 생성 된 Ethereum id를 안전 하 게 보관 하는 각 멤버의 구독에 배포 됩니다. 각 유효성 검사기에 대해 오케스트레이션 컨테이너는 Ethereum 개인 키를 생성 하 고 Azure Key Vault에 저장 합니다.

## <a name="deploy-ethereum-consortium-network"></a>Ethereum consortium 네트워크 배포

이 연습에서는 다중 파티 Ethereum 컨소시엄 네트워크를 만드는 경우를 가정 하겠습니다. 다음 흐름은 다중 파티 배포의 예입니다.

1. 세 명의 멤버가 각각 MetaMask를 사용하여 Ethereum 계정을 생성합니다.
1. *Member a* 가 Ethereum poa를 배포 하 고 해당 Ethereum 공용 주소를 제공 합니다.
1. *멤버 A* 가 *멤버 B* 및 *멤버 C* 에게 컨소시엄 URL을 제공합니다.
1. *멤버 B* 및 *멤버 C* 가 Ethereum PoA를 배포하여 해당 Ethereum 공용 주소 및 *멤버 A* 의 컨소시엄 URL을 제공합니다.
1. *멤버 A* 가 *멤버 B* 에게 관리자로 투표합니다.
1. *멤버 A* 및 *멤버 B* 가 모두 *멤버 C* 에게 관리자로 투표합니다.

다음 섹션에서는 네트워크에서 첫 번째 구성원의 공간을 구성 하는 방법을 보여 줍니다.

### <a name="create-resource"></a>리소스 만들기

[Azure Portal](https://portal.azure.com)의 왼쪽 위 모서리에서 **리소스 만들기** 를 선택 합니다.

**Blockchain**  >  **Ethereum 인증 기관 Consortium (미리 보기)** 를 선택 합니다.

### <a name="basics"></a>기본 사항

**기본** 에서 배포에 대 한 표준 매개 변수의 값을 지정 합니다.

![기본 사항](./media/ethereum-poa-deployment/basic-blade.png)

매개 변수 | 설명 | 예제 값
----------|-------------|--------------
새 네트워크를 만들거나 기존 네트워크에 가입 | 새 consortium 네트워크를 만들거나 기존 컨소시엄 네트워크에 가입할 수 있습니다. 기존 네트워크를 조인 하려면 추가 매개 변수가 필요 합니다. | 새로 만들기
메일 주소 | 배포가 완료 되 면 배포에 대 한 정보를 사용 하 여 전자 메일 알림을 받게 됩니다. | 유효한 전자 메일 주소
VM 사용자 이름 | 배포 된 각 VM의 관리자 사용자 이름 | 1-64 영숫자 문자
인증 유형 | 가상 머신을 인증하는 방법. | 암호
암호 | 배포된 각 가상 머신의 관리자 계정 암호. 모든 Vm은 처음에 동일한 암호를 가집니다. 프로 비전 한 후 암호를 변경할 수 있습니다. | 12-72자 
Subscription | 컨소시엄 네트워크를 배포하는 구독 |
리소스 그룹| 컨소시엄 네트워크를 배포하는 리소스 그룹. | myResourceGroup
위치 | 리소스 그룹에 대한 Azure 지역입니다. | 미국 서부 2

**확인** 을 선택합니다.

### <a name="deployment-regions"></a>배포 지역

*배포 지역* 에서 각에 대 한 지역 및 위치 수를 지정 합니다. 최대 5 개 지역에 배포할 수 있습니다. 첫 번째 지역은 *기본 사항* 섹션의 리소스 그룹 위치와 일치 해야 합니다. 개발 또는 테스트 네트워크의 경우에는 멤버 당 단일 지역을 사용할 수 있습니다. 프로덕션의 경우 고가용성을 위해 둘 이상의 지역에 배포 합니다.

![배포 지역](./media/ethereum-poa-deployment/deployment-regions.png)

매개 변수 | 설명 | 예제 값
----------|-------------|--------------
지역 수|컨소시엄 네트워크를 배포하는 지역 수| 2
첫 번째 지역 | 컨소시엄 네트워크를 배포하는 첫 번째 지역 | 미국 서부 2
두 번째 지역 | 컨소시엄 네트워크를 배포 하는 두 번째 지역입니다. 영역 수가 둘 이상인 경우에는 추가 지역이 표시 됩니다. | 미국 동부 2

**확인** 을 선택합니다.

### <a name="network-size-and-performance"></a>네트워크 크기 및 성능

*네트워크 크기 및 성능* 에서 consortium 네트워크 크기에 대 한 입력을 지정 합니다. 유효성 검사기 노드 저장소 크기는 블록 체인의 잠재적 크기를 결정 합니다. 배포 후에 크기를 변경할 수 있습니다.

![네트워크 크기 및 성능](./media/ethereum-poa-deployment/network-size-and-performance.png)

매개 변수 | 설명 | 예제 값
----------|-------------|--------------
부하가 분산된 유효성 검사기 노드 수 | 네트워크의 일부로 프로 비전 할 유효성 검사기 노드의 수입니다. | 2
유효성 검사기 노드 스토리지 성능 | 배포 된 각 유효성 검사기 노드에 대 한 관리 디스크의 유형입니다. 가격 책정에 대 한 자세한 내용은 [저장소 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/) 을 참조 하세요. | 표준 SSD
유효성 검사기 노드 가상 머신 크기 | 유효성 검사기 노드에 사용되는 가상 머신 크기입니다. 가격 책정에 대 한 자세한 내용은 [가상 머신 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 을 참조 하세요. | 표준 D2 v3

가상 컴퓨터 및 저장소 계층은 네트워크 성능에 영향을 줍니다.  다음 표를 사용 하 여 비용 효율성을 선택할 수 있습니다.

가상 머신 SKU|스토리지 계층|가격|처리량|대기 시간
---|---|---|---|---
F1|표준 SSD|low|low|high
D2_v3|표준 SSD|중간|중간|중간
F16s|프리미엄 SSD|high|high|low

**확인** 을 선택합니다.

### <a name="ethereum-settings"></a>Ethereum 설정

*Ethereum 설정* 에서 Ethereum 관련 구성 설정을 지정 합니다.

![Ethereum 설정](./media/ethereum-poa-deployment/ethereum-settings.png)

매개 변수 | 설명 | 예제 값
----------|-------------|--------------
컨소시엄 멤버 ID | 컨소시엄 네트워크에 참여 하는 각 멤버와 연결 된 ID입니다. 이는 충돌을 방지 하기 위해 IP 주소 공간을 구성 하는 데 사용 됩니다. 개인 네트워크의 경우 구성원 ID는 동일한 네트워크에 있는 여러 조직에서 고유 해야 합니다.  고유 멤버 ID는 같은 조직이 여러 지역에 배포하는 경우에도 필요합니다. 충돌이 발생 하지 않도록 하기 위해 다른 조인 멤버와 공유 해야 하므로이 매개 변수의 값을 기록해 둡니다. 유효한 범위는 0에서 255 까지입니다. | 0
네트워크 ID | 배포하는 컨소시엄 Ethereum 네트워크의 네트워크 ID입니다. 각 Ethereum 네트워크에는 공용 네트워크에 대한 ID를 1로 하는 자체의 네트워크 ID가 있습니다. 유효한 범위는 5에서 999999999 까지입니다. | 10101010
관리자 Ethereum 주소 | PoA 거 버 넌 스에 참여 하는 데 사용 되는 Ethereum 계정 주소입니다. MetaMask를 사용 하 여 Ethereum 주소를 생성할 수 있습니다. |
고급 옵션 | Ethereum 설정에 대한 고급 옵션 | 사용
공용 IP를 사용 하 여 배포 | 개인 VNet을 선택 하면 네트워크는 VNet 게이트웨이 뒤에 배포 되 고 피어 링 액세스를 제거 합니다. 사설 VNet의 경우 연결을 호환 하려면 모든 멤버가 VNet 게이트웨이를 사용 해야 합니다. | 공용 IP
블록 가스 한도 | 네트워크의 시작 블록 가스 한도입니다. | 5000만
블록 다시 봉인 기간(초) | 네트워크에 트랜잭션이 없을 때 빈 블록이 작성되는 빈도입니다. 빈도가 높으면 블록이 더 빨리 작성되지만 스토리지 비용이 증가합니다. | 15
트랜잭션 권한 계약 | 트랜잭션 권한 지정 계약에 대한 바이트 코드입니다. 허용 되는 Ethereum 계정 목록으로 스마트 계약 배포 및 실행을 제한 합니다. |

**확인** 을 선택합니다.

### <a name="monitoring"></a>모니터링

모니터링을 통해 네트워크에 대 한 로그 리소스를 구성할 수 있습니다. 모니터링 에이전트는 네트워크 상태를 신속 하 게 확인 하거나 문제를 디버그 하는 기능을 제공 하는 네트워크에서 유용한 메트릭과 로그를 수집 하 고 표시 합니다.

![Azure Monitor](./media/ethereum-poa-deployment/azure-monitor.png)

매개 변수 | 설명 | 예제 값
----------|-------------|--------------
모니터링 | 모니터링을 사용 하도록 설정 하는 옵션 | 사용
기존 Azure Monitor 로그에 연결 | 새 Azure Monitor 로그 인스턴스를 만들거나 기존 인스턴스에 조인 하는 옵션 | 새로 만들기
위치 | 새 인스턴스가 배포 되는 지역입니다. | 미국 동부
기존 log analytics 작업 영역 ID (기존 Azure Monitor 로그에 연결 = 기존 조인)|기존 Azure Monitor logs 인스턴스의 작업 영역 ID||해당 없음
기존 log analytics 기본 키 (기존 Azure Monitor 로그에 연결 = 기존 조인)|기존 Azure Monitor logs 인스턴스에 연결 하는 데 사용 되는 기본 키입니다.||해당 없음

**확인** 을 선택합니다.

### <a name="summary"></a>요약

요약을 클릭 하 여 지정 된 입력을 검토 하 고 기본 배포 전 유효성 검사를 실행 합니다. 을 배포 하기 전에 템플릿 및 매개 변수를 다운로드할 수 있습니다.

**만들기** 를 선택 하 여 배포 합니다.

배포에 VNet 게이트웨이가 포함 된 경우 배포는 45 ~ 50 분까지 걸릴 수 있습니다.

## <a name="deployment-output"></a>배포 출력

배포가 완료 되 면 Azure Portal를 사용 하 여 필요한 매개 변수에 액세스할 수 있습니다.

### <a name="confirmation-email"></a>확인 이메일

전자 메일 주소 ([기본 사항 섹션](#basics))를 제공 하면 배포 정보 및이 설명서에 대 한 링크가 포함 된 전자 메일이 전송 됩니다.

![배포 이메일](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>포털

배포가 성공적으로 완료 되 고 모든 리소스가 프로 비전 되 면 리소스 그룹에서 출력 매개 변수를 볼 수 있습니다.

1. 포털에서 리소스 그룹으로 이동 합니다.
1. **개요 > 배포** 를 선택 합니다.

    ![리소스 그룹 개요](./media/ethereum-poa-deployment/resource-group-overview.png)

1. Microsoft azure-blockchain **. 에테르 스코프-...** 배포를 선택 합니다.
1. **출력** 섹션을 선택 합니다.

    ![배포 출력](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>컨소시엄 확장

컨소시엄을 확장하려면 먼저 실제 네트워크를 연결해야 합니다. VPN 뒤에 배포 하는 경우 [VNet 게이트웨이 연결](#connecting-vnet-gateways) 새 구성원 배포의 일부로 네트워크 연결 구성 섹션을 참조 하세요. 배포가 완료 되 면 [거 버 넌 스 앱](#governance-dapp) 을 사용 하 여 네트워크 관리자가 됩니다.

### <a name="new-member-deployment"></a>새 멤버 배포

다음 정보를 조인 멤버와 공유합니다. 정보는 배포 후 전자 메일 또는 포털 배포 출력에서 찾을 수 있습니다.

* 컨소시엄 데이터 URL
* 배포한 노드 수
* VNet 게이트웨이 리소스 ID(VPN을 사용하는 경우)

배포 구성원은 다음 지침을 사용 하 여 네트워크 상태를 배포할 때 동일한 Ethereum 증명 기관 컨소시엄 솔루션 템플릿을 사용 해야 합니다.

* **기존 조인** 을 선택합니다.
* 공평 하 게 표현할 수 있도록 네트워크에서 나머지 멤버와 동일한 수의 유효성 검사기 노드를 선택 합니다.
* 동일한 Admin Ethereum 주소를 사용 합니다.
* *Ethereum 설정* 에서 제공 된 *Consortium 데이터 Url* 사용
* 네트워크의 나머지 부분이 VPN 뒤에 있는 경우 고급 섹션에서 **개인 VNet** 을 선택 합니다.

### <a name="connecting-vnet-gateways"></a>VNet 게이트웨이 연결

이 섹션은 개인 VNet을 사용 하 여 배포 하는 경우에만 필요 합니다. 공용 IP 주소를 사용 하는 경우이 섹션을 건너뛸 수 있습니다.

개인 네트워크의 경우 다른 구성원은 VNet 게이트웨이 연결을 통해 연결 됩니다. 구성원이 네트워크에 연결 하 여 트랜잭션 트래픽을 볼 수 있으려면 기존 구성원은 VPN 게이트웨이에서 최종 구성을 수행 하 여 연결을 수락 해야 합니다. 조인 하는 멤버의 Ethereum 노드는 연결이 설정 될 때까지 실행 되지 않습니다. 단일 실패 지점이 발생 하는 가능성을 줄이려면 컨소시엄에서 중복 된 네트워크 연결을 만듭니다.

새 멤버가 배포되면 기존 멤버는 새 멤버에 대한 VNet 게이트웨이 연결을 설정하여 양방향 연결을 완료해야 합니다. 기존 멤버에는 다음이 필요 합니다.

* 연결 멤버의 VNet 게이트웨이 ResourceID입니다. [배포 출력](#deployment-output)을 참조 하세요.
* 공유 연결 키입니다.

기존 멤버는 다음 PowerShell 스크립트를 실행하여 연결을 완료해야 합니다. 포털에서 오른쪽 위에 있는 탐색 모음에 있는 Azure Cloud Shell를 사용할 수 있습니다.

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

## <a name="governance-dapp"></a>거버넌스 DApp

인증 증명의 중심에는 분산 거버넌스가 있습니다. 권한 증명은 네트워크를 정상적으로 유지 하기 위해 허용 되는 네트워크 인증 목록에 의존 하므로이 사용 권한 목록을 수정 하는 데에는 공평 한 메커니즘을 제공 하는 것이 중요 합니다. 각 배포에는이 허용 된 목록의 체인 거 버 넌 스에 대 한 일련의 스마트 계약과 포털이 제공 됩니다. 제안된 변경이 컨소시엄 멤버의 과반수 표결에 도달하면 해당 변경이 시행됩니다. 투표를 사용 하면 새 합의 참가자를 추가 하거나 손상 된 참가자를 사용 하는 투명 한 방식으로 참가자를 제거할 수 있습니다.

거버넌스 DApp는 네트워크에서 기관을 관리하는 데 사용되는 미리 배포된 [스마트 계약](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) 집합 및 웹 애플리케이션입니다. 기관은 관리 id 및 유효성 검사기 노드로 분류 됩니다.
관리자는 유효성 검사 노드 집합에 대 한 참여를 위임할 수 있습니다. 관리자는 네트워크 내부 또는 외부에서 다른 관리자에게 투표할 수도 있습니다.

![거버넌스 DApp](./media/ethereum-poa-deployment/governance-dapp.png)

* **분산 거 버 넌 스:** 네트워크 기관의 변경 내용은 선택한 관리자에의 한 체인 투표를 통해 관리 됩니다.
* **유효성 검사기 위임:** 기관은 각 PoA 배포에 설정 된 유효성 검사기 노드를 관리할 수 있습니다.
* **감사 가능한 변경 기록:** 각 변경 내용은 투명 및 auditability을 제공 하는 블록 체인에 기록 됩니다.

### <a name="getting-started-with-governance"></a>거버넌스 시작

거 버 넌 스를 통해 모든 종류의 트랜잭션을 수행 하려면 Ethereum을 사용 해야 합니다. 가장 간단한 방법은 [MetaMask](https://metamask.io)와 같은 브라우저 내 전자 지갑를 사용 하는 것입니다. 그러나 이러한 스마트 계약은 네트워크에 배포 되므로 거 버 넌 스 계약에 대 한 상호 작용을 자동화할 수도 있습니다.

MetaMask를 설치한 후 브라우저에서 거버넌스 DApp로 이동합니다.  배포 출력에서 Azure Portal를 통해 URL을 찾을 수 있습니다.  브라우저 내 전자 지갑이 설치 되어 있지 않은 경우 아무 작업도 수행할 수 없습니다. 그러나 관리자 상태를 볼 수 있습니다.  

### <a name="becoming-an-admin"></a>관리자 되기

네트워크에 배포 된 첫 번째 구성원 인 경우 자동으로 관리자가 되며 패리티 노드가 유효성 검사기로 나열 됩니다. 네트워크에 가입 하는 경우 과반수 (50% 이상)로 관리자에 게 투표 해야 합니다. 기존 관리 집합의입니다. 관리자가 되지 않도록 선택한 경우에도 노드는 블록 체인을 동기화 하 고 유효성을 검사 합니다. 그러나 블록 생성 프로세스에는 참여 하지 않습니다. 관리자가 될 투표 프로세스를 시작 하려면 **추천** 를 선택 하 고 Ethereum 주소와 별칭을 입력 합니다.

![지정](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>후보

**후보** 탭을 선택 하면 현재 후보 관리자 집합이 표시 됩니다.  현재 관리자의 과반수 투표에 도달 하면 후보는 관리자로 승격 됩니다.  후보에 투표 하려면 행을 선택 하 고 **투표** 를 선택 합니다. 투표를 변경 하는 경우 후보를 선택 하 고 **Rescind 투표** 를 선택 합니다.

![후보](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>관리자

**관리자** 탭은 현재 관리자 집합을 표시 하 고 투표 기능을 제공 합니다.  관리자가 50%를 초과 하면 네트워크에서 관리자 권한으로 제거 됩니다. 관리자가 소유 하는 모든 유효성 검사기 노드는 유효성 검사기 상태를 상실 하 고 네트워크에서 트랜잭션 노드가 됩니다. 관리자는 여러 가지 이유로 제거 될 수 있습니다. 그러나 미리 정책에 동의 하는 것은 컨소시엄의 것입니다.

![관리자](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>유효성 검사기

**유효성 검사기** 탭을 선택 하면 인스턴스에 대 한 현재 배포 된 패리티 노드와 현재 상태 (노드 유형)가 표시 됩니다. 이 보기는 현재 배포 된 컨소시엄 멤버를 나타내므로 각 consortium 구성원에는이 목록에 있는 다른 유효성 검사기 집합이 있습니다. 인스턴스를 새로 배포 하 고 유효성 검사기를 추가 하지 않은 경우에는 **유효성 검사기를 추가할** 수 있는 옵션이 제공 됩니다. 유효성 검사기를 추가 하면 자동으로 지역적으로 분산 된 패리티 노드 집합이 선택 되 고 유효성 검사기 집합에 할당 됩니다. 허용 된 용량 보다 더 많은 노드를 배포한 경우 나머지 노드는 네트워크의 트랜잭션 노드가 됩니다.

각 유효성 검사기의 주소는 Azure의 [ID 저장소](#identity-store)를 통해 자동으로 할당됩니다.  노드가 다운 되 면 해당 id를 내어 줍니다 배포의 다른 노드가 해당 위치를 사용할 수 있습니다. 이 프로세스를 통해 합의 참여를 항상 사용할 수 있습니다.

![유효성 검사기](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>컨소시엄 이름

모든 관리자가 컨소시엄 이름을 업데이트할 수 있습니다.  왼쪽 위에서 기어 아이콘을 선택 하 여 컨소시엄 이름을 업데이트 합니다.

### <a name="account-menu"></a>계정 메뉴

오른쪽 위에서은 Ethereum 계정 별칭 및 identicon입니다.  관리자 인 경우 별칭을 업데이트할 수 있습니다.

![계정](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="support-and-feedback"></a>지원 및 피드백<a id="tutorials"></a>

Azure Blockchain 뉴스의 경우 [Azure Blockchain 블로그](https://azure.microsoft.com/blog/topics/blockchain/)를 방문하여 Azure Blockchain 엔지니어링 팀의 블록체인 서비스 제공 및 정보를 최신 상태로 유지하세요.

제품 피드백을 제공하거나 새로운 기능을 요청하려면 [블록체인에 대한 Azure 피드백 포럼](https://aka.ms/blockchainuservoice)을 통해 아이디어를 게시하거나 투표하세요.

### <a name="community-support"></a>커뮤니티 지원

Microsoft 엔지니어 및 Azure Blockchain 커뮤니티 전문가와 소통하세요.

* [Microsoft Q&질문 페이지](/answers/topics/azure-blockchain-workbench.html)입니다. 블록 체인 템플릿에 대 한 엔지니어링 지원은 배포 문제로 제한 됩니다.
* [Microsoft 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [스택 오버플로](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>다음 단계

Azure Blockchain 솔루션에 대 한 자세한 내용은 [Azure blockchain 설명서](../index.yml)를 참조 하세요.
