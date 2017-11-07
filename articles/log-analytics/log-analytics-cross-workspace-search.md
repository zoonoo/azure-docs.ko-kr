---
title: "Azure Log Analytics 작업 영역에서 쿼리 수행 | Microsoft Docs"
description: "이 문서는 수행할 예제로 구독에서 여러 작업 영역을 쿼리할 수 있는 방법을 설명합니다."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: magoedte
ms.openlocfilehash: 6b6dc6f472a87178c006301f4f692aeff15e257e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="perform-queries-across-multiple-log-analytics-workspaces"></a>여러 Log Analytics 작업 영역에서 쿼리 수행

이전에는 Azure Log Analytics로 현재 작업 영역 내의 데이터만 분석할 수 있었기 때문에 구독에 정의된 여러 작업 영역을 쿼리할 수 없었습니다.  

이제 여러 작업 영역에 걸쳐 쿼리를 수행할 수 있기 때문에 데이터에 대한 시스템 전반 뷰를 파악할 수 있습니다.  이러한 유형의 쿼리는 Azure Portal이 아닌 [고급 포털](log-analytics-log-search-portals.md#advanced-analytics-portal)에서만 수행할 수 있습니다.  

## <a name="querying-across-log-analytics-workspaces"></a>Log Analytics 작업 영역 전체 쿼리
쿼리에 다른 작업 영역을 참조하려면 *작업 영역* ID를 사용합니다.  예를 들어 다음 쿼리는 현재 작업 영역과 *contosoretail-it*라는 다른 작업 영역의 업데이트 테이블의 분류에 필요한 요약된 업데이트 수를 반환합니다.  


## <a name="identifying-resources"></a>리소스 식별
작업 영역 식별은 여러 가지 방법으로 수행될 수 있습니다.

* 리소스 이름 - 사람이 읽을 수 있는 작업 영역 이름이며 *구성 요소 이름*이라고도 합니다. 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >작업 영역을 이름으로 식별하면 액세스 가능한 모든 구독에서 고유한 것으로 가정합니다. 지정된 이름의 응용 프로그램이 여러 개 있으면 모호성으로 인해 쿼리가 실패합니다. 이런 경우 다른 식별자 중 하나를 사용해야 합니다.

* 정규화된 이름 - 구독 이름, 리소스 그룹 및 구성 요소 이름이 *subscriptionName/resourceGroup/componentName* 형식으로 구성된 작업 영역의 "전체 이름"입니다. 

    `workspace('contoso/contosoretail/development').requests | count `

    >[!NOTE]
    >Azure 구독 이름은 고유하지 않기 때문에 이 식별자는 모호할 수 있습니다. 
    >

* 작업 영역 ID - 작업 영역 ID는 GUID(Globally Unique Identifier)로 나타낸 각 작업 영역에 할당된 변경되지 않는 고유한 식별자입니다.

    `workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count`

* Azure Resource ID – Azure에서 정의한 작업 영역의 고유 ID입니다. 리소스 이름이 모호한 경우에 사용합니다.  작업 영역의 형: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspaces/componentName*.  

    예:
    ``` 
    `workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
    ```

## <a name="next-steps"></a>다음 단계

Log Analytics에 사용할 수 있는 모든 쿼리 구문 옵션을 보려면 [Log Analytics 로그 검색 참조](https://docs.loganalytics.io/docs/Language-Reference)를 검토하세요.    