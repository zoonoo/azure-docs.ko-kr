---
title: 개념-Network 상호 연결과
description: Azure VMware 솔루션에서 네트워킹 및 상호 연결과의 주요 측면과 사용 사례에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 4ffcdd8ea42df127ee1480927f4fdf2eb8f137b8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316899"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Azure VMware 솔루션 네트워킹 및 상호 연결과 개념

[!INCLUDE [avs-networking-description](includes/avs-networking-description.md)]

상호 연결과에 대 한 유용한 관점은 두 가지 유형의 Azure VMware 솔루션 사설 클라우드 구현을 고려 하는 것입니다.

1. [**기본 azure 전용 상호 연결과**](#azure-virtual-network-interconnectivity) 를 사용 하면 azure에서 단일 가상 네트워크로 사설 클라우드를 관리 하 고 사용할 수 있습니다. 이 구현은 온-프레미스 환경에서 액세스 하지 않아도 되는 Azure VMware 솔루션 평가 또는 구현에 가장 적합 합니다.

1. [**모든 온-프레미스에서 사설 클라우드로의 클라우드 상호 연결과**](#on-premises-interconnectivity) 기본 Azure 전용 구현을 확장 하 여 온-프레미스와 Azure VMware 솔루션 사설 클라우드 간에 상호 연결과를 포함 합니다.
 
이 문서에서는 요구 사항 및 제한 사항을 포함 하 여 네트워킹 및 상호 연결과를 설정 하는 몇 가지 주요 개념을 다룹니다. 또한 두 가지 유형의 Azure VMware 솔루션 사설 cloud 상호 연결과 구현에 대 한 자세한 내용을 다룹니다. 이 문서에서는 Azure VMware 솔루션과 제대로 작동 하도록 네트워킹을 구성 하는 데 필요한 정보를 제공 합니다.

## <a name="azure-vmware-solution-private-cloud-use-cases"></a>Azure VMware 솔루션 사설 클라우드 사용 사례

Azure VMware 솔루션 사설 클라우드의 사용 사례는 다음과 같습니다.
- 클라우드의 새 VMware VM 워크 로드
- 클라우드로의 VM 워크 로드 버스트 (온-프레미스에서 Azure VMware 솔루션 으로만)
- 클라우드로의 VM 워크 로드 마이그레이션 (온-프레미스에서 Azure VMware 솔루션 으로만)
- 재해 복구 (azure vmware 솔루션에 대 한 azure vmware 솔루션 또는 온-프레미스에서 Azure VMware 솔루션으로)
- Azure 서비스 사용

> [!TIP]
> Azure VMware 솔루션 서비스의 모든 사용 사례는 온-프레미스에서 사설 클라우드 연결을 사용 하 여 사용 하도록 설정 됩니다.

## <a name="azure-virtual-network-interconnectivity"></a>Azure virtual network 상호 연결과

사설 클라우드 구현에 대 한 가상 네트워크에서 Azure VMware 솔루션 사설 클라우드를 관리 하 고, 사설 클라우드에서 워크 로드를 사용 하 고, Express 경로 연결을 통해 Azure 서비스에 액세스할 수 있습니다. 

아래 다이어그램은 사설 클라우드 배포 시 설정 된 기본 네트워크 상호 연결과를 보여 줍니다. Azure의 가상 네트워크와 사설 클라우드의 논리적 Express 경로 기반 네트워킹을 보여 줍니다. 상호 연결과는 다음과 같은 세 가지 주요 사용 사례를 충족 합니다.
* 온-프레미스 시스템이 아닌 Azure 구독의 Vm에서 액세스할 수 있는 vCenter 서버 및 NSX T 관리자에 대 한 인바운드 액세스입니다. 
* Vm에서 Azure 서비스로의 아웃 바운드 액세스. 
* 사설 클라우드를 실행 하는 작업의 인바운드 액세스 및 사용.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="사설 클라우드 연결에 대 한 기본 가상 네트워크" border="false":::

## <a name="on-premises-interconnectivity"></a>온-프레미스 상호 연결과

가상 네트워크 및 온-프레미스에서 전체 사설 클라우드 구현으로 온-프레미스 환경에서 Azure VMware 솔루션 사설 클라우드에 액세스할 수 있습니다. 이 구현은 이전 섹션에서 설명 하는 기본 구현의 확장입니다. 기본 구현과 마찬가지로 Express 경로 회로가 필요 하지만,이 구현을 사용 하는 경우 온-프레미스 환경에서 Azure의 사설 클라우드로 연결 하는 데 사용 됩니다. 

아래 다이어그램은 온-프레미스에서 사설 클라우드로 상호 연결과를 보여 주며,이를 통해 다음과 같은 사용 사례를 사용할 수 있습니다.
* 핫/콜드 교차 vCenter vMotion
* 온-프레미스와 Azure VMware 솔루션 사설 클라우드 관리 액세스

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="가상 네트워크 및 온-프레미스 전체 사설 클라우드 연결" border="false":::

사설 클라우드에 대 한 전체 상호 연결과 경우 Express 경로 Global Reach를 사용 하도록 설정 하 고 Azure Portal의 Global Reach에 대 한 인증 키 및 개인 피어 링 ID를 요청 합니다. 권한 부여 키 및 피어 링 ID는 구독의 Express 경로 회로와 새 사설 클라우드의 Express 경로 회로 간에 Global Reach를 설정 하는 데 사용 됩니다. 연결 되 면 두 개의 Express 경로 회로에서 온-프레미스 환경 간의 네트워크 트래픽을 사설 클라우드로 라우팅합니다.  권한 부여 키 및 피어 링 ID를 요청 하 고 사용 하는 절차는 [사설 클라우드로의 express 경로 Global Reach 피어 링을 만들기 위한 자습서](tutorial-expressroute-global-reach-private-cloud.md) 를 참조 하세요.



## <a name="next-steps"></a>다음 단계 
[사설 클라우드 저장소 개념](concepts-storage.md)에 대해 알아봅니다.


<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->

