---
title: Ethereum 작업 증명 컨소시엄 솔루션 템플릿
description: Etherereum 작업 증명 컨소시엄 솔루션 템플릿을 사용하여 복수 멤버 컨소시엄 Ethereum 네트워크 배포 및 구성
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 823bea9bac8ff270d5b5c02e3b76a2f7236c9c99
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48241690"
---
# <a name="ethereum-proof-of-work-consortium-solution-template"></a>Ethereum 작업 증명 컨소시엄 솔루션 템플릿

Ethereum 작업 증명 컨소시엄 솔루션 템플릿은 Azure 및 Ethereum을 조금만 알면 복수 멤버 컨소시엄 Ethereum 네트워크를 더 쉽고 빠르게 배포 및 구성할 수 있도록 설계되었습니다.

Azure Portal을 통해 몇 가지 사용자 입력과 한 번 클릭으로 배포할 수 있으므로, 각 멤버는 전 세계 어디서나 Microsoft Azure Compute, 네트워킹 및 저장소 서비스를 사용하여 자신의 네트워크 사용 공간을 프로비전할 수 있습니다. 각 멤버의 네트워크 사용 공간은 응용 프로그램 또는 사용자가 상호 작용하여 제출할 수 있는 부하가 분산된 트랜잭션 노드 집합, 트랜잭션을 기록할 마이닝 노드 집합 및 VPN Gateway로 구성됩니다. 후속 연결 단계에서 게이트웨이들을 연결하여 완전히 구성된 복수 멤버 블록체인 네트워크를 만듭니다.

## <a name="about-blockchain"></a>블록체인 정보

블록체인 커뮤니티에 처음 들어온 경우, 이 솔루션의 릴리스는 Azure에서 이 기술을 쉽고 구성 가능한 방법으로 배울 좋은 기회입니다. 그러나 시작하려면 복수 멤버 컨소시엄 네트워크를 구축하기 전에 이 가이드형 연습을 사용하여 더 단순한 독립 실행형 Ethereum 네트워크 토폴로지를 배포하는 것이 좋습니다.

### <a name="mining-node-details"></a>마이닝 노드 세부 정보

트랜잭션을 마이닝하는 노드는 두 작업이 같은 리소스를 경합하지 않도록 트랜잭션을 수용하는 노드에서 분리됩니다.

컨소시엄 멤버는 관리 디스크의 지원을 받아 하나 이상의 마이닝 노드를 포함하는 최대 5개의 지역을 프로비전할 수 있습니다. 네트워크의 노드에 대한 동적 검색을 지원하기 위해 해당 지역에 있는 하나 이상의 노드는 부팅 노드로 구성됩니다. 마이닝 노드는 다른 마이닝 노드와 통신하여 기본 분산 원장의 상태에 관한 합의에 도달합니다. 응용 프로그램이 이러한 노드에 주의하거나 해당 노드와 통신할 필요가 없습니다. 개인 네트워크에 집중하면 마이닝 노드가 인바운드 공용 인터넷 트래픽에서 격리되어 두 번째 보호 계층이 추가됩니다. 아웃바운드 트래픽은 허용되지만 Ethereum 검색 포트로 향할 수는 없습니다.

모든 노드는 Go Ethereum(Geth) 클라이언트의 안정된 버전을 가지고 있으며 마이닝 노드가 되도록 구성됩니다. 사용자 지정 최초 블록을 제공하지 않은 경우, 모든 노드는 Ethereum 계정 암호로 보호되는 같은 Ethereum 주소 및 키 쌍을 사용합니다. 제공한 Ethereum 암호를 사용하여 각 마이닝 노드의 기본 계정(코인 베이스)을 생성합니다. 마이닝 노드는 마이닝함에 따라 이 계정에 추가된 요금을 수집합니다.

컨소시엄 멤버당 마이닝 노드 수는 원하는 네트워크의 전체 크기 및 각 멤버가 전용하는 해싱 파워의 양에 따라 달라집니다. 네트워크가 클수록 불공정 이익을 얻기 위해 해결해야 하는 노드 수가 더 많아집니다. 템플릿은 가상 머신 확장 집합을 사용하여 프로비전한 지역당 최대 15개의 마이닝 노드를 지원합니다.

### <a name="transaction-node-details"></a>트랜잭션 노드 세부 정보

컨소시엄 멤버는 부하가 분산된 트랜잭션 노드의 집합도 가지고 있습니다. 이러한 노드는 가상 네트워크 외부에서 연결할 수 있으므로 응용 프로그램은 이러한 노드를 사용하여 블록체인 네트워크 내에서 트랜잭션을 제출하거나 스마트 계약을 실행할 수 있습니다. 모든 노드는 Go Ethereum(Geth) 클라이언트의 안정된 버전을 가지고 있으며 분산 원장의 전체 원장을 유지하도록 구성됩니다. 사용자 지정 최초 블록을 제공하지 않은 경우, 이러한 노드는 Ethereum 계정 암호로 보호되는 같은 Ethereum 계정을 사용합니다.

