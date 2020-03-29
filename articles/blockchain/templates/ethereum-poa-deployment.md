---
title: Azure에 이더리움 인증 컨소시엄 솔루션 템플릿 배포
description: 이더리움 인증 컨소시엄 솔루션을 사용하여 Azure에서 다중 멤버 컨소시엄 이더리움 네트워크를 배포하고 구성합니다.
ms.date: 12/18/2019
ms.topic: article
ms.reviewer: coborn
ms.openlocfilehash: 7e9af5c501b58f6828360ee280440ea85698bf16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75387682"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Azure에 이더리움 인증 컨소시엄 솔루션 템플릿 배포

[이더리움 인증 컨소시엄 미리 보기 Azure 솔루션 템플릿을](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) 사용하여 최소한의 Azure 및 이더리움 지식으로 다중 구성원 컨소시엄 증명 이더리움 네트워크를 배포, 구성 및 제어할 수 있습니다.

솔루션 템플릿은 각 컨소시엄 구성원이 Microsoft Azure 계산, 네트워킹 및 저장소 서비스를 사용하여 블록 체인 네트워크 공간을 프로비전하는 데 사용할 수 있습니다. 각 컨소시엄 구성원의 네트워크 사용 공간은 응용 프로그램 또는 사용자가 이더리움 트랜잭션을 제출하기 위해 상호 작용할 수 있는 로드 균형 유효성 검사기 노드 집합으로 구성됩니다.

## <a name="choose-an-azure-blockchain-solution"></a>Azure 블록체인 솔루션 선택

이더리움 증명 컨소시엄 솔루션 템플릿을 사용하기 전에 시나리오를 사용 가능한 Azure Blockchain 옵션의 일반적인 사용 사례와 비교합니다.

옵션 | 서비스 모델 | 일반적인 사용 사례
-------|---------------|-----------------
솔루션 템플릿 | IaaS | 솔루션 템플릿은 완전히 구성된 블록 체인 네트워크 토폴로지프로프로비전하는 데 사용할 수 있는 Azure 리소스 관리자 템플릿입니다. 템플릿은 지정된 블록 체인 네트워크 유형에 대한 Microsoft Azure 계산, 네트워킹 및 저장소 서비스를 배포하고 구성합니다.
[Azure Blockchain 서비스](../service/overview.md) | PaaS | Azure 블록 체인 서비스 미리 보기는 컨소시엄 블록 체인 네트워크의 형성, 관리 및 거버넌스를 단순화합니다. PaaS, 컨소시엄 관리 또는 계약 및 트랜잭션 개인 정보가 필요한 솔루션에 Azure 블록 체인 서비스를 사용합니다.
[Azure Blockchain Workbench](../workbench/overview.md) | 이아와 파스 | Azure Blockchain Workbench 미리 보기는 비즈니스 프로세스 및 데이터를 다른 조직과 공유하기 위해 블록체인 애플리케이션을 만들고 배포할 수 있도록 설계된 Azure 서비스 및 기능 컬렉션입니다. 블록 체인 솔루션 또는 블록 체인 응용 프로그램 개념 증명을 프로토 타이핑하기 위해 Azure 블록 체인 워크 벤치를 사용합니다.

## <a name="solution-architecture"></a>솔루션 아키텍처

이더리움 솔루션 템플릿을 사용하여 단일 또는 다중 리전 기반 의 이더리움 증명 컨소시엄 네트워크를 배포할 수 있습니다.

![배포 아키텍처](./media/ethereum-poa-deployment/deployment-architecture.png)

각 컨소시엄 멤버 배포에는 다음이 포함됩니다.

* PoA 유효성 검사기 실행용 가상 머신
* RPC, 피어링 및 거버넌스 DApp 요청을 배포하기 위한 Azure 로드 밸러저
* 유효성 검사기 ID 보안을 위한 Azure Key Vault
* 지속적인 네트워크 정보 호스팅 및 임대 조정을 위한 Azure Storage
* 로그 및 성능 통계를 집계하기 위한 Azure Monitor
* 프라이빗 VNet 간 VPN 연결을 허용하기 위한 VNet 게이트웨이(선택 사항)

