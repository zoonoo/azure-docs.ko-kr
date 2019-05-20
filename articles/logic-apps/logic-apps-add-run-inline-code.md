---
title: 추가한 코드 조각을-Azure Logic Apps 실행
description: 추가 하 고 Azure Logic Apps에서 인라인 코드를 사용 하 여 코드 조각을 실행합니다
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: derek1ee, LADocs
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: 0bfa98396ee3afb80b486a5a17959664dfbe603c
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602126"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>추가 하 고 Azure Logic Apps에서 인라인 코드를 사용 하 여 코드 조각을 실행합니다

논리 앱 내에서 코드 조각을 실행 하려는 경우에 기본 제공을 추가할 수 있습니다 **인라인 코드** 논리 앱 워크플로에서 단계와 작업을 수행 합니다. 이 그러면이이 시나리오에 맞는 코드를 실행 하려는 경우에 가장 적합 합니다.

* JavaScript에서 실행 됩니다. 더 많은 언어를 곧 제공 될 예정입니다.
* 5 초 또는 더 적은 수의 실행이 완료 합니다.
* 데이터 처리에 최대 50MB 크기에서입니다.
* Node.js 버전 8.11.1 사용합니다. 자세한 내용은 [표준 기본 제공 개체](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)합니다. 

  > [!NOTE]
  > Require () 함수에서 지원 되지 않는 합니다 **인라인 코드** JavaScript를 실행 하기 위한 작업입니다.

이 작업 코드 조각을 실행 하 고 해당 코드 조각에서 명명 된 토큰으로 출력을 반환 합니다 **결과**, 논리 앱에서 후속 작업에서 사용할 수 있습니다. 다른 시나리오의 코드에 대 한 함수를 만들려는 경우 시도 [를 만들고 Azure function을 호출](../logic-apps/logic-apps-azure-functions.md) 논리 앱에서.

이 문서의 예제에서는 논리 앱 트리거 때 새 전자 메일을 Office 365 Outlook 계정에 도착 합니다. 코드 조각을 추출 하 고 전자 메일 본문에 나타나는 모든 전자 메일 주소를 반환 합니다.

