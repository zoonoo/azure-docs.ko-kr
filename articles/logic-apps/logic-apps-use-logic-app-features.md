---
title: "조건 논리 추가 및 워크플로 시작 - Azure Logic Apps | Microsoft Docs"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 9f7d623ec213de6d46f59547aff9d4417ac95ede
ms.openlocfilehash: 41aafe94d24f0e22fe2256ab213c7668b670764c
ms.contentlocale: ko-kr
ms.lasthandoff: 02/15/2017


---
# <a name="use-logic-apps-features"></a>논리 앱 기능 사용
[이전 항목](../logic-apps/logic-apps-create-a-logic-app.md)에서는 첫 번째 논리 앱을 만들었습니다. 이제 Azure Logic Apps을 사용하여 전체 프로세스를 빌드합니다. 이 항목에서는 다음과 같은 새 Azure Logic Apps 개념을 소개합니다.

* 조건부 논리 - 특정 조건이 충족될 때만 작업을 실행합니다.
* 기존 논리 앱을 편집하기 위한 코드 보기.
* 워크플로 시작 옵션.

이 항목을 완료하기 전에 [새 논리 앱 만들기](../logic-apps/logic-apps-create-a-logic-app.md)의 단계를 완료해야 합니다. [Azure Portal]에서 논리 앱을 찾은 다음 요약에서 **트리거 및 동작** 을 클릭하여 논리 앱 정의를 편집합니다.

## <a name="reference-material"></a>참조 자료
유용한 다음 문서를 볼 수 있습니다.

* [관리와 런타임 REST API](https://msdn.microsoft.com/library/azure/mt643787.aspx) - 논리 앱을 직접 호출 하는 방법 포함
* [언어 참조](https://msdn.microsoft.com/library/azure/mt643789.aspx) - 모든 지원 되는 함수/식의 포괄적인 목록
* [트리거 및 작업 형식](https://msdn.microsoft.com/library/azure/mt643939.aspx) - 다양한 유형의 동작 및 동작에 필요한 입력
* [앱 서비스의 개요](../app-service/app-service-value-prop-what-is.md) - 솔루션을 구축하는 시점을 선택하는 구성 요소의 설명

## <a name="add-conditional-logic-to-your-logic-app"></a>논리 앱에 조건부 논리 추가

논리 앱의 원래 흐름이 작동하더라도 일부 영역을 개선할 수 있습니다.

### <a name="conditional"></a>조건부

첫 번째 논리 앱은 전자 메일을 너무 많이 가져오게 될 수 있습니다. 다음 단계에서는 특정 수의 팔로워를 보유한 사람으로부터 트윗이 오는 경우에만 전자 메일을 받도록 조건 논리를 추가합니다.

0. Logic App Designer에서 **새 단계**(+) > **작업 추가**를 선택합니다.
0.    Twitter에 대한 **사용자 가져오기** 작업을 찾고 추가합니다.
0. Twitter 사용자에 대한 정보를 가져오기 위해 트리거에서 **트윗 보낸 사람** 필드를 찾고 추가합니다.

    ![사용자 가져오기](media/logic-apps-use-logic-app-features/getuser.png)

0. **새 단계**(+) > **조건 추가**를 선택합니다.
0. 사용자의 팔로워 수를 필터링하려면 **개체 이름**에서 **동적 콘텐츠 추가**를 선택합니다. 
0.    검색 상자에서 **팔로워 수** 필드를 찾고 추가합니다.
0. **관계** 아래에서 **다음보다 큼**을 선택합니다.
0. **값** 상자에서 원하는 사용자의 팔로워 수를 입력합니다.

    ![조건부](media/logic-apps-use-logic-app-features/conditional.png)

0. 마지막으로 **전자 메일 보내기** 상자를 **예인 경우** 상자로 끌어 놓습니다. 

이제 팔로워 수가 조건을 충족하는 경우에 전자 메일을 받게 됩니다.

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

### <a name="parameters"></a>매개 변수

일부 Logic Apps 기능은 매개 변수와 같은 코드 보기에서만 사용할 수 있습니다. 매개 변수를 통해 논리 앱 전체에서 쉽게 값을 다시 사용할 수 있습니다. 예를 들어 여러 작업에서 사용할 메일 주소가 있는 경우 해당 전자 메일 주소를 매개 변수로 정의해야 합니다.

매개 변수는 자주 변경하는 값을 끌어오는 데 유용합니다. 다양한 환경에서 매개 변수를 재정의해야 하는 경우에 특히 유용합니다. 환경에 따라 매개 변수를 재정의하는 방법을 알아보려면 [REST API 설명서](https://docs.microsoft.com/rest/api/logic)를 참조하세요.

이 예제에서는 쿼리 용어에 대한 매개 변수를 사용할 수 있도록 기존 논리 앱을 업데이트하는 방법을 보여 줍니다.

1. 코드 보기에서 `parameters : {}` 개체를 찾고 항목 개체를 추가합니다.

        "topic" : {
            "type" : "string",
            "defaultValue" : "MicrosoftAzure"
        }

2. `twitterconnector` 작업으로 이동하여 쿼리 값을 찾고 해당 값을 `#@{parameters('topic')}`로 바꿉니다. 

    두 개 이상의 문자열을 조인하기 위해 `concat` 함수를 사용할 수도 있습니다. 
    예를 들어, `@concat('#',parameters('topic'))`는 앞의 경우와 동일하게 작동합니다.

3.    완료하면 **저장**을 선택합니다. 

    이제 리트윗이&5;개를 초과한 새 트윗은 매시간마다 Dropbox의 **tweets** 폴더로 배달됩니다.

논리 앱 정의에 대해 더 알아보려면 [논리 응용 프로그램 정의 작성](../logic-apps/logic-apps-author-definitions.md)을 참조하십시오.

## <a name="start-logic-app-workflows"></a>논리 앱 워크플로 시작

논리 앱에 정의된 워크플로를 시작하는 여러 가지 옵션이 있습니다. [Azure Portal]에서 언제든지 요청 시 워크플로를 시작할 수 있습니다.

### <a name="recurrence-triggers"></a>되풀이 트리거

되풀이 트리거는 지정한 간격마다 실행됩니다. 트리거에 조건부 논리가 있을 경우 트리거가 워크플로를 실행할지 여부를 결정합니다. 트리거는 `200` 상태 코드를 반환하여 워크플로를 실행한다는 것을 의미합니다. 워크플로 실행할 필요가 없는 경우 트리거는 `202` 상태 코드를 반환합니다.

### <a name="callback-using-rest-apis"></a>REST API를 사용한 콜백

워크플로를 시작하려면 서비스에서는 논리 앱 끝점을 호출할 수 있습니다. 이러한 종류의 요청 시 논리 앱을 시작하려면 명령 모음에서 **지금 실행** 단추를 클릭합니다. [논리 앱 끝점을 트리거로 호출하여 워크플로 시작](../logic-apps/logic-apps-http-endpoint.md)을 참조하세요. 

<!-- Shared links -->
[Azure Portal]: https://portal.azure.com

