<properties pageTitle="푸시 알림 시작(Windows 스토어) | 모바일 개발자 센터" metaKeywords="" description="Azure 모바일 서비스를 사용하여 Windows 스토어 앱에 푸시 알림(레거시 푸시)을 보내는 방법에 대해 알아봅니다." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/11/2014" ms.author="glenga" />

# 모바일 서비스 앱에 푸시 알림 추가(레거시 푸시)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#" class="current">Windows 스토어 C#</a>
    <a href="/ko-kr/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows 스토어 JavaScript</a>
    <a href="/ko-kr/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/ko-kr/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/ko-kr/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/ko-kr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/ko-kr/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
	<a href="/ko-kr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title=".NET backend">.NET 백 엔드</a> | <a href="/ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/"  title="JavaScript backend" class="current">JavaScript 백 엔드</a></div>	

이 항목에서는 Visual Studio 2013에서 Azure 모바일 서비스를 사용하여 Windows 스토어 앱에 푸시 알림을 전송하는 방법에 대해 설명합니다. 이 자습서에서는 WNS(Windows 푸시 알림 서비스)를 사용하여 Visual Studio에서 직접 quickstart 프로젝트에 푸시 알림을 추가합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 푸시 알림을 보냅니다.

>[WACOM.NOTE]이 항목에서는 아직 알림 허브 통합을 사용하도록 <em>아직 업그레이드되지 않은</em> <em>기존</em> 모바일 서비스를 지원합니다. <em>새</em> 모바일 서비스를 만드는 경우 이 통합 기능이 자동으로 사용하도록 설정됩니다. 새 모바일 서비스의 경우 [푸시 알림 시작](/ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/)을 참조하세요.
>
>모바일 서비스가 Azure 알림 허브와 통합되어 템플릿, 다중 플랫폼, 향상된 확장 등 추가적인 푸시 알림 기능을 지원합니다. <em>가능한 경우 알림 허브를 사용하도록 기존 모바일 서비스를 업그레이드해야 합니다</em>. 업그레이드한 후에는 [푸시 알림 시작](/ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/)을 참조하세요.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1. [푸시 알림을 위한 앱 등록 및 모바일 서비스 구성]
2. [생성된 푸시 알림 코드 업데이트]
3. [알림을 받기 위한 데이터 삽입]

이 자습서는 모바일 서비스 빠른 시작을 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작] 또는 [데이터 작업 시작]를 완료하여 프로젝트를 모바일 서비스에 연결해야 합니다. 모바일 서비스가 연결되지 않은 경우 푸시 알림 추가 마법사에서 대신 연결해줍니다. 

<h2><a name="register"></a>앱에서 푸시 알림 추가 및 구성</h2>

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p><strong>서비스</strong>, <strong>모바일 서비스</strong>, 서비스 이름을 차례로 확장하고, 생성된 코드 파일을 연 다음, 장치에 대한 설치 ID 및 채널을 가져와 이 데이터를 새로운 channels 테이블에 삽입하는 <strong>UploadChannel</strong> 메서드를 검토합니다.</p> 

<p>이 메서드에 대한 호출도 마법사에 의해 App.xaml.cs 코드 파일의 <strong>OnLaunched</strong> 이벤트 처리기에 추가됩니다. 그러면 앱이 실행될 때마다 장치 등록이 시도됩니다.</p></li> 
<li><p>서버 탐색기에서 <strong>Azure</strong>, <strong>모바일 서비스</strong>, 서비스 이름, <strong>channels</strong>를 차례로 확장하고 insert.js 파일을 엽니다.</p> 

<p>모바일 서비스에 저장되는 이 파일에는, 클라이언트가 channels 테이블에 데이터를 삽입하여 장치를 등록해달라는 요청을 전송할 때 실행되는 JavaScript 코드가 포함되어 있습니다.</p> 

<div class="dev-callout"><b>참고</b>
	<p>이 파일의 초기 버전에는 기존 장치 등록을 확인하는 코드가 포함되어 있습니다. channels 테이블에 새 등록이 추가될 때 푸시 알림을 전송하는 코드도 포함되어 있습니다. 푸시 알림을 전송하는 코드는 다른 등록된 스크립트 파일에 포함될 수도 있습니다. 이 스크립트의 위치는 알림이 트리거되는 방법에 따라 다릅니다. 테이블에 대한 삽입, 업데이트, 삭제 또는 읽기 작업에 대해 예약된 작업으로서 또는 사용자 지정 API로서 스크립트를 등록할 수 있습니다.  자세한 내용은 <a href="http://go.microsoft.com/fwlink/p/?LinkID=287178">모바일 서비스에서 서버 스크립트 작업</a>을 참조하세요.</p>
