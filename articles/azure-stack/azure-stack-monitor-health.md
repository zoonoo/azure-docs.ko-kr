---
title: "Azure Stack의 상태 및 경고 모니터링 | Microsoft Docs"
description: "Azure Stack에서 상태 및 경고를 모니터링하는 방법을 알아봅니다."
services: azure-stack
documentationcenter: 
author: chasat-MS
manager: dsavage
editor: 
ms.assetid: 69901c7b-4673-4bd8-acf2-8c6bdd9d1546
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: chasat
translationtype: Human Translation
ms.sourcegitcommit: a84441cfc41872e32b5ebd4a5d68d4b6919c33c0
ms.openlocfilehash: 41b311a05615e67f568f8708a7c0029836531aba


---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>Azure Stack의 상태 및 경고 모니터링
Microsoft Azure Stack Technical Preview 2에서는 여러분 같은 클라우드 관리자가 Azure Stack의 상태 및 경고를 볼 수 있는 새로운 인프라 모니터링 기능이 도입되었습니다.

이 Azure Stack 미리 보기 릴리스에서는 **지역 관리** 타일에 제공되는 지역 관리 기능 집합이 도입되었습니다. 기본적으로 관리 구독 멤버에 대해 고정되는 지역 관리 타일은 배포된 모든 Azure Stack 지역을 나열합니다. 또한 각 지역에 대한 활성 위험 및 경고 알림의 수를 계산합니다. 이 타일은 Azure Stack의 상태 및 경고 기능으로 들어가는 진입점입니다.

 ![지역 관리 타일](media/azure-stack-monitor-health/image1.png)

 ## <a name="understand-health-in-azure-stack"></a>Azure Stack의 상태 이해

 상태 및 경고는 Azure Stack에서 상태 리소스 공급자가 관리합니다. Azure Stack 구성 요소는 Azure Stack이 배포 및 구성되는 동안 상태 리소스 공급자에 등록합니다. 등록을 수행하는 목적은 상태 및 경고를 표시하기 위함입니다. Azure Stack의 상태는 간단한 개념입니다. 등록된 구성 요소 인스턴스가 있으면 해당 구성 요소의 상태에 최악의 활성 경고 심각도인 경고 또는 위험이 반영됩니다.
 
 Azure Stack 포털과 Rest API 및 PowerShell을 통해 구성 요소의 상태를 볼 수 있습니다. TP2 릴리스에서는 인프라 역할의 상태를 볼 수 있습니다. 그러나 경고 및 상태를 보고하는 인프라 역할은 상태 컨트롤러밖에 없습니다. 이후 릴리스에서는 다른 인프라 역할도 경고를 표시하고 상태를 보고할 것입니다.

![인프라 역할 목록](media/azure-stack-monitor-health/image2.png)
 
## <a name="view-alerts"></a>경고 보기

각 Azure Stack 영역에 대한 활성 경고 목록은 지역 관리 블레이드에서 바로 사용할 수 있습니다.  기본 구성의 첫 번째 타일은 지역에 대한 위험 및 경고 요약 정보를 표시하는 경고 타일입니다. 이 블레이드의 다른 타일과 마찬가지로 경고 타일을 대시보드에 고정하면 신속하게 액세스할 수 있습니다.   

![경고를 표시하는 경고 타일](media/azure-stack-monitor-health/image3.png)

**경고** 타일을 선택하면 지역에 대한 모든 활성 경고 목록으로 이동됩니다. 타일 내에서 **위험** 또는 **경고** 줄 항목을 선택하면 필터링된 경고 목록(위험 또는 경고)으로 이동됩니다. 

![필터링된 경고 알림](media/azure-stack-monitor-health/image4.png)
  
경고 블레이드는 상태(활성 또는 종결) 및 심각도(위험 또는 경고)를 기준으로 필터링하는 기능을 지원합니다. 기본 보기에는 모든 활성 경고가 표시됩니다. 모든 종결된 경고는&7;일 후 시스템에서 제거됩니다.

![위험 또는 경고 상태로 필터링하는 필터 창](media/azure-stack-monitor-health/image5.png)

또한 경고 목록 블레이드에는 목록 보기를 생성하는 데 사용되는 Rest API를 표시하는 **보기 API**라는 작업이 표시됩니다. 이 작업은 기존 데이터 센터 모니터링, 보고 및 티켓팅 솔루션의 자동화 및 통합에 사용할 경고를 쿼리할 수 있는 Rest API를 빠르게 숙지하는 방법을 제공합니다. 

![Rest API를 보여주는 보기 API 옵션](media/azure-stack-monitor-health/image6.png)

경고 목록 블레이드에서 경고를 선택하여 **알림 세부 정보** 블레이드로 이동할 수 있습니다. 경고 세부 정보 블레이드는 경고와 관련된 모든 필드를 표시하며 영향을 받는 구성 요소 및 경고의 원본을 신속하게 탐색할 수 있습니다. 예를 들어 인프라 역할 인스턴스 중 하나가 오프라인이 되거나 액세스할 수 없게 되면 다음 경고가 발생합니다. 인프라 역할 인스턴스가 다시 온라인 상태가 되면 이 경고는 자동으로 종료됩니다. 

![경고 세부 정보 블레이드](media/azure-stack-monitor-health/image7.png)

지금까지 Azure Stack의 상태 및 경고 보기에 대해 알아보았으며, 이제부터는 Azure Stack을 업데이트하는 방법을 자세히 알아보겠습니다.

## <a name="next-steps"></a>다음 단계
[Azure Stack의 업데이트 관리](azure-stack-updates.md)




<!--HONumber=Feb17_HO3-->