트랜잭션 노드는 높은 가용성을 유지하기 위해 가용성 집합 내에서 부하가 분산됩니다. 템플릿은 가상 머신 확장 집합을 사용하여 프리비전한 트랜잭션 노드를 5개까지 지원합니다.

### <a name="log-analytics-details"></a>로그 분석 세부 정보

또한 각 배포는 새 Log Analytics 인스턴스를 만들거나 기존 인스턴스를 조인할 수도 있습니다. 따라서 배포된 네트워크를 구성하는 각 가상 머신의 여러 성능 메트릭을 모니터링할 수 있습니다.

## <a name="deployment-architecture"></a>배포 아키텍처

### <a name="description"></a>설명

이 솔루션 템플릿은 단일 또는 복수 지역 기반 복수 멤버 Ethereum 컨소시엄 네트워크를 배포할 수 있습니다. 각 지역의 가상 네트워크는 VNET 게이트웨이 및 연결 리소스를 사용하여 체인 토폴로지의 다른 지역에 연결됩니다. 또한 각 지역에 배포된 모든 마이너 및 트랜잭션 노드의 필수 정보를 포함하는 등록 기관을 프로비전합니다.

### <a name="standalone-and-consortium-leader-overview"></a>독립 실행형 및 컨소시엄 선행부 개요

![독립 실행형 및 컨소시엄 선행부 개요](./media/ethereum-deployment/leader-overview.png)

### <a name="joining-consortium-member-overview"></a>컨소시엄 멤버 조인 개요

![컨소시엄 멤버 조인 개요](./media/ethereum-deployment/member-overview.png)

## <a name="getting-started"></a>시작

이 프로세스를 실행하려면 여러 가상 머신 확장 집합 및 관리 디스크의 배포를 지원할 수 있는 Azure 구독이 필요합니다. 필요한 경우 시작하기 위해 무료 Azure 계정을 만듭니다.

구독이 보호된 후 Azure Portal로 이동합니다. **+ 리소스 만들기**, Marketplace(all 참조)를 선택하고 **Ethereum 작업 증명 컨소시엄**을 검색합니다.

템플릿 배포에서는 네트워크의 첫 번째 멤버의 사용 공간을 구성하는 과정을 안내합니다. 배포 흐름은 기본 사항, 작업 관리 스위트, 배포 지역, 네트워크 크기 및 성능, Ethereum 설정의 다섯 단계로 나눕니다.

### <a name="basics"></a>기본 사항

**기본 사항** 아래에서 구독, 리소스 그룹 등 배포에 대한 표준 매개 변수의 값 및 기본 가상 머신 속성을 지정합니다.

![기본 사항](./media/ethereum-deployment/sample-deployment.png)

매개 변수 이름|설명| 허용되는 값|기본값
---|---|---|---
새 네트워크를 만드시겠습니까, 아니면 기존 네트워크를 조인하시겠습니까?|새 네트워크 만들기 또는 기존 컨소시엄 네트워크 조인|새로 만들기 기존 조인|새로 만들기
컨소시엄의 일부가 될 네트워크를 배포하시겠습니까?|컨소시엄 네트워크를 사용하면 향후 배포에서 이 네트워크를 조인할 수 있음(위에서 *새로 만들기*를 선택하면 볼 수 있음)|독립 실행형 컨소시엄|독립 실행형
리소스 접두사 - |리소스 이름을 지정하는 기본으로 사용하는 문자열(영숫자 문자 2 ~ 4자)입니다. 리소스별 정보가 추가되는 동안 일부 리소스에 대한 문자열에 고유한 해시가 추가됩니다.|길이 2 ~ 4자의 영숫자 문자|해당 없음
VM 사용자 이름| 각 배포된 VM의 관리자 사용자 이름(영숫자 문자만)|1 ~ 64자 |gethadmin
인증 유형|가상 머신을 인증하는 방법. |암호 또는 SSH 공개 키|암호
암호(인증 형식 = 암호)|배포된 각 가상 머신에 대한 관리자 계정의 암호입니다. 암호는 다음 요구 사항 중 3 가지를 포함해야 합니다. 대문자 1자, 소문자 1자, 숫자 1개 및 특수 문자 1자. <br />처음에는 모든 VM이 같은 암호를 갖지만, 프로비전 후 암호를 변경할 수 있습니다.|12-72자|해당 없음
SSH 키(인증 형식 = 공개 키)|원격 로그인에 사용하는 보안 셸 키.|| 해당 없음
구독| 컨소시엄 네트워크를 배포하는 구독||해당 없음
리소스 그룹| 컨소시엄 네트워크를 배포하는 리소스 그룹.||해당 없음
위치| 리소스 그룹에 대한 Azure 지역입니다. ||해당 없음



### <a name="operations-management-suite"></a>Operations Management Suite

