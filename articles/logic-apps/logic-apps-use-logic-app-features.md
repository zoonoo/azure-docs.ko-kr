---
title: "조건과 트리거를 추가하여 워크플로 실행 - Azure Logic Apps | Microsoft Docs"
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
ms.author: stepsic
translationtype: Human Translation
ms.sourcegitcommit: d090ce5a912a2079d2e47d13caf60ca701f0e548
ms.openlocfilehash: 330359583346f7e1944b53ec3fd34104f10e1261


---
# <a name="use-logic-apps-features"></a>논리 앱 기능 사용
[이전 항목](../logic-apps/logic-apps-create-a-logic-app.md)에서는 첫 번째 논리 앱을 만들었습니다. 이제 앱 서비스 논리 앱을 사용하여 보다 완전한 프로세스를 빌드하는 방법을 설명하겠습니다. 이 항목에서는 다음과 같은 새 논리 앱 개념을 소개합니다.

* 조건부 논리 - 특정 조건이 충족될 때만 작업을 실행합니다.
* 기존 논리 앱을 편집하기 위한 코드 보기.
* 워크플로 시작 옵션.

이 항목을 완료하기 전에 [새 논리 앱 만들기](../logic-apps/logic-apps-create-a-logic-app.md)의 단계를 완료해야 합니다. [Azure 포털]에서 논리 앱을 찾은 다음 요약에서 **트리거 및 동작** 을 클릭하여 논리 앱 정의를 편집합니다.

## <a name="reference-material"></a>참조 자료
유용한 다음 문서를 볼 수 있습니다.

