---
title: Language Understanding 봇 C# v4
titleSuffix: Azure Cognitive Services
description: C#을 사용하여 LUIS(Language Understanding)와 통합된 챗봇을 빌드합니다. 봇은 Bot Framework 버전 4 및 Azure 웹앱 봇 서비스를 사용하여 빌드되었습니다.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: 210724e8a8b9b585a3e308b8e321d809e4e897a1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560655"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>자습서: C#에서 Language Understanding으로 구현된 웹앱 봇 사용

C#을 사용하여 LUIS(Language Understanding)와 통합된 챗봇을 빌드합니다. 이 봇은 Azure [웹앱 봇](https://docs.microsoft.com/azure/bot-service/) 리소스 및 [Bot Framework 버전](https://github.com/Microsoft/botbuilder-dotnet) V4를 사용하여 빌드되었습니다.

**이 자습서에서 학습할 내용은 다음과 같습니다.**

> [!div class="checklist"]
> * 웹앱 봇 만들기. 이 프로세스는 새 LUIS 앱을 만듭니다.
> * 웹 봇 서비스에서 만든 봇 프로젝트 다운로드
> * 컴퓨터에서 로컬로 봇 및 에뮬레이터 시작
> * 봇에서 발화 결과 보기

## <a name="prerequisites"></a>필수 조건

