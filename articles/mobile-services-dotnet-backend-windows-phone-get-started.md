<properties linkid="develop-mobile-tutorials-get-started-wp8" urlDisplayName="Get Started (WP8)" pageTitle="Get Started with Azure Mobile Services for Windows Phone apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Phone development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# <a name="getting-started"> </a>모바일 서비스 시작

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

이 자습서는 Azure 모바일 서비스를 사용하여 Windows Phone 8 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다. 이 자습서에서는 새 모바일 서비스와 새 모바일 서비스에 앱 데이터를 저장하는 간단한 *할 일 모음* 앱을 둘 다 만듭니다. 생성되는 모바일 서비스에서는 Visual Studio에서 지원되는 .NET 언어를 서버 쪽 비즈니스 논리와 모바일 서비스 관리에 사용합니다. JavaScript에서 서버 쪽 비즈니스 논리를 작성할 수 있게 해 주는 모바일 서비스를 만들려면 이 항목의 [JavaScript 백 엔드 버전][JavaScript 백 엔드 버전]을 참조하세요.

완성된 앱의 스크린샷은 다음과 같습니다.

![][0]

> [WACOM.NOTE] 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 서비스를 사용할 수 있습니다. 이러한 서비스는 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 [Azure 무료 평가판][Azure 무료 평가판]을 참조하세요.
> 이 자습서를 완료하려면 [Visual Studio Professional 2013][Visual Studio Professional 2013]이 필요합니다. 무료 평가판을 이용할 수 있습니다. 새 Windows Phone 8.1 앱을 만들려면 Visual Studio 2013 업데이트 2 이상 버전이 있어야 합니다.

## <a name="create-new-service"> </a>새 모바일 서비스 만들기

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## 새 Windows Phone 앱 만들기

모바일 서비스를 만든 후 관리 포털에서 쉬운 빠른 시작을 따라 모바일 서비스에 연결할 새 앱을 만들거나 기존 앱을 수정할 수 있습니다.

이 섹션에서는 모바일 서비스에 연결된 새 Windows Phone 8 앱을 만듭니다.

1.  관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

2.  빠른 시작 탭에서 **플랫폼 선택** 아래의 **Windows Phone 8**을 클릭하고 **새 Windows Phone 8 앱 만들기**를 확장합니다.

    ![][1]

    그러면 모바일 서비스에 연결된 Windows Phone 앱을 만들기 위한 쉬운 3단계가 표시됩니다.

    ![][2]

3.  아직 하지 않은 경우 로컬 컴퓨터나 가상 컴퓨터에 [Visual Studio Professional 2013][Visual Studio Professional 2013]을 다운로드하여 설치합니다.

4.  **서비스를 다운로드하고 클라우드에 게시** 아래에서 **다운로드**를 클릭합니다.

    모바일 서비스 및 여기에 연결된 샘플 *할 일 모음* 응용 프로그램 모두를 위한 프로젝트가 포함된 솔루션이 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

5.  **클라우드에 서비스 게시** 아래에서 게시 프로필을 다운로드하고, 다운로드한 파일을 로컬 컴퓨터에 저장하고, 저장 위치를 기록해 둡니다.

## 로컬 컴퓨터에서 모바일 서비스 테스트

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

> [WACOM.NOTE]로컬 서비스를 연결하는 Windows Phone 앱을 실행하는 데 필요한 추가 구성 단계가 있습니다. 이 항목에서는 이러한 단계에 대해 설명하지 않지만 [Windows Phone 8 에뮬레이터에서 로컬 웹 서비스에 연결하는 방법][Windows Phone 8 에뮬레이터에서 로컬 웹 서비스에 연결하는 방법]에서 자세히 알아볼 수 있습니다.

## 모바일 서비스 게시

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

