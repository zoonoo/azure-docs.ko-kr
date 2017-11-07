---
title: "Azure Advisor 고가용성 권장 사항 | Microsoft Docs"
description: "Azure Advisor를 사용하여 Azure 배포의 가용성을 향상시킵니다."
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 7b63de2453180e562596c211d40cebe85b95bd54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="advisor-high-availability-recommendations"></a>Advisor 고가용성 권장 사항

Azure Advisor는 업무상 중요한 응용 프로그램의 연속성을 보장하고 향상시키는 데 도움이 됩니다. Advisor 대시보드의 **고가용성** 탭에서 Advisor의 고가용성 권장 사항을 가져올 수 있습니다.

![Advisor 대시보드의 고가용성 단추](./media/advisor-high-availability-recommendations/advisor-high-availability-tab.png)


## <a name="ensure-virtual-machine-fault-tolerance"></a>가상 컴퓨터 내결함성 보장

Advisor는 가용성 집합에 속하지 않는 가상 컴퓨터를 식별하고 이러한 가상 컴퓨터를 가용성 집합으로 이동할 것을 권장합니다. 응용 프로그램에 중복성을 제공하기 위해 여러 개의 가상 컴퓨터를 가용성 집합으로 그룹화하는 것이 좋습니다. 이 구성은 계획된 유지 관리 또는 계획되지 않은 유지 관리 이벤트 중에 적어도 하나의 가상 컴퓨터를 사용할 수 있고 Azure 가상 컴퓨터 SLA가 충족되도록 합니다. 가상 컴퓨터에 대한 가용성 집합을 만들거나, 기존 가용성 집합에 가상 컴퓨터를 추가할 수도 있습니다.

> [!NOTE]
> 가용성 집합을 만들도록 선택한 경우 해당 가용성 집합에 적어도 하나 이상의 가상 컴퓨터를 추가해야 합니다. 중단 시간 동안 하나 이상의 컴퓨터를 사용할 수 있도록 하기 위해 가용성 집합에 둘 이상의 가상 컴퓨터를 그룹화하는 것이 좋습니다.

![Advisor 권장 사항: 가상 컴퓨터 중복성을 위해 가용성 집합 사용](./media/advisor-high-availability-recommendations/advisor-high-availability-create-availability-set.png)

## <a name="ensure-availability-set-fault-tolerance"></a>가용성 집합 내결함성 보장 

Advisor는 단일 가상 컴퓨터를 포함하는 가용성 집합을 식별하고 여기에 하나 이상의 가상 컴퓨터를 추가할 것을 권장합니다. 응용 프로그램에 중복성을 제공하기 위해 여러 개의 가상 컴퓨터를 가용성 집합으로 그룹화하는 것이 좋습니다. 이 구성은 계획된 유지 관리 또는 계획되지 않은 유지 관리 이벤트 중에 적어도 하나의 가상 컴퓨터를 사용할 수 있고 Azure 가상 컴퓨터 SLA가 충족되도록 합니다. 가상 컴퓨터를 만들거나 기존 가상 컴퓨터를 사용하도록 선택하고 해당 컴퓨터를 가용성 집합에 추가할 수 있습니다.  

![Advisor 권장 사항: 이 가용성 집합에 하나 이상의 VM 추가](./media/advisor-high-availability-recommendations/advisor-high-availability-add-vm-to-availability-set.png)


## <a name="ensure-application-gateway-fault-tolerance"></a>응용 프로그램 게이트웨이 내결함성 보장
응용 프로그램 게이트웨이에서 구동되는 중요 업무용 응용 프로그램의 비즈니스 연속성을 위해 Advisor는 내결함성에 대해 구성되지 않은 응용 프로그램 게이트웨이 인스턴스를 식별하고 수행할 수 있는 수정 작업을 제안합니다. Advisor는 중간 규모 또는 대규모 단일 인스턴스 응용 프로그램 게이트웨이를 식별하고 하나 이상의 인스턴스 추가를 권장합니다. 또한 단일 또는 다중 인스턴스 소규모 응용 프로그램 게이트웨이를 식별하고 중간 규모 또는 대규모 SKU로 마이그레이션을 권장합니다. Advisor는 응용 프로그램 게이트웨이 인스턴스가 이러한 리소스에 대한 현재 SLA 요구 사항을 만족하도록 구성되어 있는지 확인하는 데 이러한 작업을 권장합니다.

