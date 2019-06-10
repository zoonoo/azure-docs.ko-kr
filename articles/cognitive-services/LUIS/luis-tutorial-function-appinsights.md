---
title: Application Insights, Node.js
titleSuffix: Azure Cognitive Services
description: Node.js를 사용하여 LUIS 애플리케이션 및 Application Insights와 통합된 봇을 빌드합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: bde1983f89cb2fcd0a6fddadc2c3379dee4310be
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399625"
---
# <a name="add-luis-results-to-application-insights-and-azure-functions"></a>Application Insights 및 Azure 함수에 LUIS 결과 추가
이 자습서에서는 [Application Insights](https://azure.microsoft.com/services/application-insights/) 원격 분석 데이터 저장소에 LUIS 요청 및 응답 정보를 추가합니다. 해당 데이터를 만든 후 Kusto 언어나 분석 집계를 Power BI를 사용 하 여 쿼리할 수 있습니다 하 고 실시간 utterance 엔터티와 의도 보고 합니다. 이 분석을 통해 LUIS 앱의 의도와 엔터티를 추가하거나 편집해야 할지 결정할 수 있습니다.

이 봇은 Bot Framework 3.x 및 Azure 웹앱 봇을 사용하여 빌드됩니다. A [Bot Framework 4.x LUIS 자습서를 사용 하 여](luis-nodejs-tutorial-bf-v4.md) 도 제공 됩니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 웹앱 봇에 Application Insights 라이브러리 추가
> * LUIS 쿼리 결과 캡처 및 Application Insights에 보내기
> * Application Insights에서 상위 의도, 점수 및 발언 쿼리

## <a name="prerequisites"></a>필수 조건

* Application Insights와 함께 **[이전 자습서](luis-nodejs-tutorial-build-bot-framework-sample.md)** 의 LUIS 웹앱 봇이 켜져 있습니다. 

> [!Tip]
> 아직 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)으로 등록할 수 있습니다.

이 자습서의 모든 코드는 [Azure-Samples GitHub 리포지토리](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/nodejs)에서 사용할 수 있으며 이 자습서와 연결된 각 줄은 `//APPINSIGHT:`를 사용하여 주석으로 처리됩니다. 

## <a name="web-app-bot-with-luis"></a>LUIS와 웹앱 봇
이 자습서에서는 다음과 같은 코드가 있거나 [다른 자습서](luis-nodejs-tutorial-build-bot-framework-sample.md)를 완료했다고 가정합니다. 

   [!code-javascript[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/nodejs/app.js "Web app bot with LUIS")]

## <a name="add-application-insights-library-to-web-app-bot"></a>웹앱 봇에 Application Insights 라이브러리 추가
현재 이 웹앱 봇에 사용되는 Application Insights 서비스는 봇에 대한 일반 상태 원격 분석 데이터를 수집합니다. 의도 및 엔터티를 확인하고 수정하는 데 필요한 LUIS 요청 및 응답 정보는 수집하지 않습니다. 

