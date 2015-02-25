<properties pageTitle="데이터 시작(Windows Universal) | 모바일 개발자 센터" description="모바일 서비스를 사용하여 범용 Windows 앱에서 데이터를 활용하는 방법에 대해 알아봅니다." services="mobile-services" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/26/2014" ms.author="glenga"/>

# 기존 앱에 모바일 서비스 추가(영문)

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

이 항목에서는 Azure 모바일 서비스를 사용하여 범용 Windows 앱에서 데이터를 활용하는 방법을 보여 줍니다. 범용 Windows 앱 솔루션에는 Windows 스토어 8.1 및 Windows Phone 스토어 8.1 앱용 프로젝트와 공통 공유 프로젝트가 포함됩니다. 자세한 내용은 [Windows 및 Windows Phone을 대상으로 하는 범용 Windows 앱 작성](http://msdn.microsoft.com/ko-kr/library/windows/apps/xaml/dn609832.aspx)을 참조하세요.

이 자습서에서는 데이터를 메모리에 저장하는 범용 Windows 앱용 Visual Studio 2013 프로젝트를 다운로드하여 새 모바일 서비스를 만들고 모바일 서비스를 앱과 통합한 후 Azure 관리 포털에 로그인하여 앱을 실행할 때 데이터에 적용된 변경 내용을 확인합니다.

>[AZURE.NOTE]이 항목에서는 Visual Studio Professional 2013 업데이트 3의 도구를 사용하여 새 모바일 서비스를 범용 Windows 앱에 연결하는 방법을 보여 줍니다. 같은 단계를 사용하여 모바일 서비스를 Windows 스토어 또는 Windows Phone 스토어 8.1 앱에 연결할 수 있습니다. 모바일 서비스를 Windows Phone 8.0 또는 Windows Phone Silverlight 8.1 앱에 연결하려면 [Windows Phone에서 데이터 시작](/ko-kr/documentation/articles/mobile-services-windows-phone-get-started-data)을 참조하세요.

> Visual Studio Professional 2013 업데이트 3으로 업그레이드할 수 없거나 Windows 스토어 앱 솔루션에 모바일 서비스 프로젝트를 수동으로 추가하려는 경우에는 항목의 [이 버전](/ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-data)을 참조하세요.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1. [Windows 스토어 앱 프로젝트 다운로드][Windows 스토어 앱 가져오기] 
2. [Visual Studio에서 모바일 서비스 만들기]
3. [저장소로 사용할 데이터 테이블 추가]
4. [모바일 서비스를 사용하도록 앱 업데이트]
5. [모바일 서비스에 대해 앱 테스트]
6. [Azure 관리 포털에서 업로드된 데이터 확인]

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://azure.microsoft.com/ko-kr/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-kr%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-dotnet-get-started-data%2F)을 참조하세요.
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Express 2013 for Windows</a>(업데이트 2 이상 버전) 


##<a name="download-app"></a>GetStartedWithData 프로젝트 다운로드

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]
 

##<a name="create-service"></a>Visual Studio에서 새 모바일 서비스 만들기

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="8"><li><p>솔루션 탐색기의 GetStartedWithData 프로젝트 폴더에서 App.xaml.cs 코드 파일을 열고 Windows 스토어 앱 조건부 컴파일 블록에서 <strong>App</strong> 클래스에 추가된 새 정적 필드를 확인합니다. 이 필드는 다음 예제와 같이 표시됩니다.</p> 

		<pre><code>public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
		    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
		        "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		</code></pre>

	<p>이 코드는 <a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">MobileServiceClient 클래스</a> 인스턴스를 사용하여 앱에서 새 모바일 서비스에 액세스할 수 있게 합니다. 클라이언트는 새 모바일 서비스의 URI 및 응용 프로그램 키를 제공하여 만듭니다. 이 정적 필드는 앱의 모든 페이지에서 사용할 수 있습니다.</p>
</li>
<li><p>Windows Phone 앱 프로젝트를 마우스 오른쪽 단추로 클릭하고 <strong>추가</strong>, <strong>연결된 서비스...</strong>를 차례로 클릭한 후에 방금 만든 모바일 서비스를 선택하고 <strong>확인</strong>을 클릭합니다. </p>
<p>같은 코드가 공유 App.xaml.cs 파일에도 추가되지만 이번에는 Windows Phone 앱 조건부 컴파일 블록 내에 추가됩니다.</p></li>
</ol>

