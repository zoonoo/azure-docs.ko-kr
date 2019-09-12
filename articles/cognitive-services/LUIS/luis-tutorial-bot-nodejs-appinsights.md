---
title: '자습서: Application Insights, Node.js - LUIS'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 Application Insights 원격 분석 데이터 스토리지에 봇 및 Language Understanding 정보가 추가됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: f488cfaf1e55edd8ae0d2bfd177553deb2677f8b
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70387918"
---
# <a name="tutorial-add-luis-results-to-application-insights-from-a-bot-in-nodejs"></a>자습서: Node.js의 봇에서 Application Insights에 LUIS 결과 추가
이 자습서에서는 [Application Insights](https://azure.microsoft.com/services/application-insights/) 원격 분석 데이터 스토리지에 봇 및 Language Understanding 정보가 추가됩니다. 해당 데이터가 있으면 Kusto 언어 또는 Power BI로 데이터를 쿼리하여 발화의 의도 및 엔터티를 실시간으로 분석, 집계 및 보고할 수 있습니다. 이 분석을 통해 LUIS 앱의 의도와 엔터티를 추가하거나 편집해야 할지 결정할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Application Insights에서 봇 및 Language Understanding 데이터 캡처
> * Language Understanding 데이터에 대한 Application Insights 쿼리

## <a name="prerequisites"></a>필수 조건

* Application Insights를 사용하여 만든 Azure Bot Service 봇
* 이전 봇 **[자습서](luis-nodejs-tutorial-bf-v4.md)** 에서 다운로드한 봇 코드 
* [봇 에뮬레이터](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

이 자습서의 모든 코드는 [Azure 샘플 Language Understanding GitHub 리포지토리](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-nodejs-bot-johnsmith-src-telemetry)에서 사용할 수 있습니다. 

## <a name="add-application-insights-to-web-app-bot-project"></a>웹앱 봇 프로젝트에 Application Insights 추가
현재 이 웹앱 봇에 사용되는 Application Insights 서비스는 봇에 대한 일반 상태 원격 분석 데이터를 수집합니다. LUIS 정보는 수집하지 않습니다. 

LUIS 정보를 캡처하려면 웹앱 봇에 **[Application Insights](https://www.npmjs.com/package/applicationinsights)** NPM 패키지가 설치 및 구성되어야 합니다.  

1. VSCode 통합 터미널에서 봇 프로젝트의 루트에 표시된 명령을 사용하여 다음 NPM 패키지를 추가합니다. 

    ```console
    npm install applicationinsights && npm install underscore
    ```
    
    **underscore** 패키지를 사용하여 Application Insights에서 쉽게 확인하고 사용할 수 있도록 LUIS JSON 구조를 평면화할 수 있습니다.
    


## <a name="capture-and-send-luis-query-results-to-application-insights"></a>LUIS 쿼리 결과 캡처 및 Application Insights에 보내기

1. VSCode에서 새 파일 **appInsightsLog.js**를 만들고 다음 코드를 추가합니다.

    ```javascript
    const appInsights = require('applicationinsights');
    const _ = require("underscore");
    
    // Log LUIS results to Application Insights
    // must flatten as name/value pairs
    var appInsightsLog = (botContext,luisResponse) => {

        appInsights.setup(process.env.MicrosoftApplicationInsightsInstrumentationKey).start();
        const appInsightsClient = appInsights.defaultClient;

        // put bot context and LUIS results into single object
        var data = Object.assign({}, {'botContext': botContext._activity}, {'luisResponse': luisResponse});
    
        // Flatten data into name/value pairs
        flatten = (x, result, prefix) => {
            if(_.isObject(x)) {
                _.each(x, (v, k) => {
                    flatten(v, result, prefix ? prefix + '_' + k : k)
                })
            } else {
                result["LUIS_" + prefix] = x
            }
            return result;
        }
    
        // call fn to flatten data
        var flattenedData = flatten(data, {});
    
        // ApplicationInsights Trace 
        console.log(JSON.stringify(flattenedData));
    
        // send data to Application Insights
        appInsightsClient.trackTrace({message: "LUIS", severity: appInsights.Contracts.SeverityLevel.Information, properties: flattenedData});
    }
    
    module.exports.appInsightsLog = appInsightsLog;
    ```

    이 파일은 봇 컨텍스트와 luis 응답을 가져와 두 개체를 모두 평면화하고 Application Insights의 **Trace** 이벤트에 삽입합니다. 이벤트의 이름은 **LUIS**입니다. 

1. **dialogs** 폴더를 연 다음 **luisHelper.js** 파일을 엽니다. 새 **appInsightsLog.js** 파일을 필수 파일로 포함하고 봇 컨텍스트 및 LUIS 응답을 캡처합니다. 이 파일의 전체 코드는 다음과 같습니다. 

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    const { appInsightsLog } = require('../appInsightsLog');
    
    class LuisHelper {
        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {*} logger
         * @param {TurnContext} context
         */
        static async executeLuisQuery(logger, context) {
            const bookingDetails = {};
    
            try {
                const recognizer = new LuisRecognizer({
                    applicationId: process.env.LuisAppId,
                    endpointKey: process.env.LuisAPIKey,
                    endpoint: `https://${ process.env.LuisAPIHostName }`
                }, {}, true);
    
                const recognizerResult = await recognizer.recognize(context);
    
                // APPINSIGHT: Log results to Application Insights
                appInsightsLog(context,recognizerResult);
    
    
                const intent = LuisRecognizer.topIntent(recognizerResult);
    
                bookingDetails.intent = intent;
    
                if (intent === 'Book_flight') {
                    // We need to get the result from the LUIS JSON which at every level returns an array
    
                    bookingDetails.destination = LuisHelper.parseCompositeEntity(recognizerResult, 'To', 'Airport');
                    bookingDetails.origin = LuisHelper.parseCompositeEntity(recognizerResult, 'From', 'Airport');
    
                    // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                    // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                    bookingDetails.travelDate = LuisHelper.parseDatetimeEntity(recognizerResult);
                }
            } catch (err) {
                logger.warn(`LUIS Exception: ${ err } Check your LUIS configuration`);
            }
            return bookingDetails;
        }
    
        static parseCompositeEntity(result, compositeName, entityName) {
            const compositeEntity = result.entities[compositeName];
            if (!compositeEntity || !compositeEntity[0]) return undefined;
    
            const entity = compositeEntity[0][entityName];
            if (!entity || !entity[0]) return undefined;
    
            const entityValue = entity[0][0];
            return entityValue;
        }
    
        static parseDatetimeEntity(result) {
            const datetimeEntity = result.entities['datetime'];
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;
    
            const timex = datetimeEntity[0]['timex'];
            if (!timex || !timex[0]) return undefined;
    
            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }
    
    module.exports.LuisHelper = LuisHelper;
    ```

## <a name="add-application-insights-instrumentation-key"></a>Application Insights 계측 키 추가 

Application Insights에 데이터를 추가하려면 계측 키가 필요합니다.

1. 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동하여 봇의 **Application Insights** 리소스를 찾습니다. 대부분의 이름이 봇의 이름이고 이름 끝에 `luis-nodejs-bot-johnsmithxqowom` 같은 임의의 문자가 있습니다. 
1. Application Insights 리소스의 **개요** 페이지에서 **계측 키**를 복사합니다.
1. VSCode에서 봇 프로젝트의 루트에 있는 **.env** 파일을 엽니다. 이 파일은 모든 환경 변수를 포함합니다.  
1. 계측 키 값을 가진 새 변수 `MicrosoftApplicationInsightsInstrumentationKey`를 추가합니다. 값을 따옴표로 묶지 않습니다. 

## <a name="start-the-bot"></a>봇 시작

1. VSCode 통합 터미널에서 봇을 시작합니다.
    
    ```console
    npm start
    ```

1. 봇 에뮬레이터를 시작하고 봇을 엽니다. 이 [단계](luis-nodejs-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot)는 이전 자습서에 나와 있습니다.

1. 봇에 질문을 합니다. 이 [단계](luis-nodejs-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent)는 이전 자습서에 나와 있습니다.

## <a name="view-luis-entries-in-application-insights"></a>Application Insights에서 LUIS 항목 보기

Application Insights를 열어 LUIS 항목을 확인합니다. 데이터가 Application Insights에 나타나는 데 몇 분 정도 걸릴 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 봇의 Application Insights 리소스를 엽니다. 
1. 리소스가 열리면 **검색**을 선택하고 이벤트 유형이 **Trace**인 최근 **30분** 동안의 모든 데이터를 검색합니다. 이름이 **LUIS**인 추적을 선택합니다. 
1. 봇 및 LUIS 정보는 **사용자 지정 속성**에서 사용할 수 있습니다. 

    ![Application Insights에 저장된 LUIS 사용자 지정 속성을 검토합니다.](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-nodejs.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Application Insights에서 의도, 점수 및 발화 쿼리
Application Insights를 사용하면 [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use) 언어로 데이터를 쿼리하고 [Power BI](https://powerbi.microsoft.com)로 데이터를 내보낼 수 있습니다. 

1. **Log(Analytics)** 를 선택합니다. 새 창이 열리며 맨 위에는 쿼리 창이 있고 그 아래에는 데이터 테이블 창이 있습니다. 이전에 데이터베이스를 사용한 경우, 이 배열이 친숙합니다. 쿼리는 이전에 필터링된 데이터를 나타냅니다. **CustomDimensions** 열에 봇 및 LUIS 정보가 있습니다.
1. 상위 의도, 점수 및 발화를 끌어오려면 쿼리 창의 마지막 줄(`|top...`줄) 바로 위에 다음을 추가합니다.

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_intent)
    | extend score = todouble(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_score)
    | extend utterance = tostring(customDimensions.LUIS_luisResponse_text)
    ```

1. 쿼리를 실행합니다. topIntent, score 및 utterance의 새 열을 사용할 수 있습니다. topIntent 열을 선택하여 정렬합니다.

[Kusto 쿼리 언어](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) 또는 [Power BI로 데이터 내보내기](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)에 대해 자세히 알아보세요. 

## <a name="next-steps"></a>다음 단계

Application Insights 데이터에 추가할 수 있는 기타 정보에는 앱 ID, 버전 ID, 마지막 모델 변경 날짜, 마지막 학습 날짜, 마지막 게시 날짜가 포함됩니다. 이러한 값은 엔드포인트 URL(앱 ID 및 버전 ID) 또는 Authoring API 호출에서 검색한 다음, 웹앱 봇 설정에서 설정하고 끌어올 수 있습니다.  

둘 이상의 LUIS 앱에 대해 동일한 엔드포인트 구독을 사용하는 경우, 구독 ID 및 공유 키임을 나타내는 속성도 포함해야 합니다. 

> [!div class="nextstepaction"]
> [예제 발언에 대해 자세히 알아보기](luis-how-to-add-example-utterances.md)