OMS(Operations Management Suite) 블레이드를 사용하면 네트워크에 맞게 OMS 리소스를 구성할 수 있습니다. OMS는 네트워크에서 유용한 메트릭 및 로그를 수집하고 표면화하여 네트워크 상태 또는 디버그 문제를 빨리 검사하는 기능을 제공합니다. OMS 무료 상품은 용량에 도달한 후 정상적으로 실패합니다.

![새 OMS 만들기](./media/ethereum-deployment/new-oms.png)

매개 변수 이름|설명| 허용되는 값|기본값
---|---|---|---
기존 OMS에 연결|새 Log Analytics 인스턴스 만들기 또는 기존 인스턴스 조인|새로 만들기 기존 조인|새 Log Analytics 위치 만들기|새 Log Analytics를 배포할 지역(*새로 만들기*를 선택하면 볼 수 있음)
기존 OMS 작업 영역 ID|기존 인스턴스의 작업 영역 ID(*기존 조인*을 선택하면 볼 수 있음) OMS 서비스 계층|새 인스턴스에 대한 가격 책정 계층을 선택합니다. https://azure.microsoft.com/pricing/details/log-analytics/에서 자세한 정보(*기존 조인*을 선택하면 볼 수 있음)|노드당 무료 독립 실행형|무료
기존 OMS 기본 키|기존 OMS 인스턴스에 연결하는 데 사용하는 기본 키(*기존 조인*을 선택하면 볼 수 있음)

### <a name="deployment-regions"></a>배포 지역

그런 다음, **배포 지역** 아래에서 **지역 수**에 대한 입력을 지정하여 주어진 지역 수를 기반으로 컨소시엄 네트워크 및 Azure 지역 선택을 배포합니다. 사용자는 최대 5개의 지역에서 배포할 수 있습니다.

![배포 지역](./media/ethereum-deployment/deployment-regions.png)

매개 변수 이름| 설명| 허용되는 값 |기본값
---|---|---|---
지역 수| 컨소시엄 네트워크를 배포하는 지역 수|1, 2, 3, 4, 5| 2
첫 번째 지역| 컨소시엄 네트워크를 배포하는 첫 번째 지역|모든 허용된 Azure 지역| 미국 서부
두 번째 지역 |컨소시엄 네트워크를 배포하는 두 번째 지역(지역 수를 2로 선택한 경우에만 볼 수 있음)|모든 허용된 Azure 지역| 미국 동부
세 번째 지역| 컨소시엄 네트워크를 배포하는 세 번째 지역(지역 수를 3으로 선택한 경우에만 볼 수 있음)|모든 허용된 Azure 지역| 미국 중부
네 번째 지역| 컨소시엄 네트워크를 배포하는 네 번째 지역(지역 수를 4로 선택한 경우에만 볼 수 있음)|모든 허용된 Azure 지역| 미국 동부 2
다섯 번째 지역| 컨소시엄 네트워크를 배포하는 다섯 번째 지역(지역 수를 5로 선택한 경우에만 볼 수 있음)|모든 허용된 Azure 지역| 미국 서부 2

### <a name="network-size-and-performance"></a>네트워크 크기 및 성능

그런 다음, **네트워크 크기 및 성능** 아래에서 컨소시엄 네트워크의 크기에 대한 입력(예: 마이닝 노드 및 트랜잭션 노드의 수와 크기)을 지정합니다.

![네트워크 크기 및 성능](./media/ethereum-deployment/network-size-performance.png)

매개 변수 이름 |설명 |허용되는 값| 기본값
---|---|---|---
마이닝 노드 수|지역당 배포하는 마이닝 노드 수|2 ~ 15| 2
마이닝 노드 저장소 성능|각 배포한 마이닝 노드를 지원하는 관리 디스크의 형식입니다.|Standard 또는 Premium|Standard
마이닝 노드 가상 머신 크기|마이닝 노드에 사용하는 가상 머신 크기입니다.|표준 A, <br />Standard D, <br />Standard D-v2, <br />Standard F 시리즈, <br />Standard DS, <br />그리고 표준 FS|표준 D1v2
부하가 분산된 트랜잭션 노드 수|네트워크의 일부로 프로비전하는 트랜잭션 노드 수입니다.|1 ~ 5| 2
트랜잭션 노드 저장소 성능|각 배포된 트랜잭션 노드를 지원하는 관리 디스크의 형식입니다.|Standard 또는 Premium|Standard
트랜잭션 노드 가상 머신 크기|트랜잭션 노드에 사용하는 가상 머신 크기입니다.|표준 A, <br />Standard D, <br />Standard D-v2, <br />Standard F 시리즈, <br />Standard DS, <br />그리고 표준 FS|표준 D1v2

### <a name="ethereum-settings"></a>Ethereum 설정

그런 다음, **Ethereum 설정** 아래에서 Ethereum 관련 구성 설정(예: 네트워크 ID 및 Ethereum 계정 암호 또는 최초 블록)을 지정합니다.

![Ethereum 설정](./media/ethereum-deployment/standalone-leader-deployment.png)

