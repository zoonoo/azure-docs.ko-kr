---
title: Azure에서 Node.js용 Bot Builder SDK를 사용하여 봇과 LUIS 통합 | Microsoft Docs
description: Bot Framework를 사용하여 LUIS 응용 프로그램과 통합된 봇을 빌드합니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/06/2018
ms.author: diberry
ms.openlocfilehash: 6d6937105b11d94138b51660dc9f3c5e682e19bc
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224078"
---
# <a name="integrate-luis-with-a-bot-using-the-bot-builder-sdk-for-nodejs"></a>Node.js용 Bot Builder SDK를 사용하여 봇과 LUIS 통합

이 자습서에서는 LUIS 앱과 통합된 [Bot Framework][BotFramework]를 사용하여 봇을 빌드하는 과정을 안내합니다.

## <a name="prerequisite"></a>필수 요소

봇을 만들기 전에 [앱 만들기](./luis-get-started-create-app.md)의 단계에 따라 봇이 사용하는 LUIS 앱을 빌드합니다.

봇은 LUIS 앱에 있는 HomeAutomation 도메인에서 의도에 응답합니다. 이러한 각 의도에 LUIS 앱은 매핑되는 의도를 제공합니다. 봇은 LUIS가 검색하는 의도를 처리하는 대화 상자를 제공합니다.

| 의도 | 예제 발화 | 봇 기능 |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | 조명을 켭니다. | `HomeAutomation.TurnOn`이 검색되면 봇이 `TurnOnDialog`를 호출합니다. 이 대화 상자에서는 IoT 서비스를 호출하여 장치를 켜고 장치가 켜졌다는 것을 사용자에게 알립니다. |
| HomeAutomation.TurnOff | 침실 조명을 끕니다. | `HomeAutomation.TurnOff`이 검색되면 봇이 `TurnOffDialog`를 호출합니다. 이 대화 상자에서는 IoT 서비스를 호출하여 장치를 끄고 장치가 꺼졌다는 것을 사용자에게 알립니다. |


## <a name="create-a-language-understanding-bot-with-bot-service"></a>Bot Service를 사용하여 Language Understanding 봇 만들기

