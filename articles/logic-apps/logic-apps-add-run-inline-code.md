---
title: 코드 조각 추가 및 실행
description: Azure Logic Apps에서 인라인 코드를 사용 하 여 코드 조각 추가 및 실행
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: f28a93e47aa028f152d7ca797abb17cb3832aa60
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792611"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Azure Logic Apps에서 인라인 코드를 사용 하 여 코드 조각 추가 및 실행

논리 앱 내에서 코드 조각을 실행 하려는 경우 논리 앱의 워크플로에서 단계로 기본 제공 **인라인 코드** 작업을 추가할 수 있습니다. 이 작업은이 시나리오에 적합 한 코드를 실행 하려는 경우에 가장 잘 작동 합니다.

* JavaScript에서 실행 됩니다. 더 많은 언어가 제공 될 예정입니다.
* 5 초 이하로 실행을 완료 합니다.
* 최대 50 크기의 데이터를 처리 합니다.
* 는 아직 지원 되지 않는 [ **Variables** 작업](../logic-apps/logic-apps-create-variables-store-values.md)을 사용할 필요가 없습니다.
* Node.js 버전 8.11.1을 사용 합니다. 자세한 내용은 [표준 기본 제공 개체](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)를 참조 하세요. 

  > [!NOTE]
  > JavaScript를 실행 하는 **인라인 코드** 작업에서는 `require()` 함수를 지원 하지 않습니다.

이 작업은 코드 조각을 실행 하 고 논리 앱의 후속 작업에서 사용할 수 있는 **Result**라는 토큰으로 해당 코드 조각의 출력을 반환 합니다. 코드에 대 한 함수를 만들려는 다른 시나리오의 경우 논리 앱에서 [Azure 함수를 만들고 호출](../logic-apps/logic-apps-azure-functions.md) 해 보세요.

이 문서에서는 Office 365 Outlook 계정에 새 전자 메일이 도착할 때 예제 논리 앱이 트리거됩니다. 코드 조각은 메일 본문에 표시 되는 모든 전자 메일 주소를 추출 하 고 반환 합니다.

