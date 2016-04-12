<properties
	pageTitle="Azure Insights에서 감사 로그를 사용하여 메일 및 Webhook 경고 알림 보내기 | Microsoft Azure"
	description="Insights에서 서비스 감사 로그 항목을 사용하여 웹 URL을 호출하거나 메일 알림을 보내는 방법을 알아봅니다."
	authors="kamathashwin"
	manager=""
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="ashwink"/>

# Azure Insights에서 감사 로그를 사용하여 메일 및 Webhook 경고 알림 보내기

이 문서에서는 감사 로그 이벤트에서 Webhook를 트리거할 때의 페이로드 스키마를 보여 주고, 동일한 이벤트를 사용하여 메일을 보낼 수 있는 방법을 설명합니다.

>[AZURE.NOTE] 이 기능은 현재 미리 보기로 제공됩니다. 향후 몇 개월 동안 이벤트 경고 인프라 및 성능이 개선될 예정입니다. 이 미리 보기에서는 Azure PowerShell 및 CLI에서만 이 기능에 액세스할 수 있습니다. Azure 포털을 사용하여 동일한 기능에 액세스하는 기능은 나중에 제공된 예정입니다.

## Webhook
Webhook를 사용하면 사후 처리 또는 사용자 지정 알림을 위해 Azure 경고 알림을 다른 시스템으로 라우팅할 수 있습니다. 예를 들어 수신 웹 요청을 처리할 수 있는 서비스로 경고를 라우팅하여 SMS를 보내거나 버그를 기록하거나 채팅 또는 메시징 서비스를 사용하여 다른 사람에게 알리는 등의 작업을 수행할 수 있습니다. Webhook URI는 유효한 HTTP 또는 HTTPS 끝점이어야 합니다.

## Email
모든 유효한 메일 주소로 메일을 보낼 수 있습니다. 규칙이 실행 중인 구독의 관리자와 공동 관리자에게도 알림이 제공됩니다.

