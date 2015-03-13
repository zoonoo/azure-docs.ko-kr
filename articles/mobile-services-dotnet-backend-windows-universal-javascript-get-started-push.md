<properties 
	pageTitle=".NET 백 엔드 모바일 서비스를 사용하여 푸시 알림 시작" 
	description="Azure 모바일 서비스와 알림 허브를 사용하여 범용 Windows 앱에 푸시 알림을 보내는 방법에 대해 알아봅니다." 
	services="mobile-services, notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/27/2014" 
	ms.author="glenga"/>


# 모바일 서비스 앱에 푸시 알림 추가

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

이 항목에서는 .NET 백 엔드와 함께 Azure 모바일 서비스를 사용하여 범용 Windows 앱에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 범용 Windows 앱 프로젝트에서 Azure 알림 허브를 사용하여 푸시 알림을 사용하도록 설정합니다. 이 작업을 완료하면 TodoList 테이블에 레코드를 삽입할 때마다 모바일 서비스가 .NET 백 엔드에서 Windows 스토어 및 Windows Phone 스토어에 등록된 모든 앱으로 푸시 알림을 보냅니다. 모바일 서비스를 통해 무료로 만드는 알림 허브는 모바일 서비스와 별도로 관리할 수 있으며, 다른 응용 프로그램과 서비스에서 도 사용 가능합니다.