1.  Windows Phone 앱 프로젝트에서 App.xaml.cs 파일을 열어 [MobileServiceClient][MobileServiceClient] 인스턴스를 만드는 코드를 찾은 다음, *localhost*를 사용하여 이 클라이언트를 만드는 코드는 주석 처리하고 원격 모바일 서비스 URL을 사용하여 클라이언트를 만드는 코드는 주석 처리를 제거합니다. 다음과 같습니다.

        public static MobileServiceClient MobileService = new MobileServiceClient(
                    "https://todolist.azure-mobile.net/",
                    "XXXXXXX-APPLICATION-KEY-XXXXXXXX");

    이제 클라이언트가 Azure에 게시된 모바일 서비스에 액세스할 수 있습니다.

2.  (옵션) Windows Phone 8.1 앱을 만드는 경우 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭하고 **대상 Windows Phone OS 버전**을 **Windows Phone 8.1**로 설정한 후 **예**를 클릭하여 프로젝트를 업그레이드합니다.

3.  **F5** 키를 눌러 프로젝트를 다시 빌드하고 앱을 시작합니다.

4.  앱에서 *Complete the tutorial* 등의 의미 있는 텍스트를 입력하고 **Save**를 클릭합니다.

    Azure에 호스트된 새 모바일 서비스에 POST 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. TodoItem 테이블에 저장된 항목이 모바일 서비스에서 반환된 후 데이터가 목록에 표시됩니다.

    <div class="dev-callout"> 
    <b>참고</b> 
    <p>모바일 서비스에 액세스하여 데이터를 쿼리 및 삽입하는 코드를 검토할 수 있습니다. 이 코드는 MainPage.xaml.cs 파일에 있습니다.</p> 
    </div>

![][3]

Azure에서 실행 중인 모바일 서비스에 대해 새 클라이언트 앱을 실행하는 방법을 보여 줍니다. 로컬 컴퓨터에서 실행 중인 모바일 서비스로 Windows Phone 앱을 테스트하려면 먼저 Windows Phone 장치 또는 에뮬레이터에서 액세스할 수 있도록 웹 서버와 방화벽을 구성해야 합니다. 자세한 내용은 [로컬 모바일 서비스에 연결할 수 있도록 로컬 웹 서버 구성][로컬 모바일 서비스에 연결할 수 있도록 로컬 웹 서버 구성]을 참조하십시오.

## <a name="next-steps"> </a>다음 단계

이제 빠른 시작을 완료했으며 모바일 서비스에서 중요한 추가 작업을 수행하는 방법을 알아보겠습니다.

-   [데이터 시작하기][데이터 시작하기]
    모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아보세요.

-   [오프라인 데이터 동기화 시작][오프라인 데이터 동기화 시작]
    오프라인 데이터 동기화를 활용하여 응답성과 견고성이 뛰어난 앱을 제작하는 방법을 살펴보십시오.

-   [인증 시작][인증 시작]
    ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

-   [푸시 알림 시작][푸시 알림 시작]
    기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

-   [모바일 서비스 .NET 백 엔드 문제 해결][모바일 서비스 .NET 백 엔드 문제 해결]
     모바일 서비스 .NET 백 엔드에서 발생할 수 있는 문제를 진단 및 해결하는 방법을 알아보십시오.

 
 


  [JavaScript 백 엔드 버전]: /ko-kr/documentation/articles/mobile-services-windows-phone-get-started
  [0]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png
  [Azure 무료 평가판]: http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=A30A4DDE2&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-phone-get-started%2F
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-portal-quickstart-wp8.png
  [2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-steps-wp8.png
  [Windows Phone 8 에뮬레이터에서 로컬 웹 서비스에 연결하는 방법]: http://go.microsoft.com/fwlink/p/?LinkId=391930
  [MobileServiceClient]: http://msdn.microsoft.com/ko-kr/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
  [3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-startup-wp8.png
  [로컬 모바일 서비스에 연결할 수 있도록 로컬 웹 서버 구성]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express
  [데이터 시작하기]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
  [오프라인 데이터 동기화 시작]: /ko-kr/documentation/articles/mobile-services-windows-phone-get-started-offline-data
  [인증 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
  [푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
  [모바일 서비스 .NET 백 엔드 문제 해결]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
