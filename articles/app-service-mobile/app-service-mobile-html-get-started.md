<properties
	pageTitle="HTML/JavaScript 앱용 모바일 앱 백 엔드로 시작 | Azure 앱 서비스 모바일 앱"
	description="이 자습서에 따라 HTML5 및 JavaScript로 웹앱용 Azure 모바일 앱 백 엔드를 사용하여 시작합니다."
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html5"
	ms.devlang="javascript"
	ms.topic="get-started-article"
	ms.date="11/18/2015"
	ms.author="glenga"/>


#HTML 앱 만들기

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]&nbsp; <!--- [AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]-->

>[AZURE.IMPORTANT]Azure 포털에서 HTML/JavaScript 앱에 대한 빠른 시작이 일시적으로 제거되었기 때문에 이 항목은 현재 모바일 앱에서 지원되지 않습니다. 가까운 미래에 다시 적용할 예정입니다. 모바일 앱 미리 보기를 완료하도록 기다려 주셔서 감사합니다.

##개요

이 자습서에서는 HTML5/JavaScript 웹앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다. 자세한 내용은 [모바일 앱 정의](app-service-mobile-value-prop.md)를 참조하세요.

완성된 앱의 스크린샷은 다음과 같습니다.

![완성된 앱의 스크린샷](./media/app-service-mobile-html-get-started/mobile-quickstart-completed-html.png)

먼저 이 자습서를 완료해야만 HTML 앱에 대한 다른 모든 모바일 앱 자습서를 학습할 수 있습니다.

##필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 앱을 가져올 수 있습니다. 이러한 앱은 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 [Azure 무료 체험](http://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* [Visual Studio Community 2013] 이상 버전

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 체험](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile)으로 이동합니다. 여기서 앱 서비스에 단기 시작 모바일 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

##새 모바일 앱 백 엔드 만들기

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## 서버 프로젝트 다운로드

1. [Azure 포털]에서 **모두 찾아보기** > **웹앱**을 클릭한 다음 방금 만든 모바일 앱 백 엔드를 클릭합니다. 

2. 모바일 앱 백엔드에서 **모든 설정**을 클릭하고 **모바일 앱**에서 **빠른 시작** > **HTML/JavaScript**를 클릭합니다.

3. **서버 프로젝트 다운로드 및 실행**의 **새 앱 만들기**에서 **다운로드**를 클릭하고, 압축된 프로젝트 파일을 로컬 컴퓨터에 추출하고 Visual Studio에서 솔루션을 엽니다.

4. NuGet 패키지를 복원하려면 프로젝트를 빌드하십시오.

##서버 프로젝트에서 CORS 사용

CORS(크로스-원본 리소스 공유)는 웹 기반 앱이 어느 도메인 요청이 안전한지 표시하는 방법으로 브라우저에서 허용되어야 합니다. 모바일 앱 백 엔드에 액세스하는 모든 웹 사이트에 대한 CORS 항목을 추가해야 합니다. 표준 ASP.NET 웹 API 동작을 사용하여 CORS 설정을 제어합니다. 자세한 내용은 [ASP.NET 웹 API에서 크로스-원본 리소스 요청 사용](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api#enable-cors)을 참조하세요.

기본적으로 포털에서 다운로드하는 클라이언트 빠른 시작 프로젝트는 포트 8000의 로컬 호스트에서 실행됩니다. 이 때문에 다음에 서버 프로젝트에서 `http://localhost:8000`에 대한 CORS를 사용하도록 설정합니다.

1. 도구 메뉴의 Visual Studio에서 **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 클릭하고, Nuget.org를 **패키지 원본**으로 선택하고 콘솔 창에서 다음 명령을 실행합니다.
 
		Install-Package Microsoft.AspNet.WebApi.Cors  

2. App\_Start/Startup.MobileApp.cs 프로젝트 파일을 열고, 명령문을 사용하여 다음을 추가합니다.

		using System.Web.Http.Cors;

3. 다음으로, **HttpConfiguration**(*config*)이 만들어진 후 다음 코드를 **Startup.ConfigureMobileApp** 메소드에 추가합니다.

        // Enable CORS support for localhost port 8000, all headers and methods.
        var cors = new EnableCorsAttribute("http://localhost:8000", "*", "*");
        config.EnableCors(cors);

4. 업데이트를 저장합니다.

다음으로, Azure에 CORS 사용 프로젝트를 배포합니다.

##Azure에 서버 프로젝트 게시

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

##클라이언트 프로젝트 다운로드 및 실행

1. 모바일 앱 백 엔드용 블레이드로 돌아가서 **모든 설정**을 클릭하고 **모바일 앱**에서 **빠른 시작** > **HTML/JavaScript**를 클릭합니다. 

2.  **HTML/Javascript 프로젝트 다운로드 및 실행**의 **새 앱 만들기**에서 **다운로드**를 클릭하고, 압축된 프로젝트 파일을 로컬 컴퓨터에 저장합니다.

3. 압축된 프로젝트 파일을 저장한 위치로 이동하고, 컴퓨터에 파일의 압축을 풀고, **서버** 하위 폴더에서 다음 명령 파일 중 하나를 실행합니다.

	+ **launch-windows**(Windows 컴퓨터)
	+ **launch-mac.command**(Mac OS X 컴퓨터)
	+ **launch-linux.sh**(Linux 컴퓨터)

	> [AZURE.NOTE]Windows 컴퓨터의 경우 PowerShell에서 스크립트를 실행할 것인지 물으면 `R`을(를) 입력하세요. 인터넷에서 다운로드한 스크립트이므로 웹 브라우저에서 스크립트를 실행하지 말라는 경고가 나타날 수 있습니다. 이 경우 브라우저에서 스크립트 로드를 계속 진행하도록 요청해야 합니다.

	새로운 앱을 호스트할 수 있도록 로컬 컴퓨터에서 웹 서버가 시작됩니다.

4. 웹 브라우저에서 URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a>을 열어 앱을 시작합니다. 클라이언트 앱은 Azure에서 모바일 앱 백 엔드에 연결하도록 구성됩니다.

5. 앱에서 _Enter new task_에 **자습서 완료**와 같은 의미 있는 텍스트를 입력하고 **추가**를 클릭합니다.

   	![앱 실행](./media/app-service-mobile-html-get-started/mobile-quickstart-startup-html.png)

   	Azure에 호스트된 새 모바일 앱 백 엔드에 POST 요청이 전송됩니다. 요청에서 데이터가 모바일 앱 스키마의 TodoItem 테이블에 삽입됩니다. 테이블에 저장된 항목이 서비스에서 반환되고 그 데이터가 앱의 두 번째 열에 표시됩니다.

	> [AZURE.TIP]모바일 서비스에 액세스하는 코드를 검토하여 데이터를 쿼리 및 삽입할 수 있습니다(app.js 파일에 있음).

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Get started with authentication]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure 포털]: https://portal.azure.com/

[Visual Studio Community 2013]: https://www.visualstudio.com/downloads
 

<!---HONumber=Nov15_HO4-->