매개 변수 이름 |설명 |허용되는 값|기본값
---|---|---|---
ConsortiumMember ID|충돌을 방지하기 위해 IP 주소 공간을 구성하는 데 사용하는 컨소시엄 네트워크에 참가하는 각 멤버와 연결된 ID입니다. <br /><br />멤버 ID는 같은 네트워크의 서로 다른 조직 간에 고유해야 합니다. 고유 멤버 ID는 같은 조직이 여러 지역에 배포하는 경우에도 필요합니다.<br /><br />이 매개 변수의 값은 다른 조인 멤버와 공유해야 하므로 메모해 두십시오.|0 ~ 255
Ethereum 네트워크 ID|배포하는 컨소시엄 Ethereum 네트워크의 네트워크 ID입니다. 각 Ethereum 네트워크에는 공용 네트워크에 대한 ID를 1로 하는 자체의 네트워크 ID가 있습니다. 마이닝 노드에 대해서는 네트워크 액세스가 제한되지만, 여전히 충돌을 방지하기 위해 큰 수를 사용하는 것이 좋습니다.|5 ~ 999,999,999| 10101010
사용자 지정 최초 블록|자동으로 최초 블록을 생성하거나 사용자 지정 최초 블록을 제공하는 옵션입니다.|예/아니요| 아니요
Ethereum 계정 암호(사용자 지정 최초 블록 = 아니요)|각 노드에 가져오는 Ethereum 계정을 보호하기 위해 사용하는 관리자 암호입니다. 암호는 다음을 포함해야 합니다. 대문자 1자, 소문자 1자 및 숫자 1개.|12자 이상|해당 없음
Ethereum 개인 키 암호(사용자 지정 최초 블록 = 아니요)|생성되는 기본 Ethereum 계정과 연결된 ECC 개인 키를 생성하는 데 사용하는 암호입니다. 미리 생성된 개인 키는 명시적으로 전달할 필요가 없습니다.<br /><br />강력한 개인 키를 확보하고 다른 컨소시엄 멤버와 겹치지 않도록 충분한 임의성을 가진 암호를 고려합니다. 암호는 최소한 다음을 포함해야 합니다. 대문자 1자, 소문자 1자 및 숫자 1개.<br /><br />참고로 두 멤버가 같은 암호를 사용하는 경우 생성되는 계정이 같습니다. 같은 암호는 단일 조직이 지역 간에 배포를 시도하고 모든 노드에 걸쳐 단일 계정(코인 베이스)을 공유하려는 경우 유용합니다.|12자 이상|해당 없음
최초 블록(사용자 지정 최초 블록 = 예)|사용자 지정 최초 블록을 나타내는 JSON 문자열입니다. 여기서 그리고 사용자 지정 네트워크 아래에서 최초 블록의 형식에 대한 더 자세한 정보를 찾을 수 있습니다.<br /><br />Ethereum 계정은 사용자 지정 최초 블록을 제공하는 경우에도 여전히 만들어집니다. 마이닝할 때까지 기다리지 않고 최초 블록에 프리 펀드된 Ethereum 계정을 지정하는 것을 고려합니다.|유효한 JSON |해당 없음
연결을 위한 공유 키|VNET 게이트웨이 간의 연결을 위한 공유 키입니다.| 12자 이상|해당 없음
컨소시엄 데이터 URL|다른 멤버의 배포에서 제공한 관련 컨소시엄 구성 데이터를 가리키는 URL입니다. <br /><br />이 정보는 배포를 가지고 있는 이미 연결된 멤버가 제공합니다. 나머지 네트워크를 배포한 경우, URL은 CONSORTIUM-DATA라는 템플릿 배포 출력입니다.||해당 없음
연결할 VNet 게이트웨이|연결할 VNet 게이트웨이의 리소스 경로입니다.<br />이 정보는 배포를 가지고 있는 이미 연결된 멤버가 제공합니다. 나머지 네트워크를 배포한 경우, URL은 CONSORTIUM_MEMBER_GATEWAY_ID라는 템플릿 배포 출력에 있습니다. 참고: 같은 멤버의 컨소시엄 데이터 URL 및 VNet 게이트웨이 리소스를 사용해야 합니다.||해당 없음
피어 정보 등록자의 엔드포인트|다른 멤버의 배포에서 제공한 피어 정보 엔드포인트|컨소시엄의 첫 번째 멤버의 유효한 엔드포인트|해당 없음
피어 정보 등록자의 키|다른 멤버의 배포에서 제공한 피어 정보 기본 키|컨소시엄의 첫 번째 멤버의 유효한 기본 키|해당 없음

### <a name="summary"></a>요약

요약 블레이드를 클릭하여 지정한 입력을 검토하고 기본 배포 전 유효성 검사를 실행합니다.

![요약](./media/ethereum-deployment/summary.png)

법률 정보 및 개인정보취급방침을 검토하고 **구입**을 클릭하여 배포합니다. 배포가 둘 이상의 지역을 포함하거나 컨소시엄 네트워크에 대한 것인 경우, 이 템플릿은 다른 멤버와의 네트워크 연결을 지원하는 데 필요한 VPN Gateway를 사전 배포합니다. 게이트웨이 배포는 최대 45분에서 ~ 50분쯤 걸릴 수 있습니다.