</div>
</li> 
<li><p>F5 키를 눌러 앱을 실행하고, 모바일 서비스에서 알림이 즉시 수신되는지 확인합니다.</p>
<p>이 알림은 새로운 channels 테이블에 행을 삽입하여(장치를 등록하여) 생성된 것입니다.</p>
</li>
</ol>
생성된 코드는 앱이 실행될 때 알림을 손쉽게 표시하는 데 도움이 되지만, 이것이 일반적으로 중요한 시나리오는 아닙니다. 다음 단계에서는 channels 테이블에서 알림 코드를 제거하고, TodoItem 테이블에서 변경된 내용으로 교체합니다. 

<h2><a name="update-scripts"></a>생성된 푸시 알림 코드 업데이트</h2>

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

<h2><a name="test"></a>앱에서 푸시 알림 테스트</h2>

1. Visual Studio에서 F5 키를 눌러 앱을 실행합니다.

2. 앱에서 **TodoItem 삽입**에 텍스트를 입력하고 **저장**을 클릭합니다.

   	![][13]

   	삽입 작업이 완료되면 앱이 WNS에서 푸시 알림을 받습니다.

   	![][14]

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 Windows 스토어 앱에서 모바일 서비스의 데이터로 작업하기 위한 기본 사항에 대해 설명했습니다. 이제 이 자습서에서 만든 GetStartedWithData 앱을 기반으로 하는 다음 자습서 중 하나를 완료하세요.

+ [알림 허브 시작]
  <br/>Windows 스토어 앱에서 알림 허브를 활용하는 방법을 알아봅니다.

+ [구독자에게 알림 보내기]
	<br/>사용자가 관심 있는 범주에 대한 푸시 알림을 등록하고 수신하게 하는 방법을 알아봅니다.

+ [사용자에게 알림 보내기]
	<br/>모바일 서비스에서 장치와 상관없이 특정 사용자에게 푸시 알림을 보내는 방법을 알아봅니다.

+ [사용자에게 플랫폼 간 알림 보내기]
	<br/>템플릿을 사용하여 백 엔드에 플랫폼별 페이로드를 작성하지 않고도 모바일 서비스에서 푸시 알림을 보내도록 하는 방법을 알아봅니다.

다음의 모바일 서비스 항목에 대해서도 자세히 알아보세요.

* [데이터 작업 시작]
  <br/>모바일 서비스를 사용하여 데이터를 저장하고 쿼리하는 방법을 알아봅니다.

* [인증 시작]
  <br/>Windows 계정을 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

* [모바일 서비스 서버 스크립트 참조]
  <br/>서버 스크립트를 등록하고 사용하는 방법을 알아봅니다.

* [모바일 서비스 .NET 방법 개념 참조]
  <br/>모바일 서비스를 .NET과 함께 사용하는 방법에 대해 알아봅니다.

<!-- Anchors. -->
[푸시 알림을 위한 앱 등록 및 모바일 서비스 구성]: #register
[생성된 푸시 알림 코드 업데이트]: #update-scripts
[알림을 받기 위한 데이터 삽입]: #test
[다음 단계]:#next-steps

<!-- Images. -->











[13]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png




<!-- URLs. -->
[앱 제출 페이지]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[내 응용 프로그램]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows용 Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[모바일 서비스 시작]: /ko-kr/develop/mobile/tutorials/get-started/
[데이터 작업 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-data-dotnet/
[인증 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-users-dotnet
[푸시 알림 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-push-dotnet
[앱 사용자에 대한 푸시 알림]: /ko-kr/develop/mobile/tutorials/push-notifications-to-users-dotnet
[스크립트를 통해 사용자 권한 부여]: /ko-kr/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript 및 HTML]: /ko-kr/develop/mobile/tutorials/get-started-with-push-js

[Azure 관리 포털]: https://manage.windowsazure.com/
[wns 개체]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[모바일 서비스 .NET 방법 개념 참조]: /ko-kr/develop/mobile/how-to-guides/work-with-net-client-library/
[스크립트를 사용하여 데이터 유효성 검사 및 수정]: /ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[페이징을 사용하여 쿼리 구체화]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-dotnet
[알림 허브 시작]: /ko-kr/manage/services/notification-hubs/getting-started-windows-dotnet/
[알림 허브 정의]: /ko-kr/develop/net/how-to-guides/service-bus-notification-hubs/
[구독자에게 알림 보내기]: /ko-kr/manage/services/notification-hubs/breaking-news-dotnet/
[사용자에게 알림 보내기]: /ko-kr/manage/services/notification-hubs/notify-users/
[사용자에게 플랫폼 간 알림 보내기]: /ko-kr/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/?LinkId=262293