![예제 개요](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 논리 앱 트리거를 포함 하 여, 코드 조각을 추가할 위치입니다. 논리 앱에 없는 경우 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토하세요.

   이 항목의 예제에서는 논리 앱이 Office 365 Outlook 트리거를 사용합니다. **새 전자 메일이 도착했을 때**

* [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 논리 앱에 연결 되어 있는

## <a name="add-inline-code"></a>인라인 코드를 추가 합니다.

1. 아직 하지 않은 경우에 [Azure portal](https://portal.azure.com), Logic App Designer에서 논리 앱을 엽니다.

1. 디자이너에 추가 합니다 **인라인 코드** 논리 앱 워크플로의 원하는 위치에서 작업 합니다.

   * 선택 작업을 워크플로 끝에 추가할 **새 단계**합니다.

   * 기존 단계 사이 작업을 추가, 이러한 단계를 연결 하는 화살표 위로 마우스 포인터를 놓습니다. 더하기 기호를 선택 (**+**)를 선택 하 고 **작업 추가**합니다.

   이 예제에서는 추가 된 **인라인 코드** Office 365 Outlook 트리거 아래에서 작업 합니다.

   ![새 단계 추가](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. 아래 **작업 선택**, 검색 상자에서 필터로 "인라인 코드"를 입력 합니다. 작업 목록에서 다음 작업을 선택합니다. **JavaScript 코드를 실행 합니다.**

   !["JavaScript 코드 실행" 선택](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   디자이너에 표시 되 고 return 문을 포함 하 여 몇 가지 기본 예제 코드가 포함 됩니다.

   ![기본 샘플 코드를 사용 하 여 인라인 코드 작업](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. 에 **코드** 상자 샘플 코드를 삭제 하 고 코드를 실행 하려면를 입력 합니다. 메서드 시그니처를 정의 하지 않고 메서드 내에서 배치 하는 코드를 작성 합니다. 

   인식할 수 있는 키워드를 입력 하면 자동 완성 목록에는 예를 들어 사용할 수 있는 키워드에서 선택할 수 있도록 표시 됩니다.

   ![키워드 자동 완성 목록](./media/logic-apps-add-run-inline-code/auto-complete.png)

   이 예제에서는 코드 조각을 저장 하는 변수를 먼저 만듭니다는 *정규식*, 입력된 텍스트에서 일치 시킬 패턴을 지정 합니다. 다음 코드는 트리거에서 전자 메일 본문 데이터를 저장 하는 변수를 만듭니다.

   ![변수 만들기](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   트리거 및 이전 작업의 결과 더 쉽게 참조 하려면 동적 콘텐츠 목록 내에 커서가 동안을 표시 합니다 **코드** 상자입니다. 예를 들어 포함 목록에는 트리거를 사용할 수 있는 결과 표시 합니다 **본문** 토큰을 선택할 수 있습니다.

   선택한 후는 **본문** 인라인 코드 작업 확인 토큰을 토큰을 `workflowContext` 전자 메일의를 참조 하는 개체 `Body` 속성 값:

   ![결과 선택](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   에 **코드** 상자에서 읽기 전용 코드 조각을 사용할 수 있습니다 `workflowContext` 입력으로 개체입니다. 이 개체에는 트리거 및 워크플로에서 이전 작업에서 결과에 액세스 코드를 제공 하는 하위 속성이 포함 합니다.
   자세한 내용은이 항목의 뒷부분에이 섹션을 참조 합니다. [코드에서 트리거 및 작업 결과 참조](#workflowcontext)합니다.

   > [!NOTE]
   >
   > 코드 조각을 점 (.) 연산자를 사용 하는 작업 이름을 참조 하는 경우 해당 작업 이름을 추가 해야 합니다 [ **동작** 매개 변수](#add-parameters)합니다. 이러한 참조 묶어야 대괄호 () 나 따옴표를 사용 하 여 작업의 이름을 예를 들어:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   인라인 코드 작업이 필요 하지 않습니다를 `return` 문과 비슷하지만 결과로 `return` 문을 통해 이후 작업에서 참조를 사용할 수는 **결과** 토큰입니다. 
   코드 조각을 호출 하 여 결과 반환 하는 예를 들어를 `match()` 함수를 정규식에 대 한 전자 메일 본문에 일치 하는 찾습니다. **Compose** 동작 사용 합니다 **결과** 참조에 토큰 인라인 결과로 작업 코드 및 단일 결과 만듭니다.

   ![완료된 논리 앱](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. 완료되면 논리 앱을 저장합니다.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>코드에서 참조 트리거 및 작업 결과

합니다 `workflowContext` 개체에 포함 하는이 구조는 `actions`를 `trigger`, 및 `workflow` 하위 속성:

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

이 표에서 이러한 하위 속성에 대 한 자세한 정보:

| 자산 | Type | 설명 |
|----------|------|-------|
| `actions` | 개체 컬렉션 | 코드 조각을 실행 되기 전에 실행 되는 작업에서 결과 개체입니다. 각 개체에는 *키-값* 쌍 여기서 키는 작업의 이름 및 값은 호출 하는 [actions() 함수](../logic-apps/workflow-definition-language-functions-reference.md#actions) 사용 하 여 `@actions('<action-name>')`입니다. 작업의 이름 사용 공간을 대체 하는 기본 워크플로 정의에서 사용 되는 동일한 작업 이름 ("") 밑줄 (_)를 사용 하 여 작업 이름에서입니다. 이 개체는 현재 워크플로 인스턴스의 실행 작업 속성 값에 액세스를 제공 합니다. |
| `trigger` | Object | 호출 하는 트리거 및 해당에서 결과 개체를 [trigger() 함수](../logic-apps/workflow-definition-language-functions-reference.md#trigger)합니다. 이 개체는 현재 워크플로 인스턴스의 실행 트리거 속성 값에 액세스를 제공 합니다. |
| `workflow` | Object | 해당 호출 하 고 워크플로 개체를 [workflow () 함수](../logic-apps/workflow-definition-language-functions-reference.md#workflow)합니다. 이 개체는 현재 워크플로 인스턴스의 실행 워크플로 이름, 실행된 ID 및 등과 같은 워크플로 속성 값에 액세스를 제공 합니다. |
|||

이 항목의 예는 `workflowContext` 개체의 코드에 액세스할 수 있는 이러한 속성:

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

명시적으로 해야 할 경우에 따라 합니다 **인라인 코드** 코드를 추가 하 여 종속성으로 참조 하는 트리거 또는 특정 작업의 결과 포함 하는 작업을 **트리거** 또는 **작업** 매개 변수입니다. 이 옵션은 런타임 시 참조 된 결과가 발견 되지 시나리오에 유용 합니다.

> [!TIP]
> 코드를 재사용 하려는 경우 사용 하 여 속성에 대 한 참조를 추가 합니다 **코드** 명시적 종속성으로 트리거 또는 작업을 추가 하는 것이 아니라 코드 확인된 토큰 참조를 포함 하도록 상자입니다.

예를 들어, 참조 하는 코드를 **SelectedOption** 에서 결과 **승인 전자 메일 보내기** Office 365 Outlook 커넥터에 대 한 작업입니다. 만드는 시점에 Logic Apps 엔진이 모든 트리거를 참조 한 여부 작업 결과 및 해당 결과 자동으로 포함을 확인 하는 코드를 분석 합니다. 런타임에 오류가 발생 하는 참조 된 트리거 또는 작업 결과에서 사용할 수 없는 지정 된 `workflowContext` 개체 종속성을 명시적으로 해당 트리거 또는 동작을 추가할 수 있습니다. 이 예에서는 추가 **작업** 매개 변수 지정 및를 **인라인 코드** 작업의 결과 명시적으로 포함는 **승인 전자 메일 보내기** 작업.

이러한 매개 변수를 추가 하려면 열을 **새 매개 변수 추가** 목록 및 매개 변수 선택:

   ![매개 변수 추가](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | 매개 변수 | 설명 |
   |-----------|-------------|
   | **actions** | 이전 작업의 결과 포함 합니다. 참조 [작업 결과 포함](#action-results)합니다. |
   | **트리거** | 트리거에서 결과 포함 합니다. 참조 [Include 트리거 결과](#trigger-results)합니다. |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>트리거가 결과 포함

선택 하는 경우 **트리거**, 결과 트리거를 포함 하려면 여부를 묻는 합니다.

* **트리거가** 목록에서 **예**합니다.

<a name="action-results"></a>

### <a name="include-action-results"></a>작업 결과 포함 합니다.

선택 하는 경우 **작업**를 묻는 추가 하려는 작업에 대 한 합니다. 그러나 작업 추가 시작 하기 전에 논리 앱의 기본 워크플로 정의에 표시 되는 작업 이름의 버전이 필요 합니다.

* 이 기능은 변수, 루프 및 반복 인덱스를 지원 하지 않습니다.

* 논리 앱 워크플로 정의에 이름에는 밑줄 (_), 공간이 아니라 사용 됩니다.

* 점 연산자 (.)를 사용 하는 작업 이름에 대 한 이러한 연산자가 예를 들어 포함:

  `My.Action.Name`

1. 디자이너 도구 모음에서 선택 **코드 보기**, 내부 검색는 `actions` 작업 이름에 대 한 특성입니다.

   예를 들어 `Send_approval_email_` 에 대 한 JSON 이름인 합니다 **승인 전자 메일 보내기** 작업.

   ![JSON에서 작업 이름을 찾으려면](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. 코드 보기 도구 모음에서 디자이너 보기로 돌아가려면 선택 **디자이너**합니다.

1. 첫 번째 작업을 추가 하는 **작업 항목-1** 상자에서 작업의 JSON 이름을 입력 합니다.

   ![첫 번째 작업 입력](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. 다른 작업을 추가 하려면 선택 **새 항목 추가**합니다.

## <a name="reference"></a>참조

에 대 한 자세한 내용은 합니다 **JavaScript 코드 실행** 작업의 구조 및 구문 워크플로 정의 언어를 사용 하 여 논리 앱의 기본 워크플로 정의에서이 작업의 참조 [참조 섹션 ](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code).

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure Logic Apps 용 커넥터](../connectors/apis-list.md)