* [봇 에뮬레이터](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-a-web-app-bot-resource"></a>웹앱 봇 리소스 만들기

1. [Azure Portal](https://portal.azure.com)에서 **새 리소스 만들기**를 선택합니다.

1. 검색 상자에서 **웹앱 봇**을 검색하여 선택합니다. **만들기**를 선택합니다.

1. **Bot Service**에서 필수 정보를 제공합니다.

    |설정|목적|권장되는 설정|
    |--|--|--|
    |봇 이름|리소스 이름|`luis-csharp-bot-` + `<your-name>`, 예: `luis-csharp-bot-johnsmith`|
    |Subscription|봇을 만들 수 있는 구독|사용자의 기본 구독
    |Resource group|Azure 리소스의 논리 그룹|이 봇에 사용되는 모든 리소스를 저장할 새 그룹을 만들고, 그룹 이름으로 `luis-csharp-bot-resource-group`을 지정합니다.|
    |위치|Azure 지역 - LUIS를 작성하거나 게시하는 지역과 같을 필요는 없습니다.|`westus`|
    |가격 책정 계층|서비스 요청 한도 및 청구에 사용됩니다.|`F0`은 평가판 계층입니다.
    |앱 이름|봇이 클라우드에 배포될 때 하위 도메인으로 사용됩니다(예: humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, 예: `luis-csharp-bot-johnsmith`|
    |봇 템플릿|봇 프레임워크 설정 - 다음 표 참조|
    |LUIS 앱 위치|LUIS 리소스 지역과 동일해야 합니다.|`westus`|
    |앱 서비스 플랜/위치|제공된 기본값에서 변경하지 않습니다.|
    |Application Insights|제공된 기본값에서 변경하지 않습니다.|
    |Microsoft 앱 ID 및 암호|제공된 기본값에서 변경하지 않습니다.|

1. **봇 템플릿**에서 다음 설정을 선택한 다음, 이러한 설정 아래에 있는 **선택** 단추를 선택합니다.

    |설정|목적|선택|
    |--|--|--|
    |SDK 버전|봇 프레임워크 버전|**SDK v4**|
    |SDK 언어|봇의 프로그래밍 언어|**C#**|
    |봇|봇의 유형|**기본 봇**|
    
1. **만들기**를 선택합니다. 이렇게 하면 봇 서비스가 만들어지고 Azure에 배포됩니다. 이 프로세스의 일환으로 `luis-csharp-bot-XXXX`라는 LUIS 앱이 만들어집니다. 이 이름은 /Azure Bot Service 앱 이름을 기반으로 합니다.

    [![웹앱 봇 만들기](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

    봇 서비스가 만들어질 때까지 기다렸다가 계속 진행합니다.

## <a name="the-bot-has-a-language-understanding-model"></a>봇에 Language Understanding 모델이 있는 경우

또한 봇 서비스 생성 프로세스는 의도와 발화 예제가 포함된 새 LUIS 앱을 만듭니다. 봇에서 제공하는 새 LUIS 앱에 대한 의도 매핑과 관련된 의도는 다음과 같습니다. 

|기본 봇 LUIS 의도|발화 예제|
|--|--|
|항공권 예약|`Travel to Paris`|
|취소|`bye`|
|없음|앱의 도메인 외부에 있는 항목입니다.|

## <a name="test-the-bot-in-web-chat"></a>웹 채팅에서 봇 테스트

1. 새 봇의 Azure Portal에서 계속 **웹 채팅에서 테스트**를 선택합니다. 
1. **메시지 입력** 텍스트 상자에 텍스트 `hello`를 입력합니다. 봇이 봇 프레임워크에 대한 정보와 특정 LUIS 모델의 쿼리 예(예: 파리행 항공권 예약)로 응답합니다. 

    ![Azure Portal의 스크린샷, 텍스트 `hello`를 입력합니다.](./media/bfv4-csharp/ask-bot-question-in-portal-test-in-web-chat.png)

    테스트 기능을 사용하여 봇을 빠르게 테스트할 수 있습니다. 디버깅을 포함한 자세한 테스트를 위해 봇 코드를 다운로드하고 Visual Studio를 사용합니다. 

## <a name="download-the-web-app-bot-source-code"></a>웹앱 봇 소스 코드 다운로드
웹앱 봇 코드를 개발하기 위해 코드를 다운로드하여 로컬 컴퓨터에서 사용합니다. 

1. Azure Portal의 **봇 관리** 섹션에서 **빌드**를 선택합니다. 

1. **봇 소스 코드 다운로드**를 선택합니다. 

    [![기본 봇용 웹앱 봇 소스 코드 다운로드](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. **다운로드된 zip 파일에 앱 설정을 포함하시겠습니까?** 팝업 대화 상자에서 **예**를 선택합니다.

1. 소스 코드가 압축되어 있으면 메시지에서 해당 코드를 다운로드할 수 있는 링크가 제공됩니다. 이 링크를 선택합니다. 

1. Zip 파일을 로컬 컴퓨터에 저장하고 파일의 압축을 풉니다. Visual Studio로 프로젝트를 엽니다. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>코드를 검토하여 발화를 LUIS로 보내고 응답 받기

1. **LuisHelper.cs** 파일을 엽니다. 이는 봇에 입력된 사용자 발화를 LUIS로 보내는 위치입니다. LUIS의 응답은 메소드에서 **BookDetails** 개체로 반환됩니다. 자체 봇을 만들 때 LUIS에서 세부 정보를 반환하는 자체 개체를 만들어야 합니다. 


    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    
    namespace Microsoft.BotBuilderSamples
    {
        public static class LuisHelper
        {
            public static async Task<BookingDetails> ExecuteLuisQuery(IConfiguration configuration, ILogger logger, ITurnContext turnContext, CancellationToken cancellationToken)
            {
                var bookingDetails = new BookingDetails();
    
                try
                {
                    // Create the LUIS settings from configuration.
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]
                    );
    
                    var recognizer = new LuisRecognizer(luisApplication);
    
                    // The actual call to LUIS
                    var recognizerResult = await recognizer.RecognizeAsync(turnContext, cancellationToken);
    
                    var (intent, score) = recognizerResult.GetTopScoringIntent();
                    if (intent == "Book_flight")
                    {
                        // We need to get the result from the LUIS JSON which at every level returns an array.
                        bookingDetails.Destination = recognizerResult.Entities["To"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
                        bookingDetails.Origin = recognizerResult.Entities["From"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
    
                        // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                        // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                        bookingDetails.TravelDate = recognizerResult.Entities["datetime"]?.FirstOrDefault()?["timex"]?.FirstOrDefault()?.ToString().Split('T')[0];
                    }
                }
                catch (Exception e)
                {
                    logger.LogWarning($"LUIS Exception: {e.Message} Check your LUIS configuration.");
                }
    
                return bookingDetails;
            }
        }
    }
    ```

1. **BookingDetails.cs**를 열어 개체에서 LUIS 정보를 추상화하는 방법을 봅니다. 

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    namespace Microsoft.BotBuilderSamples
    {
        public class BookingDetails
        {
            public string Destination { get; set; }
    
            public string Origin { get; set; }
    
            public string TravelDate { get; set; }
        }
    }
    ```

1. **Dialogs -> BookingDialog.js**를 열어 BookingDetails 개체를 사용하여 대화 흐름을 관리하는 방법을 이해합니다. 여러 단계에 걸쳐 여행 세부 정보가 요청된 다음, 전체 예약이 확인되고 마지막으로 사용자에게 다시 반복됩니다. 

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Recognizers.Text.DataTypes.TimexExpression;
    
    namespace Microsoft.BotBuilderSamples.Dialogs
    {
        public class BookingDialog : CancelAndHelpDialog
        {
            public BookingDialog()
                : base(nameof(BookingDialog))
            {
                AddDialog(new TextPrompt(nameof(TextPrompt)));
                AddDialog(new ConfirmPrompt(nameof(ConfirmPrompt)));
                AddDialog(new DateResolverDialog());
                AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
                {
                    DestinationStepAsync,
                    OriginStepAsync,
                    TravelDateStepAsync,
                    ConfirmStepAsync,
                    FinalStepAsync,
                }));
    
                // The initial child Dialog to run.
                InitialDialogId = nameof(WaterfallDialog);
            }
    
            private async Task<DialogTurnResult> DestinationStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                if (bookingDetails.Destination == null)
                {
                    return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Where would you like to travel to?") }, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.Destination, cancellationToken);
                }
            }
    
            private async Task<DialogTurnResult> OriginStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.Destination = (string)stepContext.Result;
    
                if (bookingDetails.Origin == null)
                {
                    return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Where are you traveling from?") }, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.Origin, cancellationToken);
                }
            }
            private async Task<DialogTurnResult> TravelDateStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.Origin = (string)stepContext.Result;
    
                if (bookingDetails.TravelDate == null || IsAmbiguous(bookingDetails.TravelDate))
                {
                    return await stepContext.BeginDialogAsync(nameof(DateResolverDialog), bookingDetails.TravelDate, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.TravelDate, cancellationToken);
                }
            }
    
            private async Task<DialogTurnResult> ConfirmStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.TravelDate = (string)stepContext.Result;
    
                var msg = $"Please confirm, I have you traveling to: {bookingDetails.Destination} from: {bookingDetails.Origin} on: {bookingDetails.TravelDate}";
    
                return await stepContext.PromptAsync(nameof(ConfirmPrompt), new PromptOptions { Prompt = MessageFactory.Text(msg) }, cancellationToken);
            }
    
            private async Task<DialogTurnResult> FinalStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                if ((bool)stepContext.Result)
                {
                    var bookingDetails = (BookingDetails)stepContext.Options;
    
                    return await stepContext.EndDialogAsync(bookingDetails, cancellationToken);
                }
                else
                {
                    return await stepContext.EndDialogAsync(null, cancellationToken);
                }
            }
    
            private static bool IsAmbiguous(string timex)
            {
                var timexProperty = new TimexProperty(timex);
                return !timexProperty.Types.Contains(Constants.TimexTypes.Definite);
            }
        }
    }
    ```


## <a name="start-the-bot-code-in-visual-studio"></a>Visual Studio에서 봇 코드 시작

Visual Studio에서 봇을 시작합니다. `http://localhost:3978/`에 있는 웹앱 봇의 웹 사이트를 통해 브라우저 창이 열립니다. 홈페이지는 봇에 대한 정보를 표시합니다.