기본적으로 RPC 및 피어링 엔드포인트는 공용 IP를 통해 액세스할 수 있으므로 구독 및 클라우드 간 연결을 간소화할 수 있습니다. 응용 프로그램 수준 액세스 제어의 경우 [패리티의 권한 부여 계약을](https://wiki.parity.io/Permissioning)사용할 수 있습니다. VNet 게이트웨이를 활용하여 구독 간 연결을 활용하는 VPN 뒤에 배포된 네트워크가 지원됩니다. VPN 및 VNet 배포는 더 복잡하기 때문에 솔루션을 프로토타이핑할 때 공용 IP 모델로 시작할 수 있습니다.

Docker 컨테이너는 안정성과 모듈성을 위해 사용됩니다. Azure 컨테이너 레지스트리는 각 배포의 일부로 버전이 있는 이미지를 호스팅하고 제공하는 데 사용됩니다. 컨테이너 이미지는 다음과 같이 구성됩니다.

* 오케스트레이터 - ID 및 거버넌스 계약을 생성합니다. ID 저장소에 ID를 저장합니다.
* 패리티 클라이언트 - ID 저장소에서 ID를 임대합니다. 동료를 발견하고 연결합니다.
* EthStats 에이전트 - RPC를 통해 로컬 로그 및 통계를 수집하고 정보를 Azure 모니터로 푸시합니다.
* 거버넌스 DApp - 거버넌스 계약과 상호 작용하기 위한 웹 인터페이스입니다.

### <a name="validator-nodes"></a>유효성 검사기 노드

인증 증명 프로토콜에서 유효성 검사기 노드는 기존의 채굴기 노드를 대신합니다. 각 유효성 검사기는 블록 생성 프로세스에 참여할 수 있는 고유한 이더리움 ID를 가지고 있습니다. 각 컨소시엄 멤버는 지리적 중복을 위해 5개 지역에 걸쳐 둘 이상의 유효성 검사기 노드를 프로비전할 수 있습니다. 유효성 검사기 노드는 다른 유효성 검사기 노드와 통신하여 기본 분산 원장의 상태에 관한 합의에 도달합니다. 네트워크에 대한 공정한 참여를 보장하기 위해 각 컨소시엄 구성원은 네트워크의 첫 번째 구성원보다 더 많은 유효성 검사기를 사용할 수 없습니다. 예를 들어 첫 번째 멤버가 세 개의 유효성 검사기를 배포하는 경우 각 멤버에는 최대 3개의 유효성 검사기만 있을 수 있습니다.

### <a name="identity-store"></a>ID 저장소

ID 저장소는 생성된 이더리움 ID를 안전하게 보유하는 각 멤버의 구독에 배포됩니다. 각 유효성 검사기의 경우 오케스트레이션 컨테이너는 이더리움 개인 키를 생성하여 Azure Key Vault에 저장합니다.

## <a name="deploy-ethereum-consortium-network"></a>이더리움 컨소시엄 네트워크 구축

이 워크에서 다자간 이더리움 컨소시엄 네트워크를 만들고 있다고 가정해 보겠습니다. 다음 흐름은 다자간 배포의 예입니다.

1. 세 명의 멤버가 각각 MetaMask를 사용하여 Ethereum 계정을 생성합니다.
1. *회원 A는* 이더리움 PoA를 배포하여 이더리움 공개 주소를 제공합니다.
1. *멤버 A*가 *멤버 B* 및 *멤버 C*에게 컨소시엄 URL을 제공합니다.
1. *멤버 B* 및 *멤버 C*가 Ethereum PoA를 배포하여 해당 Ethereum 공용 주소 및 *멤버 A*의 컨소시엄 URL을 제공합니다.
1. *멤버 A*가 *멤버 B*에게 관리자로 투표합니다.
1. *멤버 A* 및 *멤버 B*가 모두 *멤버 C*에게 관리자로 투표합니다.

다음 섹션에서는 네트워크에서 첫 번째 구성원의 공간을 구성하는 방법을 보여 주며, 이 섹션에서는 첫 번째 멤버의 공간을 구성하는 방법을 보여 준다.

### <a name="create-resource"></a>리소스 만들기

Azure [포털에서](https://portal.azure.com)왼쪽 상단 모서리에 **있는 리소스 만들기를** 선택합니다.

**블록 체인** > **이더리움 기관 증명 컨소시엄 (미리보기)을 선택하십시오.**

### <a name="basics"></a>기본 사항

**Basics에서**모든 배포에 대한 표준 매개 변수에 대한 값을 지정합니다.

![기본 사항](./media/ethereum-poa-deployment/basic-blade.png)

매개 변수 | 설명 | 예제 값
----------|-------------|--------------
새 네트워크 만들기 또는 기존 네트워크 가입 | 새 컨소시엄 네트워크를 만들거나 기존 컨소시엄 네트워크에 참여할 수 있습니다. 기존 네트워크에 가입하려면 추가 매개 변수가 필요합니다. | 새로 만들기
메일 주소 | 배포가 완료되면 배포에 대한 정보가 포함된 전자 메일 알림을 받습니다. | 유효한 이메일 주소
VM 사용자 이름 | 배포된 각 VM의 관리자 사용자 이름 | 영숫자 1-64자
인증 유형 | 가상 머신을 인증하는 방법. | 암호
암호 | 배포된 각 가상 머신의 관리자 계정 암호. 모든 VM은 처음에는 동일한 암호를 가있습니다. 프로비저닝 후 암호를 변경할 수 있습니다. | 12-72자 
Subscription | 컨소시엄 네트워크를 배포하는 구독 |
리소스 그룹| 컨소시엄 네트워크를 배포하는 리소스 그룹. | myResourceGroup
위치 | 리소스 그룹에 대한 Azure 지역입니다. | 미국 서부 2

**확인**을 선택합니다.

### <a name="deployment-regions"></a>배포 지역

*배포 영역에서*각 지역의 지역 및 위치 수를 지정합니다. 최대 5개 지역에서 배포할 수 있습니다. 첫 번째 지역은 Basics 섹션의 리소스 그룹 위치와 일치해야 *합니다.* 개발 또는 테스트 네트워크의 경우 멤버당 단일 리전을 사용할 수 있습니다. 프로덕션의 경우 고가용성을 위해 두 개 이상의 지역에 배포합니다.

![배포 지역](./media/ethereum-poa-deployment/deployment-regions.png)

매개 변수 | 설명 | 예제 값
----------|-------------|--------------
지역 수|컨소시엄 네트워크를 배포하는 지역 수| 2
첫 번째 지역 | 컨소시엄 네트워크를 배포하는 첫 번째 지역 | 미국 서부 2
두 번째 지역 | 컨소시엄 네트워크를 배포하는 두 번째 지역입니다. 영역 수가 두 개 이상일 때 추가 영역이 표시됩니다. | 미국 동부 2

**확인**을 선택합니다.

### <a name="network-size-and-performance"></a>네트워크 크기 및 성능

*네트워크 크기 및 성능에서*컨소시엄 네트워크 크기에 대한 입력을 지정합니다. 유효성 검사기 노드 저장소 크기는 블록 체인의 잠재적 인 크기를 결정합니다. 배포 후 크기를 변경할 수 있습니다.

![네트워크 크기 및 성능](./media/ethereum-poa-deployment/network-size-and-performance.png)

매개 변수 | 설명 | 예제 값
----------|-------------|--------------
부하가 분산된 유효성 검사기 노드 수 | 네트워크의 일부로 프로비전할 유효성 검사기 노드의 수입니다. | 2
유효성 검사기 노드 스토리지 성능 | 배포된 각 유효성 검사기 노드에 대한 관리 디스크 유형입니다. 가격에 대한 자세한 내용은 [스토리지 가격 을](https://azure.microsoft.com/pricing/details/managed-disks/) 참조하십시오. | 표준 SSD
유효성 검사기 노드 가상 머신 크기 | 유효성 검사기 노드에 사용되는 가상 머신 크기입니다. 가격에 대한 자세한 내용은 [가상 컴퓨터 가격 책정을](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 참조하십시오. | 표준 D2 v3

가상 컴퓨터 및 스토리지 계층은 네트워크 성능에 영향을 미칩니다.  다음 표를 사용하여 비용 효율성을 선택할 수 있습니다.

가상 머신 SKU|스토리지 계층|Price|처리량|대기 시간
---|---|---|---|---
F1|표준 SSD|low|low|high
D2_v3|표준 SSD|중간|중간|중간
F16s|프리미엄 SSD|high|high|low

**확인**을 선택합니다.

### <a name="ethereum-settings"></a>Ethereum 설정

*이더리움 설정에서*이더리움 관련 구성 설정을 지정합니다.

![Ethereum 설정](./media/ethereum-poa-deployment/ethereum-settings.png)

매개 변수 | 설명 | 예제 값
----------|-------------|--------------
컨소시엄 멤버 ID | 컨소시엄 네트워크에 참여하는 각 멤버와 연결된 ID입니다. 충돌을 피하기 위해 IP 주소 공간을 구성하는 데 사용됩니다. 개인 네트워크의 경우 멤버 ID는 동일한 네트워크의 여러 조직에서 고유해야 합니다.  고유 멤버 ID는 같은 조직이 여러 지역에 배포하는 경우에도 필요합니다. 충돌이 발생하지 않도록 다른 조인 멤버와 공유해야 하므로 이 매개변수의 값을 기록해 둡을 확인합니다. 유효한 범위는 0부터 255사이입니다. | 0
네트워크 ID | 배포하는 컨소시엄 Ethereum 네트워크의 네트워크 ID입니다. 각 Ethereum 네트워크에는 공용 네트워크에 대한 ID를 1로 하는 자체의 네트워크 ID가 있습니다. 유효한 범위는 5 ~ 999,999,999입니다. | 10101010
관리자 Ethereum 주소 | PoA 거버넌스에 참여하는 데 사용되는 이더리움 계정 주소입니다. 메타마스크를 사용하여 이더리움 주소를 생성할 수 있습니다. |
고급 옵션 | Ethereum 설정에 대한 고급 옵션 | 사용
공용 IP를 사용하여 배포 | 개인 VNet을 선택하면 네트워크가 VNet 게이트웨이 뒤에 배포되고 피어링 액세스가 제거됩니다. 프라이빗 VNet의 경우 모든 구성원이 연결이 호환되려면 VNet 게이트웨이를 사용해야 합니다. | 공용 IP
블록 가스 제한 | 네트워크의 시작 블록 가스 한계입니다. | 50000000
블록 다시 봉인 기간(초) | 네트워크에 트랜잭션이 없을 때 빈 블록이 작성되는 빈도입니다. 빈도가 높으면 블록이 더 빨리 작성되지만 스토리지 비용이 증가합니다. | 15
거래 권한 계약 | 트랜잭션 권한 지정 계약에 대한 바이트 코드입니다. 스마트 계약 배포 및 실행을 허용된 이더리움 계정 목록으로 제한합니다. |

**확인**을 선택합니다.

### <a name="monitoring"></a>모니터링

모니터링을 사용하면 네트워크에 대한 로그 리소스를 구성할 수 있습니다. 모니터링 에이전트는 네트워크에서 유용한 메트릭 및 로그를 수집하고 표시하여 네트워크 상태를 신속하게 확인하거나 디버그 문제를 확인할 수 있습니다.

![Azure Monitor](./media/ethereum-poa-deployment/azure-monitor.png)

매개 변수 | 설명 | 예제 값
----------|-------------|--------------
모니터링 | 모니터링을 활성화하는 옵션 | 사용
기존 Azure 모니터 로그에 연결 | 새 Azure Monitor 로그 인스턴스를 만들거나 기존 인스턴스에 참여하는 옵션 | 새로 만들기
위치 | 새 인스턴스가 배포되는 지역 | 미국 동부
기존 로그 분석 작업 영역 ID(기존 Azure 모니터 로그에 연결 = 기존 가입)|기존 Azure 모니터 로그 인스턴스의 작업 영역 ID||해당 없음
기존 로그 분석 기본 키(기존 Azure 모니터 로그에 연결 = 기존 가입)|기존 Azure Monitor 로그 인스턴스에 연결하는 데 사용되는 기본 키||해당 없음

**확인**을 선택합니다.

### <a name="summary"></a>요약

요약을 클릭하여 지정된 입력을 검토하고 기본 배포 전 유효성 검사를 실행합니다. 배포하기 전에 템플릿 및 매개 변수를 다운로드할 수 있습니다.

배포할 **만들기를** 선택합니다.

배포에 VNet 게이트웨이가 포함된 경우 배포에 최대 45~50분이 걸릴 수 있습니다.

## <a name="deployment-output"></a>배포 출력

배포가 완료되면 Azure 포털을 사용하여 필요한 매개 변수에 액세스할 수 있습니다.

### <a name="confirmation-email"></a>확인 이메일

전자 메일[주소(기본 섹션)를](#basics)제공하는 경우 배포 정보와 이 설명서에 대한 링크가 포함된 전자 메일이 전송됩니다.

![배포 이메일](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>포털

배포가 성공적으로 완료되고 모든 리소스가 프로비전되면 리소스 그룹의 출력 매개 변수를 볼 수 있습니다.

1. 포털의 리소스 그룹으로 이동합니다.
1. **배포 > 개요를 선택합니다.**

    ![리소스 그룹 개요](./media/ethereum-poa-deployment/resource-group-overview.png)

1. **microsoft-azure-blockchain.azure-blockchain-에테르-...** 배포를 선택합니다.
1. 출력 섹션을 **선택합니다.**

    ![배포 출력](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>컨소시엄 확장

컨소시엄을 확장하려면 먼저 실제 네트워크를 연결해야 합니다. VPN 뒤에 배포하는 경우 [VNet 게이트웨이 연결](#connecting-vnet-gateways) 섹션에서 새 구성원 배포의 일부로 네트워크 연결을 구성합니다. 배포가 완료되면 [거버넌스 DApp을](#governance-dapp) 사용하여 네트워크 관리자가 됩니다.

### <a name="new-member-deployment"></a>새 멤버 배포

다음 정보를 조인 멤버와 공유합니다. 이 정보는 배포 후 전자 메일 또는 포털 배포 출력에서 찾을 수 있습니다.

* 컨소시엄 데이터 URL
* 배포한 노드 수
* VNet 게이트웨이 리소스 ID(VPN을 사용하는 경우)

배포 멤버는 다음 지침을 사용하여 네트워크 존재를 배포할 때 동일한 이더리움 권한 증명 컨소시엄 솔루션 템플릿을 사용해야 합니다.

* **기존 조인**을 선택합니다.
* 공정한 표현을 보장하기 위해 네트워크의 나머지 멤버와 동일한 수의 유효성 검사기 노드를 선택합니다.
* 동일한 관리자 이더리움 주소 사용
* *이더리움 설정에서* 제공된 *컨소시엄 데이터 URL* 사용
* 네트워크의 나머지 부분이 VPN 뒤에 있는 경우 고급 섹션에서 **비공개 VNet을** 선택합니다.

### <a name="connecting-vnet-gateways"></a>VNet 게이트웨이 연결

이 섹션은 개인 VNet을 사용하여 배포한 경우에만 필요합니다. 공용 IP 주소를 사용하는 경우 이 섹션을 건너뛸 수 있습니다.

개인 네트워크의 경우 다른 구성원이 VNet 게이트웨이 연결을 통해 연결됩니다. 구성원이 네트워크에 가입하여 트랜잭션 트래픽을 볼 수 있도록 하려면 기존 구성원이 VPN 게이트웨이에서 최종 구성을 수행하여 연결을 수락해야 합니다. 연결 설정 될 때까지 조인 멤버의 이더리움 노드 실행 되지 않습니다. 단일 실패 지점의 가능성을 줄이려면 컨소시엄에서 중복 네트워크 연결을 만듭니다.

새 멤버가 배포되면 기존 멤버는 새 멤버에 대한 VNet 게이트웨이 연결을 설정하여 양방향 연결을 완료해야 합니다. 기존 구성원은 다음을 필요로 합니다.

* 연결 멤버의 VNet 게이트웨이 ResourceID입니다. [배포 출력을](#deployment-output)참조하십시오.
* 공유 연결 키입니다.

기존 멤버는 다음 PowerShell 스크립트를 실행하여 연결을 완료해야 합니다. 포털의 오른쪽 상단 탐색 모음에 있는 Azure 클라우드 셸을 사용할 수 있습니다.

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

## <a name="service-monitoring"></a>서비스 모니터링

배포 전자 메일의 링크를 따르거나 배포 출력 [OMS_PORTAL_URL]에서 매개 변수를 찾아Azure Monitor 포털을 찾을 수 있습니다.

포털에서 먼저 고급 네트워크 통계와 노드 개요를 표시합니다.

![카테고리 모니터링](./media/ethereum-poa-deployment/monitor-categories.png)

**노드 개요를** 선택하면 노드별 인프라 통계가 표시됩니다.

![노드 통계](./media/ethereum-poa-deployment/node-stats.png)

**네트워크 통계를** 선택하면 이더리움 네트워크 통계가 표시됩니다.

![네트워크 통계](./media/ethereum-poa-deployment/network-stats.png)

### <a name="sample-kusto-queries"></a>샘플 쿠스토 쿼리

모니터링 로그를 쿼리하여 오류 또는 설정 임계값 경고를 조사할 수 있습니다. 다음 쿼리는 *로그 검색* 도구에서 실행할 수 있는 예제입니다.

하나 이상의 유효성 검사기 쿼리에 의해 보고된 목록 블록은 체인 포크를 찾는 데 유용할 수 있습니다.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

5분 버킷평균이 지정된 유효성 검사기 노드의 평균 피어 수를 가져옵니다.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

## <a name="ssh-access"></a>SSH 액세스

보안상의 이유로 SSH 포트 액세스는 기본적으로 네트워크 그룹 보안 규칙에 의해 거부됩니다. PoA 네트워크의 가상 시스템 인스턴스에 액세스하려면 다음 보안이 *허용*규칙으로 변경해야 합니다.

1. Azure 포털에서 배포된 리소스 그룹의 **개요** 섹션으로 이동합니다.

    ![SSH 개요](./media/ethereum-poa-deployment/ssh-overview.png)

1. 액세스하려는 VM 지역에 대한 **네트워크 보안 그룹을** 선택합니다.

    ![SSH NSG](./media/ethereum-poa-deployment/ssh-nsg.png)

1. **ssh** 허용 규칙을 선택합니다.

    ![allow-ssh](./media/ethereum-poa-deployment/ssh-allow.png)

1. **허용하기** 위한 변경 **작업**

    ![SSH 사용 허용](./media/ethereum-poa-deployment/ssh-enable-allow.png)

1. **저장**을 선택합니다. 변경 사항이 적용되려면 몇 분 정도 걸릴 수 있습니다.

제공된 관리자 사용자 이름 및 암호/SSH 키를 사용하여 SSH를 통해 유효성 검사기 노드의 가상 컴퓨터에 원격으로 연결할 수 있습니다. 첫 번째 유효성 검사기 노드에 액세스하는 SSH 명령이 템플릿 배포 출력에 나열됩니다. 예를 들어:

``` bash
ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com.
```

추가 트랜잭션 노드로 이동하려면 포트 번호를 하나씩 늘입니다.

두 개 이상의 지역에 배포한 경우 해당 지역의 로드 밸러버의 DNS 이름 또는 IP 주소로 명령을 변경합니다. 다른 지역의 DNS 이름 또는 IP 주소를 찾으려면 명명 규칙 ** \* \* \* \* \*-lbpip-reg가\# ** 있는 리소스를 찾아 해당 DNS 이름 및 IP 주소 속성을 봅니다.

## <a name="azure-traffic-manager-load-balancing"></a>Azure Traffic Manager 부하 분산

Azure Traffic Manager는 들어오는 트래픽을 여러 지역의 여러 배포로 라우팅하여 가동 중지 시간을 줄이고 PoA 네트워크의 응답성을 향상시킬 수 있습니다. 기본 제공 상태 검사 및 자동 경로 변경은 RPC 끝점과 거버넌스 DApp의 고가용성을 보장하는 데 도움이 됩니다. 이 기능은 여러 지역에 배포하고 프로덕션을 준비하는 경우에 유용합니다.

트래픽 관리자를 사용하여 자동 장애 조치(failover)를 통해 PoA 네트워크 가용성을 개선합니다. 또한 트래픽 관리자를 사용하여 최종 사용자를 네트워크 대기 시간이 가장 낮은 Azure 위치로 라우팅하여 네트워크 응답성을 높일 수도 있습니다.

Traffic Manager 프로필을 만들려는 경우 네트워크에 액세스하기 위해 프로필의 DNS 이름을 사용할 수 있습니다. 다른 컨소시엄 멤버가 네트워크에 추가되면 Traffic Manager를 사용하여 배포된 유효성 검사기에서 부하를 분산시킬 수도 있습니다.

### <a name="creating-a-traffic-manager-profile"></a>Traffic Manager 프로필 만들기

1. Azure [포털에서](https://portal.azure.com)왼쪽 상단 모서리에 **있는 리소스 만들기를** 선택합니다.
1. 트래픽 **관리자 프로필**검색 .

    ![Azure 트래픽 관리자 검색](./media/ethereum-poa-deployment/traffic-manager-search.png)

    프로필에 고유한 이름을 지정하고 PoA 배포에 사용된 리소스 그룹을 선택합니다.

1. 배포할 **만들기를** 선택합니다.

    ![트래픽 관리자 만들기](./media/ethereum-poa-deployment/traffic-manager-create.png)

1. 배포되면 리소스 그룹에서 인스턴스를 선택합니다. 트래픽 관리자에 액세스하는 DNS 이름은 개요 탭에서 찾을 수 있습니다.

    ![Traffic Manager DNS 찾기](./media/ethereum-poa-deployment/traffic-manager-dns.png)

1. **끝점** 탭을 선택하고 **추가** 단추를 선택합니다.
1. 그런 다음 엔드포인트에 고유한 이름을 지정하고
1. **대상 리소스 유형의** **경우 공용 IP 주소를**선택합니다.
1. 첫 번째 리전의 로드 밸런서의 공용 IP 주소를 선택합니다.

    ![Traffic Manager 라우팅](./media/ethereum-poa-deployment/traffic-manager-routing.png)

배포된 네트워크의 각 지역에 대해 반복합니다. 끝점이 **활성화된** 상태가 되면 트래픽 관리자의 DNS 이름으로 자동으로 로드되고 지역 균형이 조정됩니다. 이제 문서의 다른 단계에서 [CONSORTIUM_DATA_URL] 매개 변수 대신 이 DNS 이름을 사용할 수 있습니다.

## <a name="data-api"></a>데이터 API

각 컨소시엄 멤버는 다른 멤버가 네트워크에 연결하는 데 필요한 정보를 호스팅합니다. 연결의 용이성을 활성화하기 위해 각 구성원은 데이터 API 끝점에서 연결 정보 집합을 호스팅합니다.

기존 멤버는 구성원의 배포 전에 [CONSORTIUM_DATA_URL]을 제공합니다. 배포 시 조인 멤버는 다음 엔드포인트의 JSON 인터페이스에서 정보를 검색합니다.

`<CONSORTIUM_DATA_URL>/networkinfo`

응답에는 멤버 가입에 유용한 정보(제네시스 블록, 유효성 검사기 세트 계약 ABI, 부트노드) 및 기존 멤버에 유용한 정보(유효성 검사기 주소)가 포함되어 있습니다. 이 표준화를 사용하여 클라우드 공급자 간에 컨소시엄을 확장할 수 있습니다. 이 API는 다음 구조로 JSON 형식의 응답을 반환합니다.

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

## <a name="governance-dapp"></a>거버넌스 DApp

인증 증명의 중심에는 분산 거버넌스가 있습니다. 권한 증명은 네트워크를 정상 상태로 유지하기 위해 허용된 네트워크 기관 목록에 의존하므로 이 권한 목록을 수정할 수 있는 공정한 메커니즘을 제공하는 것이 중요합니다. 각 배포에는 이 허용된 목록의 온체인 거버넌스를 위한 스마트 계약 및 포털 집합이 함께 제공됩니다. 제안된 변경이 컨소시엄 멤버의 과반수 표결에 도달하면 해당 변경이 시행됩니다. 투표를 통해 새로운 합의 참가자를 추가하거나 손상된 참가자를 정직한 네트워크를 장려하는 투명한 방식으로 제거할 수 있습니다.

거버넌스 DApp는 네트워크에서 기관을 관리하는 데 사용되는 미리 배포된 [스마트 계약](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) 집합 및 웹 애플리케이션입니다. 당국은 관리 ID 및 유효성 검사기 노드로 나뉩니까?
관리자는 유효성 검사기 노드 집합에 합의 참여를 위임할 수 있습니다. 관리자는 네트워크 내부 또는 외부에서 다른 관리자에게 투표할 수도 있습니다.

![거버넌스 DApp](./media/ethereum-poa-deployment/governance-dapp.png)

* **분산 된 거버넌스 :** 네트워크 기관의 변경 사항은 일부 관리자가 연쇄 투표를 통해 관리합니다.
* **유효성 검사기 위임:** 당국은 각 PoA 배포에 설정된 유효성 검사기 노드를 관리할 수 있습니다.
* **감사 가능한 변경 기록:** 각 변경 사항은 투명성과 감사 가능성을 제공하는 블록 체인에 기록됩니다.

### <a name="getting-started-with-governance"></a>거버넌스 시작

거버넌스 DApp을 통해 모든 종류의 거래를 수행하려면 이더리움 지갑을 사용해야 합니다. 가장 간단한 방법은 [MetaMask와](https://metamask.io)같은 브라우저 내 지갑을 사용하는 것입니다. 그러나 이러한 스마트 계약은 네트워크에 배포되므로 거버넌스 계약에 대한 상호 작용을 자동화할 수도 있습니다.

MetaMask를 설치한 후 브라우저에서 거버넌스 DApp로 이동합니다.  배포 출력에서 Azure 포털을 통해 URL을 찾을 수 있습니다.  브라우저 내 지갑이 설치되어 있지 않으면 작업을 수행할 수 없습니다. 그러나 관리자 상태를 볼 수 있습니다.  

### <a name="becoming-an-admin"></a>관리자 되기

네트워크에 배포된 첫 번째 구성원인 경우 자동으로 관리자가 되고 패리티 노드가 유효성 검사기로 나열됩니다. 네트워크에 가입하는 경우 과반수(50% 이상)의 관리자로 투표해야 합니다. 기존 관리자 집합의 관리자가 되지 않도록 선택하면 노드가 여전히 동기화되고 블록 체인의 유효성을 검사합니다. 그러나 블록 생성 프로세스에는 참여하지 않습니다. 투표 프로세스를 시작하여 관리자가 되려면 **지명을** 선택하고 이더리움 주소와 별칭을 입력합니다.

![지정](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>후보

**후보** 탭을 선택하면 현재 후보 관리자 집합이 표시됩니다.  후보자가 현재 관리자의 과반수 투표에 도달하면 후보자는 관리자로 승격됩니다.  후보자에 투표하려면 행을 선택하고 **에서 투표를**선택합니다. 투표에 대한 생각이 바뀌면 후보를 선택하고 **철회 투표를**선택하십시오.

![후보](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>관리자

**관리자** 탭에는 현재 관리자 집합이 표시되고 투표할 수 있는 기능이 제공됩니다.  관리자가 50% 이상의 지원을 잃으면 네트워크에서 관리자로 제거됩니다. 관리자가 소유한 모든 유효성 검사기 노드는 유효성 검사기 상태를 잃고 네트워크의 트랜잭션 노드가 됩니다. 관리자는 여러 가지 이유로 제거될 수 있습니다. 그러나 사전에 정책에 동의하는 것은 컨소시엄의 방침입니다.

![관리자](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>유효성 검사기

유효성 **검사기** 탭을 선택하면 인스턴스에 대해 배포된 현재 패리티 노드와 해당 상태(노드 유형)가 표시됩니다. 이 보기는 현재 배포된 컨소시엄 멤버를 나타내므로 각 컨소시엄 멤버는 이 목록에 다른 유효성 검사기 집합을 가있습니다. 인스턴스가 새로 배포되고 유효성 검사기를 추가하지 않은 경우 **유효성 검사기 추가**옵션을 사용할 수 있습니다. 유효성 검사기를 추가하면 지역적으로 균형 잡힌 패리티 노드 집합이 자동으로 선택되고 유효성 검사기 집합에 할당됩니다. 허용된 용량보다 더 많은 노드를 배포한 경우 나머지 노드는 네트워크의 트랜잭션 노드가 됩니다.

각 유효성 검사기의 주소는 Azure의 [ID 저장소](#identity-store)를 통해 자동으로 할당됩니다.  노드가 다운되면 해당 노드가 해당 ID를 포기하고 배포의 다른 노드가 해당 노드를 대신할 수 있습니다. 이 프로세스는 합의 참여가 높은 가용성을 보장합니다.

![유효성 검사기](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>컨소시엄 이름

모든 관리자는 컨소시엄 이름을 업데이트할 수 있습니다.  왼쪽 상단에 있는 기어 아이콘을 선택하여 컨소시엄 이름을 업데이트합니다.

### <a name="account-menu"></a>계정 메뉴

오른쪽 상단에 는 이더리움 계정 별칭과 iddenticon이 있습니다.  관리자인 경우 별칭을 업데이트할 수 있습니다.

![계정](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="ethereum-development"></a>이더리움 개발<a id="tutorials"></a>

스마트 계약을 컴파일, 배포 및 테스트하려면 이더리움 개발에 고려할 수 있는 몇 가지 옵션이 있습니다.
* [트러플 스위트](https://www.trufflesuite.com/docs/truffle/overview) - 고객 기반 이더리움 개발 환경
* [이더리움 리믹스](https://remix-ide.readthedocs.io/en/latest/index.html ) - 브라우저 기반 및 로컬 이더리움 개발 환경

### <a name="compile-deploy-and-execute-smart-contract"></a>스마트 계약 컴파일, 배포 및 실행

다음 예제에서는 간단한 스마트 계약을 만듭니다. 트러플을 사용하여 스마트 계약을 컴파일하고 블록 체인 네트워크에 배포합니다. 배포되면 트랜잭션을 통해 스마트 계약 함수를 호출합니다.

#### <a name="prerequisites"></a>사전 요구 사항

* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/)를 설치합니다. 트러플과 Web3에는 파이썬이 필요합니다. 경로에 파이썬을 포함하도록 설치 옵션을 선택합니다.
* 트러플 v5.0.5를 `npm install -g truffle@v5.0.5`설치합니다. Truffle을 사용하려면 [Node.js](https://nodejs.org), [Git](https://git-scm.com/)를 포함한 여러 도구를 설치해야 합니다. 자세한 내용은 [트러플 문서를](https://github.com/trufflesuite/truffle)참조하십시오.

### <a name="create-truffle-project"></a>Truffle 프로젝트 만들기

스마트 계약을 컴파일하고 배포하려면 송로버섯 프로젝트를 만들어야 합니다.

1. 명령 프롬프트 또는 셸을 엽니다.
1. `HelloWorld`라는 폴더를 만듭니다.
1. 디렉터리를 새 `HelloWorld` 폴더로 변경합니다.
1. 명령을 `truffle init`사용하여 새 송로버섯 프로젝트를 초기화합니다.

    ![새로운 트러플 프로젝트 만들기](./media/ethereum-poa-deployment/create-truffle-project.png)

### <a name="add-a-smart-contract"></a>스마트 계약 추가

트러플 프로젝트의 **계약** 하위 디렉토리에서 스마트 계약을 만듭니다.

1. 트러플 프로젝트의 `postBox.sol` **계약** 하위 디렉토리에 명명된 파일을 만듭니다.
1. **postBox.sol**에 다음 솔리드 코드를 추가합니다.

    ```javascript
    pragma solidity ^0.5.0;
    
    contract postBox {
        string message;
        function postMsg(string memory text) public {
            message = text;
        }
        function getMsg() public view returns (string memory) {
            return message;
        }
    }
    ```

### <a name="deploy-smart-contract-using-truffle"></a>트러플을 사용한 스마트 계약 배포

트러플 프로젝트에는 블록 체인 네트워크 연결 세부 사항에 대한 구성 파일이 포함되어 있습니다. 네트워크에 대한 연결 정보를 포함하도록 구성 파일을 수정합니다.

> [!WARNING]
> 네트워크를 통해 이더리움 개인 키를 보내지 마십시오. 각 트랜잭션을 로컬에서 먼저 서명한 다음, 네트워크를 통해 서명된 트랜잭션을 보내도록 합니다.

1. 블록 체인 네트워크를 배포 할 [때 사용되는 이더리움 관리자 계정에](#ethereum-settings)대한 니모닉 문구가 필요합니다. 메타마스크를 사용하여 계정을 만든 경우 메타마스크에서 니모닉을 검색할 수 있습니다. MetaMask 확장의 오른쪽 상단에 있는 관리자 계정 아이콘을 선택하고 **보안 & > 시드 단어 공개 > 보안 > 설정 선택합니다.**
1. 트러플 프로젝트의 `truffle-config.js` 내용을 다음 콘텐츠로 바꿉습니다. 자리 표시자 끝점 과 니모닉 값을 바꿉습니다.

    ```javascript
    const HDWalletProvider = require("truffle-hdwallet-provider");
    const rpc_endpoint = "<Ethereum RPC endpoint>";
    const mnemonic = "Twelve words you can find in MetaMask > Security & Privacy > Reveal Seed Words";

    module.exports = {
      networks: {
        development: {
          host: "localhost",
          port: 8545,
          network_id: "*" // Match any network id
        },
        poa: {
          provider: new HDWalletProvider(mnemonic, rpc_endpoint),
          network_id: 10101010,
          gasPrice : 0
        }
      }
    };
    ```

1. 트러플 HD 지갑 공급자를 사용 하 고 있기 때문에 `npm install truffle-hdwallet-provider --save`명령을 사용 하 여 프로젝트에 모듈을 설치 합니다.

트러플은 마이그레이션 스크립트를 사용하여 스마트 계약을 블록 체인 네트워크에 배포합니다. 새 스마트 계약을 배포하려면 마이그레이션 스크립트가 필요합니다.

1. 새 마이그레이션을 추가하여 새 계약을 배포합니다. 트뤼플 프로젝트의 `2_deploy_contracts.js` **마이그레이션** 하위 디렉터리에서 파일을 만듭니다.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = deployer => {
        deployer.deploy(postBox);
    };
    ```

1. 트뤼플 마이그레이션 명령을 사용하여 PoA 네트워크에 배포합니다. 트러플 프로젝트 디렉터리에서 명령 프롬프트에서 다음을 실행합니다.

    ```javascript
    truffle migrate --network poa
    ```

### <a name="call-a-smart-contract-function"></a>스마트 계약 기능 호출

스마트 계약이 배포되었으므로 함수를 호출하는 트랜잭션을 보낼 수 있습니다.

1. 트러플 프로젝트 디렉터리에서 `sendtransaction.js`.
1. **sendtransaction.js에**다음 내용을 추가합니다.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the postBox smart contract")
      postBox.deployed().then(function(instance) {
        console.log("Calling postMsg function for contract ", instance.address);
        return instance.postMsg("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. 트뤼플 실행 명령을 사용하여 스크립트를 실행합니다.

    ```javascript
    truffle exec sendtransaction.js --network poa
    ```

    ![트랜잭션을 통해 함수를 호출하는 스크립트 실행](./media/ethereum-poa-deployment/send-transaction.png)

## <a name="webassembly-wasm-support"></a>WebAssembly(WASM) 지원

WebAssembly 지원은 새로 배포된 PoA 네트워크에서 이미 사용하도록 설정되어 있습니다. 이를 통해 스마트 계약을 웹 어셈블리로 변환 컴파일되는 모든 언어(Rust, C, C++)로 개발할 수 있습니다. 자세한 내용은 다음을 참조하십시오: [패리티 테크의 웹 어셈블리](https://wiki.parity.io/WebAssembly-Home) 및 [자습서의](https://github.com/paritytech/pwasm-tutorial) 패리티 개요

## <a name="faq"></a>FAQ

### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>네트워크에 전송하지 않은 트랜잭션이 많이 있습니다. 이러한 트랜잭션은 어디서 오는 것인가요?

[개인 API](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html)를 잠금 해제하는 것은 안전하지 않습니다. 봇은 잠금 해제된 Ethereum 계정을 수신 대기하고 자금을 고갈시키려고 시도합니다. 봇은 이러한 계정에 실제 Ether가 있다고 가정하고 잔액을 가장 먼저 빼돌리려고 시도합니다. 개인 API는 네트워크에서 사용하도록 설정하지 않습니다. 대신 MetaMask와 같은 지갑을 수동으로 사용하거나 프로그래밍 방식으로 거래에 미리 서명하십시오.

### <a name="how-to-ssh-onto-a-vm"></a>VM에 SSH를 연결하려면 어떻게 할까요?

SSH 포트는 보안상의 이유로 표시되지 않습니다. [이 가이드](#ssh-access)에 따라 SSH 포트를 사용하도록 설정합니다.

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>멤버 또는 트랜잭션 노드를 설정하고 감사하려면 어떻게 할까요?

트랜잭션 노드는 네트워크를 통해 피어링되지만 합의에 참여하지 않는 패리티 클라이언트 집합입니다. 이러한 노드는 여전히 Ethereum 트랜잭션을 제출하고 스마트 계약 상태를 읽는 데 사용할 수 있습니다. 이 메커니즘은 네트워크의 비권한 컨소시엄 구성원에게 감사 기능을 제공하는 데 작동합니다. 이를 위해 [컨소시엄 성장의](#growing-the-consortium)단계를 따르십시오.

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>MetaMask 트랜잭션이 오래 걸리는 이유는 무엇인가요?

트랜잭션이 올바른 순서로 수신되도록 하기 위해 각 Ethereum 트랜잭션에는 증분 논스(nonce)가 제공됩니다. 다른 네트워크에서 MetaMask에서 계정을 사용한 경우 nonce 값을 재설정해야 합니다. 설정 아이콘 (세 개의 막대), 설정, 계정 재설정을 클릭합니다. 트랜잭션 기록이 지워지고, 이제 트랜잭션을 다시 제출할 수 있습니다.

### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>MetaMask에서 가스 요금을 지정해야 하나요?

인증 증명 컨소시엄에서는 Ethereum이 사용되지 않습니다. 따라서 MetaMask에서 거래를 제출할 때 가스 요금을 지정할 필요가 없습니다.

### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Azure OMS로의 프로비전 실패로 인해 배포가 실패하면 어떻게 해야 하나요?

모니터링은 선택적 기능입니다. Azure Monitor 리소스를 성공적으로 프로비전할 수 없기 때문에 배포가 실패하는 드문 경우지만 Azure Monitor 없이 다시 배포할 수 있습니다.

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>공용 IP 배포는 사설망 배포와 호환되나요?

아니요. 피어링은 양방향 통신이 필요하므로 전체 네트워크가 공용 또는 비공개여야 합니다.

### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>인증 증명의 예상 트랜잭션 처리량은 어떻게 되나요?

트랜잭션 처리량은 네트워크 토폴로지 및 트랜잭션 유형에 따라 크게 달라집니다. 단순 트랜잭션을 사용한 벤치마크 테스트 결과 여러 지역에 걸쳐 배포된 네트워크에서 초당 평균 트랜잭션 수는 400개였습니다.

### <a name="how-do-i-subscribe-to-smart-contract-events"></a>스마트 계약 이벤트를 구독하려면 어떻게 해야 하나요?

Ethereum 인증 증명에서는 이제 웹 소켓을 지원합니다.  배포 출력을 확인하여 웹 소켓 URL 및 포트를 찾습니다.

## <a name="next-steps"></a>다음 단계

더 많은 Azure 블록 체인 솔루션은 [Azure 블록 체인 설명서를](https://docs.microsoft.com/azure/blockchain/)참조하십시오.
