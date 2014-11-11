<properties linkid="develop-mobile-tutorials-get-started-with-data-android" urlDisplayName="Get Started with Data - Android" pageTitle="Get started with data (Android) | Mobile Dev Center" metaKeywords="Azure android data, Azure mobile services data, windows droid, windows android, microsoft droid, microsoft android" description="Learn how to get started using Mobile Services to leverage data in your Android app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="ricksal,glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/29/2014" ms.author="ricksal,glenga" />

# 모바일 서비스에서 데이터 시작

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started-data.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>이 항목에서는 Azure 모바일 서비스를 사용하여 Android 앱에서 데이터를 활용하는 방법을 보여 줍니다. 이 자습서에서는 데이터를 메모리에 저장하는 앱을 다운로드하여 새 모바일 서비스를 만들고 모바일 서비스를 앱과 통합한 후 Azure 관리 포털에 로그인하여 앱을 실행할 때 수행된 데이터 변경 내용을 확인합니다.</p>

</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">자습서 보기</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">동영상 재생</span></a><span class="time">오후 3:32:00</span></div>

</div>

<div class="dev-callout"><b>참고</b>
<p>이 자습서는 Android 앱에서 모바일 서비스를 통해 Azure를 사용하여 데이터를 저장하고 검색할 수 있는 방법을 더욱 잘 이해할 수 있도록 돕기 위한 것입니다. 이 항목에서는 모바일 서비스 퀵 스타트에서 완료한 다수의 단계를 순서대로 안내합니다. 모바일 서비스를 처음 사용하는 경우 먼저 <a href="/ko-kr/develop/mobile/tutorials/get-started-android">모바일 서비스 시작</a> 자습서를 완료하는 것이 좋습니다.</p>
</div>

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1.  [Android 앱 프로젝트 다운로드][Android 앱 프로젝트 다운로드]
2.  [모바일 서비스 만들기][모바일 서비스 만들기]
3.  [저장소로 사용할 데이터 테이블 추가][저장소로 사용할 데이터 테이블 추가]
4.  [모바일 서비스를 사용하도록 앱 업데이트][모바일 서비스를 사용하도록 앱 업데이트]
5.  [모바일 서비스에 대해 앱 테스트][모바일 서비스에 대해 앱 테스트]

<div class="dev-callout"><strong>참고</strong> <p>이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=AED8DE357" target="_blank">Azure 무료 평가판</a>을 참조하세요.</p></div>

이 자습서에는 [모바일 서비스 Android SDK][모바일 서비스 Android SDK], Eclipse IDE(통합 개발 환경) 및 ADT(Android 개발자 도구) 플러그 인이 포함된 [Android SDK][Android SDK], Android 4.2 이상 버전이 필요합니다.

<div class="dev-callout"><b>참고</b>
<p>이 자습서에서는 Android SDK 및 모바일 서비스 Android SDK 설치에 대한 지침을 제공합니다. 다운로드한 GetStartedWithData 프로젝트에는 Android 4.2 이후 버전이 필요합니다. 하지만 모바일 서비스 SDK에 필요한 Android는 2.2 이상 버전이면 됩니다.</p>
</div>

## <a name="download-app"></a><span class="short-header">프로젝트 다운로드</span>GetStartedWithData 프로젝트 다운로드

### 샘플 코드 가져오기

[WACOM.INCLUDE [download-android-sample-code](../includes/download-android-sample-code.md)]

### Android SDK 버전 확인

[WACOM.INCLUDE [Verify SDK](../includes/mobile-services-verify-android-sdk-version.md)]

### 샘플 코드 검사 및 실행

[WACOM.INCLUDE [mobile-services-android-run-sample-code](../includes/mobile-services-android-run-sample-code.md)]

## <a name="create-service"></a><span class="short-header">모바일 서비스 만들기</span>관리 포털에서 새 모바일 서비스 만들기

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a><span class="short-header">새 테이블 추가</span>모바일 서비스에 새 테이블 추가

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