## <a name="post-deployment-sanity-checks"></a>배포 후 온전성 검사

### <a name="administrator-page"></a>관리자 페이지

배포가 성공적으로 완료되고 모든 리소스가 프로비전된 후 관리자 페이지로 이동하여 블록체인 네트워크의 뷰를 가져오고 배포 상태의 온전성을 검사할 수 있습니다. 관리자 페이지의 URL은 부하 분산 장치의 DNS 이름이며, 이는 ADMIN_SITE라는 템플릿 배포의 출력 섹션에 있습니다.

이를 찾으려면 배포된 리소스 그룹을 선택합니다. 그런 다음, **개요**를 선택하고 성공한 번호를 보여 주는 **배포** 바로 아래의 링크를 클릭합니다.

![리소스 그룹 개요](./media/ethereum-deployment/resource-overview.png)

새 화면에 배포 기록을 표시합니다. 첫 번째 배포 리소스(예: microsoft-azure-blockchain.azure-blockchain-servi...)를 선택하고 페이지의 하반부에서 **출력** 섹션을 찾습니다. 템플릿 배포 출력 매개 변수에 ADMIN_SITE로 나열된 관리자 페이지의 URL이 보입니다.

![리소스 배포](./media/ethereum-deployment/resource-deployments.png)

![배포 출력](./media/ethereum-deployment/deployment-output.png)

관리 페이지를 이동하려면 **ADMIN-SITE** 출력을 복사하여 다른 탭에서 엽니다.

관리자 페이지에서 Ethereum 노드 상태 섹션을 검토하여 배포한 토폴로지의 고급 개요를 가져올 수 있습니다. 이는 모든 노드 호스트 이름, 해당 피어 개수 및 최근에 본 블록을 포함합니다. 각 노드에 대한 피어 개수는 *총 노드 수*보다 1만큼 작은 최솟값과 구성된 최대 피어 개수 사이입니다. 참고로 피어 개수는 네트워크 내에서 배포할 수 있는 노드 수를 제한하지 않습니다.
경우에 따라 피어 개수가 변동하여 (총 노드 수 - 1)보다 작아집니다. 원장의 포크는 피어 개수에 경미한 변화를 일으킬 수 있으므로, 개수 차이가 언제나 노드가 정상이 아니라는 징후인 것은 아닙니다. 끝으로, 네트워크의 각 노드에서 본 최신 블록을 검사하여 시스템의 포크 또는 지연 시간을 결정할 수 있습니다.

![노드 상태](./media/ethereum-deployment/node-status.png)

노드 상태는 10초마다 새로 고쳐집니다. 브라우저 또는 **다시 로드** 단추를 통해 페이지를 다시 로드하여 뷰를 업데이트합니다.

### <a name="oms-portal"></a>OMS 포털

배포 출력(OMSPORTALURL)의 링크를 따라가거나 배포된 리소스 그룹에서 OMS 리소스를 선택하여 OMS 포털을 찾을 수 있습니다.

![OMS URL](./media/ethereum-deployment/oms-url.png)

![OMS 리소스](./media/ethereum-deployment/oms-resource.png)

포털은 먼저 고급 네트워크 통계를 개요로 표시합니다.

![OMS 개요](./media/ethereum-deployment/oms-overview.png)

개요를 클릭하면 노드 통계를 볼 수 있는 포털로 이동됩니다.

![노드별 통계](./media/ethereum-deployment/per-node-stats.png)

### <a name="accessing-nodes"></a>노드 액세스

제공된 관리자 사용자 이름 및 암호/SSH 키를 사용하여 SSH를 통해 트랜잭션 노드에 대한 가상 머신에 원격으로 연결할 수 있습니다. 트랜잭션 노드 VM에는 자체의 공용 IP 주소가 없으므로 부하 분산 장치를 통해 이동하여 포트 번호를 지정해야 합니다. 첫 번째 트랜잭션 노드에 액세스하기 위해 실행할 SSH 명령은 템플릿 배포 출력에 **SSH_TO_FIRST_TX_NODE**(샘플 배포의 경우: ssh -p 4000 gethadmin@leader4vb.eastus.cloudapp.azure.com)로 나열됩니다. 추가 트랜잭션 노드를 가져오려면 포트 번호를 1만큼 증가합니다(예를 들어 첫 번째 트랜잭션 노드는 포트 4000에 있음).

마이닝 노드가 실행하는 가상 머신은 외부에서 액세스할 수 없으므로 트랜잭션 노드 중 하나를 통해 이동해야 합니다. 트랜잭션 노드에 대한 SSH 섹션을 가진 후 개인 키를 트랜잭션 노드에 설치하거나 암호를 사용하여 마이닝 노드에 대한 SSH 세션을 시작합니다.

**참고**

