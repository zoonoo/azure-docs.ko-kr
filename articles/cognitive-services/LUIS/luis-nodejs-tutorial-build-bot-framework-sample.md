---
title: 봇 - Node.js - v3
titleSuffix: Azure Cognitive Services
description: Bot Framework 3.x 및 Azure 웹앱 봇을 사용하여 LUIS 애플리케이션과 통합된 봇을 빌드합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: 8bd6a9df9518cb1b2312b525e8876629a2189bd2
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399611"
---
# <a name="luis-bot-in-nodejs-with-the-bot-framework-3x-and-the-azure-web-app-bot"></a>Bot Framework 3.x 및 Azure 웹앱 봇을 사용하는 Node.js의 LUIS 봇

Node.js를 사용하여 LUIS(Language Understanding)와 통합된 챗봇을 빌드합니다. 이 챗봇은 사전 빌드된 HomeAutomation 도메인을 사용하여 신속하게 봇 솔루션을 구현합니다. 이 봇은 Bot Framework 3.x 및 Azure 웹앱 봇을 사용하여 빌드됩니다.

A [Bot Framework 4.x LUIS 자습서를 사용 하 여](luis-nodejs-tutorial-bf-v4.md) 도 제공 됩니다.

## <a name="prerequisite"></a>필수 요소

봇을 만들기 전에 [앱 만들기](./luis-get-started-create-app.md)의 단계에 따라 봇이 사용하는 LUIS 앱을 빌드합니다.

봇은 LUIS 앱에 있는 HomeAutomation 도메인에서 의도에 응답합니다. 이러한 각 의도에 LUIS 앱은 매핑되는 의도를 제공합니다. 봇은 LUIS가 검색하는 의도를 처리하는 대화 상자를 제공합니다.

| 의도 | 예제 발화 | 봇 기능 |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | 조명을 켭니다. | `HomeAutomation.TurnOn`이 검색되면 봇이 `TurnOnDialog`를 호출합니다. 이 대화 상자에서는 IoT 서비스를 호출하여 디바이스를 켜고 디바이스가 켜졌다는 것을 사용자에게 알립니다. |
| HomeAutomation.TurnOff | 침실 조명을 끕니다. | `HomeAutomation.TurnOff`이 검색되면 봇이 `TurnOffDialog`를 호출합니다. 이 대화 상자에서는 IoT 서비스를 호출하여 디바이스를 끄고 디바이스가 꺼졌다는 것을 사용자에게 알립니다. |


## <a name="create-a-language-understanding-bot-with-bot-service"></a>Bot Service를 사용하여 Language Understanding 봇 만들기

