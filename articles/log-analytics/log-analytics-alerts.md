---
title: Log Analytics의 경고 | Microsoft Docs
description: Log Analytics의 경고는 OMS 저장소의 중요한 정보를 식별하며 문제를 미리 알리거나 작업을 호출하여 문제 해결을 시도합니다.  이 문서에서는 경고 규칙을 만드는 방법을 설명하고 규칙에서 실행할 수 있는 여러 가지 작업을 자세히 설명합니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn

ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/22/2016
ms.author: bwren

---
# <a name="alerts-in-log-analytics"></a>Log Analytics의 경고
Log Analytics의 경고는 OMS 저장소에서 중요한 정보를 식별합니다.  경고 규칙은 일정에 따라 로그 검색을 자동으로 실행하고 결과가 특정 조건과 일치하면 경고 레코드를 생성합니다.  그런 다음 규칙에 따라 하나 이상의 작업이 자동으로 실행되어 경고를 미리 알리거나 다른 프로세스를 호출할 수 있습니다.   

![Log Analytics rudrh](media/log-analytics-alerts/overview.png)

## <a name="creating-an-alert-rule"></a>경고 규칙 만들기
경고 규칙을 만들려면 경고를 호출해야 하는 레코드에 대한 로그 검색을 만드는 것으로 시작합니다.  그러면 **경고** 단추를 사용할 수 있으며 이 단추를 사용하여 경고 규칙을 만들고 구성할 수 있습니다.

1. OMS 개요 페이지에서 **로그 검색**을 클릭합니다.
2. 새 로그 검색 쿼리를 만들거나 저장된 로그 검색을 선택합니다. 
3. 페이지 위쪽에서 **경고**를 클릭하여 **경고 규칙 추가** 화면을 엽니다.
4. 경고를 구성하는 옵션에 대한 자세한 내용은 아래 표를 참조하세요.
5. 경고 규칙에 대한 기간을 제공하는 경우 해당 기간에 대한 검색 조건과 일치하는 기존 레코드 수가 표시됩니다.  이 정보는 예상하는 결과 수를 제공할 빈도를 결정하는 데 도움이 될 수 있습니다.
6. **저장** 을 클릭하여 경고 규칙을 완료합니다.  실행을 즉시 시작합니다.

![경고 규칙 추가](media/log-analytics-alerts/add-alert-rule.png)

| 속성 | 설명 |
|:--- |:--- |
| **경고 정보** | |
| 이름 |경고 규칙을 식별하는 고유 이름입니다. |
| 심각도 |이 규칙에 의해 생성되는 경고의 심각도입니다. |
| 검색 쿼리 |**현재 검색 쿼리 사용** 을 선택하여 현재 쿼리를 사용하거나 목록에서 기존 저장된 검색을 선택합니다.  쿼리 구문이 텍스트 상자에 제공되며 필요하면 텍스트 상자의 내용을 수정할 수 있습니다. |
| 기간 |쿼리에 대한 시간 범위를 지정합니다.  쿼리에서는 현재 시간의 이 범위 내에서 생성된 레코드만 반환합니다.  이는 5 분에서 24 시간 사이의 임의 값일 수 있습니다.  이는 경고 빈도보다 크거나 같아야 합니다.  <br><br>  예를 들어 기간을 60 분으로 설정했는데 오후 1시 15분에 쿼리를 실행하면 오후 12시 15분부터 오후 1시 15분 사이의 레코드만 반환됩니다. |
| **일정** | |
| 임계값 |경고를 만드는 시기에 대한 조건입니다.  쿼리에서 반환된 레코드 수가 이 조건과 일치하면 경고가 생성됩니다. |
| 경고 빈도 |쿼리를 실행해야 하는 빈도를 지정합니다.  5 분에서 24 시간 사이의 임의 값일 수 있습니다.  기간보다 작거나 같아야 합니다. |
| 알림 표시 안 함 |경고 규칙 표시 안 함 기능을 켜면 규칙에 대한 작업이 새 경고를 만든 후 정의된 기간 동안 비활성화됩니다.  규칙은 여전히 실행되고 있으며 조건을 만족하면 경고 레코드를 생성합니다.  이 기능을 사용하여 중복 작업을 실행하지 않고 문제를 해결할 시간 여유를 갖게 됩니다. |
| **actions** | |
| 메일 알림 |경고가 트리거될 때 전자 메일을 보내려면 **예** 를 지정합니다. |
| 제목 | |
| 전자 메일의 제목입니다.  메일의 본문을 수정할 수 없습니다. | |
| 받는 사람 |모든 전자 메일 받는 사람의 주소입니다.  둘 이상의 주소를 지정하는 경우 주소를 세미콜론(;)으로 구분합니다. |
| 웹후크 |경고가 트리거될 때 웹후크를 호출하려면 **예** 를 지정합니다. |
| Webhook URL |웹후크의 URL입니다. |
| 사용자 지정 JSON 페이로드 포함 |기본 페이로드를 사용자 지정 페이로드로 바꾸려면 이 옵션을 선택합니다. |
| 사용자 지정 JSON 페이로드 입력 |웹후크에 대한 사용자 지정 페이로드입니다.  자세한 내용은 이전 섹션을 참조하세요. |
| Runbook |경고가 트리거될 때 Azure 자동화 Runbook을 시작하려면 **예** 를 지정합니다. |
| Runbook 선택 |자동화 솔루션에서 구성한 자동화 계정의 Runbook에서 시작할 Runbook을 선택합니다. |
| 실행 |Azure 클라우드에서 Runbook을 실행하려면 **Azure** 를 선택합니다.  로컬 환경의 **Hybrid Runbook Worker** 에서 Runbook을 실행할 [Hybrid Worker](../automation/automation-hybrid-runbook-worker.md) 를 선택합니다. |

