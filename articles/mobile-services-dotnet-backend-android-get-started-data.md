<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-android" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# 모바일 서비스에서 데이터 시작

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="Windows 스토어 C#">Windows 스토어 C#</a>
    <a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="Windows 스토어 JavaScript">Windows 스토어 JavaScript</a>
    <a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a>
    <a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title="iOS">iOS</a>
    <a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title="Android" class="current">Android</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title=".NET 백 엔드" class="current">.NET 백 엔드</a> | 
    <a href="/ko-kr/develop/mobile/tutorials/get-started-with-data-android/"  title="JavaScript 백 엔드">JavaScript 백 엔드</a>
</div>

이 항목에서는 Android 앱용 백 엔드 데이터 소스로서 Azure 모바일 서비스를 사용하는 방법을 보여 줍니다. 이 자습서에서는 새 모바일 서비스를 만들고, 데이터를 메모리에 저장하는 앱용 Eclipse Android 프로젝트를 다운로드하고, 모바일 서비스를 앱과 통합하고, 앱을 실행할 때 수행된 데이터 변경 내용을 확인합니다.

이 자습서에서 만드는 모바일 서비스는 모바일 서비스의 .NET 런타임을 지원합니다. 따라서 사용자는 모바일 서비스에서 서버 쪽 비즈니스 논리에 .NET 언어 및 Visual Studio를 사용할 수 있습니다. JavaScript에서 서버 쪽 비즈니스 논리를 작성할 수 있게 해 주는 모바일 서비스를 만들려면 이 항목의 [JavaScript 백 엔드 버전][JavaScript 백 엔드 버전]을 참조하세요.

<div class="dev-callout"><b>참고</b>
<p>이 자습서를 완료하려면 Visual Studio 2013이 필요합니다.</p>
</div>

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1.  [새 모바일 서비스 만들기][새 모바일 서비스 만들기]
2.  [로컬로 서비스 다운로드][로컬로 서비스 다운로드]
3.  [모바일 서비스 테스트][모바일 서비스 테스트]
4.  [Azure에 모바일 서비스 게시][Azure에 모바일 서비스 게시]
5.  [GetStartedWithData 프로젝트 다운로드][GetStartedWithData 프로젝트 다운로드]
6.  [데이터 액세스에 모바일 서비스를 사용하도록 앱 업데이트][데이터 액세스에 모바일 서비스를 사용하도록 앱 업데이트]
7.  [게시된 모바일 서비스에 대해 앱 테스트][게시된 모바일 서비스에 대해 앱 테스트]

<div class="dev-callout"><strong>참고</strong> <p>이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.</p></div>

## <a name="create-service"></a><span class="short-header">새 모바일 서비스 만들기</span>새 모바일 서비스 만들기

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## <a name="download-the-service"></a><span class="short-header">서비스 다운로드</span>로컬 컴퓨터로 서비스 다운로드

[WACOM.INCLUDE [mobile-services-download-service-locally](../includes/mobile-services-download-service-locally.md)]

## <a name="test-the-service"></a><span class="short-header">서비스 테스트</span>모바일 서비스 테스트

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## <a name="publish-the-service"></a><span class="short-header">서비스 게시</span>Azure에 모바일 서비스 게시

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="download-app"></a><span class="short-header">프로젝트 다운로드</span>GetStartedWithData 프로젝트 다운로드

### 샘플 코드 가져오기

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

### Android SDK 버전 확인

[WACOM.INCLUDE [mobile-services-verify-android-sdk-version](../includes/mobile-services-verify-android-sdk-version.md)]

### 샘플 코드 검사 및 실행

[WACOM.INCLUDE [mobile-services-android-run-sample-code](../includes/mobile-services-android-run-sample-code.md)]

## <a name="update-app"></a><span class="short-header">앱 업데이트</span>데이터 액세스에 모바일 서비스를 사용하도록 앱 업데이트

[WACOM.INCLUDE [mobile-services-android-getting-started-with-data](../includes/mobile-services-android-getting-started-with-data.md)]

## <a name="test-app"></a><span class="short-header">앱 테스트</span>게시된 모바일 서비스에 대해 앱 테스트

앱이 백 엔드 저장소용으로 모바일 서비스를 사용하도록 업데이트되었으므로, 이제 Android 에뮬레이터나 Android 휴대폰을 사용하여 모바일 서비스에 대해 앱을 테스트할 수 있습니다.

1.  **Run** 메뉴에서 **Run**을 클릭하여 프로젝트를 시작합니다.

    그러면 Android SDK를 사용하여 빌드된 앱이 실행되며 클라이언트 라이브러리를 사용하여 모바일 서비스의 항목을 반환합니다.

2.  이전처럼 의미 있는 텍스트를 입력한 후 **Add**를 클릭합니다.

    그러면 새 항목이 모바일 서비스에 삽입으로 전송됩니다.

    변경 사항이 Azure의 데이터베이스에서 유지되었는지 확인하려면 앱을 다시 시작할 수 있습니다. 또한 Azure 관리 포털을 사용하여 데이터 집합을 확인할 수 있습니다. 다음 두 단계에서 이 작업을 수행하여 데이터 집합의 변경 내용을 확인합니다.

3.  Azure 관리 포털에서 모바일 서비스와 연결된 데이터베이스에 대해 관리를 클릭합니다.

    ![][0]

4.  관리 포털에서 쿼리를 실행하여 Windows 스토어 앱에 의한 변경 사항을 확인합니다. 쿼리는 다음과 유사하지만, `todolist` 대신 데이터베이스 이름을 사용하게 됩니다.

        SELECT * FROM [todolist].[todoitems]

    ![][2]

이제 Android용 **데이터 시작** 자습서를 마쳤습니다.

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 Android 앱에서 모바일 서비스의 데이터로 작업하기 위한 기본 사항에 대해 설명했습니다.

<!--Next, consider completing one of the following tutorials that is based on the GetStartedWithData app that you created in this tutorial:  * [Validate and modify data with scripts]   <br/>Learn more about using server scripts in Mobile Services to validate and change data sent from your app.  * [Refine queries with paging]   <br/>Learn how to use paging in queries to control the amount of data handled in a single request.  Once you have completed the data series, try -->

다른 자습서도 시도해보세요.

-   [인증 시작][인증 시작]
    <br/>앱 사용자를 인증하는 방법을 알아봅니다.

-   [푸시 알림 시작][푸시 알림 시작]
    <br/>기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

-   [모바일 서비스 .NET 방법 개념 참조][모바일 서비스 .NET 방법 개념 참조]
    <br/>.NET과 함께 모바일 서비스를 사용하는 방법에 대해 자세히 알아보세요.





  [JavaScript 백 엔드 버전]: /ko-kr/develop/mobile/tutorials/get-started-with-data-android
  [새 모바일 서비스 만들기]: #create-service
  [로컬로 서비스 다운로드]: #download-the-service-locally
  [모바일 서비스 테스트]: #test-the-service
  [Azure에 모바일 서비스 게시]: #publish-mobile-service
  [GetStartedWithData 프로젝트 다운로드]: #download-app
  [데이터 액세스에 모바일 서비스를 사용하도록 앱 업데이트]: #update-app
  [게시된 모바일 서비스에 대해 앱 테스트]: #test-app
  [0]: ./media/mobile-services-dotnet-backend-android-get-started-data/manage-sql-azure-database.png
  [2]: ./media/mobile-services-dotnet-backend-android-get-started-data/sql-azure-query.png
  [인증 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-users-android
  [푸시 알림 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-push-android
  [모바일 서비스 .NET 방법 개념 참조]: /ko-kr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