![예제 개요](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>전제 조건

* Azure 구독. Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 트리거를 포함 하 여 코드 조각을 추가 하려는 논리 앱입니다. 논리 앱이 없는 경우 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조 하세요.

   이 항목의 예제 논리 앱에서는 **새 전자 메일이 도착** 하는 경우 Office 365 Outlook 트리거를 사용 합니다.

* 논리 앱에 연결 된 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

  > [!NOTE]
  > 사용 사례 또는 시나리오에 적합 한 통합 계정을 사용 하 고 있는지 확인 합니다. 예를 들어, [무료 계층](../logic-apps/logic-apps-pricing.md#integration-accounts) 통합 계정은 프로덕션 시나리오가 아닌 예비 시나리오와 워크 로드에만 사용 되며, 사용량 및 처리량이 제한 되며, SLA (서비스 수준 계약)에서 지원 되지 않습니다. 다른 계층은 비용이 발생 하지만 SLA 지원을 포함 하 여 더 많은 처리량을 제공 하 고 더 높은 한도를 제공 합니다. 통합 계정 [계층](../logic-apps/logic-apps-pricing.md#integration-accounts), [가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)및 [제한](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)에 대해 자세히 알아보세요.

## <a name="add-inline-code"></a>인라인 코드 추가

1. 아직 [Azure Portal](https://portal.azure.com)하지 않은 경우 논리 앱 디자이너에서 논리 앱을 엽니다.

1. 디자이너에서 논리 앱의 워크플로에서 원하는 위치에 **인라인 코드** 작업을 추가 합니다.

   * 워크플로의 끝에 작업을 추가 하려면 **새 단계**를 선택 합니다.

   * 기존 단계 사이에 작업을 추가 하려면 해당 단계를 연결 하는 화살표 위로 마우스 포인터를 이동 합니다. 더하기 기호 ( **+** )를 선택 하 고 **작업 추가**를 선택 합니다.

   이 예제에서는 Office 365 Outlook 트리거에서 **인라인 코드** 작업을 추가 합니다.

   ![새 단계 추가](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. **작업 선택**아래의 검색 상자에 "인라인 코드"를 필터로 입력 합니다. 작업 목록에서 **JavaScript 코드 실행** 작업을 선택 합니다.

   !["JavaScript 코드 실행"을 선택 합니다.](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   작업은 디자이너에 표시 되며 return 문을 포함 하 여 몇 가지 기본 예제 코드를 포함 합니다.

   ![기본 샘플 코드를 사용 하는 인라인 코드 작업](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. **코드** 상자에서 샘플 코드를 삭제 하 고 실행 하려는 코드를 입력 합니다. 메서드 시그니처를 정의 하지 않고 메서드 내에 배치 하는 코드를 작성 합니다. 

   인식 된 키워드를 입력 하면 사용 가능한 키워드 중에서 선택할 수 있도록 자동 완성 목록이 표시 됩니다. 예를 들면 다음과 같습니다.

   ![키워드 자동 완성 목록](./media/logic-apps-add-run-inline-code/auto-complete.png)

   이 예제 코드 조각은 먼저 입력 텍스트에서 일치 하는 패턴을 지정 하는 *정규식*을 저장 하는 변수를 만듭니다. 그런 다음 트리거에서 전자 메일 본문 데이터를 저장 하는 변수를 만듭니다.

   ![변수 만들기](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   트리거와 이전 작업의 결과를 쉽게 참조할 수 있도록 커서를 **코드** 상자 안에 있는 동안 동적 콘텐츠 목록이 표시 됩니다. 이 예의 경우 목록에는 현재 선택할 수 있는 **본문** 토큰을 비롯 하 여 트리거에서 사용 가능한 결과가 표시 됩니다.

   **본문** 토큰을 선택 하면 인라인 코드 작업에서 토큰을 전자 메일의 `Body` 속성 값을 참조 하는 `workflowContext` 개체로 확인 합니다.

   ![결과 선택](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   코드 상자에서 **코드** 조각이 읽기 전용 `workflowContext` 개체를 입력으로 사용할 수 있습니다. 이 개체에는 워크플로의 트리거와 이전 작업의 결과에 대 한 코드 액세스를 제공 하는 하위 속성이 있습니다.
   자세한 내용은이 항목의 뒷부분에 나오는 [참조 트리거 및 작업 결과에서 코드](#workflowcontext)를 참조 하세요.

   > [!NOTE]
   >
   > 코드 조각이 점 (.) 연산자를 사용 하는 동작 이름을 참조 하는 경우 해당 동작 이름을 [ **Actions** 매개 변수에](#add-parameters)추가 해야 합니다. 이러한 참조는 또한 작업 이름을 대괄호 ([])와 큰따옴표로 묶어야 합니다. 예를 들면 다음과 같습니다.
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   인라인 코드 작업에는 `return` 문이 필요 하지 않지만 `return` 문의 결과는 이후 작업에서 **결과** 토큰을 통해 참조할 수 있습니다. 
   예를 들어 코드 조각은 정규식에 대해 전자 메일 본문에서 일치 항목을 찾는 `match()` 함수를 호출 하 여 결과를 반환 합니다. **작성** 작업은 **결과** 토큰을 사용 하 여 인라인 코드 작업의 결과를 참조 하 고 단일 결과를 만듭니다.

   ![완료된 논리 앱](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. 완료되면 논리 앱을 저장합니다.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>코드의 참조 트리거 및 작업 결과

`workflowContext` 개체는 `actions`, `trigger`및 `workflow` 하위 속성을 포함 하는이 구조를 포함 합니다.

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

이 테이블에는 다음 하위 속성에 대 한 자세한 내용이 포함 되어 있습니다.

| 자산 | Type | 설명 |
|----------|------|-------|
| `actions` | 개체 컬렉션 | 코드 조각이 실행 되기 전에 실행 되는 작업의 결과 개체입니다. 각 개체에는 키가 동작 이름인 *키-값* 쌍이 있으며 값은 `@actions('<action-name>')`를 사용 하 여 [actions () 함수](../logic-apps/workflow-definition-language-functions-reference.md#actions) 를 호출 하는 것과 같습니다. 작업 이름은 기본 워크플로 정의에 사용 되는 것과 동일한 동작 이름을 사용 합니다 .이 이름은 작업 이름의 공백 ("")을 밑줄 (_)로 대체 합니다. 이 개체는 현재 워크플로 인스턴스 실행에서 작업 속성 값에 대 한 액세스를 제공 합니다. |
| `trigger` | Object | 트리거의 결과 개체 이며 [trigger () 함수](../logic-apps/workflow-definition-language-functions-reference.md#trigger)를 호출 하는 것과 동일 합니다. 이 개체는 현재 워크플로 인스턴스 실행의 트리거 속성 값에 대 한 액세스를 제공 합니다. |
| `workflow` | Object | 워크플로 개체 이며 [workflow () 함수](../logic-apps/workflow-definition-language-functions-reference.md#workflow)를 호출 하는 것과 동일 합니다. 이 개체를 사용 하면 현재 워크플로 인스턴스가 실행 되는 워크플로 이름, 실행 ID 등의 워크플로 속성 값에 액세스할 수 있습니다. |
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

**트리거** 또는 **작업** 매개 변수를 추가 하 여 코드에서 종속성으로 참조 하는 트리거 또는 특정 작업의 결과가 **인라인 코드** 작업에 포함 되도록 명시적으로 지정 해야 하는 경우도 있습니다. 이 옵션은 참조 된 결과가 런타임에 없는 시나리오에 유용 합니다.

> [!TIP]
> 코드를 다시 사용 하려는 경우 트리거 또는 작업을 명시적 종속성으로 추가 하는 대신 코드에 확인 된 토큰 참조가 포함 되도록 **코드** 상자를 사용 하 여 속성에 대 한 참조를 추가 합니다.

예를 들어 Office 365 Outlook 커넥터에 대 한 **승인 전자 메일 보내기** 작업의 **SelectedOption** 결과를 참조 하는 코드가 있다고 가정 합니다. 만든 시간에 Logic Apps 엔진은 코드를 분석 하 여 트리거 또는 작업 결과를 참조 하 고 해당 결과를 자동으로 포함 하는지 여부를 확인 합니다. 런타임에는 지정 된 `workflowContext` 개체에서 참조 된 트리거 또는 작업 결과를 사용할 수 없다는 오류가 표시 됩니다. 해당 트리거나 작업을 명시적 종속성으로 추가할 수 있습니다. 이 예제에서는 **작업** 매개 변수를 추가 하 고 **인라인 코드** 작업에 **승인 전자 메일 보내기** 작업의 결과가 명시적으로 포함 되도록 지정 합니다.

이러한 매개 변수를 추가 하려면 **새 매개 변수 추가** 목록을 열고 원하는 매개 변수를 선택 합니다.

   ![매개 변수 추가](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | 매개 변수를 포함해야 합니다. | 설명 |
   |-----------|-------------|
   | **Actions** | 이전 작업의 결과를 포함 합니다. [작업 결과 포함](#action-results)을 참조 하세요. |
   | **트리거** | 트리거의 결과를 포함 합니다. [트리거 결과 포함](#trigger-results)을 참조 하세요. |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>트리거 결과 포함

**트리거**를 선택 하면 트리거 결과를 포함할지 여부를 묻는 메시지가 표시 됩니다.

* **트리거** 목록에서 **예**를 선택 합니다.

<a name="action-results"></a>

### <a name="include-action-results"></a>작업 결과 포함

**작업**을 선택 하면 추가 하려는 작업을 묻는 메시지가 표시 됩니다. 그러나 작업 추가를 시작 하기 전에 논리 앱의 기본 워크플로 정의에 표시 되는 작업 이름의 버전이 필요 합니다.

* 이 기능은 변수, 루프 및 반복 인덱스를 지원 하지 않습니다.

* 논리 앱의 워크플로 정의에 있는 이름은 공백이 아닌 밑줄 (_)을 사용 합니다.

* 점 연산자 (.)를 사용 하는 작업 이름에는 다음과 같은 연산자를 포함 합니다.

  `My.Action.Name`

1. 디자이너 도구 모음에서 **코드 보기**를 선택 하 고 작업 이름에 대 한 `actions` 특성 내에서 검색 합니다.

   예를 들어 `Send_approval_email_`은 **승인 전자 메일 보내기** 작업의 JSON 이름입니다.

   ![JSON에서 작업 이름 찾기](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. 디자이너 뷰로 돌아가려면 코드 보기 도구 모음에서 **디자이너**를 선택 합니다.

1. 첫 번째 작업을 추가 하려면 작업 **항목-1** 상자에 작업의 JSON 이름을 입력 합니다.

   ![첫 번째 작업 입력](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. 다른 작업을 추가 하려면 **추가 새 항목**을 선택 합니다.

## <a name="reference"></a>참조

워크플로 정의 언어를 사용 하 여 논리 앱의 기본 워크플로 정의에서 **JavaScript 코드** 작업의 구조 및 구문을 실행 하는 방법에 대 한 자세한 내용은이 작업의 [참조 섹션](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Azure Logic Apps 커넥터](../connectors/apis-list.md) 에 대 한 자세한 정보
