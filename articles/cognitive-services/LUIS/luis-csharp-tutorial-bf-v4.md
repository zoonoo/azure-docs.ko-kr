---
title: C#을 사용하는 LUIS 봇 - 자습서 - 웹앱 봇 - Bot Framework SDK 4.0
titleSuffix: Azure Cognitive Services
description: C#을 사용하여 LUIS(Language Understanding)와 통합된 챗봇을 빌드합니다. 이 챗봇은 인적 자원 앱을 사용하여 봇 솔루션을 빠르게 구현합니다. 봇은 Bot Framework 버전 4.x 및 Azure 웹앱 봇을 사용하여 빌드되었습니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/25/2018
ms.author: diberry
ms.openlocfilehash: 3ccec4fbd0fd69539e29e2f15f71115444bf0a48
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389295"
---
# <a name="tutorial-luis-bot-in-c"></a>자습서: C#의 LUIS 봇
C#을 사용하여 LUIS(Language Understanding)와 통합된 챗봇을 빌드할 수 있습니다. 이 봇은 HomeAutomation 앱을 사용하여 봇 솔루션을 구현합니다. 봇은 [Bot Framework 버전](https://github.com/Microsoft/botbuilder-js) v4 및 Azure [웹앱 봇](https://docs.microsoft.com/azure/bot-service/)을 사용하여 빌드되었습니다.

**이 자습서에서 학습할 내용은 다음과 같습니다.**

> [!div class="checklist"]
> * 웹앱 봇 만들기. 이 프로세스는 새 LUIS 앱을 만듭니다.
> * 새 LUIS 모델에 미리 빌드된 도메인 추가
> * 웹 봇 서비스에서 만든 프로젝트 다운로드
> * 컴퓨터에서 로컬로 봇 및 에뮬레이터 시작
> * 새 LUIS 의도에 대한 봇 코드 수정
> * 봇에서 발화 결과 보기

## <a name="prerequisites"></a>필수 조건