이제 Windows 스토어 및 Windows Phone 스토어 앱이 모두 새 모바일 서비스에 연결되었습니다. 다음 단계에서는 모바일 서비스에서 새 TodoItem 테이블을 만듭니다.

##<a name="add-table"></a>모바일 서비스에 새 테이블 추가

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

>[AZURE.NOTE]새 테이블은 Id, __createdAt, __updatedAt 및 __version 열을 사용하여 만들어집니다. 동적 스키마가 사용하도록 설정된 경우 모바일 서비스에서 삽입 또는 업데이트 요청의 JSON 개체를 기준으로 새 열을 자동으로 생성합니다. 자세한 내용은 [동적 스키마](http://msdn.microsoft.com/ko-kr/library/windowsazure/jj193175.aspx)를 참조하세요.

#<a name="update-app"></a>모바일 서비스를 사용하도록 앱 업데이트

[AZURE.INCLUDE [mobile-services-windows-dotnet-update-data-app](../includes/mobile-services-windows-dotnet-update-data-app.md)]

##<a name="test-azure-hosted"></a>Azure에서 호스트되는 모바일 서비스 테스트

이제 Azure에서 호스트되는 모바일 서비스에 대해 범용 Windows 앱의 두 버전을 모두 테스트할 수 있습니다.

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

<ol start="4">
<li><p><a href="https://manage.windowsazure.com/" target="_blank">관리 포털</a>에서 <strong>모바일 서비스</strong>를 클릭한 후 해당 모바일 서비스를 클릭합니다.<p></li>
<li><p><strong>데이터</strong> 탭을 클릭한 후 <strong>찾아보기</strong>를 클릭합니다.</p>
<p>이제 <strong>TodoItem</strong> 테이블에 모바일 서비스에서 생성된 id 값을 가진 데이터가 포함되었으며 해당 열이 앱의 TodoItem 클래스와 일치하도록 테이블에 자동으로 추가되었습니다.</p></li>
</ol>

![](./media/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data/mobile-todoitem-data-browse.png)
     	
이제 **데이터 시작 자습서**를 마쳤습니다.

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 범용 Windows 앱이 모바일 서비스의 데이터를 사용하도록 설정하기 위한 기본 사항에 대해 설명했습니다. 다음에는 이 자습서에서 만든 GetStartedWithData 앱을 기반으로 하는 다음 자습서 중 하나를 완료해보세요.

* [스크립트를 사용하여 데이터 유효성 검사 및 수정]
  <br/>모바일 서비스에서 서버 스크립트를 사용하여 앱에서 전송된 데이터의 유효성을 검사하고 변경하는 방법에 대해 자세히 알아봅니다.

* [페이징을 사용하여 쿼리 구체화]
  <br/>쿼리에 페이징을 사용하여 단일 요청으로 처리되는 데이터 양을 제어하는 방법을 알아봅니다.

데이터 계열을 완료한 후에는 다음 자습서 중 하나를 시도해 보세요.

* [인증 시작](영문)
  <br/>앱 사용자를 인증하는 방법을 알아봅니다.

* [푸시 알림 시작] 
  <br/>앱에 기본적인 푸시 알림을 보내는 방법을 알아봅니다.

* [모바일 서비스 .NET 방법 개념 참조]
  <br/>모바일 서비스를 .NET과 함께 사용하는 방법에 대해 알아봅니다.
  
<!-- Anchors. -->

[Windows 스토어 앱 다운로드]: #download-app
[Visual Studio에서 모바일 서비스 만들기]: #create-service
[저장소로 사용할 데이터 테이블 추가]: #add-table
[모바일 서비스를 사용하도록 앱 업데이트]: #update-app
[모바일 서비스에 대해 앱 테스트]: #test-app
[Azure 관리 포털에서 업로드된 데이터 확인]: #view-data
[다음 단계]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[스크립트를 사용하여 데이터 유효성 검사 및 수정]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/
[페이징을 사용하여 쿼리 구체화]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data/
[모바일 서비스 시작](영문): /ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started/
[데이터 시작]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[인증 시작](영문): /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
[모바일 서비스 .NET 방법 개념 참조]: /ko-kr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/

[Azure 관리 포털]: https://manage.windowsazure.com/
[관리 포털]: https://manage.windowsazure.com/
[모바일 서비스 SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[개발자 코드 샘플 사이트](영문):  http://go.microsoft.com/fwlink/p/?LinkID=510826

[MobileServiceClient 클래스](영문): http://go.microsoft.com/fwlink/p/?LinkId=302030


<!--HONumber=42-->
