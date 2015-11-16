<properties 
	pageTitle="기존 범용 Windows 스토어 앱에 모바일 서비스 추가 | Microsoft Azure" 
	description="모바일 서비스를 사용하여 Windows 스토어 앱에서 데이터를 활용하는 방법에 대해 알아봅니다." 
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
	ms.date="11/02/2015" 
	ms.author="glenga"/>

# 기존 앱에 모바일 서비스 추가

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##개요

이 항목에서는 Windows 스토어 앱용 백 엔드 데이터 소스로 Azure 모바일 서비스를 사용하는 방법을 보여 줍니다. 이 자습서에서는 데이터를 메모리에 저장하는 앱용 Visual Studio 2013 프로젝트를 다운로드하고, 새 모바일 서비스를 만들고, 모바일 서비스를 앱과 통합하고, 앱을 실행할 때 수행된 데이터 변경 내용을 확인합니다.

이 자습서에서 만들 모바일 서비스는 .NET 백 엔드 모바일 서비스입니다. .NET 백 엔드를 사용하면 모바일 서비스에서 서버 쪽 비즈니스 논리에 .NET 언어 및 Visual Studio를 사용할 수 있으며, 로컬 컴퓨터에서 모바일 서비스를 실행 및 디버그할 수 있습니다. JavaScript에서 서버 쪽 비즈니스 논리를 작성하는 데 사용할 수 있는 모바일 서비스를 만들려면 이 항목의 JavaScript 백 엔드 버전을 참조하세요.

>[AZURE.NOTE]이 항목에서는 Visual Studio Professional 2013 업데이트 3의 도구를 사용하여 새 모바일 서비스를 범용 Windows 앱에 연결하는 방법을 보여 줍니다. 같은 단계를 사용하여 모바일 서비스를 Windows 스토어 또는 Windows Phone 스토어 8.1 앱에 연결할 수 있습니다. 모바일 서비스를 Windows Phone 8.0 또는 Windows Phone Silverlight 8.1 앱에 연결하려면 [Windows Phone에서 데이터 시작](mobile-services-dotnet-backend-windows-phone-get-started-data.md)을 참조하세요.

> Visual Studio Professional 2013 업데이트 3으로 업그레이드할 수 없거나 Windows 스토어 앱 솔루션에 모바일 서비스 프로젝트를 수동으로 추가하려는 경우에는 항목의 [이 버전](../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md)을 참조하세요.

##필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-dotnet-get-started-data%2F)을 참조하십시오.
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio 2013</a>(Update 3 이상 버전). 

##GetStartedWithData 프로젝트 다운로드

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../../includes/mobile-services-windows-universal-dotnet-download-project.md)]

##Visual Studio에서 새 모바일 서비스 만들기

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013](../../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md)]

&nbsp;&nbsp;7. 솔루션 탐색기의 GetStartedWithData 프로젝트 폴더에서 App.xaml.cs 코드 파일을 열고 Windows 스토어 앱 조건부 컴파일 블록에서 **App** 클래스에 추가된 새 정적 필드를 확인합니다. 이 필드는 다음 예제와 같이 표시됩니다.

	public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
	    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
	        "https://todolist.azure-mobile.net/",
	        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		

&nbsp;&nbsp;이 코드는 [MobileServiceClient](http://go.microsoft.com/fwlink/p/?LinkId=302030) 클래스 인스턴스를 사용하여 앱에서 새 모바일 서비스에 액세스할 수 있게 합니다. 클라이언트는 새 모바일 서비스의 URI 및 응용 프로그램 키를 제공하여 만듭니다. 이 정적 필드는 앱의 모든 페이지에서 사용할 수 있습니다.

&nbsp;&nbsp;8. Windows Phone 앱 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**, **연결된 서비스...**를 차례로 클릭한 후에 방금 만든 모바일 서비스를 선택하고 **확인**을 클릭합니다. 같은 코드가 공유 App.xaml.cs 파일에도 추가되지만 이번에는 Windows Phone 앱 조건부 컴파일 블록 내에 추가됩니다.

이제 Windows 스토어 및 Windows Phone 스토어 앱이 모두 새 모바일 서비스에 연결되었습니다. 다음 단계에서는 새 모바일 서비스 프로젝트를 테스트합니다.


##로컬에서 모바일 서비스 프로젝트 테스트

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]


##모바일 서비스를 사용하도록 앱 업데이트

이 섹션에서는 모바일 서비스를 응용 프로그램용 백 엔드 서비스로 사용하기 위해 범용 Windows 앱을 업데이트합니다. GetStartedWithData.Shared 프로젝트 폴더의 MainPage.xaml.cs 프로젝트 파일만 변경하면 됩니다.

[AZURE.INCLUDE [mobile-services-windows-dotnet-update-data-app](../../includes/mobile-services-windows-dotnet-update-data-app.md)]


##Azure에 모바일 서비스 게시

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]


##Azure에서 호스트되는 모바일 서비스 테스트

이제 Azure에서 호스트되는 모바일 서비스에 대해 범용 Windows 앱의 두 버전을 모두 테스트할 수 있습니다.

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../../includes/mobile-services-windows-universal-test-app.md)]

##SQL 데이터베이스에 저장된 데이터 확인

[AZURE.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../../includes/mobile-services-dotnet-backend-view-sql-data.md)]
 
이제 자습서가 완료되었습니다.

##다음 단계

이 자습서에서는 범용 Windows 앱 프로젝트가 모바일 서비스의 데이터를 사용하도록 설정하기 위한 기본 사항에 대해 설명했습니다. 다음으로 다른 항목 중 하나를 읽는 것이 좋습니다.

* [인증 시작] <br/>앱 사용자를 인증하는 방법을 알아봅니다.

* [푸시 알림 시작] <br/>기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

* [모바일 서비스 C# 방법 개념 참조](mobile-services-windows-dotnet-how-to-use-client-library.md) <br/>.NET과 함께 모바일 서비스를 사용하는 방법을 자세히 알아봅니다.


<!-- Images. -->



<!-- URLs. -->
[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refine queries with paging]: /develop/mobile/tutorials/add-paging-to-data-dotnet
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[인증 시작]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[푸시 알림 시작]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md

[Get started with offline data sync]: mobile-services-windows-store-dotnet-get-started-offline-data.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkID=510826
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md
[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  

<!---HONumber=Nov15_HO2-->