### 메일 규칙 예
메일 규칙 및 Webhook 규칙을 설정한 다음 감사 로그 이벤트가 발생한 경우 규칙이 시작되도록 지정해야 합니다. PowerShell 사용 예는 [Azure Insights PowerShell 빠른 시작 샘플](insights-powershell-samples.md#alert-on-audit-log-event)에서 확인할 수 있습니다.


## 인증
다음 두 가지 인증 URI 양식이 있습니다.

1. Webhook URI를 토큰 ID와 함께 쿼리 매개 변수로 저장하여 토큰 기반 인증. 위치(예:https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue
2. 사용자 ID 및 암호를 사용하여 기본 인증. 위치(예:https://userid:password@mysamplealert/webcallback?someparamater=somevalue&parameter=value

## 감사 로그 이벤트 알림 Webhook 페이로드 스키마
새 이벤트를 사용할 수 있게 되면 감사 로그 이벤트에 대한 경고가 Webhook 페이로드의 이벤트 메타데이터와 함께 구성된 Webhook를 실행합니다. 다음 예제에서는 Webhook 페이로드 스키마를 보여 줍니다.

```
{
        "status": "Activated",
        "context": {
                "resourceProviderName": "Microsoft.Web",
                "event": {
                        "$type": "Microsoft.WindowsAzure.Management.Monitoring.Automation.Notifications.GenericNotifications.Datacontracts.InstanceEventContext, Microsoft.WindowsAzure.Management.Mon.Automation",
                        "authorization": {
                                "action": "Microsoft.Web/sites/start/action",
                                "scope": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest"
                        },
                        "eventDataId": "327caaca-08d7-41b1-86d8-27d0a7adb92d",
                        "category": "Administrative",
                        "caller": "myname@mycompany.com",
                        "httpRequest": {
                                "clientRequestId": "f58cead8-c9ed-43af-8710-55e64def208d",
                                "clientIpAddress": "104.43.166.155",
                                "method": "POST"
                        },
                        "status": "Succeeded",
                        "subStatus": "OK",
                        "level": "Informational",
                        "correlationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "eventDescription": "",
                        "operationName": "Microsoft.Web/sites/start/action",
                        "operationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "properties": {
                                "$type": "Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage",
                                "statusCode": "OK",
                                "serviceRequestId": "f7716681-496a-4f5c-8d14-d564bcf54714"
                        }
                },
                "timestamp": "Friday, March 11, 2016 9:13:23 PM",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/alertonevent2",
                "name": "alertonevent2",
                "description": "test alert on event start",
                "conditionType": "Event",
                "subscriptionId": "s1",
                "resourceId": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest",
                "resourceGroupName": "rg1"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```

|요소 이름|	설명|
|---|---|
|status |항상 "activated"로 설정|
|context|이벤트의 컨텍스트|
|resourceProviderName|영향을 받는 리소스의 리소스 공급자|
|conditionType |"Event"|
|name |경고 규칙의 이름|
|id |경고의 리소스 ID|
|description|	경고를 만든 사람이 제공하는 경고에 설정된 설명|
|subscriptionId |Azure 구독 GUID|
|timestamp|	이벤트에 해당하는 요청을 처리한 Azure 서비스에 의해 이벤트가 생성된 타임스탬프|
|resourceId |해당 리소스를 고유하게 식별하는 리소스 ID URI|
|resourceGroupName|영향을 받는 리소스의 리소스-그룹-이름|
|properties |이벤트에 대한 세부 정보를 포함하는 <Key  Value> 쌍의 집합(즉, 사전<String  String>)|
|event|이벤트에 대한 메타데이터가 포함된 요소|
|권한 부여|이벤트의 RBAC 속성을 캡처합니다. 여기는 일반적으로 "action", "role" 및 "scope"가 포함됩니다.|
|카테고리 | 이벤트의 범주. 지원되는 값: Administrative, Alert, Security, ServiceHealth, Recommendation|
|caller|가용성을 기반으로 작업, UPN 클레임 또는 SPN 클레임을 수행한 사용자의 메일 주소. 특정 시스템 호출의 경우 null일 수 있습니다.|
|CorrelationId|	일반적으로 문자열 형식의 GUID. correlationId가 있는 이벤트는 동일한 상위 작업에 속하며 일반적으로 동일한 correlationId를 공유합니다.|
|eventDescription |이벤트를 설명하는 정적 텍스트|
|eventDataId|이벤트의 고유 식별자|
|eventSource |이벤트를 생성한 Azure 서비스 또는 인프라의 이름|
|httpRequest|	일반적으로 “clientRequestId”, “clientIpAddress” 및 “method”(PUT 등의 HTTP 메서드) 포함|
|level|다음 값 중 하나: “Critical”, “Error”, “Warning”, “Informational” 및 “Verbose”|
|operationId|일반적으로 단일 작업에 해당하는 이벤트 간에 공유되는 GUID|
|operationName|작업의 이름|
|properties |이벤트 요소 내의 요소는 이벤트의 속성을 포함합니다.|
|status|작업의 상태를 설명하는 문자열. 일반적인 값: Started, In Progress, Succeeded, Failed, Active, Resolved|
|subStatus|	일반적으로 해당 REST 호출의 HTTP 상태 코드를 포함합니다. 하위 상태를 설명하는 다른 문자열을 포함할 수도 있습니다. 일반적인 하위 상태 값: OK(HTTP 상태 코드: 200), Created(HTTP 상태 코드: 201), Accepted(HTTP 상태 코드: 202), No Content(HTTP 상태 코드: 204), Bad Request(HTTP 상태 코드: 400), Not Found(HTTP 상태 코드: 404), Conflict(HTTP 상태 코드: 409), Internal Server Error(HTTP 상태 코드: 500), Service Unavailable(HTTP 상태 코드:503), Gateway Timeout(HTTP 상태 코드: 504)|

<!---HONumber=AcomDC_0330_2016-->