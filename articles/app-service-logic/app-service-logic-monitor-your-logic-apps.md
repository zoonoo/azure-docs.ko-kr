---
title: "Azure App Service에서 논리 앱 모니터링 | Microsoft Docs"
description: "논리 앱의 기능을 확인하는 방법"
author: jeffhollan
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: cc73caf6ffdc47199369112a5310a3f4dd0cc9bf


---
# <a name="monitor-your-logic-apps"></a>논리 앱 모니터링
[논리 앱을 만든](app-service-logic-create-a-logic-app.md)후에 Azure 포털에서 전체 실행 기록을 확인할 수 있습니다.  Azure 진단 및 Azure Alerts와 같은 서비스를 설정하여 이벤트를 실시간으로 모니터링하고 "1시간 이내 5번 이상 실행에 실패할 때"와 같은 이벤트를 사용자에게 알릴 수도 있습니다.

## <a name="monitor-in-the-azure-portal"></a>Azure 포털에서 모니터
기록을 보려면 **찾아보기**를 선택하고 **논리 앱**을 선택합니다. 구독의 논리 앱 목록이 모두 표시됩니다.  모니터링하려는 논리 앱을 선택합니다.  이 논리 앱에 대해 발생한 모든 작업 및 트리거 목록이 표시됩니다.

![개요](./media/app-service-logic-monitor-your-logic-apps/overview.png)

이 블레이드에 대해 유용한 몇 가지 섹션이 있습니다.

* **요약**에 **모든 실행** 및 **트리거 기록**이 나열됩니다.
  * **모든 실행** 에는 최신 논리 앱 실행이 나열됩니다.  실행 세부 사항에 대한 행을 클릭하거나 타일을 클릭하여 더 많은 실행을 나열할 수 있습니다.
  * **트리거 기록** 에는 이 논리 앱에 대한 모든 트리거 작업이 나열됩니다.  트리거 작업은 새 데이터 확인에 대해 "건너뜀"일 수 있으며(예: 새 파일이 FTP에 추가되었는지 확인) "성공"은 논리 앱을 실행하기 위해 데이터가 반환되었음을 의미하고 "실패"는 구성에 오류가 있는 것입니다.
