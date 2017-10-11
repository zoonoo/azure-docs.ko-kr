---
title: "조건 추가 및 워크플로 시작 - Azure Logic Apps | Microsoft Docs"
description: "조건부 논리, 트리거, 동작 및 매개 변수를 추가하여 Azure Logic Apps에서 워크플로가 실행되는 방식을 제어합니다."
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e4e24de4-049a-4b3a-a14c-3bf3163287a8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2017
ms.author: LADocs; stepsic
ms.openlocfilehash: e632c48ed31e82536db55a9c54438bece0c38fd4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="use-logic-apps-features"></a>논리 앱 기능 사용

[이전 항목](../logic-apps/logic-apps-create-a-logic-app.md)에서는 첫 번째 논리 앱을 만들었습니다. 논리 앱의 워크플로를 제어하기 위해 실행할 논리 앱에 대한 다른 경로와 배열, 컬렉션 및 일괄 처리의 데이터 처리 방법을 지정할 수 있습니다. 논리 앱 워크플로에 포함될 수 있는 요소는 다음과 같습니다.

* 조건 및 [switch 문](../logic-apps/logic-apps-switch-case.md) - 특정 조건이 충족되는지 여부에 따라 논리 앱에서 다른 동작을 실행하도록 합니다.

* [루프](../logic-apps/logic-apps-loops-and-scopes.md) - 논리 앱에서 단계를 반복적으로 실행하도록 합니다. 예를 들어 **For_each** 루프를 사용할 때 배열에 대해 동작을 반복할 수 있습니다. 또는 **Until** 루프를 사용할 때 조건이 충족될 때까지 동작을 반복할 수 있습니다.

* [범위](../logic-apps/logic-apps-loops-and-scopes.md) - 예를 들어 예외 처리를 구현하기 위해 일련의 동작을 그룹화하도록 합니다.

* [분리](../logic-apps/logic-apps-loops-and-scopes.md) - **SplitOn** 명령을 사용할 때 논리 앱에서 배열의 항목에 대해 별도의 워크플로를 시작하도록 합니다.

이 항목에서는 논리 앱을 빌드하기 위한 다른 개념에 대해 설명합니다.

* 기존 논리 앱을 편집하기 위한 코드 보기
* 워크플로 시작 옵션

## <a name="conditions-run-steps-only-after-meeting-a-condition"></a>조건: 조건 충족 후에만 단계 실행

데이터가 특정 기준을 충족하는 경우에만 논리 앱에서 단계를 실행하도록 하려면 워크플로의 데이터와 특정 필드 또는 값을 비교하는 조건을 추가할 수 있습니다.

예를 들어 웹 사이트의 RSS 피드에 있는 게시물에 대해 너무 많은 전자 메일을 보내는 논리 앱이 있다고 가정해 보겠습니다. 새 게시물이 특정 범주에 속한 경우에만 논리 앱에서 전자 메일을 보내도록 조건을 추가할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)의 Logic App Designer에서 논리 앱을 찾아서 엽니다.

2. 원하는 워크플로 위치에 조건을 추가합니다. 

   논리 앱 워크플로의 기존 단계 사이에 조건을 추가하려면 조건을 추가하려는 화살표 위로 포인터를 이동합니다. 
   **더하기 기호**(**+**)를 선택한 다음 **조건 추가**를 선택합니다. 예:

   ![논리 앱에 조건 추가](./media/logic-apps-use-logic-app-features/add-condition.png)

   > [!NOTE]
   > 현재 워크플로의 끝에 조건을 추가하려면 논리 앱의 아래쪽으로 이동하여 **+ 새 단계**를 선택합니다.

3. 이제 조건을 정의합니다. 평가할 원본 필드, 수행할 작업 및 대상 값 또는 필드를 지정합니다. 현재 필드를 조건에 추가하려면 **동적 콘텐츠 추가 목록**에서 선택합니다.

   예:

   ![기본 모드에서 조건 편집](./media/logic-apps-use-logic-app-features/edit-condition-basic-mode.png)

   완성된 조건은 다음과 같습니다.

   ![완성된 조건](./media/logic-apps-use-logic-app-features/edit-condition-basic-mode-2.png)

   > [!TIP]
   > 코드에서 조건을 정의하려면 **고급 모드에서 편집**을 선택합니다. 예:
   > 
   > ![코드에서 조건 편집](./media/logic-apps-use-logic-app-features/edit-condition-advanced-mode.png)

4. **IF YES** 및 **IF NO** 아래에서 조건이 충족되는지 여부에 따라 수행할 단계를 추가합니다.

   예:

   ![YES 및 NO 경로가 있는 조건](./media/logic-apps-use-logic-app-features/condition-yes-no-path.png)

   > [!TIP]
   > 기존 동작을 **IF YES** 및 **IF NO** 경로로 끌 수 있습니다.

5. 완료되면 논리 앱을 저장합니다.

이제 게시물이 조건을 충족할 때만 전자 메일을 받습니다.