## <a name="manage-alert-rules"></a>경고 규칙 관리
Log Analytics **설정**의 **경고** 메뉴에서 모든 경고 규칙의 목록을 가져올 수 있습니다.  

![경고 관리](./media/log-analytics-alerts/configure.png)

1. OMS 콘솔에서 **설정** 타일을 선택합니다.
2. **경고**를 선택합니다.

이 뷰에서 여러 작업을 수행할 수 있습니다.

* 옆에 있는 **끄기** 를 선택하여 규칙을 사용하지 않도록 설정합니다.
* 옆에 있는 연필 아이콘을 클릭하여 경고 규칙을 편집합니다.
* 옆에 있는 **X** 아이콘을 클릭하여 경고 규칙을 제거합니다. 

## <a name="setting-time-windows"></a>기간 설정
### <a name="event-alerts"></a>이벤트 경고
이벤트는 Windows 이벤트 로그, Syslog 및 사용자 지정 로그와 같은 데이터 원본을 포함합니다.  특정 오류 이벤트가 생성될 때 또는 특정 기간 내에 복수의 오류 이벤트가 생성되는 경우 경고를 만드는 것이 좋습니다.

단일 이벤트에 대해 경고하려면 결과 수를 0보다 큰 수로 설정하고 빈도와 기간을 모두 5 분으로 설정합니다.  그러면 매 5 분마다 쿼리를 실행하고 쿼리를 마지막 실행한 이후 생성된 단일 이벤트 발생을 확인합니다.  빈도가 더 길면 수집 중인 이벤트와 생성 중인 경고 사이의 시간이 지연될 수 있습니다.

일부 응용 프로그램은 경고를 발생시키지 않아야 할 가끔씩 발생하는 오류를 기록할 수 있습니다.  예를 들어 응용 프로그램이 오류 이벤트를 생성한 프로세스를 재시도한 후 다음에 성공할 수 있습니다.  이 경우 특정 기간 내에 복수의 이벤트가 생성되지 않은 한 경고를 만들지 않는 것이 좋습니다.  

경우에 따라 이벤트가 없을 때 경고를 만드는 것이 좋습니다.  예를 들어 프로세스에서 일반 이벤트가 올바르게 작동 중임을 나타내기 위해 일반 이벤트를 기록할 수 있습니다.  특정 기간 내에 이러한 이벤트 중 하나가 기록되지 않으면 경고를 만들어야 합니다.  이 경우 임계값을 *1 보다 작게*설정할 수 있습니다.

### <a name="performance-alerts"></a>성능 경고
[성능 데이터](log-analytics-data-sources-performance-counters.md) 는 이벤트와 유사한 레코드로 OMS 저장소에 저장됩니다.  각 레코드의 값은 이전 30 분 동안 측정된 평균입니다.  성능 카운터가 특정 임계값을 초과하는 경우 경고하도록 하려면 해당 임계값을 쿼리에 포함시켜야 합니다.

예를 들어 프로세서가 30분 동안 90% 넘게 실행하는 경우 경고하게 하려면*Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90*과 같은 쿼리를 사용하고 경고 규칙에 대한 임계값을 *0보다 큰* 값으로 설정할 수 있습니다.  

 [성능 레코드](log-analytics-data-sources-performance-counters.md) 는 각 카운터를 수집하는 빈도와 상관없이 매 30 분마다 집계되며, 기간이 30 분보다 작으면 레코드가 반환되지 않을 수 있습니다.  기간을 30 분으로 설정하면 해당 시간에 대한 평균을 나타내는 각 연결된 소스에 대해 단일 레코드를 얻을 수 있습니다.