## <a name="update-app"></a><span class="short-header">앱 업데이트</span>데이터 액세스에 모바일 서비스를 사용하도록 앱 업데이트

[WACOM.INCLUDE [mobile-services-android-getting-started-with-data](../includes/mobile-services-android-getting-started-with-data.md)]

## <a name="test-app"></a><span class="short-header">앱 테스트</span>모바일 서비스에 대해 앱 테스트

앱이 백 엔드 저장소용으로 모바일 서비스를 사용하도록 업데이트되었으므로, 이제 Android 에뮬레이터나 Android 휴대폰을 사용하여 모바일 서비스에 대해 앱을 테스트할 수 있습니다.

1.  **Run** 메뉴에서 **Run**을 클릭하여 프로젝트를 시작합니다.

    그러면 Android SDK를 사용하여 빌드된 앱이 실행되며 클라이언트 라이브러리를 사용하여 모바일 서비스의 항목을 반환합니다.

2.  이전처럼 의미 있는 텍스트를 입력한 후 **Add**를 클릭합니다.

    그러면 새 항목이 모바일 서비스에 삽입으로 전송됩니다.

3.  [관리 포털][관리 포털]에서 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다.

4.  **데이터** 탭을 클릭한 후 **찾아보기**를 클릭합니다.

    ![][0]

    이제 **TodoItem** 테이블에 모바일 서비스에서 생성된 일부 값을 가진 데이터가 포함되었으며 해당 열이 앱의 TodoItem 클래스와 일치하도록 테이블에 자동으로 추가되었습니다.

이제 Android용 **데이터 시작** 자습서를 마쳤습니다.

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 Android 앱에서 모바일 서비스의 데이터로 작업하기 위한 기본 사항에 대해 설명했습니다.

다음에는 이 자습서에서 만든 GetStartedWithData 앱을 기반으로 하는 다음 자습서 중 하나를 완료해보세요.

-   [스크립트를 사용하여 데이터 유효성 검사 및 수정][스크립트를 사용하여 데이터 유효성 검사 및 수정]
    모바일 서비스에서 서버 스크립트를 사용하여 앱에서 전송된 데이터의 유효성을 검사하고 변경하는 방법을 자세히 알아봅니다.

-   [페이징을 사용하여 쿼리 구체화][페이징을 사용하여 쿼리 구체화]
    쿼리에 페이징을 사용하여 단일 요청으로 처리되는 데이터 양을 제어하는 방법을 알아봅니다.

데이터 시리즈를 완료한 후에는 다른 Android 자습서 중 하나를 시도해 보십시오.

-   [인증 시작][인증 시작]
    앱 사용자를 인증하는 방법을 알아봅니다.

-   [푸시 알림 시작][푸시 알림 시작]
    모바일 서비스를 사용하여 기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started-data.md
  [Android 앱 프로젝트 다운로드]: #download-app
  [모바일 서비스 만들기]: #create-service
  [저장소로 사용할 데이터 테이블 추가]: #add-table
  [모바일 서비스를 사용하도록 앱 업데이트]: #update-app
  [모바일 서비스에 대해 앱 테스트]: #test-app
  [모바일 서비스 Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
  [Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
  [download-android-sample-code]: ../includes/download-android-sample-code.md
  [Verify SDK]: ../includes/mobile-services-verify-android-sdk-version.md
  [mobile-services-android-run-sample-code]: ../includes/mobile-services-android-run-sample-code.md
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [mobile-services-android-getting-started-with-data]: ../includes/mobile-services-android-getting-started-with-data.md
  [관리 포털]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
  [스크립트를 사용하여 데이터 유효성 검사 및 수정]: /ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [페이징을 사용하여 쿼리 구체화]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-android
  [인증 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-users-android
  [푸시 알림 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-push-android