## <a name="repeat-actions-over-a-list-with-foreach"></a>forEach를 포함한 목록에 작업 반복

forEach 루프는 작업을 반복하는 배열을 지정합니다. 배열이 아닌 경우 흐름이 실패합니다. 예를 들어, 메시지의 배열을 출력하는 동작1이 있고 각 메시지를 전송하려는 경우 이 forEach 문을 작업의 속성에서 포함할 수 있습니다. `forEach : "@action('action1').outputs.messages"`

## <a name="edit-the-code-definition-for-a-logic-app"></a>논리 앱에 대한 코드 정의 편집

Logic App Designer가 있더라도 논리 앱을 정의하는 코드를 직접 편집할 수 있습니다.

1. 명령 모음에서 **코드 보기**를 선택합니다.

    전체 편집기가 열리고 편집한 정의를 표시합니다.

    ![코드 보기](media/logic-apps-use-logic-app-features/codeview.png)

    텍스트 편집기에서 동일한 논리 앱 내에서 또는 논리 앱 간에 작업을 개수 제한 없이 복사 및 붙여넣을 수 있습니다. 
    정의에서 전체 섹션을 쉽게 추가하거나 제거하고 정의를 다른 사람들과 공유할 수도 있습니다.

2. 편집을 저장하려면 **저장**을 선택합니다.

## <a name="parameters"></a>매개 변수

일부 Logic Apps 기능은 매개 변수와 같은 코드 보기에서만 사용할 수 있습니다. 매개 변수를 통해 논리 앱 전체에서 쉽게 값을 다시 사용할 수 있습니다. 예를 들어 여러 작업에서 사용할 메일 주소가 있는 경우 해당 전자 메일 주소를 매개 변수로 정의해야 합니다.

매개 변수는 자주 변경하는 값을 끌어오는 데 유용합니다. 다양한 환경에서 매개 변수를 재정의해야 하는 경우에 특히 유용합니다. 환경에 따라 매개 변수를 재정의하는 방법을 알아보려면 [논리 앱 정의 작성](../logic-apps/logic-apps-author-definitions.md) 및 [REST API 설명서](https://docs.microsoft.com/rest/api/logic)를 참조하세요.

이 예제에서는 쿼리 용어에 대한 매개 변수를 사용할 수 있도록 기존 논리 앱을 업데이트하는 방법을 보여 줍니다.

1. 코드 보기에서 `parameters : {}` 개체를 찾고 `currentFeedUrl` 개체를 추가합니다.

        "currentFeedUrl" : {
            "type" : "string",
            "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
        }

2. `When_a_feed-item_is_published` 동작으로 이동하여 `queries` 섹션을 찾고 쿼리 값을 `"feedUrl": "#@{parameters('currentFeedUrl')}"`로 바꿉니다. 

    두 개 이상의 문자열을 조인하기 위해 `concat` 함수를 사용할 수도 있습니다. 
    예를 들어 `"@concat('#',parameters('currentFeedUrl'))"` 는 위 항목과 동일 하 게 작동 합니다.

3.  완료하면 **저장**을 선택합니다. 

    이제 `currentFeedURL`개 개체를 통해 다른 URL을 전달하여 웹 사이트의 RSS 피드를 변경할 수 있습니다.

[논리 앱 정의를 작성하는 방법](../logic-apps/logic-apps-author-definitions.md)에 대해 자세히 알아보세요.

## <a name="start-logic-app-workflows"></a>논리 앱 워크플로 시작

논리 앱에 정의된 워크플로를 시작하는 여러 가지 옵션이 있습니다. [Azure Portal]에서 언제든지 요청 시 워크플로를 시작할 수 있습니다.

### <a name="recurrence-triggers"></a>되풀이 트리거

되풀이 트리거는 지정한 간격마다 실행됩니다. 트리거에 조건부 논리가 있을 경우 트리거가 워크플로를 실행할지 여부를 결정합니다. 트리거는 `200` 상태 코드를 반환하여 워크플로를 실행한다는 것을 의미합니다. 워크플로 실행할 필요가 없는 경우 트리거는 `202` 상태 코드를 반환합니다.

### <a name="callback-using-rest-apis"></a>REST API를 사용한 콜백

워크플로를 시작하려면 서비스에서는 논리 앱 끝점을 호출할 수 있습니다. 이러한 종류의 요청 시 논리 앱을 시작하려면 명령 모음에서 **지금 실행** 단추를 클릭합니다. [논리 앱 끝점을 트리거로 호출하여 워크플로 시작](../logic-apps/logic-apps-http-endpoint.md)을 참조하세요. 

<!-- Shared links -->
[Azure Portal]: https://portal.azure.com

## <a name="next-steps"></a>다음 단계

* [Switch 문](../logic-apps/logic-apps-switch-case.md) 
* [루프, 범위 및 분할](../logic-apps/logic-apps-loops-and-scopes.md)
* [작성자 논리 앱 정의](../logic-apps/logic-apps-author-definitions.md)