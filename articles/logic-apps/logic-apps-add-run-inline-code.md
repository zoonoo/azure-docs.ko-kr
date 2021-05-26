---
title: 인라인 코드를 사용하여 코드 조각 추가 및 실행
description: Azure Logic Apps를 사용하여 만든 자동화된 작업 및 워크플로에 대한 인라인 코드 작업을 사용하여 코드 조각을 만들고 실행하는 방법 알아보기
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: article
ms.date: 05/25/2021
ms.custom: devx-track-js
ms.openlocfilehash: 139c8336d4f40bc12cd942f27b5726a555f58605
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110372967"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Azure Logic Apps에서 인라인 코드를 사용하여 코드 조각 추가 및 실행

논리 앱 워크플로 내에서 코드 조각을 실행하려는 경우 논리 앱의 워크플로에서 기본 제공 인라인 코드 작업을 하나의 단계로 추가할 수 있습니다. 이 작업은 이 시나리오에 적합한 코드를 실행하려는 경우에 가장 적합합니다.

* JavaScript에서 실행됩니다. 더 많은 언어가 개발 중입니다.

* 5초 안에 실행을 완료합니다.

* 최대 50MB 크기의 데이터를 처리합니다.

* 아직 지원되지 않는 [**변수** 작업](../logic-apps/logic-apps-create-variables-store-values.md)을 사용할 필요가 없습니다.