* **진단** 을 통해 런타임 세부 정보 및 이벤트를 보고 [Azure Alerts](#adding-azure-alerts)

> [!NOTE]
> 모든 런타임 세부 정보 및 이벤트는 논리 앱 서비스 내에서 휴지 상태로 암호화됩니다. 사용자의 보기 요청이 있을 때에만 해독됩니다. 이러한 이벤트에 대한 액세스는 Azure RBAC(역할 기반 Access Control)를 통해서도 제어할 수 있습니다.
> 
> 

### <a name="view-the-run-details"></a>실행 세부 정보 보기
이 실행 목록은 특정 실행의 **상태**, **시작 시간** 및 **기간**을 보여 줍니다. 행을 선택하면 해당 실행의 세부 정보가 표시됩니다.

모니터링 보기는 각 실행 단계, 입력 및 출력, 발생할 수 있는 모든 오류 메시지를 보여 줍니다.

![실행 및 동작](./media/app-service-logic-monitor-your-logic-apps/monitor-view.png)

실행 **상관 관계 ID**(REST API에 대해 사용 가능)와 같은 추가 정보가 필요한 경우 **실행 정보** 단추를 클릭할 수 있습니다.  여기에는 실행에 대한 모든 단계, 상태 및 입력/출력이 포함됩니다.

## <a name="azure-diagnostics-and-alerts"></a>Azure 진단 및 경고
Azure 포털 및 REST API에서 제공하는 위의 세부 정보 외에도 Azure 진단을 사용하여 보다 자세한 정보와 디버깅을 제공하도록 논리 앱을 구성할 수 있습니다.

1. 논리 앱 블레이드의 **진단** 섹션을 클릭합니다.
2.  **진단 설정**
3. 데이터를 내보낼 이벤트 허브 또는 저장소 계정을 구성합니다.
   
    ![Azure 진단 설정](./media/app-service-logic-monitor-your-logic-apps/diagnostics.png)

### <a name="adding-azure-alerts"></a>Azure Alerts 추가
진단을 구성한 후에는 특정 임계값을 벗어날 경우 실행할Azure Alerts를 추가할 수 있습니다.  **진단** 블레이드에서 **경고** 타일 및 **경고 추가**를 선택합니다.  여기서는 다양한 임계값 및 메트릭을 기반으로 경고를 구성하는 과정을 안내합니다.

![Azure Alert 메트릭](./media/app-service-logic-monitor-your-logic-apps/alerts.png)

**조건**, **임계값** 및 **기간**을 원하는 대로 구성할 수 있습니다.  마지막으로 알림을 보낼 전자 메일 주소 또는 웹후크를 구성할 수 있습니다.  논리 앱에서 경고에 실행할 [요청 트리거](../connectors/connectors-native-reqres.md)도 사용할 수 있습니다([Slack에 게시](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app), [텍스트 보내기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app), [큐에 메시지 추가](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)와 같은 작업 수행).

### <a name="azure-diagnostics-settings"></a>Azure 진단 설정
이러한 각 이벤트에는 논리 앱 및 상태와 같은 이벤트의 세부 정보가 포함됩니다.  다음은 *ActionCompleted* 이벤트의 예입니다.

```javascript
{
            "time": "2016-07-09T17:09:54.4773148Z",
            "workflowId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
            "resourceId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-06-01",
                "startTime": "2016-07-09T17:09:53.4336305Z",
                "endTime": "2016-07-09T17:09:53.5430281Z",
                "status": "Succeeded",
                "code": "OK",
                "resource": {
                    "subscriptionId": "80d4fe69-ABCD-EFGH-a938-9250f1c8ab03",
                    "resourceGroupName": "MyResourceGroup",
                    "workflowId": "cff00d5458f944d5a766f2f9ad142553",
                    "workflowName": "MyLogicApp",
                    "runId": "08587361146922712057",
                    "location": "eastus",
                    "actionName": "Http"
                },
                "correlation": {
                    "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
                    "clientTrackingId": "my-custom-tracking-id"
                },
                "trackedProperties": {
                    "myProperty": "<value>"
                }
            }
        }
```

추적 및 모니터링에 특히 유용한 두 가지 속성은 *clientTrackingId* 및 *trackedProperties*입니다.  

#### <a name="client-tracking-id"></a>클라이언트 추적 ID
클라이언트 추적 ID는 논리 앱의 일부로 호출되는 모든 중첩된 워크플로를 비롯하여, 논리 앱 실행 간의 이벤트를 상호 연결하는 값입니다.  이 ID는 제공되지 않으면 자동 생성되지만 트리거 요청(요청 트리거, HTTP 트리거 또는 웹후크 트리거)에서 ID 값으로 `x-ms-client-tracking-id` 헤더를 전달하여 트리거에서 클라이언트 추적 ID를 수동으로 지정할 수 있습니다.

#### <a name="tracked-properties"></a>추적된 속성
추적된 속성을 워크플로 정의의 작업에 추가하여 진단 데이터에서 입력 또는 출력을 추적할 수 있습니다.  원격 분석에서 "주문 ID"와 같은 데이터를 추적하려는 경우 유용할 수 있습니다.  추적된 속성을 추가하려면 작업에 `trackedProperties` 속성을 포함합니다.  추적된 속성으로 단일 작업 입력 및 출력만 추적할 수 있지만 이벤트의 `correlation` 속성을 사용하여 실행에 있는 작업 간에 상호 연결할 수 있습니다.

```javascript
{
    "myAction": {
        "type": "http",
        "inputs": {
            "uri": "http://uri",
            "headers": {
                "Content-Type": "application/json"
            },
            "body": "@triggerBody()"
        },
        "trackedProperties":{
            "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
            "myActionHTTPValue": "@action()['outputs']['body']['foo']",
            "transactionId": "@action()['inputs']['body']['bar']"
        }
    }
}
```

### <a name="extending-your-solutions"></a>솔루션 확장
Event Hub 또는 저장소에서 이 원격 분석을 [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite), [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) 및 [Power BI](https://powerbi.com)와 같은 다른 서비스에 활용하여 통합 워크플로의 실시간 모니터링이 가능합니다.

## <a name="next-steps"></a>다음 단계
* [논리 앱에 대한 일반적인 예제 및 시나리오](app-service-logic-examples-and-scenarios.md)
* [논리 앱 배포 템플릿 만들기](app-service-logic-create-deploy-template.md)
* [엔터프라이즈 통합 기능](app-service-logic-enterprise-integration-overview.md)




<!--HONumber=Nov16_HO3-->


