<properties pageTitle="Get Started with Azure Mobile Services for Android apps" metaKeywords="Azure android application, mobile service android, getting started Azure android, azure droid, getting started droid windows" description="Follow this tutorial to get started using Azure Mobile Services for Android development." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# <a name="getting-started"> </a>모바일 서비스 시작

[WACOM.INCLUDE [mobile-services-selector-get-started][mobile-services-selector-get-started]]

이 자습서는 Azure 모바일 서비스를 사용하여 Android 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다. 이 자습서에서는 새 모바일 서비스와 새 모바일 서비스에 앱 데이터를 저장하는 간단한 *할 일 모음* 앱을 둘 다 만듭니다. 생성되는 모바일 서비스에서는 Visual Studio에서 지원되는 .NET 언어를 서버 쪽 비즈니스 논리와 모바일 서비스 관리에 사용합니다. JavaScript에서 서버 쪽 비즈니스 논리를 작성할 수 있게 해 주는 모바일 서비스를 만들려면 이 항목의 [JavaScript 백 엔드 버전][JavaScript 백 엔드 버전]을 참조하세요.

완성된 앱의 스크린샷은 다음과 같습니다.

![][]

이 자습서를 완료하려면 Eclipse IDE(통합 개발 환경), ADT(Android 개발자 도구) 플러그 인 및 최신 Android 플랫폼이 포함된 [Android 개발자 도구][Android 개발자 도구](영문)가 필요합니다. Android 4.2 이상 버전이 필요합니다.

다운로드한 퀵 스타트 프로젝트에는 Android용 모바일 서비스 SDK가 포함되어 있습니다. 이 프로젝트에는 Android 4.2 이상 버전이 필요하지만 모바일 서비스 SDK에는 Android 2.2 이상 버전만 있으면 됩니다.

<div class="dev-callout"><strong>참고</strong> <p>이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 서비스를 사용할 수 있습니다. 이러한 서비스는 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure 무료 평가판</a>을 참조하세요.</p></div>

## <a name="create-new-service"> </a>새 모바일 서비스 만들기

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][mobile-services-dotnet-backend-create-new-service]]

## 로컬 컴퓨터에 모바일 서비스 다운로드

모바일 서비스를 만들었으므로 이제 로컬 컴퓨터 또는 가상 컴퓨터에서 실행할 수 있는 개인 설정된 모바일 서비스 프로젝트를 다운로드합니다.

1.  방금 만든 모바일 서비스를 클릭한 다음 빠른 시작 탭에서 **플랫폼 선택** 아래의 **Android**를 클릭하고 **새 Android 앱 만들기**를 확장합니다.

    ![][1]

2.  아직 하지 않은 경우 [Visual Studio Professional 2013][Visual Studio Professional 2013] 이상 버전을 다운로드하여 설치합니다.

3.  **서비스를 다운로드하고 클라우드에 게시** 아래의 **다운로드**를 클릭합니다.

    모바일 서비스를 구현하는 Visual Studio 프로젝트가 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

4.  또한 게시 프로필을 다운로드하고, 다운로드한 파일을 로컬 컴퓨터에 저장한 다음 저장 위치를 기록해 둡니다.

## 모바일 서비스 테스트

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][mobile-services-dotnet-backend-test-local-service]]

## 모바일 서비스 게시

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][mobile-services-dotnet-backend-publish-service]]

## 새 Android 앱 만들기

이 섹션에서는 모바일 서비스에 연결된 새 Android 앱을 만듭니다.

1.  [관리 포털][관리 포털]에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

2.  빠른 시작 탭에서 **플랫폼 선택** 아래의 **Android**를 클릭하고 **새 Android 앱 만들기**를 확장합니다.

    ![][2]

3.  아직 하지 않은 경우 로컬 컴퓨터나 가상 컴퓨터에서 [Android 개발자 도구][Android 개발자 도구]를 다운로드하여 설치합니다.

4.  **Download and run your app** 아래에서 **다운로드**를 클릭합니다.

    모바일 서비스에 연결된 샘플 *할 일 모음* 응용 프로그램에 대한 프로젝트가 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

## Android 앱 실행

이 자습서의 최종 단계는 새 앱을 빌드하고 실행하는 것입니다.

1.  압축된 프로젝트 파일을 저장한 위치로 이동한 후 컴퓨터에서 파일 압축을 풉니다.

2.  Eclipse에서 **File**과 **Import**를 클릭하고 **Android**를 확장한 후 **Existing Android Code into Workspace**를 클릭한 다음 **Next**를 클릭합니다.

    ![][3]

3.  **Browse**를 클릭하고 압축을 푼 프로젝트 파일 위치로 이동한 후 **OK**를 클릭합니다. TodoActivity 프로젝트가 선택되었는지 확인하고 **Finish**를 클릭합니다.

    ![][4]

    현재 작업 영역에 프로젝트 파일을 가져옵니다.

    ![][5]

4.  **Run** 메뉴에서 **Run**을 클릭하여 Android 에뮬레이터에서 프로젝트를 시작합니다.

    <div class="dev-callout"><strong>참고</strong> <p>Android 에뮬레이터에서 프로젝트를 실행할 수 있으려면 AVD(Android Virtual Device)를 하나 이상 정의해야 합니다. AVD Manager를 사용하여 이러한 장치를 만들고 관리합니다.</p></div>

5.  앱에서 *Complete the tutorial* 등의 의미 있는 텍스트를 입력하고 **Add**를 클릭합니다.

    ![][6]

    Azure에 호스트된 새 모바일 서비스에 POST 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. TodoItem 테이블에 저장된 항목이 모바일 서비스에서 반환된 후 데이터가 목록에 표시됩니다.

    <div class="dev-callout"><strong>참고</strong> 
<p>모바일 서비스에 액세스하여 데이터를 쿼리 및 삽입하는 코드를 검토할 수 있습니다. 이 코드는 ToDoActivity.java 파일에 있습니다.</p>
</div>

<!--This shows how to run your new client app against the mobile service running in Azure. Before you can test the Android app with the mobile service running on a local computer, you must configure the Web server and firewall to allow access from your Android development computer. For more information, see [Configure the local web server to allow connections to a local mobile service](/ko-kr/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).-->

## <a name="next-steps"> </a>다음 단계

이제 빠른 시작을 완료했으며 모바일 서비스에서 중요한 추가 작업을 수행하는 방법을 알아보겠습니다.

-   [데이터 시작하기][데이터 시작하기]
    모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아보세요.

-   [인증 시작][인증 시작]
    ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

-   [푸시 알림 시작][푸시 알림 시작]
    기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

-   [모바일 서비스 .NET 백 엔드 문제 해결][모바일 서비스 .NET 백 엔드 문제 해결]
     모바일 서비스 .NET 백 엔드에서 발생할 수 있는 문제를 진단 및 해결하는 방법을 알아보십시오.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [JavaScript 백 엔드 버전]: /ko-kr/documentation/articles/mobile-services-android-get-started/
  []: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png
  [Android 개발자 도구]: https://go.microsoft.com/fwLink/p/?LinkID=280125
  [Azure 무료 평가판]: http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=AE564AB28
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [1]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-vs.png
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=391934
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [관리 포털]: https://manage.windowsazure.com/
  [2]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
  [3]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png
  [4]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png
  [5]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png
  [6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-startup-android.png
  [데이터 시작하기]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
  [인증 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
  [푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-push
  [모바일 서비스 .NET 백 엔드 문제 해결]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
