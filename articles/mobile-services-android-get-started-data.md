<properties 
	pageTitle="데이터 시작(Android) | 모바일 개발자 센터" 
	description="모바일 서비스를 사용하여 Android 앱에서 데이터를 활용하는 방법에 대해 알아봅니다." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="ricksal,glenga"/>

# 기존 앱에 모바일 서비스 추가(영문)

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

## 요약

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>이 항목에서는 Azure 모바일 서비스를 사용하여 Android 앱에서 데이터를 활용하는 방법을 보여 줍니다. 이 자습서에서는 데이터를 메모리에 저장하는 앱을 다운로드하여 새 모바일 서비스를 만들고 모바일 서비스를 앱과 통합한 후 Azure 관리 포털에 로그인하여 앱을 실행할 때 변경된 데이터를 확인합니다.</p>

</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">자습서 보기</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">비디오 재생</span></a><span class="time">오후 3:32</span></div>
</div>

<p>이 자습서는 Android 앱에서 모바일 서비스를 통해 Azure를 사용하여 데이터를 저장하고 검색할 수 있는 방법을 더욱 잘 이해할 수 있도록 돕기 위한 것입니다. 이 항목에서는 모바일 서비스 퀵 스타트에서 완료한 여러 단계를 순서대로 안내합니다. 모바일 서비스를 처음 사용하는 경우, 먼저 <a href="/develop/mobile/tutorials/get-started-android">모바일 서비스 시작</a>(영문) 자습서를 완료할 때 만든 프로젝트를 엽니다.</p>

> [AZURE.NOTE] 완료된 앱의 소스 코드를 보려면 <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithData/Android/GetStartedWithData">여기</a>를 참조하세요.

이 자습서의 Eclipse 버전을 참조하려면 [Eclipse를 사용하여 데이터 시작](/documentation/articles/mobile-services-android-get-started-data-EC/)으로 이동하세요.

> [AZURE.NOTE] 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AED8DE357" target="_blank">Azure 무료 평가판</a>을 참조하세요.

이 자습서를 완료하려면 [모바일 서비스 Android SDK](영문)가 필요합니다. 이 <a  href="https://developer.android.com/sdk/index.html" target="_blank">Android Studio 통합 개발 환경에는</a>Android SDK 및 Android 4.2 이상 버전이 포함되어 있습니다. 

> [AZURE.NOTE] 이 자습서에서는 Android SDK 및 모바일 서비스 Android SDK 설치에 대한 지침을 제공합니다. 다운로드한 GetStartedWithData 프로젝트에는 Android 4.2 이후 버전이 필요합니다. 하지만 모바일 서비스 SDK에 필요한 Android는 2.2 이상 버전이면 됩니다.
<!-- -->

> [AZURE.NOTE] 이 자습서에서는 최신 버전의 모바일 서비스 SDK를 사용합니다. 이전 버전과의 호환성을 위해 <a href="http://go.microsoft.com/fwlink/p/?LinkID=280126"> 여기</a> 에서 이전 버전을 찾을 수 있지만, 이전 버전을 사용할 경우 이 자습서에 포함된 코드가 작동하지 않습니다.

<h2><a name="download-app"></a>GetStartedWithData 프로젝트 다운로드</h2>

### 샘플 코드 가져오기

[AZURE.INCLUDE [download-android-sample-code](../includes/download-android-sample-code.md)]

### Android SDK 버전 확인

[AZURE.INCLUDE [Verify SDK](../includes/mobile-services-verify-android-sdk-version.md)]


### 샘플 코드 검사 및 실행

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../includes/mobile-services-android-run-sample-code.md)]

<h2><a name="create-service"></a>관리 포털에서 새 모바일 서비스 만들기</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>모바일 서비스에 새 테이블 추가</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a>데이터 액세스에 모바일 서비스를 사용하도록 앱 업데이트</h2>

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../includes/mobile-services-android-getting-started-with-data.md)]


<h2><a name="test-app"></a>새 모바일 서비스에 대해 앱 테스트</h2>

앱이 백 엔드 저장소용으로 모바일 서비스를 사용하도록 업데이트되었으므로, 이제 Android 에뮬레이터나 Android 휴대폰을 사용하여 모바일 서비스에 대해 앱을 테스트할 수 있습니다.

1. **실행** 메뉴에서 **앱 실행**을 클릭하여 프로젝트를 시작합니다.

	그러면 Android SDK를 사용하여 빌드된 앱이 실행되며 클라이언트 라이브러리를 사용하여 모바일 서비스의 항목을 반환합니다.

5. 이전처럼 의미 있는 텍스트를 입력한 후 **추가**를 클릭합니다.

   	그러면 새 항목이 모바일 서비스에 삽입으로 전송됩니다.

3. [관리 포털]에서 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다.

4. **데이터** 탭을 클릭한 후 **찾아보기**를 클릭합니다.

   	![][9]
  
   	이제 **TodoItem** 테이블에는 모바일 서비스에서 생성한 값을 비롯한 데이터가 포함되었으며, 앱의 TodoItem 클래스와 일치시키기 위해 자동으로 테이블에 열이 추가되었습니다.

이제 Android용 **데이터 시작** 자습서를 마쳤습니다.

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 Android 앱에서 모바일 서비스의 데이터로 작업하기 위한 기본 사항에 대해 설명했습니다. 

다음에는 이 자습서에서 만든 GetStartedWithData 앱을 기반으로 하는 다음 자습서 중 하나를 완료해보세요.

* [스크립트를 사용하여 데이터 유효성 검사 및 수정]
  <br/>모바일 서비스에서 서버 스크립트를 사용하여 앱에서 전송된 데이터의 유효성을 검사하고 변경하는 방법에 대해 자세히 알아봅니다.

* [페이징을 사용하여 쿼리 구체화]
  <br/>쿼리에 페이징을 사용하여 단일 요청으로 처리되는 데이터 양을 제어하는 방법을 알아봅니다.

데이터 시리즈를 완료한 후에는 다음과 같은 다른 Android 자습서를 시도해 보세요.

* [인증 시작] 
	<br/>앱 사용자를 인증하는 방법을 알아봅니다.

* [푸시 알림 시작] 
  <br/>모바일 서비스를 통해 기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

<!-- Anchors. -->
[Android 앱 프로젝트 다운로드]: #download-app
[모바일 서비스 만들기]: #create-service
[저장소로 사용할 데이터 테이블 추가]: #add-table
[모바일 서비스를 사용하도록 앱 업데이트]: #update-app
[모바일 서비스에 대해 앱 테스트]: #test-app
[다음 단계]:#next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->
[스크립트를 사용하여 데이터 유효성 검사 및 수정]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[페이징을 사용하여 쿼리 구체화]: /develop/mobile/tutorials/add-paging-to-data-android
[모바일 서비스 시작]: /develop/mobile/tutorials/get-started-android
[데이터 시작]: /develop/mobile/tutorials/get-started-with-data-android
[데이터 시작(Eclipse)]: /documentation/articles/mobile-services-android-get-started-data-EC/
[인증 시작]: /develop/mobile/tutorials/get-started-with-users-android
[푸시 알림 시작]: /develop/mobile/tutorials/get-started-with-push-android

[Azure 관리 포털]: https://manage.windowsazure.com/
[관리 포털]: https://manage.windowsazure.com/
[모바일 서비스 Android SDK]: http://aka.ms/Iajk6q
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

<!--HONumber=47-->
