---
title: "Azure Logic Apps에서 다양한 작업에 대한 Switch 문 | Microsoft 문서"
description: "Switch 문을 사용하여 식 값에 따라 Logic Apps에서 수행할 다양한 작업을 선택합니다."
services: logic-apps
keywords: "Switch 문"
author: derek1ee
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: LADocs; deli
ms.openlocfilehash: a459fb131cb9f917f2b0cf79679b04da48d76c42
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="perform-different-actions-in-logic-apps-with-a-switch-statement"></a>Switch 문으로 Logic Apps에서 다양한 작업 수행

워크플로를 작성할 때 종종 개체 또는 식의 값을 기반으로 하는 다양한 작업을 수행해야 합니다. 예를 들어 논리 앱을 HTTP 요청의 상태 코드 또는 전자 메일에서 선택한 옵션에 따라 서로 다르게 작동하도록 하고자 할 수 있습니다.

Switch 문을 사용하여 이러한 시나리오를 구현할 수 있습니다. 논리 앱에서 토큰 또는 식을 평가하고 같은 값을 가진 사례를 선택하여 지정된 작업을 실행합니다. 하나의 사례만이 switch 문과 일치해야 합니다.

> [!TIP]
> 모든 프로그래밍 언어와 마찬가지로 Switch 문도 같음 연산자만을 지원합니다. 다른 관계형 연산자(예: “초과”)가 필요한 경우 조건문을 사용합니다.
> 결정적 실행 동작을 확인하려면 사례에는 동적 토큰이나 식이 아닌 고유한 정적 값이 포함되어야 합니다.

## <a name="prerequisites"></a>필수 조건

- 활성 Azure 구독. 활성 Azure 구독이 없는 경우 시작하기 전에 [무료 계정을 만들거나](https://azure.microsoft.com/free/) [무료로 Logic Apps](https://tryappservice.azure.com/)을 사용해 보세요.
- [Logic Apps에 대한 기본 지식](logic-apps-overview.md)

## <a name="add-a-switch-statement-to-your-workflow"></a>Switch 문을 워크플로에 추가

Switch 문의 작동 원리를 보여 주기 위해 이 예제에서는 Dropbox에 업로드된 파일을 모니터링하는 논리 앱을 만듭니다. 새 파일이 업로드될 때 논리 앱은 해당 파일을 SharePoint로 전송할지 여부를 선택하는 승인자에게 전자 메일을 보냅니다. 앱에서는 승인자가 선택하는 값에 따라 서로 다른 작업을 수행하는 Switch 문을 사용합니다.

1. 논리 앱을 만들고 **Dropbox - 파일을 만들 경우** 트리거를 선택합니다.

   ![Dropbox - 파일을 만들 경우 트리거 사용](./media/logic-apps-switch-case/dropbox-trigger.jpg)

2. 트리거 아래에 **Outlook.com - 승인 전자 메일 보내기** 작업을 추가합니다.

   > [!TIP]
   > 또한 Logic Apps는 Office 365 Outlook 계정에서 승인 전자 메일을 보내는 시나리오를 지원합니다.

   - 기존 연결이 없으면 새로 만들라는 메시지가 표시됩니다.
   - 필수 필드를 입력합니다. 예를 들어 **받는 사람** 아래에 승인자 전자 메일을 보내기 위한 전자 메일 주소를 지정합니다.
   - **사용자 옵션**에서 `Approve, Reject`을 입력합니다.

   ![연결 구성](./media/logic-apps-switch-case/send-approval-email-action.jpg)

3. Switch 문을 추가합니다.

   - **+ 새 단계** > **... 추가 정보** > **Switch 사례 추가**를 선택합니다. 
   - 이제 *승인 전자 메일 보내기* 작업의 `SelectedOptions` 출력을 기반으로 수행할 작업을 선택하려고 합니다. 
   **동적 콘텐츠 추가** 선택기에서 이 필드를 찾을 수 있습니다.
   - *사례 1*을 사용하여 승인자가 `Approve`을 선택한 경우를 처리합니다.
     - 승인된 경우 [**SharePoint Online - 파일 만들기** 작업](../connectors/connectors-create-api-sharepointonline.md)을 사용하여 원래 파일을 SharePoint Online에 복사합니다.
     - 새 파일을 SharePoint에서 사용할 수 있다는 것을 사용자에게 알리는 사례 내에 다른 작업을 추가합니다.
   - 또 다른 사례를 추가하여 사용자가 `Reject`을 선택한 경우를 처리합니다.
     - 거부되는 경우 파일이 거부되고 추가 조치가 필요하지 않다는 것을 다른 승인자에게 알리는 알림 전자 메일을 보냅니다.
   - `SelectedOptions`은 두 옵션만 제공하므로 **기본값** 사례를 비워 둘 수 있습니다.

   ![Switch 문](./media/logic-apps-switch-case/switch.jpg)

   > [!NOTE]
   > Switch 문은 기본 사례 외에도 적어도 한 가지 사례가 필요합니다.

4. Switch 문 뒤에 **Dropbox - 파일 삭제** 작업을 추가하여 Dropbox로 업로드된 원래 파일을 삭제합니다.

5. 논리 앱을 저장합니다. Dropbox에 파일을 업로드하여 앱을 테스트합니다. 곧 승인 전자 메일을 받게 됩니다. 옵션을 선택하고 동작을 관찰합니다.

   > [!TIP]
   > [Logic Apps를 모니터링](logic-apps-monitor-your-logic-apps.md)하는 방법을 확인합니다.

## <a name="understand-the-code-behind-switch-statements"></a>Switch 문 뒤의 코드 이해

Switch 문을 사용하여 논리 앱을 만들었으므로, 이제 Switch 문 뒤의 코드 정의를 살펴보겠습니다.

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

* `"Switch"`은 Switch 문의 이름이며, 가독성을 위해 이 이름을 바꿀 수 있습니다. 
* `"type": "Switch"`은 작업이 Switch 문임을 나타냅니다. 
* `"expression"`은 이 예에서 승인자가 선택한 옵션이며 나중에 정의에서 선언된 각 사례와 비교하여 평가됩니다. 
* `"cases"`은 임의의 사례 수를 포함할 수 있습니다. 각 사례에 대해 `"Case *"`은 사례의 기본 이름이며, 가독성을 위해 이 이름을 바꿀 수 있습니다. 
`"case"`은 Switch 식이 비교에 사용할 사례 레이블을 지정하며 상수의 고유한 값이어야 합니다. Switch 식과 일치하는 사례가 없는 경우 `"default"` 아래의 작업이 실행됩니다.

## <a name="get-help"></a>도움말 보기

질문하고, 질문에 답변하고, 다른 Azure Logic Apps 사용자가 어떤 일을 하는지 확인하려면 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문하세요.

Azure Logic Apps 및 커넥터 개선에 도움을 주려면 [Azure Logic Apps 사용자 의견 사이트](http://aka.ms/logicapps-wish)에서 투표하고 아이디어를 제출하세요.

## <a name="next-steps"></a>다음 단계

- [조건 추가](logic-apps-use-logic-app-features.md) 방법을 알아봅니다.
- [오류 및 예외 처리](logic-apps-exception-handling.md)에 대해 알아봅니다.
- [워크플로 언어 기능](logic-apps-author-definitions.md)을 자세히 살펴봅니다.