* [다중 테넌트 기반 논리 앱](logic-apps-overview.md)의 경우 Node.js 버전 8.11.1을 사용하고, [단일 테넌트 기반 논리 앱](single-tenant-overview-compare.md)의 경우 [Node.js 버전 10.x.x, 11.x.x 또는 12.x.x](https://nodejs.org/en/download/releases/)를 사용합니다.

  자세한 내용은 [표준 기본 제공 개체](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)를 참조하세요.

  > [!NOTE]
  > JavaScript를 실행하기 위한 인라인 코드 작업에서는 `require()` 함수를 지원하지 않습니다.

이 작업은 코드 조각을 실행하고 해당 코드 조각의 출력을 `Result`라는 토큰으로 반환합니다. 논리 앱의 워크플로에 있는 후속 작업에 이 토큰을 사용할 수 있습니다. 코드에 대한 함수를 만들려는 다른 시나리오의 경우 논리 앱에서 [대신 Azure Functions를 통해 함수를 만들고 호출](../logic-apps/logic-apps-azure-functions.md)해 보세요.

이 문서에서 예제 논리 앱은 회사 또는 학교 계정에 새 메일이 도착할 때 트리거됩니다. 코드 조각은 메일 본문에 표시되는 메일 주소를 추출하고 반환합니다.

![예제 논리 앱을 보여 주는 스크린샷](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 트리거를 포함하여 코드 조각을 추가하려는 논리 앱 워크플로. 이 항목의 예제에서는 **새 메일이 도착할 때** 라는 Office 365 Outlook 트리거를 사용합니다.

  논리 앱이 없는 경우 다음 설명서를 검토하세요.

  * [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)
  * 단일 테넌트: [단일 테넌트 기반 논리 앱 워크플로 만들기](create-single-tenant-workflows-azure-portal.md)

* 논리 앱이 다중 테넌트 또는 단일 테넌트인지에 따라 다음 정보를 검토합니다.

  * 다중 테넌트: Node.js 버전 8.11.1이 필요합니다. 또한 논리 앱에 연결된 빈 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)도 필요합니다. 사용 사례 또는 시나리오에 적합한 통합 계정을 사용하고 있는지 확인합니다.

    예를 들어, [무료 계층](../logic-apps/logic-apps-pricing.md#integration-accounts) 통합 계정은 프로덕션 시나리오가 아닌 예비 시나리오 및 워크로드의 경우에만 사용량 및 처리량이 제한되며, SLA(서비스 수준 계약)에서 지원되지 않습니다.

    다른 통합 계정 계층은 비용이 발생하지만 SLA 지원을 포함하고, 더 많은 처리량을 제공하고 더 높은 한도가 적용됩니다. 통합 계정 [계층](../logic-apps/logic-apps-pricing.md#integration-accounts), [가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/) 및 [제한](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)에 대해 자세히 알아보세요.

  * 단일 테넌트: [Node.js 버전 10.x.x, 11.x.x 또는 12.x.x](https://nodejs.org/en/download/releases/)가 필요합니다. 그러나 통합 계정이 필요하지는 않지만 인라인 코드 작업(Action) 이름이 **인라인 코드 작업(Operation)** 으로 바뀌었으며 [제한 업데이트되었습니다](logic-apps-limits-and-config.md).

## <a name="add-inline-code"></a>인라인 코드 추가

1. 아직 열지 않았다면 [Azure Portal](https://portal.azure.com)의 디자이너에서 논리 앱 워크플로를 엽니다.

1. 워크플로에서 인라인 코드 작업을 워크플로 끝의 새 단계로 추가하거나 단계 사이에 추가하도록 선택합니다.

   단계 사이에 작업을 추가하려면 해당 단계를 연결하는 화살표 위로 미ㅏ우스 포인터를 이동합니다. 표시되는 더하기 기호( **+** )를 선택하고 **동작 추가** 를 선택합니다.

   이 예제에서는 Office 365 Outlook 트리거에서 작업을 추가합니다.

   ![트리거 아래에 새 단계 추가](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. 작업 검색 상자에 `inline code`를 입력합니다. 작업 목록에서 **JavaScript 코드 실행** 이라는 작업을 선택합니다.

   !["JavaScript 코드 실행" 작업 선택](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   이 작업은 디자이너에 표시되며 기본적으로 `return` 문을 포함하는 일부 샘플 코드를 포함합니다.

   ![기본 샘플 코드를 사용하는 인라인 코드 작업](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. **코드** 상자에서 샘플 코드를 삭제하고 코드를 입력합니다. 메서드 시그니처를 제외하고 메서드 내에 배치할 코드를 작성합니다.

   인식된 키워드를 입력하기 시작하면 사용 가능한 키워드 중에서 선택할 수 있도록 자동 완성 목록이 표시됩니다. 예를 들면 다음과 같습니다.

   ![키워드 자동 완성 목록](./media/logic-apps-add-run-inline-code/auto-complete.png)

   이 예제 코드 조각은 먼저 입력 텍스트에서 일치시킬 패턴을 지정하는 *정규식* 을 저장하는 변수를 만듭니다. 그런 다음, 트리거에서 메일 본문 데이터를 저장하는 변수를 만듭니다.

   ![변수 만들기](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   트리거와 이전 작업의 결과를 보다 쉽게 참조할 수 있도록 커서를 **코드** 상자 안에 놓으면 동적 콘텐츠 목록이 표시됩니다. 이 예제의 경우 목록에는 현재 선택할 수 있는 **Body** 토큰을 비롯하여 트리거의 사용 가능한 결과가 표시됩니다.

   **Body** 토큰을 선택하면 인라인 코드 작업이 해당 토큰을 메일의 `Body` 속성 값을 참조하는 `workflowContext` 개체로 확인합니다.

   ![결과 선택](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   **코드** 상자에서 코드 조각은 읽기 전용 `workflowContext` 개체를 입력으로 사용할 수 있습니다. 이 개체에는 트리거의 결과 및 워크플로의 이전 작업에 대한 코드 액세스를 제공하는 속성이 있습니다. 자세한 내용은 이 항목의 뒷부분에 나오는 [코드의 참조 트리거 및 작업 결과](#workflowcontext)를 참조하세요.

   > [!NOTE]
   > 코드 조각이 점(.) 연산자를 사용하는 작업 이름을 참조하는 경우 해당 작업 이름을 [**Actions** 매개 변수](#add-parameters)에 추가해야 합니다. 이러한 참조는 작업 이름을 대괄호([])와 큰따옴표로 묶어야 합니다. 예를 들면 다음과 같습니다.
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   인라인 코드 작업에는 `return` 문이 필요하지 않지만 `return` 문의 결과는 이후 작업에서 **Result** 토큰을 통해 참조할 수 있습니다. 예를 들어, 코드 조각은 정규식에 대해 메일 본문에서 일치 항목을 찾는 `match()` 함수를 호출하여 결과를 반환합니다. **Compose** 작업은 **Result** 토큰을 사용하여 인라인 코드 작업의 결과를 참조하고 단일 결과를 만듭니다.

   ![완료된 논리 앱](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. 완료되면 논리 앱을 저장합니다.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>코드의 참조 트리거 및 작업 결과

`workflowContext` 개체는 `actions`, `trigger` 및 `workflow` 하위 속성을 포함하는 다음 구조를 나타냅니다.

```json
{
   "workflowContext": {
      "actions": {
         "<action-name-1>": @actions('<action-name-1>'),
         "<action-name-2>": @actions('<action-name-2>')
      },
      "trigger": {
         @trigger()
      },
      "workflow": {
         @workflow()
      }
   }
}
```

이 표에는 이러한 하위 속성에 대한 자세한 내용이 포함되어 있습니다.

| 속성 | Type | Description |
|----------|------|-------|
| `actions` | 개체 컬렉션 | 코드 조각이 실행되기 전에 실행되는 작업의 결과 개체입니다. 각 개체에는 ‘키-값’ 쌍이 있습니다. 여기서 키는 작업의 이름이고 값은 `@actions('<action-name>')`을 사용하여 [actions() 함수](../logic-apps/workflow-definition-language-functions-reference.md#actions)를 호출하는 것과 같습니다. 작업의 이름은 기본 워크플로 정의에 사용되는 것과 동일한 작업 이름을 사용합니다. 작업 이름의 공백("")은 밑줄(_)로 바뀝니다. 이 개체를 사용하면 현재 워크플로 인스턴스 실행의 작업 속성 값에 액세스할 수 있습니다. |
| `trigger` | Object | 트리거의 결과 개체로, [trigger() 함수](../logic-apps/workflow-definition-language-functions-reference.md#trigger)를 호출하는 것과 동일합니다. 이 개체를 사용하면 현재 워크플로 인스턴스 실행의 트리거 속성 값에 액세스할 수 있습니다. |
| `workflow` | Object | 워크플로 개체로, [workflow() 함수](../logic-apps/workflow-definition-language-functions-reference.md#workflow)를 호출하는 것과 동일합니다. 이 개체를 사용하면 현재 워크플로 인스턴스 실행의 워크플로 속성 값(예: 워크플로 이름, 실행 ID 등)에 액세스할 수 있습니다. |
|||

이 항목의 예제에서 `workflowContext` 개체에는 코드에서 액세스할 수 있는 다음과 같은 속성이 있습니다.

```json
{
   "workflowContext": {
      "trigger": {
         "name": "When_a_new_email_arrives",
         "inputs": {
            "host": {
               "connection": {
                  "name": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/office365"
               }
            },
            "method": "get",
            "path": "/Mail/OnNewEmail",
            "queries": {
               "includeAttachments": "False"
            }
         },
         "outputs": {
            "headers": {
               "Pragma": "no-cache",
               "Content-Type": "application/json; charset=utf-8",
               "Expires": "-1",
               "Content-Length": "962095"
            },
            "body": {
               "Id": "AAMkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgBGAAAAAABmZwxUQtCGTqSPpjjMQeD",
               "DateTimeReceived": "2019-03-28T19:42:16+00:00",
               "HasAttachment": false,
               "Subject": "Hello World",
               "BodyPreview": "Hello World",
               "Importance": 1,
               "ConversationId": "AAQkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgAQ",
               "IsRead": false,
               "IsHtml": true,
               "Body": "Hello World",
               "From": "<sender>@<domain>.com",
               "To": "<recipient-2>@<domain>.com;<recipient-2>@<domain>.com",
               "Cc": null,
               "Bcc": null,
               "Attachments": []
            }
         },
         "startTime": "2019-05-03T14:30:45.971564Z",
         "endTime": "2019-05-03T14:30:50.1746874Z",
         "scheduledTime": "2019-05-03T14:30:45.8778117Z",
         "trackingId": "1cd5ffbd-f989-4df5-a96a-6e9ce31d03c5",
         "clientTrackingId": "08586447130394969981639729333CU06",
         "originHistoryName": "08586447130394969981639729333CU06",
         "code": "OK",
         "status": "Succeeded"
      },
      "workflow": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>",
         "name": "<logic-app-workflow-name>",
         "type": "Microsoft.Logic/workflows",
         "location": "<Azure-region>",
         "run": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>/runs/08586453954668694173655267965CU00",
            "name": "08586453954668694173655267965CU00",
            "type": "Microsoft.Logic/workflows/runs"
         }
      }
   }
}
```

<a name="add-parameters"></a>

## <a name="add-parameters"></a>매개 변수 추가

**Trigger** 또는 **Actions** 매개 변수를 추가하여 코드에서 종속성으로 참조하는 트리거 또는 특정 작업의 결과가 인라인 코드 작업에 포함되도록 명시적으로 지정해야 하는 경우도 있습니다. 이 옵션은 참조된 결과가 런타임에 없는 시나리오에 유용합니다.

> [!TIP]
> 코드를 다시 사용하려는 경우 트리거 또는 작업을 명시적 종속성으로 추가하는 대신 코드에 확인된 토큰 참조가 포함되도록 **코드** 상자를 사용하여 속성에 대한 참조를 추가합니다.

예를 들어, Office 365 Outlook 커넥터에 대한 **승인 메일 보내기** 작업의 **SelectedOption** 결과를 참조하는 코드가 있다고 가정합니다. 생서 시에 Logic Apps 엔진은 코드를 분석하여 트리거 또는 작업 결과를 참조했는지를 확인하고 해당 결과를 자동으로 포함합니다. 런타임에는 참조된 트리거나 작업 결과를 지정된 `workflowContext` 개체에서 사용할 수 없다는 오류가 표시될 경우 해당 트리거나 작업을 명시적 종속성으로 추가할 수 있습니다. 이 예제에서는 **Actions** 매개 변수를 추가하고 인라인 코드 작업에 **승인 메일 보내기** 작업의 결과가 명시적으로 포함되도록 지정합니다.

이러한 매개 변수를 추가하려면 **새 매개 변수 추가** 목록을 열고 원하는 매개 변수를 선택합니다.

   ![매개 변수 추가](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | 매개 변수 | 설명 |
   |-----------|-------------|
   | **actions** | 이전 작업의 결과를 포함합니다. [작업 결과 포함](#action-results)을 참조하세요. |
   | **트리거** | 트리거의 결과를 포함합니다. [트리거 결과 포함](#trigger-results)을 참조하세요. |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>트리거 결과 포함

**트리거** 를 선택하면 트리거 결과를 포함할지 여부를 묻는 메시지가 표시됩니다.

* **트리거** 목록에서 **예** 를 선택합니다.

<a name="action-results"></a>

### <a name="include-action-results"></a>작업 결과 포함

**작업** 을 선택하면 추가하려는 작업을 묻는 메시지가 표시됩니다. 그러나 작업 추가를 시작하기 전에 논리 앱의 기본 워크플로 정의에 표시되는 작업 이름의 버전이 필요합니다.

* 이 기능은 변수, 루프 및 반복 인덱스를 지원하지 않습니다.

* 논리 앱의 워크플로 정의에 있는 이름은 공백이 아닌 밑줄(_)을 사용합니다.

* 점 연산자(.)를 사용하는 작업 이름에는 다음과 같이 해당 연산자를 포함합니다.

  `My.Action.Name`

1. 디자이너 도구 모음에서 **코드 보기** 를 선택하고 `actions` 특성 내에서 작업 이름을 검색합니다.

   예를 들어, `Send_approval_email_`은 **승인 메일 보내기** 작업의 JSON 이름입니다.

   ![JSON에서 작업 이름 찾기](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. 디자이너 뷰로 돌아가려면 코드 보기 도구 모음에서 **디자이너** 를 선택합니다.

1. 첫 번째 작업을 추가하려면 **작업 항목 - 1** 상자에 작업의 JSON 이름을 입력합니다.

   ![첫 번째 작업 입력](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. 다른 작업을 추가하려면 **새 항목 추가** 를 선택합니다.

## <a name="reference"></a>참조

워크플로 정의 언어를 사용하는 논리 앱의 기본 워크플로 정의에 포함된 **JavaScript 코드 실행** 작업의 구조 및 구문에 대한 자세한 내용은 이 작업의 [참조 섹션](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure Logic Apps용 커넥터](../connectors/apis-list.md)의 자세한 내용을 알아봅니다.