호스트 이름은 관리 사이트 또는 Azure Portal에서 가져올 수 있습니다. Azure Portal에서 VMSS(가상 머신 확장 집합) 리소스에 있는 노드의 호스트 이름은 **인스턴스** 아래에 나열되며, 실제 호스트 이름과 다릅니다. 예를 들어 Azure Portal의 호스트 이름은 **mn-asdfmv-reg1_0**이지만 실제 호스트 이름은 **mn-asdfmv-reg**와 같을 수 있습니다.

예: 

Azure Portal 호스트 이름| 실제 호스트 이름
---|---
mn-ethwvu-reg1_0| mn-ethwvu-reg1000000
mn-ethwvu-reg1_1 |mn-ethwvu-reg1000001
mn-ethwvu-reg1_2 |mn-ethwvu-reg1000002

## <a name="adding-a-new-consortium-member"></a>새 컨소시엄 멤버 추가

### <a name="sharing-data"></a>데이터 공유

첫 번째 멤버(또는 연결된 멤버)는 다른 멤버가 조인하여 해당 멤버의 연결을 설정할 수 있도록 정보의 일부분을 해당 멤버에게 제공해야 합니다. 구체적으로 살펴보면 다음과 같습니다.

1. **공유 컨소시엄 구성 데이터**: 두 멤버 사이의 Ethereum 연결을 오케스트레이션하는 데 사용되는 데이터 집합이 있습니다. 최초 블록, 컨소시엄 네트워크 ID 및 부팅 노드를 포함하여 필요한 정보는 선행부 또는 다른 배포된 멤버의 트랜잭션 노드에 있는 파일에 씁니다. 이 파일의 위치는 **CONSORTIUM-DATA**라는 템플릿 배포 출력 매개 변수에 나열됩니다.
2. **피어 정보 엔드포인트**: 선행부 또는 다른 멤버의 배포에서 Ethereum 네트워크에 이미 연결된 모든 노드의 정보를 가져오기 위한 피어 정보 등록자 엔드포인트입니다. DB는 네트워크에 연결된 각 노드에 곤한 정보의 집합, 즉 노드의 호스트 이름, 개인 IP 주소 등과 같은 정보를 저장합니다. 이는 **PEER_INFO_ENDPOINT**라는 템플릿 배포 출력 매개 변수입니다.
3. **피어 정보 기본 키**: 피어 정보 등록자 기본 키는 선행부 또는 다른 멤버의 피어 정보 기본 키에 대한 액세스 권한을 얻기 위해 사용됩니다. 이는 **PEER_INFO_PRIMARY_KEY**라는 템플릿 배포 출력 매개 변수입니다.


4. **VNET 게이트웨이**: 각 멤버는 기존 멤버를 통해 전체 블록체인 네트워크에 대한 연결을 설정합니다. VNET에 연결하려면 연결하는 멤버의 VNET 게이트웨이에 대한 리소스 경로가 필요합니다. 이는 **CONSORTIUM_MEMBER_GATEWAY_I**라는 템플릿 배포 출력 매개 변수입니다.
5. **공유 키**: 연결을 설정하는 컨소시엄 네트워크의 두 멤버 간에 미리 설정한 암호입니다. 이는 배포 컨텍스트 외부에서 합의한 영숫자 문자열(1 ~ 128문자)입니다. (예: **MySharedKeyAbc123**)

### <a name="acceptance-of-new-member"></a>새 멤버의 동의

이 단계는 조인 멤버가 네트워크를 성공적으로 배포한 후 수행해야 합니다. 기존 멤버가 본인의 VPN Gateway에서 연결에 동의하도록 최종 구성을 수행해야 멤버가 네트워크에 조인하여 트랜잭션 트래픽을 볼 수 있습니다. 즉, 조인 멤버의 Ethereum 노드는 연결이 설정될 때까지 실행되지 않습니다. 이 구성은 PowerShell 또는 xPlat CLI를 통해 수행할 수 있습니다. PowerShell 모듈 및 xPlat CLI 스크립트도 컨소시엄 데이터와 함께 트랜잭션 노드에 저장됩니다. 스크립트 위치는 각각 **PAIR-GATEWAY-PS-MODULE** 및 **PAIR-GATEWAY-AZURE-CLISCRIPT**라는 배포 출력 매개 변수입니다.

**PowerShell/CLI 설치**

Azure PowerShell cmdlet 및 Azure xPlat CLI를 시작하는 방법에 대한 추가 정보는 Azure 설명서에서 찾을 수 있습니다.

Azure cmdlet의 최신 버전을 로컬로 설치하고 세션을 열어야 합니다. Azure 구독 자격 증명으로 세션에 로그인해야 합니다.

**PowerShell: 연결 설정**

PowerShell 모듈을 다운로드하여 로컬로 저장합니다. PowerShell 모듈의 위치는 **PAIR-GATEWAY-PS-MODULE** 템플릿 배포 출력 매개 변수로 지정됩니다.

아직 활성화하지 않은 경우, 로컬 세션에 대한 **Set-ExecutionPolicy** cmdlet을 사용하여 서명하지 않은 모듈의 실행을 허용합니다.