1. [Azure Portal](https://portal.azure.com)의 메뉴 블레이드에서 새 리소스 **만들기**를 선택하고 **모두 표시**를 선택합니다.

    ![새 리소스 만들기](./media/luis-tutorial-node-bot/bot-service-creation.png)

2. 검색 상자에서 **웹앱 봇**을 검색합니다. 

    ![새 리소스 만들기](./media/luis-tutorial-node-bot/bot-service-selection.png)

3. **Bot Service** 블레이드에서 필요한 정보를 제공하고 **만들기**를 선택합니다. 이렇게 하면 Bot Service 및 LUIS 앱이 만들어지고 Azure에 배포됩니다. [음성 초기화](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)를 사용하려면 봇을 만들기 전에 [지역 요구 사항](luis-resources-faq.md#what-luis-regions-support-bot-framework-speech-priming)을 검토합니다. 
    * **앱 이름**을 봇 이름으로 설정합니다. 이 이름은 봇이 클라우드에 배포될 때 하위 도메인으로 사용됩니다(예: mynotesbot.azurewebsites.net). <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
    * 구독, [리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), App Service 계획 및 [위치](https://azure.microsoft.com/regions/)를 선택합니다.
    * **봇 템플릿** 필드에 **Language Understanding(Node.js)** 템플릿을 선택합니다.
    * **LUIS 앱 위치**를 선택합니다. 이것은 앱이 생성된 작성 [지역][LUIS]입니다.
    * 법적 고지 사항의 확인 확인란을 선택합니다. 법적 고지 사항의 조건은 확인란 아래에 있습니다.

    ![Bot Service 블레이드](./media/luis-tutorial-node-bot/bot-service-setting-callout-template.png)


4. Bot Service가 배포되었는지 확인합니다.
    * 알림(Azure Portal의 위쪽 가장자리에 있는 벨 아이콘)을 선택합니다. 알림이 **배포가 시작됨**에서 **배포 성공**으로 변경됩니다.
    * 알림이 **배포 성공**으로 변경된 후 해당 알림에서 **리소스로 이동**을 선택합니다.

## <a name="try-the-default-bot"></a>기본 봇 체험해 보기

**알림**을 선택하여 봇이 배포되었는지 확인합니다. 알림이 **배포 진행 중...** 에서 **배포 성공**으로 변경됩니다. **리소스로 이동** 단추를 선택하여 봇의 리소스 블레이드를 엽니다.

<!-- this step isn't supposed to be necessary -->
## <a name="install-npm-resources"></a>NPM 리소스 설치
다음 단계를 사용하여 NPM 패키지를 설치합니다.

1. 웹앱 봇의 **봇 관리** 섹션에서 **빌드**를 선택합니다. 

2. 새로운 두 번째 브라우저 창이 열립니다. **온라인 코드 편집기 열기**를 선택합니다.

3. 위쪽 탐색 모음에서 웹앱 봇 이름 `homeautomationluisbot`을 선택합니다. 

4. 드롭다운 목록에서 **Kudu 콘솔 열기**를 선택합니다.

5. 새 브라우저 창이 열립니다. 콘솔에서 다음 명령을 입력합니다.

    ```
    cd site\wwwroot && npm install
    ```

    설치 프로세스가 완료될 때까지 기다립니다. 첫 번째 브라우저 창으로 돌아갑니다. 

## <a name="test-in-web-chat"></a>웹 채팅에서 테스트
봇이 등록되면 **웹 채팅에서 테스트**를 선택하여 [웹 채팅] 창을 엽니다. 웹 채팅에 “hello”를 입력합니다.

  ![웹 채팅에서 봇 테스트](./media/luis-tutorial-node-bot/bot-service-web-chat.png)

봇이 다음과 같이 응답합니다. “You have reached Greeting. You said: hello”. 이는 봇이 메시지를 수신했고 봇이 만든 기본 LUIS 앱에 메시지를 전달했음을 확인합니다. 이 기본 LUIS 앱이 Greeting 의도를 검색했습니다. 다음 단계에서는 기본 LUIS 앱 대신 이전에 만든 LUIS 앱에 봇을 연결합니다.

## <a name="connect-your-luis-app-to-the-bot"></a>봇에 LUIS 앱 연결

첫 번째 브라우저 창에서 **응용 프로그램 설정**을 열고 **LuisAppId** 필드를 편집하여 LUIS 앱의 응용 프로그램 ID를 포함합니다.

  ![Azure에서 LUIS 앱 ID 업데이트](./media/luis-tutorial-node-bot/bot-service-app-id.png)

LUIS 앱 ID가 없는 경우, Azure에 로그인하는 데 사용하는 것과 동일한 계정을 사용하여 [LUIS](luis-reference-regions.md) 웹 사이트에 로그인합니다. **내 앱**을 선택합니다. 

1. HomeAutomation 도메인에서 의도 및 엔터티를 포함하는 이전에 만든 LUIS 앱을 찾습니다.

2. LUIS 앱의 **설정** 페이지에서 앱 ID를 찾아서 복사합니다.

3. 앱을 학습시키지 않은 경우 오른쪽 위에서 **학습** 단추를 선택하여 앱을 학습시킵니다.

4. 앱을 게시하지 않은 경우, 위쪽 탐색 모음에서 **게시**를 선택하여 **게시** 페이지를 엽니다. 프로덕션 슬롯과 **게시** 단추를 선택합니다.

## <a name="modify-the-bot-code"></a>봇 코드 수정

계속 열려 있거나 첫 번째 브라우저 창에 있는 경우, 두 번째 브라우저 창으로 이동하고, **빌드**를 선택한 다음, **온라인 코드 편집기 열기**를 선택합니다.

   ![온라인 코드 편집기 열기](./media/luis-tutorial-node-bot/bot-service-build.png)

코드 편집기에서 `app.js`를 엽니다. 다음 코드를 포함합니다.

```javascript
/*-----------------------------------------------------------------------------
A simple Language Understanding (LUIS) bot for the Microsoft Bot Framework. 
-----------------------------------------------------------------------------*/

var restify = require('restify');
var builder = require('botbuilder');
var botbuilder_azure = require("botbuilder-azure");

// Setup Restify Server
var server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
   console.log('%s listening to %s', server.name, server.url); 
});
  
// Create chat connector for communicating with the Bot Framework Service
var connector = new builder.ChatConnector({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword,
    openIdMetadata: process.env.BotOpenIdMetadata 
});

// Listen for messages from users 
server.post('/api/messages', connector.listen());

/*----------------------------------------------------------------------------------------
* Bot Storage: This is a great spot to register the private state storage for your bot. 
* We provide adapters for Azure Table, CosmosDb, SQL Azure, or you can implement your own!
* For samples and documentation, see: https://github.com/Microsoft/BotBuilder-Azure
* ---------------------------------------------------------------------------------------- */

var tableName = 'botdata';
var azureTableClient = new botbuilder_azure.AzureTableClient(tableName, process.env['AzureWebJobsStorage']);
var tableStorage = new botbuilder_azure.AzureBotStorage({ gzipData: false }, azureTableClient);

// Create your bot with a function to receive messages from the user
// This default message handler is invoked if the user's utterance doesn't
// match any intents handled by other dialogs.
var bot = new builder.UniversalBot(connector, function (session, args) {
    session.send('You reached the default message handler. You said \'%s\'.', session.message.text);
});

bot.set('storage', tableStorage);

// Make sure you add code to validate these fields
var luisAppId = process.env.LuisAppId;
var luisAPIKey = process.env.LuisAPIKey;
var luisAPIHostName = process.env.LuisAPIHostName || 'westus.api.cognitive.microsoft.com';

const LuisModelUrl = 'https://' + luisAPIHostName + '/luis/v2.0/apps/' + luisAppId + '?subscription-key=' + luisAPIKey;

// Create a recognizer that gets intents from LUIS, and add it to the bot
var recognizer = new builder.LuisRecognizer(LuisModelUrl);
bot.recognizer(recognizer);

// Add a dialog for each intent that the LUIS app recognizes.
// See https://docs.microsoft.com/bot-framework/nodejs/bot-builder-nodejs-recognize-intent-luis 
bot.dialog('GreetingDialog',
    (session) => {
        session.send('You reached the Greeting intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Greeting'
})

bot.dialog('HelpDialog',
    (session) => {
        session.send('You reached the Help intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Help'
})

bot.dialog('CancelDialog',
    (session) => {
        session.send('You reached the Cancel intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Cancel'
})
```

app.js의 기존 의도는 무시됩니다. 그대로 유지해도 됩니다. 

## <a name="add-a-dialog-that-matches-homeautomationturnon"></a>HomeAutomation.TurnOn과 일치하는 대화 상자를 추가합니다.

다음 코드를 복사하고 `app.js`에 추가합니다.

```javascript
bot.dialog('TurnOn',
    (session) => {
        session.send('You reached the TurnOn intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOn'
})
```

대화 상자에 연결된 [triggerAction][triggerAction]의 [matches][matches] 옵션은 의도의 이름을 지정합니다. 인식기는 봇이 사용자로부터 발화를 수신할 때마다 실행됩니다. 검색된 최고 점수 의도가 대화 상자에 바인딩된 `triggerAction`과 일치하면 봇이 해당 대화 상자를 호출합니다.

## <a name="add-a-dialog-that-matches-homeautomationturnoff"></a>HomeAutomation.TurnOff와 일치하는 대화 상자를 추가합니다.

다음 코드를 복사하고 `app.js`에 추가합니다.

```javascript
bot.dialog('TurnOff',
    (session) => {
        session.send('You reached the TurnOff intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOff'
})
```
## <a name="test-the-bot"></a>봇 테스트

Azure Portal에서 **웹 채팅에서 테스트**를 선택하여 봇을 테스트합니다. “조명 꺼줘” 및 “히터 켜줘” 같은 메시지를 입력하여 메시지에 추가한 의도를 호출합니다.
   ![웹 채팅에서 HomeAutomation 봇 테스트](./media/luis-tutorial-node-bot/bot-service-chat-results.png)

> [!TIP]
> 봇이 항상 올바른 의도나 엔터티를 인식하지는 않는다는 것을 알게 되면 더 많은 예제 발화를 제공하여 학습시키는 방식으로 LUIS 앱 성능을 개선합니다. 봇의 코드를 수정하지 않고 LUIS 앱을 다시 학습시킬 수 있습니다. [예제 발화 추가](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) 및 [LUIS 앱 학습 및 테스트](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-interactive-test)를 참조하세요.

## <a name="learn-more-about-bot-framework"></a>Bot Framework에 대해 자세히 알아보기
[Bot Framework](https://dev.botframework.com/)와 [3.x](https://github.com/Microsoft/BotBuilder) 및 [4.x](https://github.com/Microsoft/botbuilder-js) SDK에 대해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계

<!-- From trying the bot, you can see that the recognizer can trigger interruption of the currently active dialog. Allowing and handling interruptions is a flexible design that accounts for what users really do. Learn more about the various actions you can associate with a recognized intent.--> Help, Cancel 및 Greeting 같은 다른 의도를 LUIS 앱에 추가해 볼 수 있습니다. 그런 다음, 새 의도의 대화 상자를 추가하고 봇을 사용하여 의도를 테스트합니다. 

<!-- 
> [!NOTE] 
> The default LUIS app that the template created contains example utterances for Cancel, Greeting, and Help intents. In the list of apps, find the app that begins with the name specified in **App name** in the **Bot Service** blade when you created the Bot Service. -->

> [!div class="nextstepaction"]
> [의도 추가](./luis-how-to-add-intents.md)
> [음성 초기화](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)


[intentDialog]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html

[intentDialog_matches]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html#matches 

[NotesSample]: https://github.com/Microsoft/BotFramework-Samples/tree/master/docs-samples/Node/basics-naturalLanguage

[triggerAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#triggeraction

[confirmPrompt]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#confirmprompt

[waterfall]: bot-builder-nodejs-dialog-manage-conversation-flow.md#manage-conversation-flow-with-a-waterfall

[session_userData]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session.html#userdata

[EntityRecognizer_findEntity]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.entityrecognizer.html#findentity

[matches]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#matches

[LUISAzureDocs]: https://docs.microsoft.com/azure/cognitive-services/LUIS/Home

[Dialog]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html

[IntentRecognizerSetOptions]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.iintentrecognizersetoptions.html

[LuisRecognizer]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.luisrecognizer

[LUISConcepts]: https://docs.botframework.com/node/builder/guides/understanding-natural-language/

[DisambiguationSample]: https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/feature-onDisambiguateRoute

[IDisambiguateRouteHandler]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.idisambiguateroutehandler.html

[RegExpRecognizer]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.regexprecognizer.html

[AlarmBot]: https://github.com/Microsoft/BotBuilder/blob/master/Node/examples/basics-naturalLanguage/app.js

[LUISBotSample]: https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/intelligence-LUIS

[UniversalBot]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.universalbot.html


<!-- Old Links -->
[Github-BotFramework-Emulator-Download]: https://aka.ms/bot-framework-emulator
[Github-LUIS-Samples]: https://github.com/Microsoft/LUIS-Samples
[Github-LUIS-Samples-node-hotel-bot]: https://github.com/Microsoft/LUIS-Samples/tree/master/bot-integration-samples/hotel-finder/nodejs
[NodeJs]: https://nodejs.org/
[BFPortal]: https://dev.botframework.com/
[RegisterInstructions]: https://docs.microsoft.com/bot-framework/portal-register-bot
[BotFramework]: https://docs.microsoft.com/bot-framework/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions

