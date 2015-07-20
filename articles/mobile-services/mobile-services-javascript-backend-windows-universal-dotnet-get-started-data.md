<properties 
	pageTitle="기존 앱에 모바일 서비스 추가(Windows 유니버설) | 모바일 개발자 센터" 
	description="모바일 서비스를 사용하여 범용 Windows 앱에서 데이터를 활용하는 방법에 대해 알아봅니다." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/02/2015" 
	ms.author="glenga"/>

# 기존 앱에 모바일 서비스 추가

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##개요

이 항목은 Azure 모바일 서비스를 사용하여 범용 Windows 앱에서 데이터를 활용하는 방법을 보여 줍니다. 범용 Windows 앱 솔루션에는 Windows 스토어 8.1 및 Windows Phone 스토어 8.1 앱용 프로젝트와 공통 공유 프로젝트가 포함됩니다. 자세한 내용은 [Windows 및 Windows Phone을 대상으로 하는 범용 Windows 앱 작성](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx)을 참조하세요.

이 자습서에서는 데이터를 메모리에 저장하는 범용 Windows 앱용 Visual Studio 2013 프로젝트를 다운로드하여 새 모바일 서비스를 만들고 모바일 서비스를 앱과 통합한 후 Azure 관리 포털에 로그인하여 앱을 실행할 때 데이터에 적용된 변경 내용을 확인합니다.

>[AZURE.NOTE]이 항목에서는 Visual Studio Professional 2013 업데이트 3의 도구를 사용하여 새 모바일 서비스를 범용 Windows 앱에 연결하는 방법을 보여 줍니다. 같은 단계를 사용하여 모바일 서비스를 Windows 스토어 또는 Windows Phone 스토어 8.1 앱에 연결할 수 있습니다. 모바일 서비스를 Windows Phone 8.0 또는 Windows Phone Silverlight 8.1 앱에 연결하려면 [Windows Phone에서 데이터 시작](mobile-services-windows-phone-get-started-data.md)을 참조하세요.

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-kr%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-dotnet-get-started-data%2F)을 참조하십시오.
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Express 2013 for Windows</a>(Update 2 이상 버전). 

##<a name="download-app"></a>GetStartedWithData 프로젝트 다운로드

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../../includes/mobile-services-windows-universal-dotnet-download-project.md)]
 

##<a name="create-service"></a>Visual Studio에서 새 모바일 서비스 만들기

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="7"><li><p>솔루션 탐색기의 GetStartedWithData 프로젝트 폴더에서 App.xaml.cs 코드 파일을 열고 Windows 스토어 앱 조건부 컴파일 블록에서 <strong>App</strong> 클래스에 추가된 새 정적 필드를 확인합니다. 이 필드는 다음 예제와 같이 표시됩니다.</p> 

		<pre><code>public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
		    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
		        "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		</code></pre>

	<p>이 코드는 <a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">MobileServiceClient 클래스</a>(영문) 인스턴스를 사용하여 앱에서 새 모바일 서비스에 액세스할 수 있게 합니다. 클라이언트는 새 모바일 서비스의 URI 및 응용 프로그램 키를 제공하여 만듭니다. 이 정적 필드는 앱의 모든 페이지에서 사용할 수 있습니다.</p>
</li>
<li><p>Windows Phone 앱 프로젝트를 마우스 오른쪽 단추로 클릭하고 <strong>추가</strong>, <strong>연결된 서비스...</strong>를 차례로 클릭한 후에 방금 만든 모바일 서비스를 선택하고 <strong>확인</strong>을 클릭합니다. </p>
<p>같은 코드가 공유 App.xaml.cs 파일에도 추가되지만 이번에는 Windows Phone 앱 조건부 컴파일 블록 내에 추가됩니다.</p></li>
</ol>

이제 Windows 스토어 및 Windows Phone 스토어 앱이 모두 새 모바일 서비스에 연결되었습니다. 다음 단계에서는 모바일 서비스에서 새 TodoItem 테이블을 만듭니다.

##<a name="add-table"></a>모바일 서비스에 새 테이블 추가

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../../includes/mobile-services-create-new-table-vs2013.md)]

##<a name="update-app"></a>모바일 서비스를 사용하도록 앱 업데이트

[AZURE.INCLUDE [mobile-services-windows-dotnet-update-data-app](../../includes/mobile-services-windows-dotnet-update-data-app.md)]

##<a name="test-azure-hosted"></a>Azure에서 호스트되는 모바일 서비스 테스트

이제 Azure에서 호스트되는 모바일 서비스에 대해 범용 Windows 앱의 두 버전을 모두 테스트할 수 있습니다.

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../../includes/mobile-services-windows-universal-test-app.md)]

<ol start="4">
<li><p><a href="https://manage.windowsazure.com/" target="_blank">관리 포털</a>에서 <strong>모바일 서비스</strong>를 클릭한 후 해당 모바일 서비스를 클릭합니다.<p></li>
<li><p><strong>데이터</strong> 탭을 클릭한 후 <strong>찾아보기</strong>를 클릭합니다.</p>
<p>이제 <strong>TodoItem</strong> 테이블에 모바일 서비스에서 생성된 id 값을 가진 데이터가 포함되었으며 해당 열이 앱의 TodoItem 클래스와 일치하도록 테이블에 자동으로 추가되었습니다.</p></li>
</ol>
이제 자습서가 완료되었습니다.

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 범용 Windows 앱이 모바일 서비스의 데이터를 사용하도록 설정하기 위한 기본 사항에 대해 설명했습니다. 다음으로 다른 항목 중 하나를 읽는 것이 좋습니다.

* [인증 시작] <br/>앱 사용자를 인증하는 방법을 알아봅니다.

* [푸시 알림 시작] <br/>기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

* [모바일 서비스 C# 방법 개념 참조](mobile-services-windows-dotnet-how-to-use-client-library.md) <br/>.NET과 함께 모바일 서비스를 사용하는 방법을 자세히 알아봅니다.
  
<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Create the mobile service from Visual Studio]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use the mobile service]: #update-app
[Test the app against Mobile Services]: #test-app
[View uploaded data in the Azure Management Portal]: #view-data
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Validate and modify data with scripts]: ../mobile-services-windows-store-dotnet-validate-modify-data-server-scripts.md
[Refine queries with paging]: ../mobile-services-windows-store-dotnet-add-paging-data.md
[Get started with Mobile Services]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md
[Get started with data]: ../mobile-services-windows-store-dotnet-get-started-data.md
[인증 시작]: ../mobile-services-windows-store-dotnet-get-started-users.md
[푸시 알림 시작]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkID=510826

[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030
 

<!---HONumber=July15_HO2-->