>[AZURE.NOTE]이 항목에서는 Visual Studio Professional 2013 업데이트 3의 도구를 사용하여 모바일 서비스에서 범용 Windows 앱으로의 푸시 알림에 대한 지원을 추가하는 방법을 보여 줍니다. 같은 단계를 사용하여 모바일 서비스에서 Windows 스토어 또는 Windows Phone 스토어 8.1 앱으로의 푸시 알림을 추가할 수 있습니다. Visual Studio Professional 2013 업데이트 3으로 업그레이드할 수 없거나 Windows 스토어 앱 솔루션에 모바일 서비스 프로젝트를 수동으로 추가하려는 경우에는 항목의 [이 버전](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push)을 참조하세요.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1. [푸시 알림에 대해 앱 등록](#register)
2. [푸시 알림을 전송하도록 서비스 업데이트](#update-service)
3. [로컬 테스트에 푸시 알림 사용](#local-testing)
4. [앱에서 푸시 알림 테스트](#test)

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 [Microsoft 스토어 계정](http://go.microsoft.com/fwlink/p/?LinkId=280045)
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Professional 2013</a>(업데이트 3 이상 버전) <br/>무료 평가판을 이용할 수 있습니다. 

##<a id="register"></a>푸시 알림에 대해 앱 등록

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>다음에는 <code>\services\mobileServices\scripts</code> 프로젝트 폴더로 이동하여 생성된 &lt;<em>your_service_name</em>&gt;.push.register.js 스크립트 파일을 공유 <code>\js</code> 폴더에 복사한 다음 개별 Windows 및 WindowsPhone 앱 프로젝트 둘 다에서 이 파일을 삭제합니다.</p></li> 
<li><p>공유 <code>\js</code> 프로젝트 폴더에서 이 스크립트 파일을 열고 장치의 채널 URL을 알림 허브에 등록하는 <em>활성화된</em> 이벤트 수신기에서 코드를 찾은 다음 <strong>done</strong> promise 함수를 삭제합니다.</p>
<p>이 자습서에서는 사용자 지정 API를 호출할 때가 아니라 새 항목을 삽입할 때 알림을 보냅니다.</p></li>
<li><p>Windows 앱 프로젝트에서 default.html 파일을 열고 공유 <code>\js</code> 프로젝트 폴더에 대한 스크립트 파일 참조의 경로를 다음과 같이 변경합니다.</p><pre><code>&lt;script src="/js/your_service_name.push.register.js"&gt;&lt;/script&gt;</code></pre></li>
<li><p>WindowsPhone 앱 프로젝트에 대해 이 단계를 반복합니다.</p>
<p>이제 두 프로젝트가 모두 푸시 등록 스크립트의 공유 버전을 사용합니다.</p></li>
</ol>

이제 앱에서 푸시 알림을 사용하도록 설정했으므로 모바일 서비스가 푸시 알림을 보내도록 업데이트해야 합니다. 

##<a id="update-service"></a>푸시 알림을 전송하도록 서비스 업데이트

다음 단계를 수행하면 새 항목 삽입 시 등록된 모든 장치로 푸시 알림을 보내도록 기존 TodoItemController 클래스를 업데이트합니다. 모든 사용자 지정 [ApiController], [TableController] 또는 백 엔드 서비스의 모든 위치에서 도 비슷한 코드를 구현할 수 있습니다. 

[AZURE.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> 로컬 테스트에 푸시 알림 사용

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

이 섹션의 나머지 단계는 필요에 따라 수행할 수 있습니다. 이러한 단계를 통해 로컬 컴퓨터에서 실행되는 모바일 서비스에 대해 앱을 테스트할 수 있습니다. Azure에서 실행되는 모바일 서비스를 사용하여 푸시 알림을 테스트하려는 경우에는 마지막 섹션만 건너뛰면 됩니다. 푸시 알림 추가 마법사가 Azure에서 실행 중인 서비스에 연결하도록 앱을 이미 구성했기 때문입니다.  

>[AZURE.NOTE]테스트 및 개발 작업에는 프로덕션 모바일 서비스를 사용하지 말고, 항상 별도의 테스트용 스테이징 서비스에 모바일 서비스 프로젝트를 게시하세요.

<ol start="5">
<li><p>다음에는 <code>\services\mobileServices\settings</code> 프로젝트 폴더로 이동하여 생성된 &lt;<em>your_service_name</em>&gt;.js 스크립트 파일을 공유 <code>\js</code> 프로젝트 폴더에 복사한 다음 개별 Windows 및 WindowsPhone 앱 프로젝트 둘 다에서 이 파일을 삭제합니다. 또한 이 파일이 각 앱 프로젝트의 <code>\services\mobileServices\scripts</code> 폴더에도 있는 경우 여기서도 삭제합니다.</p></li> 
<li><p>공유되는 <code>\js</code> 프로젝트 폴더에서 이 스크립트 파일을 열고 Azure에서 실행되는 <a href="http://msdn.microsoft.com/library/azure/jj554219.aspx">MobileServiceClient object</a>를 정의하는 기존 코드를 주석 처리합니다.</p></li>
<li><p>같은 이름의 새 <strong>MobileServiceClient</strong> 개체 정의를 추가하되 다음과 같이 생성자에서 로컬 호스트의 URL을 사용합니다.</p>
<pre><code>// This MobileServiceClient has been configured to communicate with your local
// test project for debugging purposes.
var todolistClient = new WindowsAzure.MobileServiceClient(
	"http://localhost:4584");
</code></pre><p>이 <strong>MobileServiceClient</strong> 개체를 사용하는 경우 앱은 Azure에서 호스트되는 버전이 아닌 로컬 서비스에 연결합니다. 다시 이전과 같이 Azure에서 호스트되는 모바일 서비스에 대해 앱을 실행하려면 원래 <strong>MobileServiceClient</strong> 개체 정의로 다시 변경하면 됩니다.</p></li>
</ol>

##<a id="test"></a> 앱에서 푸시 알림 테스트

[AZURE.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 Windows 스토어 앱에서 모바일 서비스 및 알림 허브를 사용하여 푸시 알림을 보내도록 설정하기 위한 기본 사항에 대해 설명했습니다. 그다음에, 태그를 사용하여 모바일 서비스에서 인증된 사용자에게만 푸시 알림을 보내는 방법을 보여 주는 다음 자습서인 [인증된 사용자에게 푸시 알림 보내기]를 완료할 수 있습니다.

다음 항목에서 모바일 서비스 및 알림 허브에 대해 알아보세요.

* [데이터 시작]
  <br/>모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아봅니다.

* [인증 시작](영문)
  <br/>모바일 서비스를 사용하여 서로 다른 계정 유형의 앱 사용자를 인증하는 방법에 대해 알아봅니다.

* [알림 허브 정의]
  <br/>모든 주요 클라이언트 플랫폼에 걸쳐 알림 허브가 앱에 알림을 전달하는 방법에 대해 알아봅니다.

* [알림 허브 응용 프로그램 디버깅](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>알림 허브 솔루션 문제를 해결하고 솔루션을 디버그하기 위한 지침을 얻습니다. 

* [Azure 모바일 서비스용 HTML/JavaScript 클라이언트를 사용하는 방법]
  <br/>모바일 서비스를 HTML 및 JavaScript 앱과 함께 사용하는 방법을 자세히 알아보세요.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[앱 제출 페이지]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[내 응용 프로그램]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows용 Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[모바일 서비스 시작](영문): /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started
[데이터 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data
[인증 시작](영문): /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-users

[인증된 사용자에게 푸시 알림 보내기]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/

[알림 허브 정의]: /ko-kr/documentation/articles/notification-hubs-overview/

[Azure 모바일 서비스용 HTML/JavaScript 클라이언트를 사용하는 방법]: /ko-kr/documentation/articles/mobile-services-html-how-to-use-client-library


<!--HONumber=42-->
