<properties
	pageTitle="Android 앱에 대해 Azure 모바일 서비스 시작"
	description="이 자습서에 따라 Azure 모바일 서비스를 사용하여 Android 개발을 시작할 수 있습니다."
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
	ms.date="06/03/2015"
	ms.author="ricksal"/>

# <a name="getting-started"> </a>모바일 서비스 시작

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started-EC.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>이 자습서는 Azure 모바일 서비스를 사용하여 Android 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다. 이 자습서에서는 새 모바일 서비스와 새 모바일 서비스에 앱 데이터를 저장하는 간단한 <em>할 일 모음</em> 앱을 둘 다 만듭니다.</p>
<p>완성된 앱의 스크린샷은 다음과 같습니다.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">자습서 보기</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-get-started-android-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">동영상 재생</span></a><span class="time">7:26</span></div>
</div>

![][0]

이 자습서를 완료하려면 Eclipse IDE(통합 개발 환경), ADT(Android 개발자 도구) 플러그 인 및 최신 Android 플랫폼이 포함된 [Android 개발자 도구][Android SDK](영문)가 필요합니다. Android 4.2 이상 버전이 필요합니다.

다운로드한 퀵 스타트 프로젝트에는 Android용 모바일 서비스 SDK가 포함되어 있습니다. 이 프로젝트에는 Android 4.2 이상 버전이 필요하지만 모바일 서비스 SDK에는 Android 2.2 이상 버전만 있으면 됩니다.

> [AZURE.IMPORTANT]이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28"%20target="_blank)을 참조하세요.

<!-- -->

>[AZURE.NOTE]완성된 앱의 소스 코드를 참조하려는 경우 <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStarted/Android" target="_blank">여기</a>로 이동하세요.

## <a name="create-new-service"> </a>새 모바일 서비스 만들기

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 새 Android 앱 만들기

모바일 서비스를 만든 후 관리 포털에서 쉬운 빠른 시작을 따라 모바일 서비스에 연결할 새 앱을 만들거나 기존 앱을 수정할 수 있습니다.

이 섹션에서는 모바일 서비스에 연결된 새 Android 앱을 만듭니다.

1.  관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

2. 빠른 시작 탭에서 **플랫폼 선택** 아래의 **Android**를 클릭하고 **새 Android 앱 만들기**를 확장합니다.

   	![][6]

   	모바일 서비스에 연결된 Android 앱을 만드는 간편한 3단계가 표시됩니다.

  	![][7]

3. 아직 하지 않은 경우 로컬 컴퓨터나 가상 컴퓨터에서 [Android 개발자 도구][Android SDK]를 다운로드하여 설치합니다.

4. **TodoItem 테이블 만들기**를 클릭하여 앱 데이터를 저장할 테이블을 만듭니다.


5. 이제 앱을 다운로드합니다.
	- 최신 앱 버전에서는 모바일 서비스 Android SDK 2.0을 사용합니다. <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures">여기</a>에서 해당 버전을 다운로드할 수 있습니다. **Zip 다운로드**를 클릭하고 압축을 풀면 Android 폴더 아래의 GettingStarted에 프로젝트가 추가됩니다.

	- 이전 버전에서는 이전 버전의 SDK를 사용합니다. 이를 사용하려면 **앱 다운로드 및 실행** 아래에서 **다운로드**를 클릭합니다. 모바일 서비스에 연결된 샘플 _할 일 모음_ 응용 프로그램에 대한 프로젝트가 다운로드됩니다. 프로젝트 파일은 압축되므로 해당 위치로 이동한 후 컴퓨터에 파일 압축을 풉니다.


## Android 앱 실행

이 자습서의 최종 단계는 새 앱을 빌드하고 실행하는 것입니다.

2. Eclipse에서 **File**과 **Import**를 클릭하고 **Android**를 확장한 후 **Existing Android Code into Workspace**를 클릭한 다음 **Next**를 클릭합니다.

 	![][14]

3. **Browse**를 클릭하고 압축을 푼 프로젝트 파일 위치로 이동한 후 **OK**를 클릭합니다. TodoActivity 프로젝트가 선택되었는지 확인하고 **Finish**를 클릭합니다.

 	![][15]

	현재 작업 영역에 프로젝트 파일을 가져옵니다.

   	![][8]

4. 2.0 버전의 SDK를 다운로드한 경우 코드를 모바일 서비스의 URL 및 키로 업데이트해야 합니다.
	- 	**TodoActivity.java**에서 **OnCreate** 메서드를 찾아 모바일 서비스 클라이언트를 인스턴스화하는 코드를 찾습니다. 이 코드는 이전 이미지에서 확인할 수 있습니다.
	- 	"MobileServiceUrl"을 모바일 서비스의 실제 URL로 바꿉니다.
	- 	"AppKey"를 모바일 서비스의 키로 바꿉니다.
	- 	자세한 내용은 <a href="http://azure.microsoft.com/documentation/articles/mobile-services-android-get-started-data/">기존 앱에 모바일 서비스 추가</a> 자습서를 참조하세요.



4. **Run** 메뉴에서 **Run**을 클릭하여 Android 에뮬레이터에서 프로젝트를 시작합니다.

	> [AZURE.IMPORTANT]Android 에뮬레이터에서 프로젝트를 실행할 수 있으려면 AVD(Android Virtual Device)를 하나 이상 정의해야 합니다. AVD Manager를 사용하여 이러한 장치를 만들고 관리합니다.

5. 앱에서 _Complete the tutorial_ 등의 의미 있는 텍스트를 입력하고 **Add**를 클릭합니다.

   	![][10]

   	Azure에 호스트된 새 모바일 서비스에 POST 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. TodoItem 테이블에 저장된 항목이 모바일 서비스에서 반환된 후 데이터가 목록에 표시됩니다.

	> [AZURE.NOTE]모바일 서비스에 액세스하여 데이터를 쿼리 및 삽입하는 코드를 검토할 수 있습니다. 이 코드는 ToDoActivity.java 파일에 있습니다.

6. 관리 포털로 돌아가서 **데이터** 탭을 클릭한 후 **TodoItems** 테이블을 클릭합니다.

   	![][11]

   	이를 통해 앱에서 테이블로 삽입된 데이터를 찾을 수 있습니다.

   	![][12]

## <a name="next-steps"> </a>다음 단계
이제 빠른 시작을 완료했으며 모바일 서비스에서 중요한 추가 작업을 수행하는 방법을 알아보겠습니다.

* [데이터 시작] <br/>모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법을 자세히 알아봅니다.

* [인증 시작] <br/>ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

* [푸시 알림 시작] <br/>기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-get-started-EC/mobile-quickstart-completed-android.png





[6]: ./media/mobile-services-android-get-started-EC/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started-EC/mobile-quickstart-steps-android-EC.png
[8]: ./media/mobile-services-android-get-started-EC/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-android-get-started-EC/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started-EC/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started-EC/mobile-data-browse.png

[14]: ./media/mobile-services-android-get-started-EC/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-EC/mobile-services-import-android-project.png

<!-- URLs. -->
[데이터 시작]: mobile-services-android-get-started-data.md
[인증 시작]: mobile-services-android-get-started-users.md
[푸시 알림 시작]: mobile-services-javascript-backend-android-get-started-push.md
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO4-->