* [관리와 런타임 REST API](https://msdn.microsoft.com/library/azure/mt643787.aspx) - 논리 앱을 직접 호출 하는 방법 포함
* [언어 참조](https://msdn.microsoft.com/library/azure/mt643789.aspx) - 모든 지원 되는 함수/식의 포괄적인 목록
* [트리거 및 작업 형식](https://msdn.microsoft.com/library/azure/mt643939.aspx) - 다양한 유형의 동작 및 동작에 필요한 입력
* [앱 서비스의 개요](../app-service/app-service-value-prop-what-is.md) - 솔루션을 구축하는 시점을 선택하는 구성 요소의 설명

## <a name="adding-conditional-logic"></a>조건부 논리 추가
원래 흐름이 제대로 작동해도 향상시킬 수 있는 일부 영역이 있습니다.

### <a name="conditional"></a>조건부
이 논리 앱에서는 많은 전자 메일을 받을 수 있습니다. 다음 단계에서는 특정 수의 팔로워를 보유한 사람으로부터 트윗이 제공된 경우에만 메일을 받도록 하는 논리를 추가합니다.

1. 더하기를 클릭하고 Twitter에 대한 *Get User* 작업을 찾습니다.
2. Twitter 사용자에 대한 정보를 가져오기 위해 트리거에서 **Tweeted by** 필드를 전달합니다.

    ![Get User](media/logic-apps-use-logic-app-features/getuser.png)
3. 더하기를 다시 클릭하되 이번에는 **Add Condition**
4. 첫 번째 상자에서 **Get User** 아래의 **...**를 클릭하여 **Followers count** 필드를 찾습니다.
5. 드롭다운에서 **Greater than**
6. 두 번째 상자에서 원하는 사용자의 팔로워 수를 입력합니다.

    ![조건부](media/logic-apps-use-logic-app-features/conditional.png)
7. 마지막으로 전자 메일 상자를 **If Yes** 상자로 끌어 놓습니다. 그러면 팔로워 수가 충족되는 경우에만 전자 메일이 제공됩니다.

## <a name="repeating-over-a-list-with-foreach"></a>forEach를 포함한 목록 반복
forEach 루프는 작업을 반복하는 배열을 지정합니다. 배열이 아닌 경우 흐름이 실패합니다. 예를 들어 메시지의 배열을 출력하는 동작1이 있고 각 메시지를 전송하려는 경우 이 forEach 문을 작업의 속성에서 포함할 수 있습니다: forEach : "@action('action1').outputs.messages"

## <a name="using-the-code-view-to-edit-a-logic-app"></a>코드 보기를 사용하여 논리 앱 편집
디자이너 외에도 논리 앱을 정의하는 코드를 다음과 같이 직접 편집할 수 있습니다.

1. 명령 모음에서 **코드 보기** 단추를 클릭합니다.

    방금 편집한 정의를 표시하는 전체 편집기가 열립니다.

    ![코드 보기](media/logic-apps-use-logic-app-features/codeview.png)

    텍스트 편집기를 사용하여 동일한 논리 앱 내에서 또는 논리 앱 간에 작업을 개수 제한 없이 복사 및 붙여넣을 수 있습니다. 정의에서 전체 섹션을 쉽게 추가하거나 제거하고 정의를 다른 사람들과 공유할 수도 있습니다.
2. 코드 보기에서 변경한 후 **저장**을 클릭하기만 하면 됩니다.

### <a name="parameters"></a>매개 변수
코드 보기에서만 사용할 수 있는 논리 앱의 몇 가지 기능이 있습니다. 한 가지 예로 매개 변수가 있습니다. 매개 변수를 통해 논리 앱 전체에서 쉽게 값을 다시 사용할 수 있습니다. 예를 들어 여러 작업에서 사용할 메일 주소가 있는 경우 해당 주소를 매개 변수로 정의해야 합니다.

다음 단계에서는 쿼리 용어에 매개 변수를 사용하도록 기존 논리 앱을 업데이트합니다.

1. 코드 보기에서 `parameters : {}` 개체를 찾아 다음 topic 개체를 삽입합니다.

        "topic" : {
            "type" : "string",
            "defaultValue" : "MicrosoftAzure"
        }
2. `twitterconnector` 동작으로 스크롤하고 쿼리 값을 찾은 다음 `#@{parameters('topic')}`으로 바꿉니다.
    **concat** 함수를 사용하여 둘 이상의 문자열을 함께 조인할 수 있습니다. 예를 들어 `@concat('#',parameters('topic'))`는 위의 동일합니다

매개 변수는 자주 변경하는 값을 끌어오는 좋은 방법입니다. 다양한 환경에서 매개 변수를 재정의해야 하는 경우에 특히 유용합니다. 환경에 따라 매개 변수를 재정의하는 방법에 대한 자세한 내용은 [REST API 설명서](https://msdn.microsoft.com/library/mt643787.aspx)를 참조하세요.

이제 **저장**을 클릭하면 리트윗이 5개를 초과한 새 트윗은 매시간마다 Dropbox의 **tweets** 폴더로 배달됩니다.

논리 앱 정의에 대해 더 알아보려면 [논리 응용 프로그램 정의 작성](../logic-apps/logic-apps-author-definitions.md)을 참조하십시오.

## <a name="starting-a-logic-app-workflow"></a>논리 앱 워크플로 시작
논리 앱에 정의된 워크플로를 시작하는 여러 가지 옵션이 있습니다. [Azure 포털]에서 요청 시 언제든지 워크플로를 시작할 수 있습니다.

### <a name="recurrence-triggers"></a>되풀이 트리거
되풀이 트리거는 지정한 간격마다 실행됩니다. 트리거에 조건부 논리가 있을 경우 트리거가 워크플로를 실행할지 여부를 결정합니다. 트리거는 `200` 상태 코드를 반환하여 워크플로를 실행하도록 지정합니다. 실행할 필요가 없는 경우 `202` 상태 코드를 반환합니다.

### <a name="callback-using-rest-apis"></a>REST API를 사용한 콜백
서비스에서 논리 앱 끝점을 호출하여 워크플로를 시작할 수 있습니다. 자세한 내용은 [호출 가능 끝점인 논리 앱](../logic-apps/logic-apps-http-endpoint.md) 을 참조하세요. 요청 시 이러한 종류의 논리 앱을 시작하려면 명령 모음에서 **지금 실행** 단추를 클릭합니다. 

<!-- Shared links -->
[Azure 포털]: https://portal.azure.com



<!--HONumber=Jan17_HO4-->