**Set-ExecutionPolicy Unrestricted CurrentUser**

그런 다음, 다음을 사용하여 원장 배포를 배포한 Azure 구독에 로그인합니다.

**Login-AzureRmAccount**

그런 다음, 모듈을 가져옵니다.

**Import-Module <filepath to downloaded file>**

끝으로, 적절한 입력을 사용하여 함수를 실행합니다.

- **MyGatewayResourceId** : 게이트웨이의 리소스 경로입니다. 이는 **CONSORTIUM_MEMBER_GATEWAY_I**라는 템플릿 배포 출력 매개 변수입니다.
- **OtherGatewayResourceId** : 조인 멤버의 게이트웨이의 리소스 경로입니다. 이는 조인 멤버가 제공하며 **CONSORTIUM_MEMBER_GATEWAY_ID**라는 템플릿 배포 출력 매개 변수이기도 합니다.
- **ConnectionName** : 이 게이트웨이 연결을 식별하는 이름입니다.
- **공유 키**: 연결을 설정하는 컨소시엄 네트워크의 두 멤버 간에 미리 설정한 암호입니다.

**CreateConnection** - MyGatewayResourceId <resource path of your Gateway> -OtherGatewayResourceId <조인 멤버의 게이트웨이의 리소스 경로> -ConnectionName myConnection -SharedKey "MySharedKeyAbc123"

**xPlat CLI: 연결 설정**

Azure CLI 스크립트를 다운로드하여 로컬로 저장합니다. Azure CLI 스크립트의 위치는 **PAIR-GATEWAY-AZURE-CLI-SCRIPT**라는 템플릿 배포 매개 변수에 지정됩니다.

적절한 입력을 포함한 스크립트를 실행합니다.

- **MyGatewayResourceId** : 게이트웨이의 리소스 경로입니다. 이는 **CONSORTIUM_MEMBER_GATEWAY_I**라는 템플릿 배포 출력 매개 변수입니다.
- **OtherGatewayResourceId** : 조인 멤버의 게이트웨이의 리소스 경로입니다. 이는 조인 멤버가 제공하며 해당 멤버의 **CONSORTIUM_MEMBER_GATEWAY_ID**라는 템플릿 배포 매개 변수이기도 합니다.
- **ConnectionName** : 이 게이트웨이 연결을 식별하는 이름입니다.
- **공유 키**: 연결을 설정하는 컨소시엄 네트워크의 두 멤버 간에 미리 설정한 암호입니다.
- **위치**: 게이트웨이 리소스가 배포된 Azure 지역입니다.

``` powershell
az network vpn-connection create --name $ConnectionName --resource-group
$MyResourceGroup --vnet-gateway1 $MyGatewayResourceId --shared-key $SharedKey --vnet-
gateway2 $OtherGatewayResourceId --enable-bgp
```

아키텍처는 **선행부**와 **멤버** 배포 사이에 연결을 성공적으로 구성한 후 다음과 같습니다.

![선행부 및 멤버 아키텍처](./media/ethereum-deployment/leader-member.png)

## <a name="fund-new-member-account"></a>새 멤버 계정 펀드

### <a name="generated-genesis-block"></a>생성된 최초 블록

첫 번째 멤버인 Ethereum 계정은 배포가 최초 블록을 생성하는 경우(사용자 지정 최초 블록 = 아니요) 1조 에테르로 펀드됩니다. 다른 멤버는 프리 펀드되지 않으며 해당 멤버의 마이닝 노드가 블록을 마이닝하기 시작함에 따라 에테르가 누적되기를 기다려야 합니다. 새 멤버가 에테르를 기다리지 않도록 하려면 조인 멤버의 Ethereum 계정을 명시적으로 펀드해야 합니다.

이렇게 하려면 조인 멤버가 사용자에게 해당 멤버의 관리 웹 사이트에 표시된 Ethereum 계정을 제공해야 합니다. 그런 다음, 사용자의 계정에서 관리 웹 사이트를 사용하여 제공된 게정을 입력하면 에테르를 해당 멤버의 계정에 이전할 수 있습니다.

### <a name="custom-genesis-block"></a>사용자 지정 최초 블록

