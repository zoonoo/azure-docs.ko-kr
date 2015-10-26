<properties
	pageTitle="Azure 경고를 구성하여 다른 시스템에 보내는 방법"
	description="Azure 경고를 다른 비Azure 시스템으로 경로를 전환합니다."
	authors="rboucher"
	manager="ronmart"
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/25/2015"
	ms.author="robb"/>

# 경고에 Webhooks를 구성하는 방법

Webhooks를 사용하면 사후 처리 및 사용자 지정 알림을 위해 다른 시스템에 Azure 경고 알림을 라우팅할 수 있습니다. 이 작업의 예제로 들어오는 채팅/메시징 서비스 등을 통해 SMS 보내기, 버그 기록, 팀 알림을 담당하는 웹 요청을 처리할 수 있는 서비스에 경고를 라우팅할 수 있습니다.

Webhook URI는 유효한 HTTP 또는 HTTPS 끝점이어야 합니다. Azure 경고 서비스는 지정된 Webhook에서 게시 작업을 만들며 이는 특정 JSON 페이로드 및 스키마에 전달합니다.

## 포털을 통해 Webhooks 구성

[Azure 포털](https://portal.azure.com/)의 만들기/업데이트 경고 화면에 Webhook URI를 추가하거나 업데이트할 수 있습니다.

![경고 규칙 추가](./media/insights-webhooks-alerts/Alertwebhook.png)


## 인증

인증은 두 형식일 수 있습니다.

1. **토큰 기반 인증** - **https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue*와 같은 토큰 ID를 사용하여 Webhook URI를 저장하는 경우입니다.
2.	**기본 인증** - 사용자 ID 및 암호를 사용하여 Webhook URI를 **https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar*로 저장하는 경우입니다.

## 페이로드 스키마

게시 작업은 모든 메트릭 기반 경고에 다음 JSON 페이로드 및 스키마를 포함합니다.

```
{
"status": "Activated",
"context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                        "metricName": "Requests",
                        "metricUnit": "Count",
                        "metricValue": "10",
                        "threshold": "10",
                        "windowSize": "15",
                        "timeAggregation": "Average",
                        "operator": "GreaterThanOrEqual"
                },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",                                
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": “https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1”                                
},
"properties": {
              "key1": "value1",
              "key2": "value2"
              }
}
```

>[AZURE.NOTE]다음 새로 고침에서 이벤트에 경고에 대 한 지원을 추가합니다.(“conditionType” : “Event”)


| 필드 | 필수? | 값의 고정된 집합? | 참고 |
| :-------------| :-------------   | :-------------   | :-------------   |
|status|Y|“Activated”, “Resolved”|이 경고가 어떤 종류인지 확인하는 방법입니다. Azure는 설정한 조건에 자동으로 활성화되고 해결된 경고를 보냅니다.|
|context| Y | | 경고 컨텍스트|
|timestamp| Y | | 경고가 트리거된 시점의 시간입니다. 메트릭을 진단 저장소에서 읽는 즉시 경고가 트리거됩니다.|
|id | Y | | 모든 경고 규칙에는 고유한 ID가 있습니다.|
|name|Y | |
|description |Y | |경고에 대한 설명입니다.|
|conditionType |Y |“Metric”, “Event” |두 형식의 경고가 지원됩니다. 메트릭에 기반한 경고 및 기타 이벤트에 기반한 경고가 있습니다. 미래에 경고 이벤트에 대해 지원할 예정이므로 이 값을 사용하여 경고가 메트릭 또는 이벤트 중 어디에 기반하는지 확인합니다.|
|condition |Y | |conditionType에 기반하는지 검사할 특정 필드가 있습니다.|
|metricName |메트릭 경고의 경우 | |규칙은 모니터링을 정의하는 메트릭의 이름입니다.|
|metricUnit |메트릭 경고의 경우 |"Bytes", "BytesPerSecond" , "Count" , "CountPerSecond" , "Percent", "Seconds"|	 메트릭에 사용되는 단위입니다. 허용되는 값은 https://msdn.microsoft.com/en-us/library/microsoft.azure.insights.models.unit.aspx|과 같습니다.
|metricValue |메트릭 경고의 경우 | |경고를 발생하는 메트릭의 실제 값|
|threshold |메트릭 경고의 경우 | |경고를 활성화하는 임계값|
|windowSize |메트릭 경고의 경우 | |임계값에 기반하여 경보 활동을 모니터링하는 데 사용되는 기간입니다. 5분에서 하루 사이여야 합니다. ISO 8601 기간 형식입니다.|
|timeAggregation |메트릭 경고의 경우 |"Average", "Last" , "Maximum" , "Minimum" , "None", "Total" |	데이터가 수집되는 방법은 시간이 지남에 따라 결합되어야 합니다. 기본값은 평균입니다. 허용되는 값은 https://msdn.microsoft.com/en-us/library/microsoft.azure.insights.models.aggregationtype.aspx|과 같습니다.
|operator |메트릭 경고의 경우 | |데이터 및 임계값을 비교하는 데 사용되는 연산자입니다.|
|subscriptionId |Y | |Azure 구독 GUID|
|resourceGroupName |Y | |영향을 받는 리소스의 리소스-그룹-이름|
|resourceName |Y | |영향을 받는 리소스의 리소스 이름|
|resourceType |Y | |영향을 받는 리소스의 리소스 형식|
|resourceId |Y | |해당 리소스를 고유하게 식별하는 리소스 ID URI|
|resourceRegion |Y | |영향을 받는 리소스의 지역/위치|
|portalLink |Y | |리소스 요약 페이지에 Azure 포털 직접 링크|
|properties |N |옵션 |이벤트에 대한 세부 정보를 포함하는 <Key  Value> 쌍의 집합입니다.(즉, 사전<String  String>) 속성 필드는 선택 사항입니다. 워크플로에 기반한 사용자 지정 UI 또는 논리 앱에서 페이로드를 통해 전달될 수 있는 키/값을 입력할 수 있습니다. 사용자 지정 속성을 Webhook에 다시 전달할 대체 방법은 Webhook URI 자체를 통하는 것입니다.(쿼리 매개 변수로)|


>[AZURE.NOTE]포털을 통해 속성 필드를 사용할 수 없습니다. Insights SDK의 향후 릴리스에서 경고 API를 통해 속성을 설정할 수 있습니다.

## 다음 단계

추가 배경으로 [PagerDuty와 Azure 경고 통합](http://go.microsoft.com/fwlink/?LinkId=627080) 비디오에서 Azure 경고 및 Webhooks에 대한 자세한 정보를 알아봅니다

프로그래밍 방식으로 Webhook을 만드는 방법을 알아보려면 [Azure Insights SDK(C#)를 사용하여 Webhooks이 있는 경고 만들기](https://code.msdn.microsoft.com/Create-Azure-Alerts-with-b938077a)를 참조하세요.

Webhooks 및 경고를 설정하면 이러한 다른 옵션을 탐색하여 자동화 스크립트를 시작합니다.

[Azure 자동화 스크립트 실행(Runbooks)](http://go.microsoft.com/fwlink/?LinkId=627081)

Azure 경고를 사용하여 다른 서비스에 메시지를 보냅니다. 다음 예제 템플릿을 사용하여 시작합니다.

[논리 앱을 사용하여 Twilio API를 통해 SMS 보내기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)

[논리 앱을 사용하여 Slack 메시지 보내기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)

[논리 앱을 사용하여 Azure 큐에 메시지 보내기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<!---HONumber=Oct15_HO3-->