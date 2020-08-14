---
title: 개념-Network 상호 연결과
description: Azure VMware 솔루션 (AVS)에서 네트워킹 및 상호 연결과의 주요 측면과 사용 사례에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 6f1f1f5a089781f1f7e882c9c8692f0c845ae485
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214101"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>AVS(Azure VMware 솔루션) 미리 보기 네트워킹 및 상호 연결 개념

Azure의 Azure VMware 솔루션 (AVS) 사설 클라우드와 온-프레미스 환경 또는 가상 네트워크 간의 네트워크 상호 연결과을 통해 사설 클라우드를 액세스 하 고 사용할 수 있습니다. 이 문서에서는 네트워킹 및 상호 연결과의 기본 사항을 설정 하는 몇 가지 주요 개념을 다룹니다.

상호 연결과에 대 한 유용한 관점은 다음과 같은 두 가지 유형의 AVS 사설 클라우드 구현을 고려 하는 것입니다.

1. [**기본 azure 전용 상호 연결과**](#azure-virtual-network-interconnectivity) 를 사용 하면 azure에서 단일 가상 네트워크로 사설 클라우드를 관리 하 고 사용할 수 있습니다. 이 구현은 온-프레미스 환경에서 액세스 하지 않아도 되는 AVS 평가 또는 구현에 가장 적합 합니다.

1. 온- [**프레미스에서 사설 클라우드로 전체 온-프레미스 상호 연결과**](#on-premises-interconnectivity) 기본 Azure 전용 구현을 확장 하 여 온-프레미스와 AVS 사설 클라우드 간에 상호 연결과를 포함 합니다.
 
다음 섹션에서 설명 하는 요구 사항 및 두 가지 유형의 AVS 사설 cloud 상호 연결과 구현에 대 한 자세한 정보를 찾을 수 있습니다.

## <a name="avs-private-cloud-use-cases"></a>AVS 사설 클라우드 사용 사례

AVS 사설 클라우드의 사용 사례는 다음과 같습니다.
- 클라우드의 새 VMware VM 워크 로드
- 클라우드로의 VM 작업 버스트 (온-프레미스에서 AVS로만)
- 클라우드로의 VM 워크 로드 마이그레이션 (온-프레미스에서 AVS로만)
- 재해 복구 (AVS에서 avs로 또는 온-프레미스에서 AVS로)
- Azure 서비스 사용량

 AVS 서비스의 모든 사용 사례는 온-프레미스에서 사설 클라우드 연결을 사용 하 여 사용 하도록 설정 됩니다. 

## <a name="virtual-network-and-expressroute-circuit-requirements"></a>가상 네트워크 및 Express 경로 회로 요구 사항
 
구독의 가상 네트워크에서 연결을 만들 때 Express 경로 회로는 피어 링을 통해 설정 되며, Azure Portal에서 요청 하는 권한 부여 키와 피어 링 ID를 사용 합니다. 피어 링은 사설 클라우드와 가상 네트워크 간의 일대일 연결입니다.

> [!NOTE] 
> Express 경로 회로는 사설 클라우드 배포에 포함 되지 않습니다. 온-프레미스 Express 경로 회로는이 문서의 범위를 벗어났습니다. 사설 클라우드에 대 한 온-프레미스 연결이 필요한 경우 기존 Express 경로 회로 중 하나를 사용 하거나 Azure Portal에서 구매할 수 있습니다.

사설 클라우드를 배포할 때 vCenter 및 NSX Manager에 대 한 IP 주소를 수신 합니다. 이러한 관리 인터페이스에 액세스 하려면 구독의 가상 네트워크에 추가 리소스를 만들어야 합니다. 자습서에서 이러한 리소스를 만들고 Express 경로 개인 피어 링을 설정 하는 절차를 찾을 수 있습니다.

사설 클라우드 논리 네트워킹에는 미리 프로 비전 된 NSX가 제공 됩니다. 계층 0 게이트웨이 및 계층 1 게이트웨이는 사용자를 위해 미리 프로 비전 됩니다. 세그먼트를 만들어 기존 계층-1 게이트웨이에 연결 하거나 정의한 새 계층 1 게이트웨이에 연결할 수 있습니다. NSX 논리 네트워킹 구성 요소는 워크 로드 간에 동-서 연결을 제공 하며 인터넷 및 Azure 서비스에 대 한 북아메리카 연결도 제공 합니다. 

## <a name="routing-and-subnet-requirements"></a>라우팅 및 서브넷 요구 사항

라우팅은 각 사설 클라우드 배포에 대해 기본적으로 자동으로 프로 비전 되 고 사용 하도록 설정 된 BGP (Border Gateway Protocol)를 기반으로 합니다. AVS 사설 클라우드의 경우 아래 표에 표시 된 서브넷에 대 한 최소/22 개의 접두사 길이 CIDR 네트워크 주소 블록을 사용 하 여 사설 클라우드 네트워크 주소 공간을 계획 해야 합니다. 주소 블록은 구독과 온-프레미스 네트워크에 있는 다른 가상 네트워크에서 사용되는 주소 블록과 겹칠 수 없습니다. 이 주소 블록 내에서 관리, 프로 비전 및 vMotion 네트워크는 자동으로 프로 비전 됩니다.

`/22` CIDR 네트워크 주소 블록의 예: `10.10.0.0/22`

서브넷:

| 네트워크 사용량             | 서브넷 | 예제        |
| ------------------------- | ------ | -------------- |
| 프라이빗 클라우드 관리  | `/24`  | `10.10.0.0/24` |
| vMotion 네트워크           | `/24`  | `10.10.1.0/24` |
| VM 워크로드              | `/24`  | `10.10.2.0/24` |
| ExpressRoute 피어링      | `/24`  | `10.10.3.8/30` |


## <a name="azure-virtual-network-interconnectivity"></a>Azure virtual network 상호 연결과

사설 클라우드 구현에 대 한 가상 네트워크에서 AVS 사설 클라우드를 관리 하 고, 사설 클라우드에서 워크 로드를 사용 하 고, Express 경로 연결을 통해 Azure 서비스에 액세스할 수 있습니다. 

아래 다이어그램은 사설 클라우드 배포 시 설정 된 기본 네트워크 상호 연결과를 보여 줍니다. Azure의 가상 네트워크와 사설 클라우드의 논리적 Express 경로 기반 네트워킹을 보여 줍니다. 상호 연결과는 다음과 같은 세 가지 주요 사용 사례를 충족 합니다.
* 온-프레미스 시스템이 아닌 Azure 구독의 Vm에서 액세스할 수 있는 vCenter 서버 및 NSX T 관리자에 대 한 인바운드 액세스입니다. 
* Vm에서 Azure 서비스로의 아웃 바운드 액세스. 
* 사설 클라우드를 실행 하는 작업의 인바운드 액세스 및 사용.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="사설 클라우드 연결에 대 한 기본 가상 네트워크" border="false":::

## <a name="on-premises-interconnectivity"></a>온-프레미스 상호 연결과

가상 네트워크 및 온-프레미스에서 전체 사설 클라우드 구현으로 온-프레미스 환경에서 AVS 사설 클라우드에 액세스할 수 있습니다. 이 구현은 이전 섹션에서 설명 하는 기본 구현의 확장입니다. 기본 구현과 마찬가지로 Express 경로 회로가 필요 하지만,이 구현을 사용 하는 경우 온-프레미스 환경에서 Azure의 사설 클라우드로 연결 하는 데 사용 됩니다. 

아래 다이어그램은 온-프레미스에서 사설 클라우드로 상호 연결과를 보여 주며,이를 통해 다음과 같은 사용 사례를 사용할 수 있습니다.
* 핫/콜드 교차 vCenter vMotion
* 온-프레미스에서 AVS 사설 클라우드 관리 액세스

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="가상 네트워크 및 온-프레미스 전체 사설 클라우드 연결" border="false":::

사설 클라우드에 대 한 전체 상호 연결과 경우 Express 경로 Global Reach를 사용 하도록 설정 하 고 Azure Portal의 Global Reach에 대 한 인증 키 및 개인 피어 링 ID를 요청 합니다. 권한 부여 키 및 피어 링 ID는 구독의 Express 경로 회로와 새 사설 클라우드의 Express 경로 회로 간에 Global Reach를 설정 하는 데 사용 됩니다. 연결 되 면 두 개의 Express 경로 회로에서 온-프레미스 환경 간의 네트워크 트래픽을 사설 클라우드로 라우팅합니다.  권한 부여 키 및 피어 링 ID를 요청 하 고 사용 하는 절차는 [사설 클라우드로의 express 경로 Global Reach 피어 링을 만들기 위한 자습서](tutorial-expressroute-global-reach-private-cloud.md) 를 참조 하세요.


## <a name="next-steps"></a>다음 단계 

다음 단계는 [사설 클라우드 저장소 개념](concepts-storage.md)에 대해 학습 하는 것입니다.

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
