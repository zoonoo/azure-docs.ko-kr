---
title: 인라인 코드를 사용하여 코드 조각 추가 및 실행
description: Azure Logic Apps를 사용하여 만드는 자동화된 작업 및 워크플로에 인라인 코드 작업을 사용하여 코드 조각을 만들고 실행하는 방법을 알아봅니다.
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: f7a134fd026b42d1666b8310b3fb0c10642c7bb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453506"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Azure 논리 앱에서 인라인 코드를 사용하여 코드 조각 추가 및 실행

논리 앱 내에서 코드 조각을 실행하려는 경우 논리 앱 워크플로의 단계로 기본 제공 **인라인 코드** 작업을 추가할 수 있습니다. 이 작업은 이 시나리오에 맞는 코드를 실행하려는 경우에 가장 적합합니다.

* 자바 스크립트에서 실행됩니다. 더 많은 언어가 곧 출시될 예정입니다.
* 5초 이하의 시간 동안 실행을 완료합니다.
* 최대 50MB 크기의 데이터를 처리합니다.
* 아직 지원되지 않는 [ **변수** 작업으로](../logic-apps/logic-apps-create-variables-store-values.md)작업할 필요가 없습니다.
* Node.js 버전 8.11.1을 사용합니다. 자세한 내용은 [표준 기본 제공 개체를](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)참조하십시오. 

  > [!NOTE]
  > 이 `require()` 함수는 자바스크립트를 실행하기 위한 **인라인 코드** 작업에서 지원되지 않습니다.

이 작업은 코드 조각을 실행하고 해당 코드 조각의 출력을 **Result라는**토큰으로 반환하며 논리 앱의 후속 작업에 사용할 수 있습니다. 코드에 대한 함수를 만들려는 다른 시나리오의 경우 논리 앱에서 [Azure 함수를 만들고 호출해](../logic-apps/logic-apps-azure-functions.md) 보십시오.

이 문서에서 예제 논리 앱은 Office 365 Outlook 계정에 새 전자 메일이 도착할 때 트리거됩니다. 코드 코드 조각은 이메일 본문에 나타나는 모든 전자 메일 주소를 추출하고 반환합니다.

