<properties
   pageTitle="Log Analytics 경고 REST API"
   description="Log Analytics 경고 REST API를 사용하여 OMS(Operations Management Suite)에서 경고를 만들고 관리할 수 있습니다.  이 문서에서는 다음 작업을 수행하기 위한 API 및 여러 예제의 세부 정보를 제공합니다."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />


# <a name="log-analytics-alert-rest-api"></a>Log Analytics 경고 REST API

Log Analytics 경고 REST API를 사용하여 OMS(Operations Management Suite)에서 경고를 만들고 관리할 수 있습니다.  이 문서에서는 다음 작업을 수행하기 위한 API 및 여러 예제의 세부 정보를 제공합니다.

Log Analytics 검색 REST API는 RESTful이며 Azure Resource Manager REST API를 통해 액세스할 수 있습니다. 이 문서에서 API가 Azure Resource Manager API를 호출하여 단순화하는 공개 소스 명령줄 도구인 [ARMClient](https://github.com/projectkudu/ARMClient)를 사용하여 PowerShell 명령줄에서 액세스하는 예제를 찾을 수 있습니다. ARMClient 및 PowerShell의 사용은 Log Analytics 검색 API에 액세스하는 다양한 옵션 중 하나입니다. 이러한 도구를 사용하면 RESTful Azure Resource Manager API를 통해 OMS 작업 영역을 호출하고 작업 영역 내에서 검색 명령을 수행할 수 있습니다. API은 JSON 형식으로 검색 결과를 출력하여 다양한 프로그래밍 방식으로 검색 결과를 사용하게 됩니다.

## <a name="prerequisites"></a>필수 조건
현재 Log Analytics에 저장된 검색을 사용해서만 경고를 만들 수 있습니다.  자세한 내용은 [로그 검색 REST API](log-analytics-log-search-api.md) 를 참조하세요.

## <a name="schedules"></a>일정
저장된 검색은 하나 이상의 일정을 가질 수 있습니다. 일정은 검색이 실행되는 빈도 및 조건이 식별되는 기간을 정의합니다.
일정은 다음 표의 속성을 가집니다.

| 속성  | 설명 |
|:--|:--|
| 간격 | 검색이 실행되는 빈도입니다. 분 단위로 측정됩니다. |
| QueryTimeSpan | 조건이 평가되는 시간 간격입니다. 간격보다 크거나 같아야 합니다. 분 단위로 측정됩니다. |
| 버전 | 사용 중인 API 버전입니다.  현재 항상 1로 설정해야 합니다. |

예를 들어 간격이 15 분이고 Timespan이 30 분인 이벤트 쿼리를 고려합니다. 이 경우 쿼리는 매 15 분마다 실행되며 조건이 30 분의 기간 동안 계속 True로 확인되었으면 경고가 트리거될 수 있습니다.

### <a name="retrieving-schedules"></a>일정 검색
Get 메서드를 사용하여 저장된 검색에 대한 모든 일정을 검색합니다.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

일정 ID와 Get 메서드를 사용하여 저장된 검색에 대한 특정 일정을 검색합니다.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

다음은 일정에 대한 샘플 응답입니다.

    {
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
        "Interval": 15,
        "QueryTimeSpan": 15
    }

### <a name="creating-a-schedule"></a>일정 만들기
고유 일정 ID와 Put 메서드를 사용하여 새 일정을 만듭니다.  두 일정은 서로 다른 저장된 검색과 연결되었다 하더라도 동일한 ID를 가질 수 없습니다.  OMS 콘솔에서 일정을 만드는 경우 일정 ID에 대해 GUID가 생성됩니다.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>일정 편집
동일한 저장된 검색에 대해 기존 일정 ID와 Put 메서드를 사용하여 해당 일정을 수정합니다.  요청 본문에는 일정의 etag가 포함되어야 합니다.

    $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>일정 삭제
일정 ID와 함께 Delete 메서드를 사용하여 일정을 삭제합니다.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>actions
일정이 여러 작업을 가질 수 있습니다. 작업은 메일 보내기 또는 Runbook 시작과 같은 하나 이상의 수행할 프로세스를 정의하거나 검색 결과가 일부 조건과 일치하는 경우를 결정하는 임계값을 정의할 수 있습니다.  일부 작업은 임계값을 만족할 때 프로세스가 수행되도록 정의합니다.

모든 작업은 다음 표의 속성을 가집니다.  서로 다른 유형의 경고는 아래에 설명하는 서로 다른 추가 속성을 가집니다.

| 속성 | 설명 |
|:--|:--|
| 형식 | 작업의 유형입니다.  현재 가능한 값은 경고 및 웹후크입니다. |
| 이름 | 경고에 대한 표시 이름입니다. |
| 버전 | 사용 중인 API 버전입니다.  현재 항상 1로 설정해야 합니다. |

### <a name="retrieving-actions"></a>작업 검색
Get 메서드를 사용하여 일정에 대한 모든 작업을 검색합니다.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

작업 ID와 함께 Get 메서드를 사용하여 일정에 대한 특정 작업을 검색합니다.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>작업 만들기 또는 편집
일정에 고유한 작업 ID와 Put 메서드를 사용하여 새 작업을 만듭니다.  OMS 콘솔에서 작업을 만드는 경우 GUID는 작업 ID에 대한 것입니다.

동일한 저장된 검색에 대해 기존 작업 ID와 Put 메서드를 사용하여 해당 일정을 수정합니다.  요청 본문에는 일정의 etag가 포함되어야 합니다.

새 작업을 만들기 위한 요청 형식은 작업 유형에 따라 다르므로 이러한 예제를 아래 섹션에 제공합니다.

### <a name="deleting-actions"></a>작업 삭제
작업 ID와 함께 Delete 메서드를 사용하여 작업을 삭제합니다.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>경고 작업
일정은 경고 작업을 한 개만 가져야 합니다.  경고 작업은 다음 표의 섹션 중 하나 이상을 가집니다.  아래에서 각 섹션을 자세히 설명합니다.

| 섹션 | 설명 |
|:--|:--|
| 임계값 | 작업이 실행되기 위한 조건입니다. |  
| EmailNotification | 복수의 받는 사람에게 메일을 보냅니다. |
| 재구성 | Azure 자동화에서 식별된 문제의 해결을 시도하는 Runbook을 시작합니다. |

#### <a name="thresholds"></a>임계값
경고 작업은 임계값을 한 개만 가져야 합니다.  저장된 검색의 결과가 해당 검색과 연결된 작업의 임계값과 일치하는 경우 해당 작업의 다른 프로세스가 실행됩니다.  작업은 임계값을 포함하지 않은 다른 유형의 작업과 함께 사용할 수 있도록 하는 임계값만 포함할 수 있습니다.

임계값은 다음 표의 속성을 가집니다.

| 속성 | 설명 |
|:--|:--|
| 연산자 | 임계값 비교를 위한 연산자입니다. <br> gt = 보다 큰 <br>  lt = 보다 작은 |
| 값 | 임계값에 대한 값입니다. |

예를 들어 간격이 15 분이고 Timespan이 30 분이고 임계값이 10보다 큰 이벤트 쿼리를 고려합니다. 이 경우 쿼리는 매 15 분마다 실행되며 경고는 30 분의 기간 동안 생성된 이벤트 10개를 반환하면 경고가 트리거될 수 있습니다.

다음은 임계값만 가진 작업에 대한 샘플 응답입니다.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

고유 작업 ID와 Put 메서드를 사용하여 일정에 대한 새 임계값 작업을 만듭니다.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

고유 작업 ID와 Put 메서드를 사용하여 일정에 대한 임계값 작업을 수정합니다.  요청 본문에는 작업의 etag가 포함되어야 합니다.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="email-notification"></a>메일 알림
전자 메일 알림은 한 명 이상의 받는 사람에게 메일을 보냅니다.  이들은 다음 표의 속성을 가집니다.

| 속성 | 설명 |
|:--|:--|
| 받는 사람 | 메일 주소 목록입니다. |
| 제목
 | 메일의 제목입니다. |
| 첨부 파일 | 첨부 파일은 현재 지원되지 않으므로이에 대한 값은 항상 "없음"입니다. |

다음은 임계값을 가진 전자 메일 알림 작업에 대한 샘플 응답입니다.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

고유한 작업 ID와 함께 Put 메서드를 사용하여 일정에 대한 새 전자 메일 작업을 만듭니다.  다음 예제에서는 저장된 검색의 결과가 임계값을 초과하는 경우 전자 메일을 보내도록 임계값을 가진 전자 메일 알림을 만듭니다.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $ emailJson

고유 작업 ID와 Put 메서드를 사용하여 일정에 대한 이메일 작업을 수정합니다.  요청 본문에는 작업의 etag가 포함되어야 합니다.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $ emailJson

#### <a name="remediation-actions"></a>수정 작업
수정은 경고에 의해 식별된 문제의 해결을 시도하는 Azure 자동화의 Runbook을 시작합니다.  수정 작업에 사용되는 Runbook에 대한 웹후크를 만든 다음 WebhookUri 속성에서 URI를 지정해야 합니다.  OMS 콘솔을 사용하여 이 작업을 만드는 경우 Runbook에 대해 새 웹후크가 자동으로 생성됩니다.

수정은 다음 표의 속성을 포함합니다.

| 속성 | 설명 |
|:--|:--|
| RunbookName | Runbook의 이름입니다. 이는 OMS 작업 영역의 자동화 솔루션에서 구성한 자동화 계정의 게시된 Runbook과 일치해야 합니다. |
| WebhookUri | 웹후크의 URI입니다.
| Expiry | 웹후크의 만료 날짜 및 시간입니다.  웹후크에 대해 만료를 지정하지 않은 경우 이는 유효한 미래 날짜일 수 있습니다. |

다음은 임계값을 가진 수정 작업에 대한 샘플 응답입니다.

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

고유한 작업 ID와 함께 Put 메서드를 사용하여 일정에 대한 새 수정 작업을 만듭니다.  다음 예제에서는 저장된 검색의 결과가 임계값을 초과하는 경우 Runbook이 시작되도록 임계값을 가진 수정을 만듭니다.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

고유 작업 ID와 Put 메서드를 사용하여 일정에 대한 수정 작업을 정정합니다.  요청 본문에는 작업의 etag가 포함되어야 합니다.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>예
다음은 새 전자 메일 알림을 만드는 전체 예제입니다.  이는 임계값 및 전자 메일을 포함하는 작업과 함께 새 일정을 만듭니다.

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $workspaceId    = "MyWorkspace"
    $searchId       = "51cf0bd9-5c74-6bcb-927e-d1e9080b934e"

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule?api-version=2015-03-20 $scheduleJson

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule/actions/mythreshold?api-version=2015-03-20 $thresholdJson

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule/actions/myemailaction?api-version=2015-03-20 $emailJson

### <a name="webhook-actions"></a>웹후크 작업
웹후크 작업은 URL을 호출하고 선택적으로 보낼 페이로드를 제공하는 것으로 프로세스를 시작합니다.  이들은 웹후크에 대해 Azure 자동화 Runbook 이외의 프로세스를 호출할 수 있다는 것을 제외하고 수정 작업과 유사합니다.  또한 원격 프로세스에 전달할 페이로드를 제공하는 추가 옵션을 제공합니다.

웹후크 작업은 임계값을 갖지 않지만 대신에 임계값과 함께 경고 작업을 가진 일정에 추가되어야 합니다.  임계값을 만족할 때 모두 실행될 복수의 웹후크 작업을 추가할 수 있습니다.

웹후크 작업은 다음 표의 속성을 포함합니다.

| 속성 | 설명 |
|:--|:--|
| WebhookUri | 메일의 제목입니다. |
| CustomPayload | 웹후크에 보낼 사용자 지정 페이로드입니다.  형식은 예상하는 웹후크에 따라 달라집니다. |

다음은 웹후크 작업 및 임계값을 가진 연결된 경고에 대한 샘플 응답입니다.

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

#### <a name="create-or-edit-a-webhook-action"></a>웹후크 작업 만들기 또는 편집
고유한 작업 ID와 함께 Put 메서드를 사용하여 일정에 대한 새 웹후크 작업을 만듭니다.  다음 예제에서는 저장된 검색의 결과가 임계값을 초과하는 경우 웹후크가 트리거되도록 웹후크 작업 및 임계값을 가진 웹후크 작업을 만듭니다.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

고유 작업 ID와 Put 메서드를 사용하여 일정에 대한 웹후크 작업을 수정합니다.  요청 본문에는 작업의 etag가 포함되어야 합니다.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

## <a name="next-steps"></a>다음 단계

- Log Analytics에서 [REST API를 사용하여 로그 검색을 수행](log-analytics-log-search-api.md) 합니다.



<!--HONumber=Oct16_HO2-->


