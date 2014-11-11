<properties pageTitle="Get Started with Mobile Services for Windows Store apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C# or JavaScript. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga" />

# <a name="getting-started"> </a>모바일 서비스 시작

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ko-kr/documentation/articles/mobile-services-windows-store-get-started" title="Windows 스토어" class="current">Windows 스토어</a>
    <a href="/ko-kr/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
    <a href="/ko-kr/documentation/articles/mobile-services-ios-get-started" title="iOS">iOS</a>
    <a href="/ko-kr/documentation/articles/mobile-services-android-get-started" title="Android">Android</a>
    <a href="/ko-kr/documentation/articles/mobile-services-html-get-started" title="HTML">HTML</a>
    <a href="/ko-kr/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/ko-kr/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
    <a href="/ko-kr/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
    <a href="/ko-kr/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>
    <a href="/ko-kr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/" title=".NET 백 엔드">.NET 백 엔드</a> | 
    <a href="/ko-kr/documentation/articles/mobile-services-windows-store-get-started/"  title="JavaScript 백 엔드" class="current">JavaScript 백 엔드</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>이 자습서는 Azure 모바일 서비스를 사용하여 Windows 스토어 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다.</p>
<p>동영상을 시청하려는 경우 오른쪽에 있는 클립은 이 자습서와 동일한 단계를 따릅니다. 동영상에서 Scott Guthrie는 모바일 서비스를 소개한 후 첫 번째 모바일 서비스를 만들고 Windows 스토어 앱에서 해당 서비스에 연결하는 과정을 단계별로 안내합니다.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">자습서 보기</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">동영상 재생</span></a> <span class="time">오전 10:08:00</span></div>

</div>

이 자습서에서는 새 모바일 서비스와 새 모바일 서비스에 앱 데이터를 저장하는 간단한 *할 일 모음* 앱을 둘 다 만듭니다. 만드는 모바일 서비스는 서버 쪽 비즈니스 논리에 JavaScript를 사용합니다. Visual Studio를 사용하여 지원되는 .NET 언어로 서버 쪽 비즈니스 논리를 작성할 수 있게 해 주는 모바일 서비스를 만들려면 이 토픽의 .NET 백 엔드 버전을 참조하세요.

완성된 앱의 스크린샷은 다음과 같습니다.

![][0]

이 자습서를 완료해야 다른 모든 Windows 스토어 앱용 모바일 서비스 자습서를 진행할 수 있습니다.

> [WACOM.NOTE] 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][Azure 무료 평가판]을 참조하세요.

> 이 자습서에는 Visual Studio 2013이 필요합니다. Visual Studio 2012를 사용하여 Windows 스토어 앱을 연결하려면 [Visual Studio 2012를 사용하여 모바일 서비스에서 데이터 시작][Visual Studio 2012를 사용하여 모바일 서비스에서 데이터 시작] 토픽의 단계를 따르십시오.

## 새 모바일 서비스 만들기

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## 새 Windows 스토어 앱 만들기

모바일 서비스를 만든 후 관리 포털에서 쉬운 빠른 시작을 따라 모바일 서비스에 연결할 새 앱을 만들거나 기존 앱을 수정할 수 있습니다.

이 섹션에서는 모바일 서비스에 연결된 새 Windows 스토어 앱을 만듭니다.

1.  관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

2.  빠른 시작 탭에서 **플랫폼 선택** 아래의 **Windows**를 클릭하고 **새 Windows 스토어 앱 만들기**를 확장합니다.

    ![][1]

    모바일 서비스에 연결된 Windows 스토어 앱을 만드는 간편한 세 가지 단계가 표시됩니다.

    ![][2]

3.  아직 수행하지 않은 경우 로컬 컴퓨터나 가상 컴퓨터에서 [Visual Studio 2013 Express for Windows][Visual Studio 2013 Express for Windows]를 다운로드하여 설치합니다.

4.  **TodoItem 테이블 만들기**를 클릭하여 앱 데이터를 저장할 테이블을 만듭니다.

5.  **앱 다운로드 및 실행**에서 앱의 언어를 선택하고 **다운로드**를 클릭합니다.

    모바일 서비스에 연결된 샘플 *할 일 모음* 응용 프로그램에 대한 프로젝트가 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

## Windows 앱 실행

이 자습서의 최종 단계는 새 앱을 빌드하고 실행하는 것입니다.

1.  압축된 프로젝트 파일을 저장한 위치로 이동한 후 컴퓨터에서 파일 압축을 풀고 Visual Studio 2013 Express for Windows에서 솔루션 파일을 엽니다.

2.  **F5** 키를 눌러 프로젝트를 다시 빌드하고 앱을 시작합니다.

3.  앱에서 **Insert a TodoItem**에서 *Complete the tutorial* 등의 의미 있는 텍스트를 입력하고 **Save**를 클릭합니다.

    ![][3]

    Azure에 호스트된 새 모바일 서비스에 POST 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. 테이블에 저장된 항목이 모바일 서비스에서 반환되고 그 데이터가 앱의 두 번째 열에 표시됩니다.

    > [WACOM.NOTE]모바일 서비스에 액세스하여 데이터를 쿼리 및 삽입하는 코드를 검토할 수 있습니다. 이 코드는 MainPage.xaml.cs 파일(C#/XAML 프로젝트) 또는 default.js(JavaScript/HTML 프로젝트) 파일에 있습니다.

4.  관리 포털로 돌아가서 **데이터** 탭을 클릭한 후 **TodoItems** 테이블을 클릭합니다.

    ![][4]

    이를 통해 앱에서 테이블로 삽입된 데이터를 찾을 수 있습니다.

    ![][5]

## 다음 단계

이제 빠른 시작을 완료했으며 모바일 서비스에서 중요한 추가 작업을 수행하는 방법을 알아보겠습니다.

-   **데이터 시작**([C#][C#]/[JavaScript][JavaScript])
    모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법을 자세히 알아봅니다.

-   **인증 시작**([C#][6]/[JavaScript][7])
    ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

-   **푸시 알림 시작**([C#][8]/[JavaScript][9])
    기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.



  [0]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-completed.png
  [Azure 무료 평가판]: http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F
  [Visual Studio 2012를 사용하여 모바일 서비스에서 데이터 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012/
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-windows-store-get-started/mobile-portal-quickstart.png
  [2]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-steps.png
  [Visual Studio 2013 Express for Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
  [3]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-startup.png
  [4]: ./media/mobile-services-windows-store-get-started/mobile-data-tab.png
  [5]: ./media/mobile-services-windows-store-get-started/mobile-data-browse.png
  [C#]: /ko-kr/develop/mobile/tutorials/get-started-with-data-dotnet
  [JavaScript]: /ko-kr/develop/mobile/tutorials/get-started-with-data-js
  [6]: /ko-kr/develop/mobile/tutorials/get-started-with-users-dotnet
  [7]: /ko-kr/develop/mobile/tutorials/get-started-with-users-js
  [8]: /ko-kr/develop/mobile/tutorials/get-started-with-push-dotnet
  [9]: /ko-kr/develop/mobile/tutorials/get-started-with-push-js
