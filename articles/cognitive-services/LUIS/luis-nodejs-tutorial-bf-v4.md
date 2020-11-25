---
title: '자습서: Language Understanding 봇 Node.js v4'
description: Node.js를 사용하여 이 자습서에서 LUIS(Language Understanding)와 통합된 챗봇을 빌드합니다. 이 챗봇은 인적 자원 앱을 사용하여 봇 솔루션을 빠르게 구현합니다. 봇은 Bot Framework 버전 4.x 및 Azure 웹앱 봇을 사용하여 빌드되었습니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: d070a03a81f70cdbf2c721cc67a3bc40c5d731fc
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018738"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>자습서: Node.js에서 Language Understanding으로 구현된 웹앱 봇 사용

Node.js를 사용하여 LUIS(Language Understanding)와 통합된 챗봇을 빌드합니다. 이 봇은 Azure [웹앱 봇](/azure/bot-service/) 리소스 및 [Bot Framework 버전](https://github.com/Microsoft/botbuilder-dotnet) V4를 사용하여 빌드되었습니다.

**이 자습서에서 학습할 내용은 다음과 같습니다.**

> [!div class="checklist"]
> * 웹앱 봇 만들기. 이 프로세스는 새 LUIS 앱을 만듭니다.
> * 웹 봇 서비스에서 만든 봇 프로젝트 다운로드
> * 컴퓨터에서 로컬로 봇 및 에뮬레이터 시작
> * 봇에서 발화 결과 보기

## <a name="prerequisites"></a>필수 구성 요소

* [Bot Framework 에뮬레이터](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

## <a name="create-a-web-app-bot-resource"></a>웹앱 봇 리소스 만들기

1. [Azure Portal](https://portal.azure.com)에서 **새 리소스 만들기** 를 선택합니다.

1. 검색 상자에서 **웹앱 봇** 을 검색하여 선택합니다. **만들기** 를 선택합니다.

1. **Bot Service** 에서 필수 정보를 제공합니다.

    |설정|목적|권장되는 설정|
    |--|--|--|
    |봇 핸들|리소스 이름|`luis-nodejs-bot-` + `<your-name>`, 예: `luis-nodejs-bot-johnsmith`|
    |Subscription|봇을 만들 수 있는 구독|사용자의 기본 구독
    |Resource group|Azure 리소스의 논리 그룹|이 봇에 사용되는 모든 리소스를 저장할 새 그룹을 만들고, 그룹 이름으로 `luis-nodejs-bot-resource-group`을 지정합니다.|
    |위치|Azure 지역 - LUIS를 작성하거나 게시하는 지역과 같을 필요는 없습니다.|`westus`|
    |가격 책정 계층|서비스 요청 한도 및 청구에 사용됩니다.|`F0`은 평가판 계층입니다.
    |앱 이름|봇이 클라우드에 배포될 때 하위 도메인으로 사용됩니다(예: humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, 예: `luis-nodejs-bot-johnsmith`|
    |봇 템플릿|봇 프레임워크 설정 - 다음 표 참조|
    |LUIS 앱 위치|LUIS 리소스 지역과 동일해야 합니다.|`westus`|
    |앱 서비스 플랜/위치|제공된 기본값에서 변경하지 않습니다.|
    |Application Insights|제공된 기본값에서 변경하지 않습니다.|
    |Microsoft 앱 ID 및 암호|제공된 기본값에서 변경하지 않습니다.|

1. **봇 템플릿** 에서 다음 설정을 선택한 다음, 이러한 설정 아래에 있는 **선택** 단추를 선택합니다.

    |설정|목적|선택|
    |--|--|--|
    |SDK 언어|봇의 프로그래밍 언어|**Node.JS**|
    |봇|봇의 유형|**기본 봇**|

1. **만들기** 를 선택합니다. 이렇게 하면 봇 서비스가 만들어지고 Azure에 배포됩니다. 이 프로세스의 일환으로 `luis-nodejs-bot-XXXX`라는 LUIS 앱이 만들어집니다. 이 이름은 /Azure Bot Service 앱 이름을 기반으로 합니다.

    > [!div class="mx-imgBorder"]
    > [![웹앱 봇 만들기](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    봇 서비스가 만들어질 때까지 기다렸다가 계속 진행합니다.

1. 알림에서 `Go to resource`를 선택하여 웹앱 봇 페이지로 이동합니다.

## <a name="the-bot-has-a-language-understanding-model"></a>봇에 Language Understanding 모델이 있는 경우

또한 봇 서비스 생성 프로세스는 의도와 발화 예제가 포함된 새 LUIS 앱을 만듭니다. 봇에서 제공하는 새 LUIS 앱에 대한 의도 매핑과 관련된 의도는 다음과 같습니다.

|기본 봇 LUIS 의도|발화 예제|
|--|--|
|항공권 예약|`Travel to Paris`|
|취소|`bye`|
|GetWeather|`what's the weather like?`|
|None|앱의 도메인 외부에 있는 항목입니다.|

## <a name="test-the-bot-in-web-chat"></a>웹 채팅에서 봇 테스트

1. 새 봇의 Azure Portal에서 계속 **웹 채팅에서 테스트** 를 선택합니다.
1. **메시지 입력** 텍스트 상자에 텍스트 `Book a flight from Seattle to Berlin tomorrow`를 입력합니다. 봇은 항공편 예약 확인으로 응답합니다.

    ![Azure Portal의 스크린샷, 텍스트 `hello`를 입력합니다.](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    테스트 기능을 사용하여 봇을 빠르게 테스트할 수 있습니다. 디버깅을 포함하여 보다 자세한 테스트를 수행하려면 봇 코드를 다운로드하고 Visual Studio Code를 사용합니다.

## <a name="download-the-web-app-bot-source-code"></a>웹앱 봇 소스 코드 다운로드
웹앱 봇 코드를 개발하기 위해 코드를 다운로드하여 로컬 컴퓨터에서 사용합니다.

1. Azure Portal의 **봇 관리** 섹션에서 **빌드** 를 선택합니다.

1. **봇 소스 코드 다운로드** 를 선택합니다.

    [![기본 봇용 웹앱 봇 소스 코드 다운로드](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. **다운로드된 zip 파일에 앱 설정을 포함하시겠습니까?** 팝업 대화 상자에서 **예** 를 선택합니다. LUIS 설정이 제공됩니다.

1. 소스 코드가 압축되어 있으면 메시지에서 해당 코드를 다운로드할 수 있는 링크가 제공됩니다. 이 링크를 선택합니다.

1. Zip 파일을 로컬 컴퓨터에 저장하고 파일의 압축을 풉니다. Visual Studio Code에서 프로젝트 폴더를 엽니다.

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>코드를 검토하여 발화를 LUIS로 보내고 응답 받기

1. 사용자 발화를 LUIS 예측 엔드포인트로 보내려면 **대화 상자 -> flightBookingRecognizer.js** 파일을 엽니다. 이는 봇에 입력된 사용자 발화를 LUIS로 보내는 위치입니다. LUIS의 응답이 **executeLuisQuery** 메서드에서 반환됩니다.

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { LuisRecognizer } = require('botbuilder-ai');

    class FlightBookingRecognizer {
        constructor(config) {
            const luisIsConfigured = config && config.applicationId && config.endpointKey && config.endpoint;
            if (luisIsConfigured) {
                // Set the recognizer options depending on which endpoint version you want to use e.g v2 or v3.
                // More details can be found in https://docs.microsoft.com/en-gb/azure/cognitive-services/luis/luis-migration-api-v3
                const recognizerOptions = {
                    apiVersion: 'v3'
                };

                this.recognizer = new LuisRecognizer(config, recognizerOptions);
            }
        }

        get isConfigured() {
            return (this.recognizer !== undefined);
        }

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        getFromEntities(result) {
            let fromValue, fromAirportValue;
            if (result.entities.$instance.From) {
                fromValue = result.entities.$instance.From[0].text;
            }
            if (fromValue && result.entities.From[0].Airport) {
                fromAirportValue = result.entities.From[0].Airport[0][0];
            }

            return { from: fromValue, airport: fromAirportValue };
        }

        getToEntities(result) {
            let toValue, toAirportValue;
            if (result.entities.$instance.To) {
                toValue = result.entities.$instance.To[0].text;
            }
            if (toValue && result.entities.To[0].Airport) {
                toAirportValue = result.entities.To[0].Airport[0][0];
            }

            return { to: toValue, airport: toAirportValue };
        }

        /**
         * This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
         * TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
         */
        getTravelDate(result) {
            const datetimeEntity = result.entities.datetime;
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;

            const timex = datetimeEntity[0].timex;
            if (!timex || !timex[0]) return undefined;

            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }

    module.exports.FlightBookingRecognizer = FlightBookingRecognizer;
    ```

1. **대화 상자 -> mainDialog** 는 발화를 캡처하여 actStep 메서드의 executeLuisQuery로 보냅니다.

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { TimexProperty } = require('@microsoft/recognizers-text-data-types-timex-expression');
    const { MessageFactory, InputHints } = require('botbuilder');
    const { LuisRecognizer } = require('botbuilder-ai');
    const { ComponentDialog, DialogSet, DialogTurnStatus, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');

    const MAIN_WATERFALL_DIALOG = 'mainWaterfallDialog';

    class MainDialog extends ComponentDialog {
        constructor(luisRecognizer, bookingDialog) {
            super('MainDialog');

            if (!luisRecognizer) throw new Error('[MainDialog]: Missing parameter \'luisRecognizer\' is required');
            this.luisRecognizer = luisRecognizer;

            if (!bookingDialog) throw new Error('[MainDialog]: Missing parameter \'bookingDialog\' is required');

            // Define the main dialog and its related components.
            // This is a sample "book a flight" dialog.
            this.addDialog(new TextPrompt('TextPrompt'))
                .addDialog(bookingDialog)
                .addDialog(new WaterfallDialog(MAIN_WATERFALL_DIALOG, [
                    this.introStep.bind(this),
                    this.actStep.bind(this),
                    this.finalStep.bind(this)
                ]));

            this.initialDialogId = MAIN_WATERFALL_DIALOG;
        }

        /**
         * The run method handles the incoming activity (in the form of a TurnContext) and passes it through the dialog system.
         * If no dialog is active, it will start the default dialog.
         * @param {*} turnContext
         * @param {*} accessor
         */
        async run(turnContext, accessor) {
            const dialogSet = new DialogSet(accessor);
            dialogSet.add(this);

            const dialogContext = await dialogSet.createContext(turnContext);
            const results = await dialogContext.continueDialog();
            if (results.status === DialogTurnStatus.empty) {
                await dialogContext.beginDialog(this.id);
            }
        }

        /**
         * First step in the waterfall dialog. Prompts the user for a command.
         * Currently, this expects a booking request, like "book me a flight from Paris to Berlin on march 22"
         * Note that the sample LUIS model will only recognize Paris, Berlin, New York and London as airport cities.
         */
        async introStep(stepContext) {
            if (!this.luisRecognizer.isConfigured) {
                const messageText = 'NOTE: LUIS is not configured. To enable all capabilities, add `LuisAppId`, `LuisAPIKey` and `LuisAPIHostName` to the .env file.';
                await stepContext.context.sendActivity(messageText, null, InputHints.IgnoringInput);
                return await stepContext.next();
            }

            const messageText = stepContext.options.restartMsg ? stepContext.options.restartMsg : 'What can I help you with today?\nSay something like "Book a flight from Paris to Berlin on March 22, 2020"';
            const promptMessage = MessageFactory.text(messageText, messageText, InputHints.ExpectingInput);
            return await stepContext.prompt('TextPrompt', { prompt: promptMessage });
        }

        /**
         * Second step in the waterfall.  This will use LUIS to attempt to extract the origin, destination and travel dates.
         * Then, it hands off to the bookingDialog child dialog to collect any remaining details.
         */
        async actStep(stepContext) {
            const bookingDetails = {};

            if (!this.luisRecognizer.isConfigured) {
                // LUIS is not configured, we just run the BookingDialog path.
                return await stepContext.beginDialog('bookingDialog', bookingDetails);
            }

            // Call LUIS and gather any potential booking details. (Note the TurnContext has the response to the prompt)
            const luisResult = await this.luisRecognizer.executeLuisQuery(stepContext.context);
            switch (LuisRecognizer.topIntent(luisResult)) {
            case 'BookFlight': {
                // Extract the values for the composite entities from the LUIS result.
                const fromEntities = this.luisRecognizer.getFromEntities(luisResult);
                const toEntities = this.luisRecognizer.getToEntities(luisResult);

                // Show a warning for Origin and Destination if we can't resolve them.
                await this.showWarningForUnsupportedCities(stepContext.context, fromEntities, toEntities);

                // Initialize BookingDetails with any entities we may have found in the response.
                bookingDetails.destination = toEntities.airport;
                bookingDetails.origin = fromEntities.airport;
                bookingDetails.travelDate = this.luisRecognizer.getTravelDate(luisResult);
                console.log('LUIS extracted these booking details:', JSON.stringify(bookingDetails));

                // Run the BookingDialog passing in whatever details we have from the LUIS call, it will fill out the remainder.
                return await stepContext.beginDialog('bookingDialog', bookingDetails);
            }

            case 'GetWeather': {
                // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                const getWeatherMessageText = 'TODO: get weather flow here';
                await stepContext.context.sendActivity(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                break;
            }

            default: {
                // Catch all for unhandled intents
                const didntUnderstandMessageText = `Sorry, I didn't get that. Please try asking in a different way (intent was ${ LuisRecognizer.topIntent(luisResult) })`;
                await stepContext.context.sendActivity(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
            }
            }

            return await stepContext.next();
        }

        /**
         * Shows a warning if the requested From or To cities are recognized as entities but they are not in the Airport entity list.
         * In some cases LUIS will recognize the From and To composite entities as a valid cities but the From and To Airport values
         * will be empty if those entity values can't be mapped to a canonical item in the Airport.
         */
        async showWarningForUnsupportedCities(context, fromEntities, toEntities) {
            const unsupportedCities = [];
            if (fromEntities.from && !fromEntities.airport) {
                unsupportedCities.push(fromEntities.from);
            }

            if (toEntities.to && !toEntities.airport) {
                unsupportedCities.push(toEntities.to);
            }

            if (unsupportedCities.length) {
                const messageText = `Sorry but the following airports are not supported: ${ unsupportedCities.join(', ') }`;
                await context.sendActivity(messageText, messageText, InputHints.IgnoringInput);
            }
        }

        /**
         * This is the final step in the main waterfall dialog.
         * It wraps up the sample "book a flight" interaction with a simple confirmation.
         */
        async finalStep(stepContext) {
            // If the child dialog ("bookingDialog") was cancelled or the user failed to confirm, the Result here will be null.
            if (stepContext.result) {
                const result = stepContext.result;
                // Now we have all the booking details.

                // This is where calls to the booking AOU service or database would go.

                // If the call to the booking service was successful tell the user.
                const timeProperty = new TimexProperty(result.travelDate);
                const travelDateMsg = timeProperty.toNaturalLanguage(new Date(Date.now()));
                const msg = `I have you booked to ${ result.destination } from ${ result.origin } on ${ travelDateMsg }.`;
                await stepContext.context.sendActivity(msg, msg, InputHints.IgnoringInput);
            }

            // Restart the main dialog with a different message the second time around
            return await stepContext.replaceDialog(this.initialDialogId, { restartMsg: 'What else can I do for you?' });
        }
    }

    module.exports.MainDialog = MainDialog;
    ```

<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="start-the-bot-code"></a>봇 코드 시작

1. Windows 콘솔 또는 Linux/macOS 터미널을 엽니다.

1. 봇 코드를 사용하여 디렉터리로 변경하고 다음 명령을 입력하여 dotenv 종속성을 설치합니다.

    ```console
    npm install dotenv --save
    ```

1. 다음 명령을 입력하여 봇을 시작합니다.

    ```console
    node index.js
    ```

`http://localhost:3978/`에 있는 웹앱 봇의 웹 사이트를 통해 브라우저 창이 열립니다. 홈페이지는 봇에 대한 정보를 표시합니다.

![홈페이지는 봇에 대한 정보를 표시합니다.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-framework-emulator-to-test-the-bot"></a>Bot Framework 에뮬레이터를 사용하여 봇 테스트

봇에게 항공편 예약 의도에 대해 질문을 합니다.

1. Bot Framework 에뮬레이터를 시작하고 **봇 열기** 를 선택합니다.
1. **봇 열기** 팝업 대화 상자에서 봇 URL(예: `http://localhost:3978/api/messages`)을 입력합니다. `/api/messages` 경로는 봇의 웹 주소입니다.
1. 다운로드한 봇 코드의 루트에 있는 **.env** 파일에서 확인할 수 있는 **Microsoft 앱 ID** 및 **Microsoft 앱 암호** 를 입력합니다.

1. Bot Framework 에뮬레이터에서 `Book a flight from Seattle to Berlin tomorrow`를 입력하고 **웹 채팅에서 테스트** 에서 받은 것과 동일한 기본 봇에 대한 응답을 받습니다.

    [![스크린샷은 Bot Framework Emulator를 보여줍니다.](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. **예** 를 선택합니다. 봇이 해당 작업의 요약으로 응답합니다.
1. Bot Framework 에뮬레이터 로그에서 `<- trace LuisV3 Trace`가 포함된 줄을 선택합니다. 여기에는 발화의 의도 및 엔터티에 대한 LUIS의 JSON 응답이 표시됩니다.

    [![스크린샷은 json 코드가 포함된 오른쪽 패널과 함께 Bot Framework Emulator를 보여줍니다.](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>다음 단계

대화형 봇을 사용하는 추가 [샘플](https://github.com/microsoft/botframework-solutions)을 참조하세요.

> [!div class="nextstepaction"]
> [사용자 지정 주제 도메인을 사용하여 Language Understanding 앱 빌드](./tutorial-intents-only.md)