![개요 예제](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 트리거를 포함하여 코드 조각을 추가하려는 논리 앱입니다. 논리 앱이 없는 경우 빠른 [시작: 첫 번째 논리 앱 만들기를](../logic-apps/quickstart-create-first-logic-app-workflow.md)참조하십시오.

   이 항목의 예제 논리 응용 프로그램은이 Office 365 Outlook **트리거를** 사용 합니다.

* 논리 앱에 연결된 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

  > [!NOTE]
  > 사용 사례 또는 시나리오에 적합한 통합 계정을 사용해야 합니다. 예를 들어 [프리 티어](../logic-apps/logic-apps-pricing.md#integration-accounts) 통합 계정은 프로덕션 시나리오가 아닌 예비 시나리오 및 워크로드에만 사용되며 사용 및 처리량이 제한되며 SLA(서비스 수준 계약)에서 지원되지 않습니다. 다른 계층에는 비용이 발생하지만 SLA 지원이 포함되고 처리량이 더 많으며 한도가 높습니다. 통합 계정 [계층,](../logic-apps/logic-apps-pricing.md#integration-accounts) [가격](https://azure.microsoft.com/pricing/details/logic-apps/)및 [제한에](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)대해 자세히 알아봅니다.

## <a name="add-inline-code"></a>인라인 코드 추가

1. 아직 하지 않은 경우 [Azure Portal에서](https://portal.azure.com)논리 앱 디자이너에서 논리 앱을 엽니다.

1. 디자이너에서 논리 앱의 워크플로에서 원하는 위치에 **인라인 코드** 작업을 추가합니다.

   * 워크플로가 끝날 때 작업을 추가하려면 **새 단계를**선택합니다.

   * 기존 단계 간에 작업을 추가하려면 마우스 포인터를 해당 단계를 연결하는 화살표 위로 이동합니다. [기호] ()**+** 및 **작업 추가를**선택합니다.

   이 예제에서는 Office 365 Outlook 트리거 아래에 **인라인 코드** 작업을 추가합니다.

   ![새 단계 추가](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. **작업 선택에서**검색 상자에 필터로 "인라인 코드"를 입력합니다. 작업 목록에서 다음 작업을 선택합니다: **자바스크립트 코드 실행**

   !["자바 스크립트 코드 실행"을 선택합니다.](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   작업은 디자이너에 나타나며 return 문을 포함한 몇 가지 기본 예제 코드를 포함합니다.

   ![기본 샘플 코드가 있는 인라인 코드 작업](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. **코드** 상자에서 샘플 코드를 삭제하고 실행할 코드를 입력합니다. 메서드 시그니처를 정의하지 않고 메서드 내부에 넣을 코드를 작성합니다. 

   인식된 키워드를 입력하면 다음과 같은 사용 가능한 키워드 중에서 선택할 수 있도록 자동 완성 목록이 나타납니다.

   ![키워드 자동 완성 목록](./media/logic-apps-add-run-inline-code/auto-complete.png)

   이 예제 코드 조각 먼저 입력 텍스트에 일치 하는 패턴을 지정 하는 *정규식을*저장 하는 변수를 만듭니다. 그런 다음 코드는 트리거에서 전자 메일 본문 데이터를 저장하는 변수를 만듭니다.

   ![변수 만들기](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   트리거 및 이전 작업의 결과를 보다 쉽게 참조할 수 있도록 커서가 **코드** 상자 안에 있는 동안 동적 콘텐츠 목록이 나타납니다. 이 예제의 경우 목록에는 이제 선택할 수 있는 **Body** 토큰을 포함하여 트리거에서 사용 가능한 결과가 표시됩니다.

   **Body** 토큰을 선택하면 인라인 코드 동작이 토큰을 `workflowContext` 전자 메일의 `Body` 속성 값을 참조하는 개체로 확인합니다.

   ![결과 선택](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   **코드** 상자에서 스니펫은 읽기 전용 `workflowContext` 개체를 입력으로 사용할 수 있습니다. 이 개체에는 워크플로의 트리거 및 이전 작업의 결과에 대한 코드 액세스 권한을 부여하는 하위 속성이 있습니다.
   자세한 내용은 이 항목의 다음 섹션: 참조 트리거 및 작업 결과 코드의 이 섹션을 [참조하십시오.](#workflowcontext)

   > [!NOTE]
   >
   > 코드 코드 조각이 dot(.) 연산자(.) 연산자인 작업 이름을 참조하는 경우 해당 작업 이름을 [ **Action** 매개 변수에](#add-parameters)추가해야 합니다. 이러한 참조는 다음과 같은 대괄호([]) 및 따옴표로 작업 이름을 동봉해야 합니다.
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   인라인 코드 작업에는 `return` 문이 필요하지 않지만 `return` **명령문의** 결과는 Result 토큰을 통해 이후 작업에서 참조할 수 있습니다. 
   예를 들어 코드 조각은 정규식에 대해 `match()` 전자 메일 본문에서 일치 항목을 찾는 함수를 호출하여 결과를 반환합니다. **[작성]** 작업은 **결과** 토큰을 사용하여 인라인 코드 작업의 결과를 참조하고 단일 결과를 만듭니다.

   ![완료된 논리 앱](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. 완료되면 논리 앱을 저장합니다.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>코드의 참조 트리거 및 작업 결과

개체에는 의 이 `trigger`및 `workflow` `actions`하위 속성을 포함하는 이 구조가 있습니다. `workflowContext`

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

이 테이블에는 이러한 하위 속성에 대한 자세한 정보가 포함되어 있습니다.

| 속성 | Type | Description |
|----------|------|-------|
| `actions` | 개체 컬렉션 | 코드 코드 조각이 실행되기 전에 실행되는 작업의 결과 개체입니다. 각 개체에는 키가 작업의 이름인 *키-값* 쌍이 있으며 값은 을 사용하여 `@actions('<action-name>')` [action() 함수를](../logic-apps/workflow-definition-language-functions-reference.md#actions) 호출하는 것과 같습니다. 작업 이름은 기본 워크플로 정의에 사용되는 것과 동일한 작업 이름을 사용하며, 작업 이름의 공백("")을 밑줄(_)으로 바꿉니다. 이 개체는 현재 워크플로 인스턴스 실행에서 작업 속성 값에 대 한 액세스를 제공합니다. |
| `trigger` | Object | 트리거에서 생성된 개체와 [트리거() 함수를](../logic-apps/workflow-definition-language-functions-reference.md#trigger)호출하는 것과 동일합니다. 이 개체는 현재 워크플로 인스턴스 실행에서 트리거 속성 값에 대 한 액세스를 제공합니다. |
| `workflow` | Object | 워크플로 개체이며 [워크플로() 함수를](../logic-apps/workflow-definition-language-functions-reference.md#workflow)호출하는 것과 동일합니다. 이 개체는 현재 워크플로 인스턴스 실행에서 워크플로 이름, 실행 ID 등과 같은 워크플로 속성 값에 대한 액세스를 제공합니다. |
|||

이 항목의 예제에서 `workflowContext` 개체에는 코드에서 액세스할 수 있는 이러한 속성이 있습니다.

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

경우에 따라 **인라인 코드** 작업에 트리거 **또는** **작업** 매개 변수를 추가하여 코드가 종속성으로 참조하는 특정 작업의 결과가 포함되도록 명시적으로 요구할 수 있습니다. 이 옵션은 런타임에 참조된 결과를 찾을 수 없는 시나리오에 유용합니다.

> [!TIP]
> 코드를 다시 사용하려는 경우 코드 **상자를** 사용하여 속성에 대한 참조를 추가하여 코드에 트리거 또는 작업을 명시적 종속성으로 추가하는 대신 해결된 토큰 참조를 포함하도록 합니다.

예를 들어 Office 365 Outlook 커넥터에 대 한 **승인 전자 메일 보내기** 작업에서 **SelectedOption** 결과를 참조 하는 코드를 가지고 있다고 가정 합니다. 생성 시 Logic Apps 엔진은 코드를 분석하여 트리거 또는 작업 결과를 참조했는지 여부를 확인하고 해당 결과를 자동으로 포함합니다. 런타임에 지정된 `workflowContext` 개체에서 참조된 트리거 또는 작업 결과를 사용할 수 없다는 오류가 발생하면 해당 트리거 또는 작업을 명시적 종속성으로 추가할 수 있습니다. 이 예제에서는 **Action** 매개 변수를 추가하고 **인라인 코드** 작업에 승인 **전자 메일 보내기** 작업의 결과가 명시적으로 포함되도록 지정합니다.

이러한 매개 변수를 추가하려면 **새 매개 변수 목록 추가를** 열고 원하는 매개 변수를 선택합니다.

   ![매개 변수 추가](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | 매개 변수 | 설명 |
   |-----------|-------------|
   | **작업** | 이전 작업의 결과를 포함합니다. [작업 결과 포함](#action-results)을 참조하십시오. |
   | **트리거** | 트리거의 결과를 포함합니다. [트리거 결과 포함](#trigger-results)을 참조하십시오. |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>트리거 결과 포함

**트리거를**선택하면 트리거 결과를 포함할지 여부가 표시됩니다.

* **트리거** 목록에서 **예**.

<a name="action-results"></a>

### <a name="include-action-results"></a>작업 결과 포함

**작업을**선택하면 추가하려는 작업에 대한 메시지가 표시됩니다. 그러나 작업을 추가하기 전에 논리 앱의 기본 워크플로 정의에 나타나는 작업 이름의 버전이 필요합니다.

* 이 기능은 변수, 루프 및 반복 인덱스를 지원하지 않습니다.

* 논리 앱의 워크플로 정의의 이름은 공백이 아닌 밑줄(_)을 사용합니다.

* 점 연산자(.)를 사용하는 작업 이름에는 다음과 같은 연산자가 포함됩니다.

  `My.Action.Name`

1. 디자이너 도구 모음에서 **코드 보기를**선택하고 `actions` 특성 내부에서 작업 이름을 검색합니다.

   예를 들어 `Send_approval_email_` 승인 전자 메일 **보내기** 작업에 대한 JSON 이름입니다.

   ![JSON에서 작업 이름 찾기](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. 디자이너 보기로 돌아가려면 코드 보기 도구 모음에서 **디자이너**를 선택합니다.

1. 첫 번째 작업을 추가하려면 **작업 항목 - 1** 상자에 작업의 JSON 이름을 입력합니다.

   ![첫 번째 작업 입력](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. 다른 작업을 추가하려면 **새 항목 추가를**선택합니다.

## <a name="reference"></a>참고

워크플로 정의 언어를 사용하여 로직 앱의 기본 워크플로 정의에서 **JavaScript 코드** 실행 작업의 구조 및 구문에 대한 자세한 내용은 이 작업의 [참조 섹션을](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code)참조하십시오.

## <a name="next-steps"></a>다음 단계

[Azure 논리 앱용 커넥터에 대해](../connectors/apis-list.md) 자세히 알아보기