LUIS 요청 및 응답을 캡처하려면 웹앱 봇에 **[Application Insights](https://www.npmjs.com/package/applicationinsights)** NPM 패키지가 설치되어 있고 **app.js** 파일에 구성되어야 합니다. 그런 후에 의도 대화 처리기는 LUIS 요청 및 응답 정보를 Application Insights에 보내야 합니다. 

1. Azure Portal의 웹앱 봇 서비스에서 **봇 관리** 섹션 아래의 **빌드**를 선택합니다. 

    ![Azure Portal의 웹앱 봇 서비스에서 "봇 관리" 섹션 아래의 "빌드"를 선택합니다.](./media/luis-tutorial-appinsights/build.png)

2. 새 웹 브라우저 탭에서 App Service 편집기가 열립니다. 위쪽 막대에서 앱 이름을 선택하고 **Kudu 콘솔 열기**를 선택합니다. 

    ![위쪽 막대에서 앱 이름을 선택한 다음, "Kudu 콘솔 열기"를 선택합니다.](./media/luis-tutorial-appinsights/kudu-console.png)

3. 콘솔에서 다음 명령을 입력하여 Application Insights 및 Underscore 패키지를 설치합니다.

    ```console
    cd site\wwwroot && npm install applicationinsights && npm install underscore
    ```

    ![npm 명령을 사용하여 Application Insights 및 Underscore 패키지를 설치합니다.](./media/luis-tutorial-appinsights/npm-install.png)

    패키지가 설치될 때까지 기다립니다.

    ```console
    luisbot@1.0.0 D:\home\site\wwwroot
    `-- applicationinsights@1.0.1 
      +-- diagnostic-channel@0.2.0 
      +-- diagnostic-channel-publishers@0.2.1 
      `-- zone.js@0.7.6 
    
    npm WARN luisbot@1.0.0 No repository field.
    luisbot@1.0.0 D:\home\site\wwwroot
    +-- botbuilder-azure@3.0.4
    | `-- azure-storage@1.4.0
    |   `-- underscore@1.4.4 
    `-- underscore@1.8.3 
    ```

    완료되면 Kudu 콘솔 브라우저 탭이 표시됩니다.

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>LUIS 쿼리 결과 캡처 및 Application Insights에 보내기
1. App Service 편집기 브라우저 탭에서 **app.js** 파일을 엽니다.

2. 기존 `requires` 줄 아래에 다음 NPM 라이브러리를 추가합니다.

   [!code-javascript[Add NPM packages to app.js](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=12-16 "Add NPM packages to app.js")]

3. Application Insights 개체를 만들고 웹앱 봇 애플리케이션 설정 **BotDevInsightsKey**를 사용합니다. 

   [!code-javascript[Create the Application Insights object](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=68-80 "Create the Application Insights object")]

4. **appInsightsLog** 함수를 추가합니다.

   [!code-javascript[Add the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=82-109 "Add the appInsightsLog function")]

    함수의 마지막 줄은 데이터가 Application Insights에 추가되는 위치입니다. 이벤트 이름은 **LUIS-results**로, 이 웹앱 봇에서 수집한 다른 원격 분석 데이터와 분리된 고유 이름입니다. 

5. **appInsightsLog** 함수를 사용합니다. 이 함수를 모든 의도 대화 상자에 추가합니다.

   [!code-javascript[Use the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=117-118 "Use the appInsightsLog function")]

6. 웹앱 봇을 테스트하려면 **웹 채팅에서 테스트** 기능을 사용합니다. 모든 작업이 봇 응답이 아닌 Application Insights에 있으므로 차이를 느낄 수 없습니다.

## <a name="view-luis-entries-in-application-insights"></a>Application Insights에서 LUIS 항목 보기
Application Insights를 열어 LUIS 항목을 확인합니다. 

1. 포털에서 **모든 리소스**를 선택한 후 웹앱 봇 이름으로 필터링합니다. **Application Insights** 유형의 리소스를 클릭합니다. Application Insights의 아이콘은 전구입니다. 

    ! [[Azure portal에서 app insights 검색](./media/luis-tutorial-appinsights/search-for-app-insights.png)

2. 리소스가 열리면 맨 오른쪽 패널에서 돋보기의 **검색** 아이콘을 클릭합니다. 오른쪽에 새 패널이 표시됩니다. 발견된 원격 분석 데이터의 양에 따라 패널을 표시하는 데 다소 시간이 걸릴 수 있습니다. `LUIS-results`를 검색하고 키보드에서 Enter 키를 누릅니다. 이 자습서에서 추가된 LUIS 쿼리 결과로만 목록 범위가 좁혀집니다.

    ![종속성 필터링](./media/luis-tutorial-appinsights/app-insights-filter.png)

3. 맨 위 항목을 선택합니다. 새 창에는 맨 오른쪽에 있는 LUIS 쿼리에 대한 사용자 지정 데이터를 포함하여 자세한 데이터가 표시됩니다. 데이터에는 상위 의도와 해당 점수가 포함됩니다.

    ![종속성 세부 정보](./media/luis-tutorial-appinsights/app-insights-detail.png)

    완료되면 맨 오른쪽 위에 있는 **X**를 선택하여 종속성 항목 목록으로 돌아갑니다. 


> [!Tip]
> 종속성 목록을 저장하고 나중에 해당 목록으로 돌아가려면 **...자세히**를 클릭하고 **즐겨찾기 저장**을 클릭합니다.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Application Insights에서 의도, 점수 및 발화 쿼리
Application Insights로 데이터를 쿼리 하는 기능을 제공 합니다 [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) 언어 뿐만 내보내기 되도록 [Power BI](https://powerbi.microsoft.com)합니다. 

1. 필터 상자 위의 종속성 목록 맨 위에서 **분석**을 클릭합니다. 

    ![Analytics 단추](./media/luis-tutorial-appinsights/analytics-button.png)

2. 새 창이 열리며 맨 위에는 쿼리 창이 있고 그 아래에는 데이터 테이블 창이 있습니다. 이전에 데이터베이스를 사용한 경우, 이 배열이 친숙합니다. 쿼리에는 이름 `LUIS-results`로 시작하는 최근 24시간의 모든 항목이 포함됩니다. **CustomDimensions** 열에는 LUIS 쿼리 결과가 이름/값 쌍으로 포함됩니다.

    ![Analytics 쿼리 창](./media/luis-tutorial-appinsights/analytics-query-window.png)

3. 상위 의도, 점수 및 발화를 끌어오려면 쿼리 창의 마지막 줄 바로 위에 다음을 추가합니다.

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_intent_intent)
    | extend score = todouble(customDimensions.LUIS_intent_score)
    | extend utterance = tostring(customDimensions.LUIS_text)
    ```

4. 쿼리를 실행합니다. 데이터 테이블의 맨 오른쪽으로 스크롤합니다. topIntent, score 및 utterance의 새 열을 사용할 수 있습니다. topIntent 열을 클릭하여 정렬합니다.

    ![Analytics 상위 의도](./media/luis-tutorial-appinsights/app-insights-top-intent.png)


에 대 한 자세한 정보는 [Kusto 쿼리 언어](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) 또는 [Power BI로 데이터 내보내기](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)합니다. 

## <a name="next-steps"></a>다음 단계

Application Insights 데이터에 추가할 수 있는 기타 정보에는 앱 ID, 버전 ID, 마지막 모델 변경 날짜, 마지막 학습 날짜, 마지막 게시 날짜가 포함됩니다. 이러한 값은 엔드포인트 URL(앱 ID 및 버전 ID) 또는 [Authoring API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) 호출에서 검색한 다음, 웹앱 봇 설정에서 설정하고 끌어올 수 있습니다.  

둘 이상의 LUIS 앱에 대해 동일한 엔드포인트 구독을 사용하는 경우, 구독 ID 및 공유 키임을 나타내는 속성도 포함해야 합니다. 

> [!div class="nextstepaction"]
> [예제 발언에 대해 자세히 알아보기](luis-how-to-add-example-utterances.md)