1. [Azure Portal](https://portal.azure.com)의 메뉴 블레이드에서 새 리소스 **만들기**를 선택하고 **모두 표시**를 선택합니다.

    ![Azure Portal에서 모든 리소스 참조](./media/luis-tutorial-node-bot/bot-service-creation.png)

2. 검색 상자에서 **웹앱 봇**을 검색합니다. 

    ![웹앱 봇을 선택하여 리소스 생성 프로세스 시작](./media/luis-tutorial-node-bot/bot-service-selection.png)

3. **Bot Service** 블레이드에서 필요한 정보를 제공하고 **만들기**를 선택합니다. 이렇게 하면 Bot Service 및 LUIS 앱이 만들어지고 Azure에 배포됩니다. [음성 초기화](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)를 사용하려면 봇을 만들기 전에 [지역 요구 사항](troubleshooting.md#what-luis-regions-support-bot-framework-speech-priming)을 검토합니다. 
   * **앱 이름**을 봇 이름으로 설정합니다. 이 이름은 봇이 클라우드에 배포될 때 하위 도메인으로 사용됩니다(예: mynotesbot.azurewebsites.net). <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
   * 구독, [리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), App Service 계획 및 [위치](https://azure.microsoft.com/regions/)를 선택합니다.
   * **봇 템플릿**의 경우 다음을 선택합니다.
       * **SDK v3**
       * **Node.JS**
       * **언어 이해**
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

    ```console
    cd site\wwwroot && npm install
    ```

    설치 프로세스가 완료될 때까지 기다립니다. 첫 번째 브라우저 창으로 돌아갑니다. 

## <a name="test-in-web-chat"></a>웹 채팅에서 테스트
봇이 등록되면 **웹 채팅에서 테스트**를 선택하여 [웹 채팅] 창을 엽니다. 웹 채팅에 “hello”를 입력합니다.

  ![웹 채팅에서 봇 테스트](./media/luis-tutorial-node-bot/bot-service-web-chat.png)

봇이 다음과 같이 응답합니다. “You have reached Greeting. You said: hello”. 이는 봇이 메시지를 수신했고 봇이 만든 기본 LUIS 앱에 메시지를 전달했음을 확인합니다. 이 기본 LUIS 앱이 Greeting 의도를 검색했습니다. 다음 단계에서는 기본 LUIS 앱 대신 이전에 만든 LUIS 앱에 봇을 연결합니다.

## <a name="connect-your-luis-app-to-the-bot"></a>봇에 LUIS 앱 연결

첫 번째 브라우저 창에서 **애플리케이션 설정**을 열고 **LuisAppId** 필드를 편집하여 LUIS 앱의 애플리케이션 ID를 포함합니다.

  ![Azure에서 LUIS 앱 ID 업데이트](./media/luis-tutorial-node-bot/bot-service-app-id.png)

LUIS 앱 ID가 없는 경우에 로그인 합니다 [LUIS](luis-reference-regions.md) Azure에 로그인 하는 데 동일한 계정을 사용 하 여 웹 사이트입니다. **내 앱**을 선택합니다. 

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

Azure portal에서 선택 **테스트 웹 채팅** 봇 테스트 합니다. “조명 꺼줘” 및 “히터 켜줘” 같은 메시지를 입력하여 메시지에 추가한 의도를 호출합니다.
   ![웹 채팅에서 HomeAutomation 봇 테스트](./media/luis-tutorial-node-bot/bot-service-chat-results.png)

> [!TIP]
> 봇이 항상 올바른 의도나 엔터티를 인식하지는 않는다는 것을 알게 되면 더 많은 예제 발화를 제공하여 학습시키는 방식으로 LUIS 앱 성능을 개선합니다. 봇의 코드를 수정하지 않고 LUIS 앱을 다시 학습시킬 수 있습니다. [예제 발화 추가](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) 및 [LUIS 앱 학습 및 테스트](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-interactive-test)를 참조하세요.

## <a name="learn-more-about-bot-framework"></a>Bot Framework에 대해 자세히 알아보기
[Bot Framework](https://dev.botframework.com/)와 [3.x](https://github.com/Microsoft/BotBuilder) 및 [4.x](https://github.com/Microsoft/botbuilder-js) SDK에 대해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계

<!-- From trying the bot, you can see that the recognizer can trigger interruption of the currently active dialog. Allowing and handling interruptions is a flexible design that accounts for what users really do. Learn more about the various actions you can associate with a recognized intent.-->
LUIS 앱 도움말, Cancel 및 Greeting와 같은 다른 의도 추가할 시도할 수 있습니다. 그런 다음, 새 의도의 대화 상자를 추가하고 봇을 사용하여 의도를 테스트합니다. 

<!-- 
> [!NOTE] 
> The default LUIS app that the template created contains example utterances for Cancel, Greeting, and Help intents. In the list of apps, find the app that begins with the name specified in **App name** in the **Bot Service** blade when you created the Bot Service. -->

> [!div class="nextstepaction"]
> [의도 추가](./luis-how-to-add-intents.md)
> [음성 초기화](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)


[triggerAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#triggeraction

[matches]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#matches


[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions

