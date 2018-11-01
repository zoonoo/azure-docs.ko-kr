---
title: 첫 번째 LUIS 앱에 대해 10분
titleSuffix: Azure Cognitive Services
description: 미리 빌드된 도메인 `HomeAutomation`을 사용하여 조명 및 어플라이언스를 켜고 끄는 LUIS 앱을 만듭니다. 미리 작성된 도메인에는 의도, 엔터티 및 예제 발언이 제공됩니다. 마치면 클라우드에서 LUIS 엔드포인트를 실행하게 됩니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: cc4a1a7f99de657a6199985dd0c963eed741ac97
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50210026"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>빠른 시작: 미리 작성된 Home automation 앱 사용

이 빠른 시작에서는 미리 작성된 도메인 `HomeAutomation`를 사용하여 조명 및 어플라이언스를 켜고 끄는 LUIS 응용 프로그램을 만듭니다. 미리 작성된 도메인에는 의도, 엔터티 및 예제 발언이 제공됩니다. 마치면 클라우드에서 LUIS 엔드포인트를 실행하게 됩니다.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 [https://www.luis.ai](https://www.luis.ai)의 LUIS 포털에 만들어진 무료 LUIS 계정이 필요합니다. 

## <a name="create-a-new-app"></a>새 앱 만들기
응용 프로그램은 **내 앱**에서 만들고 관리할 수 있습니다. 

1. LUIS 포털에 로그인합니다.

2. **새 앱 만들기**를 선택합니다.

    [![](media/luis-quickstart-new-app/app-list.png "앱 목록 스크린샷")](media/luis-quickstart-new-app/app-list.png)

3. 대화 상자에서 응용 프로그램 이름을 "Home Automation"으로 지정합니다.

    [![](media/luis-quickstart-new-app/create-new-app-dialog.png "새 앱 만들기 팝업 대화 상자의 스크린샷")](media/luis-quickstart-new-app/create-new-app-dialog.png)

4. 응용 프로그램 문화권을 선택합니다. Home Automation 앱의 경우 영어를 선택합니다. 그런 후 **완료**를 선택합니다. LUIS에서 Home Automation 앱이 만들어집니다. 

    >[!NOTE]
    >응용 프로그램을 만든 후에는 문화권을 변경할 수 없습니다. 

## <a name="add-prebuilt-domain"></a>미리 빌드된 도메인 추가

왼쪽 탐색 창에서 **미리 작성된 도메인**을 선택합니다. 그런 다음, "Home"을 검색합니다. **도메인 추가**를 선택합니다.

[![](media/luis-quickstart-new-app/home-automation.png "미리 작성된 도메인에서 호출된 Home Automation의 스크린샷")](media/luis-quickstart-new-app/home-automation.png)

도메인 추가에 성공하면 미리 작성된 도메인 상자에 **도메인 제거** 단추가 표시됩니다.

[![](media/luis-quickstart-new-app/remove-domain.png "도메인 제거 단추가 있는 Home Automation 도메인의 스크린샷")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>의도 및 엔터티

왼쪽 탐색 창에서 **의도**를 선택하여 HomeAutomation 도메인 의도를 검토합니다. 의도마다 예제 발언이 있습니다.

> [!NOTE]
> **없음**은 모든 LUIS 앱에 제공되는 의도입니다. 이것은 앱에 제공되는 기능과 일치하지 않는 발언을 처리하는 데 사용됩니다. 

**HomeAutomation.TurnOff** 의도를 선택합니다. 엔터티를 사용하여 레이블이 지정된 발언 목록이 의도에 포함된 것을 볼 수 있습니다.

[![](media/luis-quickstart-new-app/home-automation-turnon.png "HomeAutomation.TurnOff 의도의 스크린샷")](media/luis-quickstart-new-app/home-automation-turnon.png)

## <a name="train-the-luis-app"></a>LUIS 앱 학습

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>앱 테스트
앱을 학습시킨 후에는 테스트할 수 있습니다. 맨 위 탐색에서 **테스트**를 선택합니다. 대화형 테스트 창에 테스트 발언(예: "Turn off the lights")을 입력하고 Enter 키를 누릅니다. 

```
Turn off the lights
```

점수가 가장 높은 의도가 각 테스트 발언에 대한 의도와 일치하는지 확인합니다.

이 예제에서 "Turn off the lights"는 "HomeAutomation.TurnOff"의 최고 득점 의도로 올바르게 식별되었습니다.

[![](media/luis-quickstart-new-app/test.png "발언이 강조 표시된 테스트 패널의 스크린샷")](media/luis-quickstart-new-app/test.png)


**테스트**를 다시 선택하여 테스트 창을 접습니다. 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>앱을 게시하여 엔드포인트 URL 가져오기

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>다른 발화를 사용하여 엔드포인트 쿼리

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. 주소의 URL 끝으로 이동하고 `turn off the living room light`를 입력한 다음, Enter 키를 누릅니다. 브라우저는 HTTP 엔드포인트의 JSON 응답을 표시합니다.

    [![](media/luis-quickstart-new-app/turn-off-living-room.png "JSON 결과에서 TurnOff 의도가 검색된 브라우저의 스크린샷")](media/luis-quickstart-new-app/turn-off-living-room.png)
    
## <a name="clean-up-resources"></a>리소스 정리

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

다음 코드에서 엔드포인트를 호출할 수 있습니다.

> [!div class="nextstepaction"]
> [코드를 사용하여 LUIS 엔드포인트 호출](luis-get-started-cs-get-intent.md)