![홈페이지는 봇에 대한 정보를 표시합니다.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>봇 에뮬레이터를 사용하여 봇 테스트

1. 봇 에뮬레이터를 시작하고 **봇 열기**를 선택합니다.
1. **봇 열기** 팝업 대화 상자에서 봇 URL(예: `http://localhost:3978/api/messages`)을 입력합니다. `/api/messages` 경로는 봇의 웹 주소입니다.
1. 다운로드한 봇 코드의 루트에 있는 **appsettings.json** 파일에서 확인할 수 있는 **Microsoft 앱 ID** 및 **Microsoft 앱 암호**를 입력합니다.

    필요한 경우 새 봇 구성을 만들고 봇의 Visual Studio 프로젝트에 있는 **appsettings.json** 파일에서 `appId` 및 `appPassword`를 복사합니다. 봇 구성 파일의 이름은 봇 이름과 동일해야 합니다. 

    ```json
    {
        "name": "<bot name>",
        "description": "<bot description>",
        "services": [
            {
                "type": "endpoint",
                "appId": "<appId from appsettings.json>",
                "appPassword": "<appPassword from appsettings.json>",
                "endpoint": "http://localhost:3978/api/messages",
                "id": "<don't change this value>",
                "name": "http://localhost:3978/api/messages"
            }
        ],
        "padlock": "",
        "version": "2.0",
        "overrides": null,
        "path": "<local path to .bot file>"
    }
    ```

1. 봇 에뮬레이터에서 `Hello`를 입력하고 **웹 채팅에서 테스트**에서 받은 것과 동일한 기본 봇 응답을 받습니다.

    [![에뮬레이터의 기본 봇 응답](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>봇에게 항공권 예약 의도에 대한 질문하기

1. 봇 에뮬레이터에서 다음 발화를 입력하여 항공권을 예약합니다. 

    ```bot
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    봇 에뮬레이터가 확인을 요청합니다. 

1. **예**를 선택합니다. 봇이 해당 작업의 요약으로 응답합니다. 
1. 봇 에뮬레이터 로그에서 `Luis Trace`가 포함된 줄을 선택합니다. 여기에는 발화의 의도 및 엔터티에 대한 LUIS의 JSON 응답이 표시됩니다.

    [![에뮬레이터의 기본 봇 응답](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>다음 단계

대화형 봇을 사용하는 추가 [샘플](https://github.com/microsoft/botframework-solutions)을 참조하세요. 

> [!div class="nextstepaction"]
> [사용자 지정 주제 도메인을 사용하여 Language Understanding 앱 빌드](luis-quickstart-intents-only.md)
