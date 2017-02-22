---
title: "Azure Logic Apps에서 Switch 문 사용 | Microsoft Docs"
description: "Switch 문을 사용하면 Logic Apps에서 식의 값을 기반으로 하는 다른 작업을 쉽게 수행할 수 있습니다."
services: logic-apps
documentationcenter: dev-center-name
manager: erikre
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: 3a028507f9bbf15c8fd52ccc7c22a5763a9b1b3e
ms.openlocfilehash: 284cfca17b5abf785f7af9569c518c4400fe36fd


---
# <a name="use-switch-statement-in-logic-apps"></a>Logic Apps에서 Switch 문 사용
워크플로를 작성할 때 종종 개체 또는 식의 값을 기반으로 하는 다른 작업을 수행해야 하는 합니다. 예를 들어, Logic App을 HTTP 요청의 상태 코드 또는 승인 전자 메일의 선택한 옵션에 따라 다르게 작동시키려고 합니다.

switch 문을 사용하여 이러한 시나리오를 수행할 수 있습니다. Logic App은 토큰이나 식을 평가하고 작업을 실행하는 값이 동일한 경우를 선택합니다. 하나의 사례만이 switch 문과 일치해야 합니다.

 > [!TIP]
 > 모든 프로그래밍 언어와 마찬가지로 switch 문은 같음 연산자만을 지원합니다. 다른 관계형 연산자가 필요한 경우(예: 초과) 조건문을 사용합니다.
 >
 > 결정적 실행 동작을 확인하려면 사례에는 동적 토큰이나 식이 아닌 고유한 정적 값이 포함되어야 합니다.

## <a name="prerequisites"></a>필수 조건

- 활성 Azure 구독.
    - 활성 Azure 구독이 없는 경우 시작하기 전에 [무료 계정을 만들거나](https://azure.microsoft.com/free/) [무료로 Logic Apps](https://tryappservice.azure.com/)을 사용해 보세요.
- [Logic Apps에 대한 기본 지식](logic-apps-what-are-logic-apps.md).

## <a name="working-with-switch-statement-in-designer"></a>디자이너에서 switch 문 사용
switch 문의 사용법을 보여 주기 위해 Dropbox에 업로드된 파일을 모니터링하는 Logic App을 만들어 보겠습니다. Logic App은 SharePoint로 전송되어야 하는지를 결정하는 승인 메일을 전송합니다. switch 문을 사용하여 선택한 값 승인자에 따라 다른 조치를 취합니다.

1. Logic App을 만들고 **Dropbox - 파일을 만들 경우** 트리거를 선택합니다.

 ![Dropbox - 파일을 만들 경우 트리거 사용](./media/logic-apps-switch-case/dropbox-trigger.jpg)

2. **Outlook.com - 승인 전자 메일 보내기** 작업을 사용하여 트리거를 덧붙입니다.

 > [!TIP]
 > 또한 Logic Apps은 Office 365 Outlook 계정에서 승인 전자 메일 시나리오를 지원합니다.

 - 기존 연결이 없으면 새로 만들라는 메시지가 표시됩니다.
 - 필수 필드를 입력하고 approvers@contoso.com로 전자 메일을 보냅니다.
 - *사용자 옵션*에서 `Approve, Reject`을 입력합니다.

 ![연결 구성](./media/logic-apps-switch-case/send-approval-email-action.jpg)

3. Switch 문을 추가합니다.
 - **+ 새 단계**, **... 추가**, **switch 문 추가**를 선택합니다.
 - *승인 전자 메일 보내기* 작업의 `SelectedOptions` 출력에 따라 실행하려는 작업을 선택하려면 **동적 콘텐츠 추가** 선택기에서 찾을 수 있습니다.
 - *사례 1*을 사용하여 사용자가 `Approve`을 선택한 경우를 처리합니다.
    - 승인된 경우 **SharePoint Online - 파일 만들기** 작업을 사용하여 원래 파일을 SharePoint Online에 복사합니다.
    - 새 파일을 SharePoint에서 사용할 수 있다는 것을 사용자에게 알리는 사례 내에 다른 작업을 추가합니다.
 - 또 다른 사례를 추가하여 사용자가 `Reject`을 선택한 경우를 처리합니다.
    - 거부되는 경우 파일이 거부되고 추가 조치가 필요하지 않다는 것을 다른 승인자에게 알리는 알림 전자 메일을 보냅니다.
 - `SelectedOptions`에는 두 개의 옵션만이 제공됩니다. *기본* 사례는 비어 있을 수 있습니다.

 ![Switch 문](./media/logic-apps-switch-case/switch.jpg)

 > [!NOTE]
 > Switch 문은 기본 사례 외에도 적어도 한 가지 사례가 필요합니다.

4. Switch 문 뒤에 **Dropbox - 파일 삭제** 작업을 사용하여 Dropbox로 업로드된 원래 파일을 삭제합니다.

5. 논리 앱을 저장하고 Dropbox에 파일을 업로드하여 테스트합니다. 승인 메일을 수신하고 나서 잠시 후에 옵션을 선택하고 동작을 관찰해야 합니다.
 > [!TIP]
 > [Logic Apps 모니터링](logic-apps-monitor-your-logic-apps.md) 방법을 확인합니다.

## <a name="understanding-code-behind"></a>숨김 코드 이해
이제 switch 문을 사용하여 Logic App을 성공적으로 생성했습니다. 다음과 같이 숨김 코드를 살펴보겠습니다.

```json
"Switch": {
    "type": "Switch",
    "expression": "@body('Send_approval_email')?['SelectedOption']",
    "cases": {
        "Case 1" : {
            "case" : "Approved",
            "actions" : {}
        },
        "Case 2" : {
            "case" : "Rejected",
            "actions" : {}
        }
    },
    "default": {
        "actions": {}
    },
    "runAfter": {
        "Send_approval_email": [
            "Succeeded"
        ]
    }
}
```

`"Switch"`은 switch 문의 이름으로써 가독성을 위해 바꿀 수 있습니다. `"type": "Switch"`은 작업이 switch 문임을 나타냅니다. 이 경우에 `"expression"`은 사용자의 선택된 옵션으로 나중에 정의에서 선언된 각 사례와 비교하여 평가됩니다. `"cases"`은 사례의 수를 포함할 수 있고 모든 사례가 switch 식과 일치하지 않는 경우 `"default"` 내의 작업이 실행됩니다.

`"cases"` 내에 있는 사례의 수일 수 있습니다. 각 사례의 경우 `"Case 1"`은 사례의 이름으로써 가독성을 위해 바꿀 수 있습니다. `"case"`은 switch 식을 비교할 사례 레이블을 지정합니다. 이 레이블은 지속적이고 고유한 값이어야 합니다.  

## <a name="next-steps"></a>다음 단계
- 다른 [Logic Apps 기능](logic-apps-use-logic-app-features.md)을 사용해 보세요.
- [오류 및 예외 처리](logic-apps-exception-handling.md)에 대해 알아봅니다.
- [워크플로 언어 기능](logic-apps-author-definitions.md)을 자세히 살펴봅니다.
- 질문이나 피드백에 의견을 남기거나 [Logic Apps을 개선할 수 있는 방법을 알려주세요](https://feedback.azure.com/forums/287593-logic-apps).


<!--HONumber=Feb17_HO2-->


