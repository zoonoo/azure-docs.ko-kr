---
title: "Azure Advisor 고가용성 권장 사항 | Microsoft Docs"
description: "Azure Advisor를 사용하여 Azure 배포의 가용성을 향상시킵니다."
services: advisor
documentationcenter: NA
author: kumudd
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
translationtype: Human Translation
ms.sourcegitcommit: e7fc6cb4ee4752bb7ed90a8f64edcc7c82ec3a88
ms.openlocfilehash: a6ee1d018bf33aa76d089d20f6e71318a3586aa8
ms.lasthandoff: 02/21/2017

---

# <a name="advisor-high-availability-recommendations"></a>Advisor 고가용성 권장 사항

Advisor는 업무상 중요한 응용 프로그램의 연속성을 보장하고 향상시키는 데 도움이 됩니다. Advisor 대시보드의 **고가용성** 탭에서 Advisor의 고가용성 권장 사항을 가져올 수 있습니다.

![](./media/advisor-high-availability-recommendations/advisor-high-availability-tab.png)


## <a name="virtual-machines-without-an-availability-set"></a>가용성 집합이 없는 가상 컴퓨터

Advisor는 가용성 집합에 속하지 않는 가상 컴퓨터를 식별하고 이러한 가상 컴퓨터를 가용성 집합으로 이동할 것을 권장합니다. 응용 프로그램에 중복성을 제공하기 위해 여러 개의 가상 컴퓨터를 가용성 집합으로 그룹화하는 것이 좋습니다. 이 구성은 계획된 유지 관리 또는 계획되지 않은 유지 관리 이벤트 중에 적어도 하나의 가상 컴퓨터를 사용할 수 있고 Azure Virtual Machine SLA가 충족되도록 합니다. 가상 컴퓨터에 대한 가용성 집합을 만들거나, 기존 가용성 집합에 가상 컴퓨터를 추가할 수도 있습니다.

> [!NOTE]
> 가용성 집합을 만들도록 선택한 경우 가용성 집합을 만든 후에 해당 가용성 집합에 적어도 하나 이상의 가상 컴퓨터를 추가해야 합니다. 중단 시간 동안 컴퓨터 중 하나를 사용할 수 있도록 하기 위해 가용성 집합에 둘 이상의 가상 컴퓨터를 그룹화하는 것이 좋습니다.

![](./media/advisor-high-availability-recommendations/advisor-high-availability-create-availability-set.png)

## <a name="availability-sets-with-a-single-virtual-machine"></a>단일 가상 컴퓨터가 있는 가용성 집합 

Advisor는 단일 가상 컴퓨터를 포함하는 가용성 집합을 식별하고 여기에 하나 이상의 가상 컴퓨터를 추가할 것을 권장합니다. 응용 프로그램에 중복성을 제공하기 위해 여러 개의 가상 컴퓨터를 가용성 집합으로 그룹화하는 것이 좋습니다. 이 구성은 계획된 유지 관리 또는 계획되지 않은 유지 관리 이벤트 중에 적어도 하나의 가상 컴퓨터를 사용할 수 있고 Azure Virtual Machine SLA가 충족되도록 합니다. 가상 컴퓨터를 만들거나 기존 가상 컴퓨터를 사용하도록 선택하고 해당 컴퓨터를 가용성 집합에 추가할 수 있습니다.  


![](./media/advisor-high-availability-recommendations/advisor-high-availability-add-vm-to-availability-set.png)

## <a name="virtual-machines-with-standard-disks"></a>표준 디스크를 포함하는 가상 시스템

Advisor는 표준 디스크를 포함하는 가상 컴퓨터를 식별하고 프리미엄 디스크로 업그레이드할 것을 권장합니다.  
Azure 프리미엄 저장소는 I/O 사용량이 많은 작업을 실행하는 가상 컴퓨터에서 대기 시간이 짧은 고성능 디스크 지원을 제공합니다. 프리미엄 저장소를 사용하는 가상 컴퓨터 디스크는 SSD(솔리드 스테이트 드라이브)에 데이터를 저장합니다. 응용 프로그램이 최고 성능을 낼 수 있도록 높은 IOPS가 필요한 모든 가상 컴퓨터 디스크를 Azure 프리미엄 디스크로 마이그레이션하는 것이 좋습니다. 디스크에 높은 IOPS가 필요하지 않으면 표준 저장소에 유지 관리하여 비용을 제한할 수 있습니다. 표준 저장소는 SSD 대신 HDD(하드 디스크 드라이브)에 가상 컴퓨터 디스크 데이터를 저장합니다. 가상 컴퓨터 디스크를 프리미엄 디스크로 마이그레이션하도록 선택할 수 있습니다. 프리미엄 디스크는 대부분의 가상 컴퓨터 SKU에서 지원됩니다. 그러나 일부 경우에 프리미엄 디스크에 사용하려는 경우 가상 컴퓨터 SKU도 업그레이드해야 할 수 있습니다.   

![](./media/advisor-high-availability-recommendations/advisor-high-availability-upgrade-to-premium-disks.png) 

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Advisor의 고가용성 권장 사항에 액세스하는 방법

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 탐색 창에서 **더 많은 서비스**를 클릭하고 서비스 메뉴 창에서 **모니터링 및 관리**로 스크롤한 후 **Azure Advisor**를 클릭합니다. 그러면 Advisor 대시보드가 시작됩니다. 
3. Advisor 대시보드에서 **고가용성** 탭을 클릭하고 권장 사항을 받아보려는 구독을 선택합니다.

> [!NOTE]
> Advisor 권장 사항을 보려면 먼저 구독을 Advisor에 **등록**해야 합니다. 구독은 **구독 소유자**가 Advisor 대시보드를 시작하고 **권장 사항 가져오기** 단추를 클릭할 때 등록됩니다. 이 작업은 **한 번만** 수행하면 됩니다. 구독이 등록되면 구독, 리소스 그룹 또는 특정 리소스에 대한 **소유자**, **참여자** 또는 **리더**는 Advisor 권장 사항에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음 리소스를 참조하세요.
-  [Azure Advisor 소개](advisor-overview.md)
-  [Advisor 시작](advisor-get-started.md)
-  [Advisor 보안 권장 사항](advisor-security-recommendations.md)
-  [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
-  [Advisor 비용 권장 사항](advisor-performance-recommendations.md)

