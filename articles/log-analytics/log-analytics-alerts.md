---
title: "OMS Log Analytics에서 경고 개요 | Microsoft Docs"
description: "Log Analytics의 경고는 OMS 저장소의 중요한 정보를 식별하며 문제를 미리 알리거나 작업을 호출하여 문제 해결을 시도합니다.  이 문서에서는 경고 규칙을 만드는 방법을 설명하고 규칙에서 실행할 수 있는 여러 가지 작업을 자세히 설명합니다."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: ec7167fb36e54219957629699f33dfce950f86d5
ms.openlocfilehash: 48d921650dbbf3f9cc2a8a0e265e0c7deaebdafd
ms.lasthandoff: 03/01/2017


---
# <a name="respond-to-issues-in-log-analytics-using-alerts"></a>경고를 사용하여 Log Analytics의 문제에 대응

Log Analytics의 경고는 Log Analytics 리포지토리에서 중요한 정보를 식별합니다.  이 정보를 사용하여 문제를 사전에 알리거나 문제에 대응하여 자동화된 조치를 취할 수 있습니다.  이 문서에서는 경고를 만들고 사용하는 방법에 대한 개요를 제공합니다.  


>[!NOTE]
> 현재 공개 미리 보기인 미터법 경고 규칙에 대한 자세한 내용은 [공개 미리 보기의 새 미터법 경고 규칙 유형](https://blogs.technet.microsoft.com/msoms/2016/11/22/new-metric-measurement-alert-rule-type-in-public-preview/)을 참조하세요.

## <a name="alert-rules"></a>경고 규칙

경고는 일정한 간격으로 로그 검색을 자동으로 실행하는 경고 규칙에 의해 만들어집니다.  로그 검색 결과가 특정 조건과 일치하는 경우 경고 레코드가 만들어집니다.  규칙에 따라 하나 이상의 작업이 자동으로 실행되어 경고를 미리 알리거나 다른 프로세스를 호출할 수도 있습니다.  

![Log Analytics rudrh](media/log-analytics-alerts/overview.png)


경고 규칙은 다음 세부 정보에 의해 정의됩니다.

- **로그 검색**.  경고 규칙이 실행될 때마다 실행되는 쿼리입니다.  이 쿼리에서 반환된 레코드는 경고를 생성할지 여부를 결정하는 데 사용됩니다.
- **기간**.  쿼리에 대한 시간 범위를 지정합니다.  쿼리에서는 현재 시간의 이 범위 내에서 생성된 레코드만 반환합니다.  이는 5 분에서 24 시간 사이의 임의 값일 수 있습니다. 예를 들어 기간을 60 분으로 설정했는데 오후 1시 15분에 쿼리를 실행하면 오후 12시 15분부터 오후 1시 15분 사이의 레코드만 반환됩니다.
- **빈도**.  쿼리를 실행해야 하는 빈도를 지정합니다. 5 분에서 24 시간 사이의 임의 값일 수 있습니다. 기간보다 작거나 같아야 합니다.  값이 기간보다 큰 경우 레코드가 누락될 위험이 있습니다.<br>예를 들어 30분의 기간과 60분의 빈도를 사용하는 것이 좋습니다.  쿼리가 1:00에 실행되면 오후 12:30 및 1:00 사이의 레코드를 반환합니다.  1:30 및 2:00 사이의 레코드를 반환하게 된다면 다음으로 쿼리가 실행되는 시간은 2:00입니다.  1:00 및 1:30 사이에 생성된 레코드는 평가되지 않습니다.
- **임계값**.  로그 검색에서 반환된 레코드 수가 임계값을 초과하면 경고가 생성됩니다.

## <a name="creating-alert-rules"></a>경고 규칙 만들기
여러 방법으로 경고 규칙을 만들고 수정할 수 있습니다.  자세한 지침은 다음 문서를 참조하세요.  

- [OMS 포털](log-analytics-alerts-creating.md)을 사용하여 경고 규칙 만들기
- [Resource Manager 템플릿](log-analytics-template-workspace-configuration.md)을 사용하여 경고 규칙 만들기
- [REST API](log-analytics-api-alerts.md)를 사용하여 경고 규칙 만들기

## <a name="alert-actions"></a>경고 작업

경고 레코드를 생성할 뿐만 아니라 경고 규칙으로 경고를 만들 때 하나 이상의 작업을 실행할 수 있습니다.  경고에 대한 응답으로 메일을 보내거나 수정 작업을 시도하는 프로세스를 시작하는 작업을 사용할 수 있습니다.  

작업을 활용하여 Log Analytics 기능을 다른 서비스로 보강할 수도 있습니다.  예를 들어 현재는 SMS 또는 전화를 사용하여 알림을 제공하는 기능이 지원되지 않습니다.  이러한 기능을 제공하는 [PagerDuty](https://www.pagerduty.com/)와 같은 서비스 호출을 통해 경고 규칙에 웹후크 작업을 사용할 수 있습니다.  [OMS Log Analytics에서 경고 웹후크 작업을 만들어 Slack에 메시지 보내기](log-analytics-alerts-webhooks.md)에서 [Slack](https://slack.com/)을 사용하여 메시지를 보내기 위한 웹후크를 만드는 예를 연습할 수 있습니다.

다음 표에는 수행할 수 있는 작업이 나열됩니다.  [Log Analytics에서 경고 규칙에 작업 추가](log-analytics-alerts-actions.md)에서 이러한 각 내용에 대해 알아볼 수 있습니다. 

| 작업 | 설명 |
|:--|:--|
| Email  |     한 명 이상의 수신자에게 경고의 세부 정보가 포함된 전자 메일을 보냅니다. |
| 웹후크 | 단일 HTTP POST 요청을 통해 외부 프로세스를 호출합니다. |
| Runbook | Azure Automation에서 Runbook 시작 |


## <a name="alerting-scenarios"></a>경고 시나리오

### <a name="events"></a>이벤트
단일 이벤트에 대해 경고하려면 결과 수가 **0보다 크고** 빈도와 기간이 모두 **5분**인 경고 규칙을 만듭니다.  그러면 매 5 분마다 쿼리를 실행하고 쿼리를 마지막 실행한 이후 생성된 단일 이벤트 발생을 확인합니다.  빈도가 더 길면 수집 중인 이벤트와 생성 중인 경고 사이의 시간이 지연될 수 있습니다.  다음과 유사한 쿼리를 사용하여 관심이 있는 이벤트를 지정합니다.

    Type=Event Source=MyApplication EventID=7019 

일부 응용 프로그램은 경고를 발생시키지 않아야 할 가끔씩 발생하는 오류를 기록할 수 있습니다.  예를 들어 응용 프로그램이 오류 이벤트를 생성한 프로세스를 재시도한 후 다음에 성공할 수 있습니다.  이 경우 특정 기간 내에 복수의 이벤트가 생성되지 않은 한 경고를 만들지 않는 것이 좋습니다.  이렇게 하려면 동일한 쿼리를 사용하지만 임계값은 더 높은 값으로 설정합니다.  예를 들어 30분에 5개의 이벤트를 경고하려면 빈도를 **5분**으로, 기간을 **30분**으로, 결과 수를 **4보다 큰 수**로 설정합니다.    

경우에 따라 이벤트가 없을 때 경고를 만드는 것이 좋습니다.  예를 들어 프로세스에서 일반 이벤트가 올바르게 작동 중임을 나타내기 위해 일반 이벤트를 기록할 수 있습니다.  특정 기간 내에 이러한 이벤트 중 하나가 기록되지 않으면 경고를 만들어야 합니다.  이 경우 결과 수를 **1보다 작게** 설정합니다.

### <a name="performance-alerts"></a>성능 경고
[성능 데이터](log-analytics-data-sources-performance-counters.md)는 이벤트와 유사한 레코드로 Log Analytics 리포지토리에 저장됩니다.  성능 카운터가 특정 임계값을 초과하는 경우 경고하도록 하려면 해당 임계값을 쿼리에 포함시켜야 합니다.

예를 들어 프로세서가 90%를 초과하여 실행되는 경우 경고하려면 다음과 같은 쿼리를 사용하고 경고 규칙에 대한 결과 값을 **0보다 크게** 설정합니다.

    Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90

특정 기간에 프로세서 평균 사용량이 90%를 초과하는 경우에 경고하려면 다음과 같은 [측정 명령](log-analytics-search-reference.md#commands)을 사용하여 쿼리하고 경고 규칙에 대한 임계값을 **0보다 크게** 설정합니다. 

    Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer | where AggregatedValue>90


## <a name="alert-records"></a>경고 레코드
Log Analytics의 규칙에 의해 만든 경고 레코드에는 **경고**의 **유형** 및 **OMS**의 **SourceSystem**이 있습니다.  이들은 다음 표의 속성을 가집니다.

| 속성 | 설명 |
|:--- |:--- |
| 형식 |*경고* |
| SourceSystem |*OMS* |
| AlertName |경고의 이름입니다. |
| AlertSeverity |경고의 심각도 수준입니다. |
| LinkToSearchResults |경고를 생성한 쿼리에서 레코드를 반환하는 Log Analytics 로그 검색에 대한 링크입니다.. |
| 쿼리 |실행된 쿼리의 텍스트입니다. |
| QueryExecutionEndTime |쿼리에 대한 시간 범위의 끝입니다. |
| QueryExecutionStartTime |쿼리에 대한 시간 범위의 시작입니다. |
| ThresholdOperator | 경고 규칙에서 사용하는 연산자입니다. |
| ThresholdValue | 경고 규칙에서 사용하는 값입니다. |
| TimeGenerated |경고가 만들어진 날짜 및 시간입니다. |

[경고 관리 솔루션](log-analytics-solution-alert-management.md) 및 [Power BI 내보내기](log-analytics-powerbi.md)에 의해 만들어진 다른 종류의 경고 레코드가 있습니다.  이들은 모두 **경고**의 **유형**을 갖지만 해당 **SourceSystem**에 의해 구별됩니다.


## <a name="next-steps"></a>다음 단계
* [OMS 포털](log-analytics-alerts-creating.md)을 사용하여 경고 규칙을 만듭니다.
* SCOM(System Center Operations Manager)에서 수집한 경고와 함께 Log Analytics에서 만든 경고를 분석하려면 [경고 관리 솔루션](log-analytics-solution-alert-management.md) 을 설치합니다.
* 경고를 생성할 수 있는 [로그 검색](log-analytics-log-searches.md) 에 대해 자세한 내용을 읽습니다.
* 경고 규칙을 사용하여 [웹후크를 구성](log-analytics-alerts-webhooks.md) 하는 연습을 완료합니다.  
* 경고에 의해 식별된 문제를 수정하는 [Azure 자동화의 Runbook](https://azure.microsoft.com/documentation/services/automation) 을 작성하는 방법에 대해 알아봅니다.


