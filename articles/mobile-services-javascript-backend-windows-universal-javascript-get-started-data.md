<properties urlDisplayName="Get Started with Data" pageTitle="데이터 작업 시작(Windows 스토어 JavaScript) | 모바일 개발자 센터" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store JavaScript app." metaCanonical="https://www.windowsazure.com/ko-kr/develop/mobile/tutorials/get-started-with-data-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />


# 기존 앱에 모바일 서비스 추가
[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

이 항목에서는 Azure 모바일 서비스를 사용하여 범용 Windows 앱에서 데이터를 활용하는 방법을 보여 줍니다. 범용 Windows 앱 솔루션에는 Windows 스토어 8.1 및 Windows Phone 스토어 8.1 앱용 프로젝트와 공통 공유 프로젝트가 포함됩니다. 자세한 내용은 [Windows 및 Windows Phone을 대상으로 하는 범용 Windows 앱 작성](http://msdn.microsoft.com/ko-kr/library/windows/apps/xaml/dn609832.aspx)을 참조하세요.

이 자습서에서는 데이터를 메모리에 저장하는 범용 Windows 앱용 Visual Studio 2013 프로젝트를 다운로드하여 새 모바일 서비스를 만들고 모바일 서비스를 앱과 통합한 후 Azure 관리 포털에 로그인하여 앱을 실행할 때 데이터에 적용된 변경 내용을 확인합니다.

>[WACOM.NOTE]이 항목에서는 Visual Studio Express 2013 for Windows 업데이트 3의 도구를 사용하여 새 모바일 서비스를 범용 Windows 앱에 연결하는 방법을 보여 줍니다. 같은 단계를 사용하여 모바일 서비스를 Windows 스토어 또는 Windows Phone 스토어 8.1 앱에 연결할 수 있습니다. 모바일 서비스를 Windows Phone 8.0 또는 Windows Phone Silverlight 8.1 앱에 연결하려면 [Windows Phone에서 데이터 시작](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data)을 참조하세요.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1. [Windows 스토어 앱 프로젝트 다운로드][Windows 스토어 앱 가져오기] 
2. [모바일 서비스 만들기]
3. [저장소로 사용할 데이터 테이블 추가]
4. [모바일 서비스를 사용하도록 앱 업데이트]
5. [모바일 서비스에 대해 앱 테스트]

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://azure.microsoft.com/ko-kr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-kr%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-javascript-get-started-data%2F)을 참조하세요.
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Express 2013 for Windows</a> 업데이트 2 이상 버전 

##<a name="download-app"></a>GetStartedWithData 프로젝트 다운로드

[WACOM.INCLUDE [mobile-services-windows-universal-javascript-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]

##<a name="create-service"></a>Visual Studio에서 새 모바일 서비스 만들기

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="8">
<li><p>솔루션 탐색기에서 <strong>services\mobileService\scripts</strong> 하위 폴더로 이동하여 service.js 스크립트 파일을 연 다음 새 전역 변수를 확인합니다. 이 변수는 다음 예제와 같이 표시됩니다.</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>이 코드는 전역 변수를 사용하여 앱에서 새 모바일 서비스에 액세스할 수 있게 합니다. 클라이언트는 새 모바일 서비스의 URI 및 응용 프로그램 키를 제공하여 만듭니다. 이 스크립트에 대한 참조가 default.html 파일에 추가되었으므로 이 페이지에서 참조된 모든 스크립트 파일도 이 변수를 사용할 수 있습니다.</p>
</li>
<li><p>default.html 프로젝트 파일을 열고 새 service.js 스크립트 파일에 대한 참조를 찾은 다음 참조되는 경로가 다음과 같은지 확인합니다.</p>
<pre><code>&lt;script src="/services/mobileServices/scripts/todolist.js"&gt;</script></code></pre>
<p>현재 Visual Studio에는 경로에 잘못된 폴더 이름을 생성하는 버그가 있습니다.</p></li>
<li><p>Windows Phone 앱 프로젝트를 마우스 오른쪽 단추로 클릭하고 <strong>추가<strong>, </strong>연결된 서비스...<strong>를 차례로 클릭한 후에 방금 만든 모바일 서비스를 선택하고 </strong>확인</strong>을 클릭합니다. </p>
<p>같은 새 코드 파일이 Windows Phone 스토어 앱 프로젝트에 추가됩니다. 여기서도 default.html 파일에 추가된 참조 경로를 수정해야 합니다.</p></li>
</ol>

##<a name="add-table"></a>모바일 서비스에 새 테이블 추가

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

>[WACOM.NOTE]새 테이블은 Id, __createdAt, __updatedAt 및 __version 열을 사용하여 만들어집니다. 동적 스키마가 사용하도록 설정된 경우 모바일 서비스에서 삽입 또는 업데이트 요청의 JSON 개체를 기준으로 새 열을 자동으로 생성합니다. 자세한 내용은 [동적 스키마](http://msdn.microsoft.com/ko-kr/library/windowsazure/jj193175.aspx)를 참조하세요.

#<a name="update-app"></a>모바일 서비스를 사용하도록 앱 업데이트

[WACOM.INCLUDE [mobile-services-windows-javascript-update-data-app](../includes/mobile-services-windows-javascript-update-data-app.md)]

##<a name="test-azure-hosted"></a>Azure에서 호스트되는 모바일 서비스 테스트

이제 Azure에서 호스트되는 모바일 서비스에 대해 범용 Windows 앱의 두 버전을 모두 테스트할 수 있습니다.

[WACOM.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

<ol start="4">
<li><p><a href="https://manage.windowsazure.com/" target="_blank">관리 포털</a>에서 <strong>모바일 서비스</strong>를 클릭한 후 해당 모바일 서비스를 클릭합니다.<p></li>
<li><p><strong>데이터</strong> 탭을 클릭한 후 <strong>찾아보기</strong>를 클릭합니다.</p>
<p>이제 <strong>TodoItem</strong> 테이블에 모바일 서비스에서 생성된 ID 값을 가진 데이터가 포함되었으며 해당 열이 앱의 TodoItem 클래스와 일치하도록 테이블에 자동으로 추가되었습니다.</p></li>
</ol>

![](./media/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data/mobile-todoitem-data-browse.png)
     	
이제 **데이터 작업 시작** 자습서를 마쳤습니다.

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 범용 Windows 앱이 모바일 서비스의 데이터를 사용하도록 설정하기 위한 기본 사항에 대해 설명했습니다. 이제 이 자습서에서 만든 GetStartedWithData 앱을 기반으로 하는 다음 자습서 중 하나를 완료하세요.

* [스크립트를 사용하여 데이터 유효성 검사 및 수정]
  <br/>모바일 서비스에서 서버 스크립트를 사용하여 앱에서 전송된 데이터의 유효성을 검사하고 데이터를 변경하는 방법에 대해 알아봅니다.

* [페이징을 사용하여 쿼리 구체화]
  <br/>쿼리에 페이징을 사용하여 단일 요청으로 처리되는 데이터 양을 제어하는 방법을 알아봅니다.

데이터 시리즈를 완료한 후에는 다음 자습서 중 하나를 시도해 보세요.

* [인증 시작]
  <br/>앱 사용자를 인증하는 방법을 알아봅니다.

* [푸시 알림 시작] 
  <br/>매우 기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

* [모바일 서비스 HTML/JavaScript 방법 개념 참조]
  <br/>모바일 서비스를 HTML 및 JavaScript와 함께 사용하는 방법을 알아봅니다.

<!-- Anchors. -->

[Windows 스토어 앱 가져오기]: #download-app
[모바일 서비스 만들기]: #create-service
[저장소로 사용할 데이터 테이블 추가]: #add-table
[모바일 서비스를 사용하도록 앱 업데이트]: #update-app
[모바일 서비스에 대해 앱 테스트]: #test-app
[다음 단계]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-quickstart-startup.png

[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png


<!-- URLs. -->
[스크립트를 사용하여 데이터 유효성 검사 및 수정]: /ko-kr/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
[페이징을 사용하여 쿼리 구체화]: /ko-kr/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
[모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started/
[데이터 작업 시작]: /ko-kr/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[인증 시작]: /ko-kr/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/

[Azure 관리 포털]: https://manage.windowsazure.com/
[관리 포털]: https://manage.windowsazure.com/
[모바일 서비스 SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[개발자 코드 샘플 사이트]:  http://go.microsoft.com/fwlink/p/?LinkID=510826
[모바일 서비스 HTML/JavaScript 방법 개념 참조]: /ko-kr/documentation/articles/mobile-services-html-how-to-use-client-library/