![Advisor 권장 사항: 두 개 이상의 중간 규모 또는 대규모 응용 프로그램 게이트웨이 인스턴스 배포](./media/advisor-high-availability-recommendations/advisor-high-availability-application-gateway.png)

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>가상 컴퓨터 디스크의 성능 및 안정성 향상

Advisor는 표준 디스크를 포함하는 가상 컴퓨터를 식별하고 프리미엄 디스크로 업그레이드할 것을 권장합니다.
 
Azure Premium Storage는 I/O 사용량이 많은 작업을 실행하는 가상 컴퓨터에서 대기 시간이 짧은 고성능 디스크 지원을 제공합니다. 프리미엄 저장소 계정을 사용하는 가상 컴퓨터 디스크는 SSD(솔리드 스테이트 드라이브)에 데이터를 저장합니다. 응용 프로그램이 최고 성능을 낼 수 있도록 높은 IOPS가 필요한 모든 가상 컴퓨터 디스크를 프리미엄 저장소로 마이그레이션하는 것이 좋습니다. 

디스크에 높은 IOPS가 필요하지 않으면 표준 저장소에 유지 관리하여 비용을 제한할 수 있습니다. 표준 저장소는 SSD 대신 HDD(하드 디스크 드라이브)에 가상 컴퓨터 디스크 데이터를 저장합니다. 가상 컴퓨터 디스크를 프리미엄 디스크로 마이그레이션하도록 선택할 수 있습니다. 프리미엄 디스크는 대부분의 가상 컴퓨터 SKU에서 지원됩니다. 그러나 일부 경우에 프리미엄 디스크를 사용하려는 경우 가상 컴퓨터 SKU도 업그레이드해야 할 수 있습니다.

![Advisor 권장 사항: 프리미엄 디스크로 업그레이드하여 가상 컴퓨터 디스크의 안정성 향상](./media/advisor-high-availability-recommendations/advisor-high-availability-upgrade-to-premium-disks.png)

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>가상 컴퓨터 데이터를 실수로 삭제되지 않도록 보호
Advisor는 백업이 활성화되지 않은 가상 컴퓨터를 식별하고 백업을 활성화할 것을 권장합니다. 가상 컴퓨터 백업 설정은 비즈니스에 중요한 데이터의 가용성을 보장하고 실수로 인한 삭제 또는 손상에 대한 보호를 제공합니다.

![Advisor 권장 사항: 중요 업무용 데이터를 보호하도록 가상 컴퓨터 백업 구성](./media/advisor-high-availability-recommendations/advisor-high-availability-virtual-machine-backup.png)

## <a name="access-high-availability-recommendations-in-advisor"></a>Advisor의 고가용성 권장 사항에 액세스

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 창에서 **더 많은 서비스**를 클릭합니다.

3. 서비스 메뉴 창의 **모니터링 및 관리** 아래에서 **Azure Advisor**를 클릭합니다.  
 Advisor 대시보드가 표시됩니다.

4. Advisor 대시보드에서 **고가용성** 탭을 클릭한 다음 권장 사항을 받아보려는 구독을 선택합니다.

> [!NOTE]
> Advisor 권장 사항에 액세스하려면 먼저 Advisor에 *구독을 등록*해야 합니다. 구독은 *구독 소유자*가 Advisor 대시보드를 시작하고 **권장 사항 가져오기** 단추를 클릭할 때 등록됩니다. 이 작업은 *한 번만* 수행하면 됩니다. 구독이 등록된 후 구독, 리소스 그룹 또는 특정 리소스에 대한 *소유자*, *참여자* 또는 *리더*로 Advisor 권장 사항에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Azure Advisor 소개](advisor-overview.md)
* [Advisor 시작](advisor-get-started.md)
* [Advisor 비용 권장 사항](advisor-performance-recommendations.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)

