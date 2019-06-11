---
title: 봇 - C# - v3
titleSuffix: Language Understanding - Azure Cognitive Services
description: C#을 사용하여 LUIS(Language Understanding)와 통합된 챗봇을 빌드합니다. 이 챗봇은 사전 빌드된 HomeAutomation 도메인을 사용하여 신속하게 봇 솔루션을 구현합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: 9d8f3461e94f04e8e82658ede5e60d53350056c5
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399648"
---
# <a name="luis-bot-in-c-with-the-bot-framework-3x-and-the-azure-web-app-bot"></a>Bot Framework 3.x 및 Azure 웹앱 봇을 사용하는 C#의 LUIS 봇

C#을 사용하여 LUIS(Language Understanding)와 통합된 챗봇을 빌드합니다. 이 챗봇은 사전 빌드된 HomeAutomation 도메인을 사용하여 신속하게 봇 솔루션을 구현합니다. 이 봇은 Bot Framework 3.x 및 Azure 웹앱 봇을 사용하여 빌드됩니다.

A [Bot Framework 4.x LUIS 자습서를 사용 하 여](luis-csharp-tutorial-bf-v4.md) 도 제공 됩니다.

## <a name="prerequisite"></a>필수 요소

* [HomeAutomation LUIS 앱](luis-get-started-create-app.md). 이 LUIS 앱의 의도는 봇의 대화 상자 처리기에 매핑됩니다. 

## <a name="luis-homeautomation-intents"></a>LUIS HomeAutomation 의도

| 의도 | 예제 발화 | 봇 기능 |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | 조명을 켭니다. | LUIS 의도 `HomeAutomation.TurnOn`이 검색되면 봇이 `OnIntent` 대화 상자 처리기를 호출합니다. 이 대화 상자에서는 IoT 서비스를 호출하여 디바이스를 켜고 디바이스가 켜졌다는 것을 사용자에게 알립니다. |
| HomeAutomation.TurnOff | 침실 조명을 끕니다. | LUIS 의도 `HomeAutomation.TurnOff`이 검색되면 봇이 `OffIntent` 대화 상자 처리기를 호출합니다. 이 대화 상자에서는 IoT 서비스를 호출하여 디바이스를 끄고 디바이스가 꺼졌다는 것을 사용자에게 알립니다. |

## <a name="create-a-language-understanding-bot-with-bot-service"></a>Bot Service를 사용하여 Language Understanding 봇 만들기