사용자 지정 최초 블록에 지정된 Ethereum 계정이 제공된 경우, MetaMask 또는 다른 도구를 사용하여 해당 지정된 계정에서 관리 웹 사이트에 보이는 미리 생성된 Ethereum 계정으로 이전할 수 있습니다. MetaMask를 사용하는 방법에 대한 지침은 [Ethereum 계정 만들기](#create-ethereum-account)를 참조하세요.

사용자 지정 최초 블록이 계정 없이 제공되거나 미리 할당된 어떤 계정에도 액세스 권한이 없는 경우에는 사용자의 마이닝 노드가 마이닝하기 시작하여 사용자의 계정(코인 베이스)에 에테르를 생성할 때까지 기다려야 합니다. 펀드가 생성되는 속도는 사용자 지정 최초 블록에서 지정하는 난이도 수준에 따라 달라집니다.

## <a name="create-ethereum-account"></a>Ethereum 계정 만들기

추가 계정을 만들려면 다양한 솔루션을 사용할 수 있습니다. 그러한 솔루션 중 하나는 MetaMask이며, 이는 Ethereum 네트워크, 공용, 테스트 또는 사용자 지정에 대한 ID 자격 증명 모음 및 연결을 제공하는 Chrome 확장입니다. MetaMask는 네트워크에 계정을 등록하는 트랜잭션을 공식화합니다.

이 트랜잭션은 다른 트랜잭션과 마찬가지로 트랜잭션 노드 중 하나로 이동하고 결국 아래 그림과 같이 블록으로 마이닝합니다.

![트랜잭션 노드](./media/ethereum-deployment/transaction-node.png)

Chrome에 확장을 설치하려면 사용자 지정으로 이동한 후 Google Chrome(오버플로 단추) > 추가 도구 > 확장 > 추가 확장 가져오기를 제어한 다음, MetaMask를 검색합니다.

![MetaMask 확장](./media/ethereum-deployment/metamask-extension.png)

설치된 후 MetaMask를 열고 새 자격 증명 모음을 만듭니다. 기본적으로 자격 증명 모음은 Morden Test Network에 연결됩니다. 배포된 개인 컨소시엄 네트워크, 구체적으로 트랜잭션 노드 앞의 부하 분산 장치에 연결하려면 연결 대상을 변경해야 합니다. 템플릿 출력에서 포트 8545에 표시된 Ethereum RPC 엔드포인트를 검색하여 `ETHEREUM-RPC-ENDPOINT`로 명명되고, 아래와 같이 사용자 지정 RPC에 입력합니다.

![MetaMask 설정](./media/ethereum-deployment/metamask-settings.png)

자격 증명 모음을 만들어 게정을 포함하는 전자지갑을 만듭니다. 추가 계정을 만들려면 **스위치 계정**을 선택한 다음, **+** 단추를 선택합니다.

![MetaMask 게정 만들기](./media/ethereum-deployment/metamask-create-account.png)

### <a name="initiate-initial-ether-allocation"></a>초기 에테르 할당 시작

관리자 페이지를 통해 미리 할당된 계정에서 다른 Ethereum 계정으로 에테르를 이전하는 트랜잭션을 공식화할 수 있습니다. 이 에테르 이전은 트랜잭션 노드로 전송되어 아래 그림과 같이 블록으로 마이닝되는 트랜잭션입니다.

![트랜잭션 노드](./media/ethereum-deployment/transaction-node-mined.png)

MetaMask 전자지갑의 클립보드 아이콘을 통해 에테르를 이전하려는 Ethereum 계정의 주소를 복사하고 관리자 페이지로 다시 이동합니다. 복사한 계정을 입력 필드에 붙여넣어 미리 할당된 Ethereum 계정에서 새로 만든 게정으로 1000 에테르를 이전합니다. **제출**을 클릭하고 트랜잭션이 블록으로 마이닝되기를 기다립니다.

![노드 상태](./media/ethereum-deployment/node-status2.png)

트랜잭션이 마이닝된 블록으로 커밋되면 MetaMask의 사용자 계정에 대한 계정 잔액이 1000 에테르 이전을 반영합니다.

![MetaMask 이전](./media/ethereum-deployment/metamask-transfer.png)

### <a name="transfer-of-ether-between-accounts"></a>계정 간의 에테르 이전

이 시점에서 개인 컨소시엄 네트워크 내에서 트랜잭션을 실행할 준비가 되었습니다. 가장 간단한 트랜잭션은 에테르를 한 계정에서 다른 계정으로 이전하는 것입니다. 그러한 트랜잭션을 공식화하려면 MetaMask를 다시 한 번 사용하여 위에서 사용한 첫 번째 계정에서 두 번째 계정으로 자금을 이체할 수 있습니다.

MetaMas의 **전자지갑 1**에서 보내기를 클릭합니다. 만든 두 번째 전자지갑의 주소를 **받는 사람 주소** 입력 필드에 복사하고 이전할 에테르 금액을 **금액** 입력 필드에 복사합니다. **보내기**를 클릭하고 트랜잭션에 동의합니다.

![MetaMask 보내기 트랜잭션](./media/ethereum-deployment/metamask-send.png)

다시 한 번 트랜잭션이 마이닝되고 블록으로 커밋될 때 계정 잔액이 그에 따라 반영됩니다. 참고로 트랜잭션을 처리하려면 마이닝 요금을 지불해야 하므로 **전자지갑 1**의 잔액은 15 에테르 초과로 공제됩니다.

![전자지갑 1](./media/ethereum-deployment/wallet1.png)

![전자지갑 2](./media/ethereum-deployment/wallet2.png)

## <a name="next-steps"></a>다음 단계

이제 개인 컨소시엄 블록체인 네트워크에 대한 응용 프로그램 및 스마트 계약 배포에 집중할 준비가 되었습니다.
