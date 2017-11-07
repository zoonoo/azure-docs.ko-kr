---
title: "Azure Monitor에서 근 실시간 메트릭 경고 | Microsoft Docs"
description: "근 실시간 메트릭 경고를 사용하면 1분 간격으로 Azure 리소스 메트릭을 모니터링할 수 있습니다."
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: aeeb6c2fb87e6c19991ef243ee7230f4e8f4e251
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="near-real-time-metric-alerts-preview"></a>근 실시간 메트릭 경고(미리 보기)
이제 Azure Monitor는 근 실시간 메트릭 경고(미리 보기)라는 새로운 유형의 메트릭 경고를 지원합니다. 이 기능은 현재 공개 미리 보기로 제공됩니다.
이러한 경고는 몇 가지 점에서 일반 메트릭 경고와 다릅니다.

- **대기 시간 향상** - 근 실시간 메트릭 경고는 1분 간격으로 메트릭 값의 변경 사항을 모니터링할 수 있습니다.
- **메트릭 조건 세부 제어** - 근 실시간 메트릭 경고를 사용하면 사용자가 경고 규칙을 다양하게 정의할 수 있습니다. 이제 경고는 메트릭의 최대, 최소, 평균 및 전체 값을 모니터링하도록 지원합니다.
- **여러 가지 메트릭의 결합 모니터링** - 근 실시간 메트릭 경고는 단일 규칙을 가진 다중 메트릭(현재 두 개)을 모니터링할 수 있습니다. 두 메트릭이 지정된 기간 동안 해당 임계값을 위반하는 경우 경고가 트리거됩니다.
- **모듈식 알림 시스템** - 근 실시간 메트릭 경고는 [작업 그룹](monitoring-action-groups.md)을 사용합니다. 이 기능을 사용하면 모듈 방식으로 작업을 만들고 여러 경고 규칙에 다시 사용할 수 있습니다.

> [!NOTE]
> 근 실시간 메트릭 경고 기능은 현재 공개 미리 보기로 제공됩니다. 기능 및 사용자 환경을 변경할 수 있습니다.
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>근 실시간 메트릭 경고을 만들 수 있는 리소스는 무엇인가요?
근 실시간 메트릭 경고에서 지원되는 리소스 종류의 전체 목록:

* Microsoft.ApiManagement/service
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Sql/servers/elasticpools
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.Timeseriesinsights
* Microsoft.CognitiveServices/accounts


## <a name="create-a-near-real-time-metric-alert"></a>근 실시간 메트릭 경고 만들기
현재 근 실시간 메트릭 경고는 Azure Portal을 통해서만 만들 수 있습니다. PowerShell, CLI(명령줄 인터페이스) 및 Azure Monitor REST API를 통해 근 실시간 메트릭 경고를 구성하는 지원이 곧 제공됩니다.

1. [포털](https://portal.azure.com/)에서 모니터링하려는 리소스를 찾아 선택합니다. 이 리소스는 [이전 섹션](#what-resources-can-i-create-near-real-time-metric-alerts-for)에 나열된 리소스 종류 중 하나여야 합니다. 모니터 > 경고에서 지원되는 모든 리소스 형식에 대해 동일하게 수행할 수 있습니다.

2. MONITORING 섹션에서 **경고** 또는 **경고 규칙**을 선택합니다. 텍스트와 아이콘은 리소스마다 약간씩 다를 수 있습니다.
   ![모니터링](./media/insights-alerts-portal/AlertRulesButton.png)

3. **근 실시간 메트릭 알림(미리 보기) 추가** 명령을 클릭합니다. 명령이 회색으로 표시되는 경우 리소스가 필터에서 선택되었는지 확인합니다.

    ![근 실시간 메트릭 경고 추가 단추](./media/monitoring-near-real-time-metric-alerts/AddNRTAlertButton.png)

4. 경고 규칙의 **이름**을 지정하고 **설명**을 선택합니다. 알림 이메일에도 표시되는 항목입니다.
5. 모니터링할 **메트릭**을 선택하고 해당 메트릭에 대한 **조건**, **시간 집계** 및 **임계값**을 선택합니다. 선택적으로 모니터링할 다른 **메트릭**을 선택하고 두 번째 메트릭에 대한 **조건**, **시간 집계** 및 **임계값**을 선택합니다. 

    ![근 실시간 메트릭 알림1 추가](./media/monitoring-near-real-time-metric-alerts/AddNRTAlert1.png) ![근 실시간 메트릭 알림2 추가](./media/monitoring-near-real-time-metric-alerts/AddNRTAlert2.png)
6. 경고를 트리거하기 전에 메트릭 규칙을 만족해야 하는 **기간**을 선택합니다. 예를 들어 "지난 5분" 기간을 사용하고 경고가 80% 이상인 CPU를 찾는다면 이 경고는 CPU가 5분 동안 계속 80%(및 500MB 이상의 네트워크 입력)를 넘으면 트리거됩니다. 첫 번째 트리거가 발생한 후 CPU가 5분 동안 80% 미만을 유지하면 다시 트리거됩니다. 경고는 **평가 빈도**에 따라 평가됩니다.


6. 드롭다운 목록에서 적절한 **심각도**를 선택합니다.

7. 기존 또는 새 **작업 그룹**을 사용할지 여부를 지정합니다.

8. **새** 작업 그룹을 만들도록 선택한 경우 동작 그룹에 이름과 약식 이름을 지정하고 작업(SMS, 전자 메일, Webhook)을 지정하고 해당 세부 정보를 입력합니다.


8. 경고 만들기가 완료되면 **확인**을 선택합니다.   

앞서 설명한 대로 몇 분 안에 경고가 활성화 및 트리거됩니다.

## <a name="managing-near-real-time-metric-alerts"></a>근 실시간 메트릭 경고 관리
경고를 만든 후 해당 경고를 선택하여 다음을 수행할 수 있습니다.

* 전날의 메트릭 임계값 및 실제 값을 표시하는 그래프 확인
* 편집 또는 삭제
* 해당 경고에 대한 알림 수신을 일시 중지 또는 재개하려면 **사용 중지** 또는 **사용**하도록 설정합니다.