1. [Azure Portal](https://portal.azure.com)의 왼쪽 위 메뉴에서 **새 리소스 만들기**를 선택합니다.

    ![Azure Portal에서 새 리소스 만들기](./media/luis-tutorial-cscharp-web-bot/bot-service-creation.png)

2. 검색 상자에서 **웹앱 봇**을 검색합니다. 

    ![웹앱 봇을 리소스 종류로 선택](./media/luis-tutorial-cscharp-web-bot/bot-service-selection.png)

3. [웹앱 봇] 창에서 **만들기**를 클릭합니다.

4. **Bot Service**에서 필수 정보를 제공하고 **만들기**를 클릭합니다. 이렇게 하면 Bot Service 및 LUIS 앱이 만들어지고 Azure에 배포됩니다. [음성 초기화](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)를 사용하려면 봇을 만들기 전에 [지역 요구 사항](troubleshooting.md#what-luis-regions-support-bot-framework-speech-priming)을 검토합니다. 
   * **앱 이름**을 봇 이름으로 설정합니다. 이 이름은 봇이 클라우드에 배포될 때 하위 도메인으로 사용됩니다(예: mynotesbot.azurewebsites.net). <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
   * 구독, [리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), App Service 계획 및 [위치](https://azure.microsoft.com/regions/)를 선택합니다.
   * **봇 템플릿**의 경우 다음을 선택합니다.
       * **SDK v3**
       * **C#**
       * **언어 이해**
   * **LUIS 앱 위치**를 선택합니다. 이것은 앱이 생성된 작성 [지역](luis-reference-regions.md)입니다.
   * 법적 고지 사항의 확인 확인란을 선택합니다. 법적 고지 사항의 조건은 확인란 아래에 있습니다.

     ![Bot 서비스](./media/luis-tutorial-cscharp-web-bot/bot-service-setting-callout-template.png)


5. Bot Service가 배포되었는지 확인합니다.
    * 알림(Azure Portal의 위쪽 가장자리에 있는 벨 아이콘)을 클릭합니다. 알림이 **배포가 시작됨**에서 **배포 성공**으로 변경됩니다.
    * 알림이 **배포 성공**으로 변경된 후 해당 알림에서 **리소스로 이동**을 클릭합니다.

> [!Note]
> 이 웹앱 봇 만들기 프로세스에서도 새 LUIS 앱이 생성되었습니다. 또한 학습 및 게시되었습니다. 

## <a name="try-the-default-bot"></a>기본 봇 체험해 보기

**알림** 확인란을 선택하여 봇이 배포되었는지 확인합니다. 알림이 **배포 진행 중...** 에서 **배포 성공**으로 변경됩니다. **리소스로 이동** 단추를 클릭하여 봇의 리소스를 엽니다.

봇이 배포되면 **웹 채팅에서 테스트**를 클릭하여 [웹 채팅] 창을 엽니다. 웹 채팅에 “hello”를 입력합니다.

  ![웹 채팅에서 봇 테스트](./media/luis-tutorial-cscharp-web-bot/bot-service-web-chat.png)

봇이 다음과 같이 응답합니다. “You have reached Greeting. You said: hello”.  이 응답은 봇이 메시지를 수신했고 봇이 만든 기본 LUIS 앱에 메시지를 전달했음을 확인합니다. 이 기본 LUIS 앱이 Greeting 의도를 검색했습니다. 다음 단계에서는 기본 LUIS 앱 대신 이전에 만든 LUIS 앱에 봇을 연결합니다.

## <a name="connect-your-luis-app-to-the-bot"></a>봇에 LUIS 앱 연결

**애플리케이션 설정**을 열고 **LuisAppId** 필드를 편집하여 LUIS 앱의 애플리케이션 ID를 포함합니다. HomeAutomation LUIS 앱을 미국 서부 이외의 지역에서 만든 경우 **LuisAPIHostName**도 변경해야 합니다. **LuisAPIKey**가 작성 키로 설정되어 있습니다. 이 작성 키는 트래픽이 체험 계층 할당량을 초과하면 끝점 키로 변경됩니다. 

  ![Azure에서 LUIS 앱 ID 업데이트](./media/luis-tutorial-cscharp-web-bot/bot-service-app-settings.png)

> [!Note]
> [홈 자동화 앱](luis-get-started-create-app.md)의 LUIS 앱 ID가 없는 경우, Azure에 로그인하는 데 사용하는 것과 동일한 계정을 사용하여 [LUIS](luis-reference-regions.md) 웹 사이트에 로그인합니다. 
> 1. **내 앱**을 클릭합니다. 
> 2. HomeAutomation 도메인에서 의도 및 엔터티를 포함하는 이전에 만든 LUIS 앱을 찾습니다.
> 3. LUIS 앱의 **설정** 페이지에서 앱 ID를 찾아서 복사합니다. LUIS 앱이 [학습](luis-interactive-test.md) 및 [게시](luis-how-to-publish-app.md)되었는지 확인합니다. 
> 
> [!WARNING]
> 앱 ID 또는 LUIS 키를 삭제 하면 봇 작동이 중지 됩니다.

## <a name="modify-the-bot-code"></a>봇 코드 수정

1. **빌드**를 클릭한 다음, **온라인 코드 편집기 열기**를 클릭합니다.

   ![온라인 코드 편집기 열기](./media/luis-tutorial-cscharp-web-bot/bot-service-build.png)

2. `build.cmd`를 마우스 오른쪽 단추로 클릭하고 **콘솔에서 실행**을 선택하여 앱을 빌드합니다. 서비스가 자동으로 완료되는 몇 가지 빌드 단계가 있습니다. “성공적으로 완료됨”으로 완료되면 빌드가 완료됩니다.

3. 코드 편집기에서 `/Dialogs/BasicLuisDialog.cs`를 엽니다. 다음 코드를 포함합니다.

   [!code-csharp[Default BasicLuisDialog.cs](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/Default_BasicLuisDialog.cs "Default BasicLuisDialog.cs")]

## <a name="change-code-to-homeautomation-intents"></a>코드를 HomeAutomation 의도로 변경


1. **Greeting**, **Cancel** 및 **Help**의 세 가지 의도 특성 및 메서드를 제거합니다. 이러한 의도는 HomeAutomation 미리 빌드된 도메인에서 사용되지 않습니다. **None** 의도 특성 및 메서드를 유지해야 합니다. 

2. 다른 종속성과 함께 파일의 맨 위에 종속성을 추가합니다.

   [!code-csharp[Dependencies](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=4-5&dedent=8 "dependencies")]

3. `BasicLuisDialog` 클래스의 맨 위에 있는 문자열을 관리하기 위한 상수를 추가합니다.

   [!code-csharp[Add Intent and Entity Constants](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=23-32&dedent=8 "Add Intent and Entity Constants")]

4. `BasicLuisDialog` 클래스 내부에 `HomeAutomation.TurnOn` 및 `HomeAutomation.TurnOff`의 새 의도 코드를 추가합니다.

   [!code-csharp[Add Intents](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=61-71&dedent=8 "Add Intents")]

5. LUIS를 통해 `BasicLuisDialog` 클래스 내부에서 찾은 엔터티를 가져오는 코드를 추가합니다.

   [!code-csharp[Collect entities](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=34-53&dedent=8 "Collect entities")]

6. `BasicLuisDialog` 클래스에서 **ShowLuisResult** 메서드를 변경하여 점수를 반올림하고, 엔터티를 수집하고, 챗봇에 응답 메시지를 표시합니다.

   [!code-csharp[Display message in chatbot](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=73-83&dedent=8 "Display message in chatbot")]

## <a name="build-the-bot"></a>봇 빌드
코드 편집기에서 `build.cmd`를 마우스 오른쪽 단추로 클릭하고 **콘솔에서 실행**을 선택합니다.

![웹 봇 빌드](./media/luis-tutorial-cscharp-web-bot/bot-service-build-run-from-console.png)

코드 보기는 빌드의 진행 상황 및 결과를 표시하는 터미널 창으로 바뀝니다.

![웹 봇 성공 빌드](./media/luis-tutorial-cscharp-web-bot/bot-service-build-success.png)

> [!TIP]
> 봇을 빌드하는 대체 방법은 맨 위 파란색 막대에서 봇 이름을 선택하고 **Kudu 콘솔 열기**를 선택하는 것입니다. 콘솔이 **D:\home**으로 열립니다. 
> 
> `cd site\wwwroot`를 입력하여 디렉터리를 **D:\home\site\wwwroot**로 변경합니다.
>
> `build.cmd`를 입력하여 빌드 스크립트를 실행합니다.

## <a name="test-the-bot"></a>봇 테스트

Azure Portal에서 **웹 채팅에서 테스트**를 클릭하여 봇을 테스트합니다. “조명 꺼줘” 및 “히터 켜줘” 같은 메시지를 입력하여 메시지에 추가한 의도를 호출합니다.

   ![웹 채팅에서 HomeAutomation 봇 테스트](./media/luis-tutorial-cscharp-web-bot/bot-service-chat-results.png)

> [!TIP]
> 봇의 코드를 수정하지 않고 LUIS 앱을 다시 학습시킬 수 있습니다. [예제 발화 추가](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) 및 [LUIS 앱 학습 및 테스트](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-interactive-test)를 참조하세요. 

## <a name="download-the-bot-to-debug"></a>디버그할 봇 다운로드
봇이 작동하지 않으면 프로젝트를 로컬 머신에 다운로드하고 계속 [디버그](https://docs.microsoft.com/bot-framework/bot-service-debug-bot)합니다. 

## <a name="learn-more-about-bot-framework"></a>Bot Framework에 대해 자세히 알아보기
[Bot Framework](https://dev.botframework.com/)와 [3.x](https://github.com/Microsoft/BotBuilder) 및 [4.x](https://github.com/Microsoft/botbuilder-dotnet) SDK에 대해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계

**Help**, **Cancel** 및 **Greeting** 의도를 처리하기 위한 LUIS 의도 및 Bot Service 대화 상자를 추가합니다. 웹앱 봇을 학습시키고 게시 및 [빌드](#build-the-bot)해야 합니다. LUIS 및 봇에 둘 다 동일한 의도가 있어야 합니다.

대화형 봇을 사용하는 추가 [샘플](https://github.com/Microsoft/AI)을 참조하세요. 

> [!div class="nextstepaction"]
> [의도 추가](./luis-how-to-add-intents.md)
> [음성 초기화](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)

<!-- tested on Win10 -->
