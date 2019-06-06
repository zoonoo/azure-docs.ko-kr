---
title: C#을 사용하는 Application Insights
titleSuffix: Azure Cognitive Services
description: C#을 사용하여 LUIS 애플리케이션 및 Application Insights와 통합된 봇을 빌드합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: 56ceb48be9d5cc9d1cdceed7505e2e3e918a7286
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399669"
---
# <a name="add-luis-results-to-application-insights-with-a-bot-in-c"></a>C#에서 봇을 사용하여 Application Insights에 LUIS 결과 추가

이 자습서에서는 [Application Insights](https://azure.microsoft.com/services/application-insights/) 원격 분석 데이터 저장소에 LUIS 응답 정보를 추가합니다. 해당 데이터를 만든 후 Kusto 언어나 분석 집계를 Power BI를 사용 하 여 쿼리할 수 있습니다 하 고 실시간 utterance 엔터티와 의도 보고 합니다. 이 분석을 통해 LUIS 앱의 의도와 엔터티를 추가하거나 편집해야 할지 결정할 수 있습니다.

이 봇은 Bot Framework 3.x 및 Azure 웹앱 봇을 사용하여 빌드됩니다. A [Bot Framework 4.x LUIS 자습서를 사용 하 여](luis-csharp-tutorial-bf-v4.md) 도 제공 됩니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 웹앱 봇에 Application Insights 추가
> * LUIS 쿼리 결과 캡처 및 Application Insights에 보내기
> * Application Insights에서 상위 의도, 점수 및 발언 쿼리

## <a name="prerequisites"></a>필수 조건

* Application Insights와 함께 **[이전 자습서](luis-csharp-tutorial-build-bot-framework-sample.md)** 의 LUIS 웹앱 봇이 켜져 있습니다.
* [Visual Studio 2017](https://www.visualstudio.com/downloads/)이 컴퓨터에 로컬로 설치되어 있습니다.

> [!Tip]
> 아직 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)으로 등록할 수 있습니다.

이 자습서의 모든 코드는 [Azure-Samples GitHub 리포지토리](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/csharp)에서 사용할 수 있으며 이 자습서와 연결된 각 줄은 `//LUIS Tutorial:`을 사용하여 주석으로 처리됩니다.

## <a name="review-luis-web-app-bot"></a>LUIS 웹앱 봇 검토

이 자습서에서는 다음과 같은 코드가 있거나 [다른 자습서](luis-csharp-tutorial-build-bot-framework-sample.md)를 완료했다고 가정합니다.

   [!code-csharp[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs "Web app bot with LUIS")]

## <a name="application-insights-in-web-app-bot"></a>웹앱 봇의 Application Insights

현재 웹앱 봇 서비스를 만드는 동안 추가된 Application Insights 서비스는 봇에 대한 일반 상태 원격 분석 데이터를 수집합니다. LUIS 응답 정보는 수집하지 않습니다. LUIS를 분석하고 개선하려면 LUIS 응답 정보가 필요합니다.  

LUIS 응답을 캡처하려면 웹앱 봇에는 프로젝트를 위해 설치 및 구성된 **[Application Insights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** 가 필요합니다.

## <a name="download-web-app-bot"></a>웹앱 봇 다운로드

[Visual Studio 2017](https://www.visualstudio.com/downloads/)을 사용하여 웹앱 봇에 대해 Application Insights를 추가하고 구성합니다. Visual Studio에서 웹앱 봇을 사용하려면 웹앱 봇 코드를 다운로드합니다.

1. Azure Portal에서 웹앱 봇에 대해 **빌드**를 선택합니다.

    ![포털에서 빌드 선택](./media/luis-tutorial-bot-csharp-appinsights/download-build-menu.png)

2. **Zip 파일 다운로드**를 선택하고 파일이 준비될 때까지 기다립니다.

    ![Zip 파일 다운로드](./media/luis-tutorial-bot-csharp-appinsights/download-link.png)

3. 팝업 창에서 **Zip 파일 다운로드**를 선택합니다. 다음 섹션에서 필요하므로 컴퓨터의 위치를 기억하세요.

    ![Zip 파일 다운로드 팝업](./media/luis-tutorial-bot-csharp-appinsights/download-popup.png)

## <a name="open-solution-in-visual-studio-2017"></a>Visual Studio 2017에서 솔루션 열기

1. 파일을 폴더로 추출합니다.

2. Visual Studio 2017을 열고 솔루션 파일 `Microsoft.Bot.Sample.LuisBot.sln`을 엽니다. 보안 경고가 표시되면 “확인”을 선택합니다.

    ![Visual Studio 2017에서 솔루션 열기](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-security-warning.png)

3. Visual Studio가 솔루션에 종속성을 추가해야 합니다. **솔루션 탐색기**에서 **참조**를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리...** 를 선택합니다.

    ![NuGet 패키지 관리](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-manage-nuget-packages.png)

4. NuGet 패키지 관리자는 설치된 패키지 목록을 표시합니다. 노란색 막대에서 **복원**을 선택합니다. 복원 프로세스가 완료될 때까지 기다립니다.

    ![NuGet 패키지 복원](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-restore-packages.png)

## <a name="add-application-insights-to-the-project"></a>프로젝트에 Application Insights 추가

Visual Studio에서 Application Insights를 설치하고 구성합니다.

1. Visual Studio 2017의 위쪽 메뉴에서 **프로젝트**를 선택한 다음, **Application Insights 원격 분석 추가...** 를 선택합니다.

2. **Application Insights 구성** 창에서 **체험하기**를 선택합니다.

    ![Application Insights 구성 시작](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configure-app-insights.png)

3. Application Insights에 앱을 등록합니다. Azure Portal 자격 증명을 입력해야 할 수 있습니다.

4. Visual Studio가 프로젝트에 Application Insights를 추가하고 진행 상태를 표시합니다.

    ![Application Insights 상태](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-adding-application-insights-to-project.png)

    프로세스가 완료되면 **Application Insights 구성**에 진행 상태가 표시됩니다.

    ![Application Insights 진행 상태](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configured-application-insights-to-project.png)

    **추적 컬렉션 사용**이 빨간색이고 사용할 수 없음을 의미합니다. 이 자습서에서는 이 기능을 사용하지 않습니다.

## <a name="build-and-resolve-errors"></a>오류 빌드 및 해결

1. **빌드** 메뉴를 선택하여 솔루션을 빌드한 다음, **솔루션 다시 빌드**를 선택합니다. 빌드가 완료될 때까지 기다립니다.

2. `CS0104` 오류와 함께 빌드가 실패하면 이를 수정해야 합니다. `Controllers` 폴더의 `MessagesController.cs file`에서 작업 유형에 커넥터 유형을 접두사로 추가하여 `Activity` 유형의 모호한 사용을 수정합니다. 이를 수행하려면 줄 22 및 36의 이름 `Activity`를 `Activity`에서 `Connector.Activity`로 변경합니다. 솔루션을 다시 빌드합니다. 다른 빌드 오류가 없어야 합니다.

    해당 파일의 전체 소스는 다음과 같습니다.

    [!code-csharp[MessagesController.cs file](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/MessagesController.cs "MessagesController.cs file")]

## <a name="publish-project-to-azure"></a>Azure에 프로젝트 게시

이제 **Application Insights** 패키지가 프로젝트에 포함되고 Azure Portal의 자격 증명에 대해 올바르게 구성되어 있습니다. 프로젝트의 변경 내용을 다시 Azure에 게시해야 합니다.

1. **솔루션 탐색기**에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 다음, **게시**를 선택합니다.

    ![포털에 프로젝트 게시](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish.png)

2. **게시** 창에서 **새 프로필 만들기**를 선택합니다.

    ![게시의 일부로 새 프로필을 만듭니다.](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-1.png)

3. **프로필 가져오기**를 선택하고 **확인**을 선택합니다.

    ![게시의 일부로 프로필을 가져옵니다.](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-2.png)

4. **게시 설정 파일 가져오기** 창에서 프로젝트 폴더로 이동하고, `PostDeployScripts` 폴더로 이동한 다음, `.PublishSettings`로 끝나는 파일을 선택하고, `Open`을 선택합니다. 이제 이 프로젝트에 대한 게시를 구성했습니다.

5. **게시** 단추를 선택하여 로컬 소스 코드를 Bot Service에 게시합니다. **출력** 창에 상태가 표시됩니다. 자습서의 나머지 부분은 Azure Portal에서 완료합니다. Visual Studio 2017을 닫습니다.

## <a name="open-three-browser-tabs"></a>세 개의 브라우저 탭 열기

Azure Portal에서 웹앱 봇을 찾아서 엽니다. 다음 단계에서는 웹앱 봇의 세 가지 보기를 사용합니다. 브라우저에서 세 개의 개별 탭을 열어 두는 것이 좋을 수 있습니다.
  
>  * 웹 채팅에서 테스트
>  * 온라인 코드 편집기 빌드/열기 -> App Service 편집기
>  * App Service 편집기/Kudu 콘솔 열기 -> 진단 콘솔

## <a name="modify-basicluisdialogcs-code"></a>BasicLuisDialog.cs 코드 수정

1. **App Service 편집기** 브라우저 탭에서 `BasicLuisDialog.cs` 파일을 엽니다.

2. 기존 `using` 줄 아래에 다음 NuGet 종속성을 추가합니다.

   [!code-csharp[Add using statement](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=11-12 "Add using statement")]

3. `LogToApplicationInsights` 함수를 추가합니다.

   [!code-csharp[Add the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=61-92 "Add the LogToApplicationInsights function")]

    Application Insights 계측 키가 이미 `BotDevInsightsKey`라는 웹앱 봇의 애플리케이션 설정에 있습니다.

    함수의 마지막 줄은 Application Insights에 데이터를 추가합니다. 추적 이름은 `LUIS`로, 이 웹앱 봇에서 수집한 다른 원격 분석 데이터와 분리된 고유 이름입니다. 또한 모든 속성에 `LUIS_`이 접두사로 지정되므로 이 자습서에서 추가하는 데이터를 웹앱 봇에서 제공하는 정보와 비교할 수 있습니다.

4. `ShowLuisResult` 함수의 맨 위에 있는 `LogToApplicationInsights` 함수를 호출합니다.

   [!code-csharp[Use the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=114-115 "Use the LogToApplicationInsights function")]

## <a name="build-web-app-bot"></a>웹앱 봇 빌드

1. 두 가지 방법 중 하나로 웹앱 봇을 빌드합니다. 첫 번째 방법은 **App Service 편집기**에서 `build.cmd`를 마우스 오른쪽 단추로 클릭한 다음, **콘솔에서 실행**을 선택합니다. 콘솔의 출력이 표시되고 `Finished successfully.`로 완료됩니다.

2. 성공적으로 완료되지 않으면 콘솔을 열고 스크립트로 이동하고 나서 다음 단계를 사용하여 스크립트를 실행해야 합니다. **App Service 편집기**의 위쪽 파란색 막대에서 봇의 이름을 선택한 다음, 드롭다운 목록에서 **Kudu 콘솔 열기**를 선택합니다.

    ![Kudu 콘솔 열기](./media/luis-tutorial-bot-csharp-appinsights/open-kudu-console.png)

3. 콘솔 창에서 다음 명령을 입력합니다.

    ```console
    cd site\wwwroot && build.cmd
    ```

    빌드가 `Finished successfully.`로 완료될 때까지 기다립니다.

## <a name="test-the-web-app-bot"></a>웹앱 봇 테스트

1. 웹앱 봇을 테스트하려면 포털에서 **웹 채팅에서 테스트** 기능을 엽니다.

2. `Coffee bar on please` 구문을 입력합니다.  

    ![채팅에서 웹앱 봇 테스트](./media/luis-tutorial-bot-csharp-appinsights/test-in-web-chat.png)

3. 챗봇 응답에는 차이점이 없습니다. 차이점은 봇 응답이 아니라 Application Insights로 데이터를 보내는 것입니다. Application Insights에 약간 더 많은 데이터가 있도록 몇 개의 발화를 추가로 입력합니다.

|발언|
|--|
|피자를 주문해줘.|
|조명을 모두 꺼.|
|거실 조명 켜.|


## <a name="view-luis-entries-in-application-insights"></a>Application Insights에서 LUIS 항목 보기

Application Insights를 열어 LUIS 항목을 확인합니다.

1. 포털에서 **모든 리소스**를 선택한 후 웹앱 봇 이름으로 필터링합니다. **Application Insights** 유형의 리소스를 클릭합니다. Application Insights의 아이콘은 전구입니다.

    ![Azure portal에서 app insights 검색](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights.png)

2. 리소스가 열리면 맨 오른쪽 패널에서 돋보기의 **검색** 아이콘을 클릭합니다. 오른쪽에 새 패널이 표시됩니다. 발견된 원격 분석 데이터의 양에 따라 패널을 표시하는 데 다소 시간이 걸릴 수 있습니다. `LUIS`를 검색합니다. 이 자습서에서 추가된 LUIS 쿼리 결과로만 목록 범위가 좁혀집니다.

    ![추적 검색](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace.png)

3. 맨 위 항목을 선택합니다. 새 창에는 맨 오른쪽에 있는 LUIS 쿼리에 대한 사용자 지정 데이터를 포함하여 자세한 데이터가 표시됩니다. 데이터에는 상위 의도와 해당 점수가 포함됩니다.

    ![추적 항목 검토](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace-item.png)

    완료되면 맨 오른쪽 위에 있는 **X**를 선택하여 종속성 항목 목록으로 돌아갑니다.

> [!Tip]
> 종속성 목록을 저장하고 나중에 해당 목록으로 돌아가려면 **...자세히**를 클릭하고 **즐겨찾기 저장**을 클릭합니다.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Application Insights에서 의도, 점수 및 발화 쿼리

Application Insights로 데이터를 쿼리 하는 기능을 제공 합니다 [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) 언어 뿐만 내보내기 되도록 [Power BI](https://powerbi.microsoft.com)합니다.

1. 필터 상자 위의 종속성 목록 맨 위에서 **분석**을 클릭합니다.

    ![Analytics 단추](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-analytics-button.png)

2. 새 창이 열리며 맨 위에는 쿼리 창이 있고 그 아래에는 데이터 테이블 창이 있습니다. 이전에 데이터베이스를 사용한 경우, 이 배열이 친숙합니다. 쿼리에는 이름 `LUIS`로 시작하는 최근 24시간의 모든 항목이 포함됩니다. **CustomDimensions** 열에는 LUIS 쿼리 결과가 이름/값 쌍으로 포함됩니다.

    ![기본 분석 보고서](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-1.png)

3. 상위 의도, 점수 및 발화를 끌어오려면 쿼리 창의 마지막 줄 바로 위에 다음을 추가합니다.

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

4. 쿼리를 실행합니다. 데이터 테이블의 맨 오른쪽으로 스크롤합니다. topIntent, score 및 utterance의 새 열을 사용할 수 있습니다. topIntent 열을 클릭하여 정렬합니다.

    ![사용자 지정 분석 보고서](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-2.png)

에 대 한 자세한 정보는 [Kusto 쿼리 언어](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) 또는 [Power BI로 데이터 내보내기](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)합니다.

## <a name="learn-more-about-bot-framework"></a>Bot Framework에 대해 자세히 알아보기

[Bot Framework](https://dev.botframework.com/)에 대해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계

Application Insights 데이터에 추가할 수 있는 기타 정보에는 앱 ID, 버전 ID, 마지막 모델 변경 날짜, 마지막 학습 날짜, 마지막 게시 날짜가 포함됩니다. 이러한 값은 엔드포인트 URL(앱 ID 및 버전 ID) 또는 [Authoring API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) 호출에서 검색한 다음, 웹앱 봇 설정에서 설정하고 끌어올 수 있습니다.  

둘 이상의 LUIS 앱에 대해 동일한 엔드포인트 구독을 사용하는 경우, 구독 ID 및 공유 키임을 나타내는 속성도 포함해야 합니다.

> [!div class="nextstepaction"]
> [예제 발언에 대해 자세히 알아보기](luis-how-to-add-example-utterances.md)