## <a name="alert-actions"></a>경고 작업
경고 레코드를 생성할 뿐만 아니라 하나 이상의 작업을 자동으로 실행하도록 경고 규칙을 구성할 수 있습니다.  작업은 경고를 미리 알리거나 감지된 문제의 해결을 시도하는 일부 프로세스를 호출할 수 있습니다.  다음 섹션에서는 현재 사용할 수 있는 작업에 대해 설명합니다.

### <a name="email-actions"></a>전자 메일 작업
전자 메일 작업은 한 명 이상의 수신자에게 경고의 세부 정보가 포함된 전자 메일을 보냅니다.  메일의 제목을 지정할 수 있지만 그 내용은 Log Analytics이 구성한 표준 형식입니다.  메일은 로그 검색에서 반환된 10개까지의 레코드에 대한 세부 정보 외에 경고의 이름과 같은 요약 정보를 포함하고 있습니다.  또한 쿼리에서 전체 레코드 집합을 반환할 Log Analytics의 로그 검색에 대한 링크를 포함하고 있습니다.   메일의 보낸 사람은 *Microsoft Operations Management Suite Team &lt;noreply@oms.microsoft.com&gt;*입니다. 

### <a name="webhook-actions"></a>웹후크 작업
웹후크 작업을 사용하여 단일 HTTP POST 요청을 통해 외부 프로세스를 호출할 수 있습니다.  호출 중인 서비스는 웹후크를 지원하고 수신하는 페이로드를 사용하는 방법을 결정해야 합니다.  또한 요청이 API에서 이해하는 형식으로 되어 있다면 웹후크를 명시적으로 지원하지 않는 REST API를 호출할 수도 있습니다.  경고에 응답하여 웹후크를 사용하는 예는 [Slack](http://slack.com)과 같은 서비스를 사용하여 경고의 세부 정보가 포함된 메시지를 보내거나 [PagerDuty](http://pagerduty.com/)와 같은 서비스에서 인시던트를 생성하는 것입니다.  

샘플 서비스에 대한 웹후크를 사용하여 경고 규칙을 생성하는 전체 연습은 [Log Analytics 경고의 웹후크](log-analytics-alerts-webhooks.md)에서 사용할 수 있습니다.

웹후크는 URL 및 외부 서비스에 보낸 데이터인 JSON 형식의 페이로드를 포함하고 있습니다.  기본적으로 페이로드는 다음 표의 값을 포함합니다.  이 페이로드를 자신만의 사용자 지정 페이로드로 바꾸도록 선택할 수 있습니다.  그러한 경우 각 매개 변수에 대한 표의 변수를 사용하여 해당 값을 사용자 지정 페이로드에 포함할 수 있습니다.

| 매개 변수 | 변수 | 설명 |
|:--- |:--- |:--- |
| AlertRuleName |#AlertRuleName |경고 규칙의 이름입니다. |
| AlertThresholdOperator |#thresholdoperator |경고 규칙에 대한 임계값 연산자입니다.  *보다 큼* 또는 *보다 작음*. |
| AlertThresholdValue |#thresholdvalue |경고 규칙에 대한 임계값입니다. |
| LinkToSearchResults |#LinkToSearchResults |경고를 생성한 쿼리에서 레코드를 반환하는 Log Analytics 로그 검색에 대한 링크입니다.. |
| ResultCount |#searchresultcount |검색 결과의 레코드 수입니다. |
| SearchIntervalEndtimeUtc |#SearchIntervalEndtimeUtc |UTC 형식의 쿼리에 대한 종료 시간입니다. |
| SearchIntervalInSeconds |#searchinterval |경고 규칙에 대한 기간입니다. |
| SearchIntervalStartTimeUtc |#SearchIntervalStartTimeUtc |UTC 형식의 쿼리에 대한 시작 시간입니다. |
| SearchQuery |#SearchQuery |경고 규칙에서 사용하는 로그 검색 쿼리입니다. |
| SearchResults |아래 참조 |JSON 형식의 쿼리에서 반환된 레코드입니다.  처음 5,000 개의 레코드로 제한됩니다. |
| WorkspaceID |#WorkspaceID |OMS 작업 영역의 ID입니다. |

예를 들어 *text*라는 단일 매개변수를 포함하는 다음과 같은 사용자 지정 페이로드를 지정할 수 있습니다.  이 웹후크가 호출하는 서비스는 이 매개 변수를 예상합니다.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

이 예제 페이로드는 웹후크에 보낼 때 다음과 같은 내용으로 확인될 수 있습니다.

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

검색 결과를 사용자 지정 페이로드에 포함하려면 JSON 페이로드의 최상위 속성으로 다음 줄을 추가합니다.  

    "IncludeSearchResults":true

예를 들어 경고 이름과 검색 결과만 포함하는 사용자 지정 페이로드를 만들려면 다음을 사용할 수 있습니다. 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


웹후크를 통해 경고 규칙을 생성하여 [Log Analytics 경고 웹후크 샘플](log-analytics-alerts-webhooks.md)에서 외부 서비스를 시작하는 전체 예제를 연습할 수 있습니다.

### <a name="runbook-actions"></a>Runbook 작업
Runbook 작업은 Azure 자동화에서 Runbook을 시작합니다.  이 유형의 작업을 사용하려면 OMS 작업 영역에 [자동화 솔루션](log-analytics-add-solutions.md) 을 설치하고 구성해야 합니다.  새 경고 규칙을 만들 때 이를 설치하지 않은 경우 해당 설치에 대한 링크가 표시됩니다.  자동화 솔루션에서 구성한 자동화 계정의 Runbook 중에서 선택할 수 있습니다.

Runbook 작업은 [웹후크](../automation/automation-webhooks.md)를 사용하여 Runbook을 시작합니다.  경고 규칙을 만들 때 이름 **OMS 경고 수정** 에 이어 GUID를 가진 Runbook에 대해 새 웹후크가 자동으로 생성됩니다.  

Runbook의 매개 변수를 직접 채울 수는 없으나 [$WebhookData 매개 변수](../automation/automation-webhooks.md)는 해당 경고를 생성한 로그 검색의 결과를 포함한 경고의 세부 정보를 포함합니다.  Runbook이 경고의 속성에 액세스하려면 **$WebhookData**를 매개 변수로 정의해야 합니다.  이 경고는 **$WebhookData**의 **RequestBody** 속성에서 **SearchResults**라고 하는 단일 속성에서 json 형식으로 사용할 수 있습니다.  이 항목은 다음 표의 속성을 가집니다.

| 노드 | 설명 |
|:--- |:--- |
| id |경로와, 검색의 GUID입니다. |
| __metadata |레코드 수 및 검색 결과 상태를 포함하는 경고 관련 정보입니다. |
| value |검색 결과의 각 레코드에 대해 개별 항목입니다.  항목의 상세 정보는 레코드의 속성 및 값과 일치합니다. |

예를 들어, 다음 Runbook은 로그 검색에서 반환된 레코드를 추출하고 각 레코드 유형에 따라 서로 다른 속성을 할당합니다.  Runbook은 JSON에서 **RequestBody**를 변환하여 시작되므로 PowerShell의 개체로 작동할 수 있습니다.

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody
    $Records     = $RequestBody.SearchResults.value

    foreach ($Record in $Records)
    {
        $Computer = $Record.Computer

        if ($Record.Type -eq 'Event')
        {
            $EventNo    = $Record.EventID
            $EventLevel = $Record.EventLevelName
            $EventData  = $Record.EventData
        }

        if ($Record.Type -eq 'Perf')
        {
            $Object    = $Record.ObjectName
            $Counter   = $Record.CounterName
            $Instance  = $Record.InstanceName
            $Value     = $Record.CounterValue
        }
    }


## <a name="alert-records"></a>경고 레코드
Log Analytics의 규칙에 의해 만든 경고 레코드에는 **경고**의 **유형** 및 **OMS**의 **SourceSystem**이 있습니다.  이들은 다음 표의 속성을 가집니다.

| 속성 | 설명 |
|:--- |:--- |
| 형식 |*경고* |
| SourceSystem |*OMS* |
| AlertSeverity |경고의 심각도 수준입니다. |
| AlertName |경고의 이름입니다. |
| 쿼리 |실행된 쿼리의 텍스트입니다. |
| QueryExecutionEndTime |쿼리에 대한 시간 범위의 끝입니다. |
| QueryExecutionStartTime |쿼리에 대한 시간 범위의 시작입니다. |
| TimeGenerated |경고가 만들어진 날짜 및 시간입니다. |

[경고 관리 솔루션](log-analytics-solution-alert-management.md) 및 [Power BI 내보내기](log-analytics-powerbi.md)에 의해 만들어진 다른 종류의 경고 레코드가 있습니다.  이들은 모두 **경고**의 **유형**을 갖지만 해당 **SourceSystem**에 의해 구별됩니다.

## <a name="next-steps"></a>다음 단계
* SCOM(System Center Operations Manager)에서 수집한 경고와 함께 Log Analytics에서 만든 경고를 분석하려면 [경고 관리 솔루션](log-analytics-solution-alert-management.md) 을 설치합니다.
* 경고를 생성할 수 있는 [로그 검색](log-analytics-log-searches.md) 에 대해 자세한 내용을 읽습니다.
* 경고 규칙을 사용하여 [웹후크를 구성](log-analytics-alerts-webhooks.md) 하는 연습을 완료합니다.  
* 경고에 의해 식별된 문제를 수정하는 [Azure 자동화의 Runbook](https://azure.microsoft.com/documentation/services/automation) 을 작성하는 방법에 대해 알아봅니다.

<!--HONumber=Oct16_HO2-->