* [봇 에뮬레이터](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-web-app-bot"></a>웹앱 봇 만들기

1. [Azure Portal](https://portal.azure.com)에서 **새 리소스 만들기**를 선택합니다.

2. 검색 상자에서 **웹앱 봇**을 검색하여 선택합니다. **만들기**를 선택합니다.

3. **Bot Service**에서 필수 정보를 제공합니다.

    |설정|목적|권장되는 설정|
    |--|--|--|
    |봇 이름|리소스 이름|`luis-csharp-bot-` + `<your-name>`, 예: `luis-csharp-bot-johnsmith`|
    |구독|봇을 만들 수 있는 구독|사용자의 기본 구독
    |리소스 그룹|Azure 리소스의 논리 그룹|이 봇에 사용되는 모든 리소스를 저장할 새 그룹을 만들고, 그룹 이름으로 `luis-csharp-bot-resource-group`을 지정합니다.|
    |위치|Azure 지역 - LUIS를 작성하거나 게시하는 지역과 같을 필요는 없습니다.|`westus`|
    |가격 책정 계층 |서비스 요청 한도 및 청구에 사용됩니다.|`F0`은 평가판 계층입니다.
    |앱 이름|봇이 클라우드에 배포될 때 하위 도메인으로 사용됩니다(예: humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, 예: `luis-csharp-bot-johnsmith`|
    |봇 템플릿|봇 프레임워크 설정 - 다음 표 참조|
    |LUIS 앱 위치|LUIS 리소스 지역과 동일해야 합니다.|`westus`|

4. **봇 템플릿 설정**에서 다음 설정을 선택한 다음, 이러한 설정 아래에 있는 **선택** 단추를 선택합니다.

    |설정|목적|선택|
    |--|--|--|
    |SDK 버전|봇 프레임워크 버전|**SDK v4**|
    |SDK 언어|봇의 프로그래밍 언어|**C#**|
    |에코/기본 봇|봇의 유형|**기본 봇**|
    
5. **만들기**를 선택합니다. 이렇게 하면 봇 서비스가 만들어지고 Azure에 배포됩니다. 이 프로세스의 일환으로 `luis-csharp-bot-XXXX`라는 LUIS 앱이 만들어집니다. 이 이름은 이전 섹션의 봇 및 앱 이름을 기반으로 합니다.

    [ ![웹앱 봇 만들기](./media/bfv4-csharp/create-web-app-service.png) ](./media/bfv4-csharp/create-web-app-service.png#lightbox)

6. 이 브라우저 탭을 열어 둡니다. LUIS 포털의 모든 단계에서 새 브라우저 탭을 엽니다. 새 봇 서비스가 배포되면 다음 섹션으로 계속 진행하세요.

## <a name="add-prebuilt-domain-to-model"></a>모델에 미리 빌드된 도메인 추가
봇 서비스 배포의 일환으로 의도와 발화 예제가 포함된 새 LUIS 앱이 만들어집니다. 봇에서 제공하는 새 LUIS 앱에 대한 의도 매핑과 관련된 의도는 다음과 같습니다. 

|기본 봇 LUIS 의도|발화 예제|
|--|--|
|취소|`stop`|
|Greeting|`hello`|
|도움말|`help`|
|없음|앱의 도메인 외부에 있는 항목입니다.|

미리 빌드된 HomeAutomation 앱을 모델에 추가하여 `Turn off the living room lights`와 같은 발화를 처리합니다.

1. [LUIS](https://www.luis.ai) 포털로 이동하고 로그인합니다.
2. **내 앱** 페이지에서 **만든 날짜** 열을 선택하여 앱을 만든 날짜를 기준으로 정렬합니다. Azure Bot Service는 이전 섹션에서 새 앱을 만들었습니다. 이름은 `luis-csharp-bot-` + `<your-name>` + 임의의 4개 문자입니다.
3. 앱을 열고, 위쪽 탐색 영역에서 **빌드** 섹션을 선택합니다.
4. 왼쪽 탐색 영역에서 **미리 빌드된 도메인**을 선택합니다.
5. 해당 카드에서 **도메인 추가**를 선택하여 **HomeAutomation** 도메인을 선택합니다.
6. 오른쪽 위 메뉴에서 **학습**을 선택합니다.
7. 오른쪽 위 메뉴에서 **게시**를 선택합니다. 

    Azure Bot Service로 만든 앱에는 이제 새 의도가 있습니다.

    |기본 봇 새 의도|발화 예제|
    |--|--|
    |HomeAutomation.TurnOn|`turn the fan to high`
    |HomeAutomation.TurnOff|`turn off ac please`|

## <a name="download-the-web-app-bot"></a>웹앱 봇 다운로드 
웹앱 봇 코드를 개발하기 위해 코드를 다운로드하여 로컬 컴퓨터에서 사용합니다. 

1. 여전히 Azure Portal의 웹앱 봇 리소스에서 **응용 프로그램 설정**을 선택하고 **botFilePath** 및 **botFileSecret**의 값을 복사합니다. 이러한 값은 나중에 환경 파일에 추가해야 합니다. 

2. Azure Portal의 **봇 관리** 섹션에서 **빌드**를 선택합니다. 

3. **봇 소스 코드 다운로드**를 선택합니다. 

    [ ![기본 봇용 웹앱 봇 소스 코드 다운로드](../../../includes/media/cognitive-services-luis/bfv4/download-code.png) ](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

4. 소스 코드가 압축되어 있으면 메시지에서 해당 코드를 다운로드할 수 있는 링크가 제공됩니다. 이 링크를 선택합니다. 

5. Zip 파일을 로컬 컴퓨터에 저장하고 파일의 압축을 풉니다. 프로젝트를 엽니다. 

6. bot.js 파일을 열고 `_services.LuisServices`를 찾습니다. 이는 봇에 입력된 사용자 발화를 LUIS로 보내는 위치입니다.

    ```csharp
    /// <summary>
    /// Run every turn of the conversation. Handles orchestration of messages.
    /// </summary>
    /// <param name="turnContext">Bot Turn Context.</param>
    /// <param name="cancellationToken">Task CancellationToken.</param>
    /// <returns>A <see cref="Task"/> representing the asynchronous operation.</returns>
    public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    {
        var activity = turnContext.Activity;

        if (activity.Type == ActivityTypes.Message)
        {
            // Perform a call to LUIS to retrieve results for the current activity message.
            var luisResults = await _services.LuisServices[LuisConfiguration].RecognizeAsync(turnContext, cancellationToken).ConfigureAwait(false);

            // If any entities were updated, treat as interruption.
            // For example, "no my name is tony" will manifest as an update of the name to be "tony".
            var topScoringIntent = luisResults?.GetTopScoringIntent();

            var topIntent = topScoringIntent.Value.intent;
            switch (topIntent)
            {
                case GreetingIntent:
                    await turnContext.SendActivityAsync("Hello.");
                    break;
                case HelpIntent:
                    await turnContext.SendActivityAsync("Let me try to provide some help.");
                    await turnContext.SendActivityAsync("I understand greetings, being asked for help, or being asked to cancel what I am doing.");
                    break;
                case CancelIntent:
                    await turnContext.SendActivityAsync("I have nothing to cancel.");
                    break;
                case NoneIntent:
                default:
                    // Help or no intent identified, either way, let's provide some help.
                    // to the user
                    await turnContext.SendActivityAsync("I didn't understand what you just said to me.");
                    break;
            }
        }
        else if (activity.Type == ActivityTypes.ConversationUpdate)
        {
            if (activity.MembersAdded.Any())
            {
                // Iterate over all new members added to the conversation.
                foreach (var member in activity.MembersAdded)
                {
                    // Greet anyone that was not the target (recipient) of this message.
                    // To learn more about Adaptive Cards, see https://aka.ms/msbot-adaptivecards for more details.
                    if (member.Id != activity.Recipient.Id)
                    {
                        var welcomeCard = CreateAdaptiveCardAttachment();
                        var response = CreateResponse(activity, welcomeCard);
                        await turnContext.SendActivityAsync(response).ConfigureAwait(false);
                    }
                }
            }
        }

    }
    ```

    봇에서 사용자의 발화를 LUIS로 보내고 결과를 가져옵니다. 맨 위에 있는 의도는 대화 흐름을 결정합니다. 


## <a name="start-the-bot"></a>봇 시작
코드 또는 설정을 변경하기 전에 봇이 작동하는지 확인합니다. 

1. Visual Studio에서 솔루션 파일을 엽니다. 

2. 봇 코드에서 찾은 봇 변수를 저장할 `appsettings.json` 파일을 만듭니다.

    ```JSON
    {
    "botFileSecret": "",
    "botFilePath": ""

    }
    ```

    변수 값을 **[웹앱 봇 다운로드](#download-the-web-app-bot)** 섹션 1 단계의 Azure 봇 서비스 [응용 프로그램 설정]에서 복사한 값으로 설정합니다.

3. Visual Studio에서 봇을 시작합니다. `http://localhost:3978/`에 있는 웹앱 봇의 웹 사이트를 통해 브라우저 창이 열립니다.

## <a name="start-the-emulator"></a>에뮬레이터 시작

1. 봇 에뮬레이터를 시작합니다.

2. 봇 에뮬레이터에서 프로젝트의 루트에 있는 *.bot 파일을 선택합니다. 이 `.bot` 파일에는 메시지에 대한 봇의 URL 엔드포인트가 포함됩니다.

    [ ![봇 에뮬레이터 v4](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png) ](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png#lightbox)

3. **[웹앱 봇 다운로드](#download-the-web-app-bot)** 섹션 1 단계의 Azure 봇 서비스 [응용 프로그램 설정]에서 복사한 봇 비밀을 입력합니다. 이렇게 하면 에뮬레이터에서 `.bot` 파일의 암호화된 필드에 액세스할 수 있습니다.

    ![봇 에뮬레이터 비밀 v4](../../../includes/media/cognitive-services-luis/bfv4/bot-secret.png)

4. 봇 에뮬레이터에서 `Hello`를 입력하고 기본 봇에 대한 적절한 응답을 가져옵니다.

    [ ![에뮬레이터의 기본 봇 응답](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png) ](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png#lightbox)

## <a name="modify-bot-code"></a>봇 코드 수정 

새 의도를 처리하는 코드를 `BasicBot.cs` 파일에 추가합니다. 

1. 파일의 위쪽에서 **지원되는 LUIS 의도** 섹션을 찾고 HomeAutomation 의도에 대한 상수를 추가합니다.

    ```csharp
    // Supported LUIS Intents
    public const string GreetingIntent = "Greeting";
    public const string CancelIntent = "Cancel";
    public const string HelpIntent = "Help";
    public const string NoneIntent = "None";
    public const string TurnOnIntent = "HomeAutomation_TurnOn"; // new intent
    public const string TurnOffIntent = "HomeAutomation_TurnOff"; // new intent
    ```

    LUIS 포털의 앱에 있는 도메인과 의도 사이의 마침표(`.`)가 밑줄(`_`)로 바뀝니다. 

2. 발화에 대한 LUIS 예측을 받는 **OnTurnAsync** 메서드를 찾습니다. switch 문에 코드를 추가하여 두 HomeAutomation 의도에 대한 LUIS 응답을 반환합니다. 

    ```csharp
    case TurnOnIntent:
        await turnContext.SendActivityAsync("TurnOn intent found, JSON response: " + luisResults?.Entities.ToString());
        break;
    case TurnOffIntent:
        await turnContext.SendActivityAsync("TurnOff intent found, JSON response: " + luisResults?.Entities.ToString());
        break;
    ```

    봇에는 LUIS REST API 요청과 똑같은 응답이 없으므로 응답 JSON을 보고 차이점을 알아보는 것이 중요합니다. 텍스트 및 의도 속성은 동일하지만 엔터티 속성 값이 수정되었습니다. 

    ```JSON
    {
        "$instance": {
            "HomeAutomation_Device": [
                {
                    "startIndex": 23,
                    "endIndex": 29,
                    "score": 0.9776345,
                    "text": "lights",
                    "type": "HomeAutomation.Device"
                }
            ],
            "HomeAutomation_Room": [
                {
                    "startIndex": 12,
                    "endIndex": 22,
                    "score": 0.9079433,
                    "text": "livingroom",
                    "type": "HomeAutomation.Room"
                }
            ]
        },
        "HomeAutomation_Device": [
            "lights"
        ],
        "HomeAutomation_Room": [
            "livingroom"
        ]
    }
    ```



## <a name="view-results-in-bot"></a>봇에서 결과 보기

1. 봇 에뮬레이터에서 `Turn on the livingroom lights to 50%` 발화를 입력합니다.

2. 봇에서 다음과 같이 응답합니다.

    ```JSON
    TurnOn intent found, JSON response: {"$instance":{“HomeAutomation_Device”:[{“startIndex”:23,“endIndex”:29,“score”:0.9776345,“text”:“lights”,“type”:“HomeAutomation.Device”}],“HomeAutomation_Room”:[{“startIndex”:12,“endIndex”:22,“score”:0.9079433,“text”:“livingroom”,“type”:“HomeAutomation.Room”}]},“HomeAutomation_Device”:[“lights”],“HomeAutomation_Room”:[“livingroom”]}
    ```    

## <a name="learn-more-about-bot-framework"></a>Bot Framework에 대해 자세히 알아보기
Azure Bot Service는 Bot Framework SDK를 사용합니다. SDK 및 봇 프레임워크에 대해 자세히 알아보세요.

* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) v4 설명서
* [Bot Builder 샘플](https://github.com/Microsoft/botbuilder-samples)
* [Bot Builder SDK](https://docs.microsoft.com/javascript/api/botbuilder-core/?view=botbuilder-ts-latest)
* [Bot Builder 도구](https://github.com/Microsoft/botbuilder-tools):

## <a name="next-steps"></a>다음 단계

Azure 봇 서비스를 만들고, 봇 비밀 및 `.bot` 파일 경로를 복사하고, 코드의 Zip 파일을 다운로드했습니다. 미리 빌드된 HomeAutomation 도메인을 새 Azure 봇 서비스의 일환으로 만든 LUIS 앱에 추가한 다음, 앱을 다시 학습하고 게시했습니다. 코드 프로젝트를 추출하고, 환경 파일(`.env`)을 만들고, 봇 비밀과 `.bot` 파일 경로를 설정했습니다. 두 개의 새 의도를 처리하는 코드가 bot.js 파일에 추가되었습니다. 그런 다음, 봇 에뮬레이터에서 봇을 테스트하여 새 의도 중 하나의 발화에 대한 LUIS 응답을 확인했습니다. 


> [!div class="nextstepaction"]
> [LUIS에서 사용자 지정 도메인 빌드](luis-quickstart-intents-only.md)
