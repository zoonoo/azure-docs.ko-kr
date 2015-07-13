<properties
	pageTitle="Azure 앱 서비스 모바일 iOS 앱 시작"
	description="이 자습서에 따라 Azure 앱 서비스를 사용하여 iOS 개발을 시작할 수 있습니다."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="ysxu"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="get-started-article"
	ms.date="06/18/2015"
	ms.author="yuaxu"/>

# <a name="getting-started"> </a>iOS 앱 만들기

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]

이 자습서에서는 Azure 앱 서비스 모바일 앱을 사용하여 iOS 앱을 만드는 방법을 보여 줍니다. 새 모바일 앱 백 엔드와 앱 데이터를 저장하는 간단한 _할 일 모음_ 앱을 만듭니다. 자습서에서는 서버 쪽 논리에 .NET 및 Visual Studio를 사용합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 앱을 가져올 수 있습니다. 이러한 앱은 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 [Azure 무료 체험](http://azure.microsoft.com/pricing/free-trial/)을 참조하십시오.
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 체험](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile)으로 이동합니다. 여기서 앱 서비스에 단기 시작 모바일 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## <a name="create-new-service"> </a>새 모바일 앱 백 엔드 만들기

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## 새 iOS 앱 만들기

모바일 백 엔드를 만들었으므로 이제 Azure Preview 포털에서 쉬운 빠른 시작을 따라 모바일 앱 백 엔드에 연결할 새 앱을 만들거나 기존 앱을 수정할 수 있습니다.

1. Azure 포털에서 **모바일 앱**을 클릭한 다음 방금 만든 모바일 앱 백 엔드를 클릭합니다.

2. 블레이드 맨 위에서 클라이언트 추가를 클릭하고 iOS를 확장합니다.

	![][6]

	모바일 앱 백 엔드에 연결된 iOS 앱을 만드는 단계가 표시됩니다.

3. 아직 하지 않은 경우 로컬 컴퓨터나 가상 컴퓨터에 <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>을 다운로드하여 설치합니다.

4. [Xcode] v4.4 이상 버전 및 [Xamarin Studio]를 다운로드하고 설치합니다. 또한 Visual Studio용 Xamarin을 사용할 수도 있습니다.

5. **서비스를 다운로드하고 클라우드에 게시** 아래에서 **다운로드**를 클릭합니다.

 모바일 앱 백 엔드 및 모바일 앱 백 엔드에 연결된 샘플 _할 일 모음_ 응용 프로그램 둘 다에 대한 프로젝트가 포함된 솔루션이 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

6. 게시 프로필을 다운로드하고, 다운로드한 파일을 로컬 컴퓨터에 저장한 다음 저장 위치를 기록해 둡니다.

## 모바일 앱 테스트

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## 모바일 앱 게시

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## iOS 앱 실행

[AZURE.INCLUDE [app-service-mobile-ios-run-app-preview](../../includes/app-service-mobile-ios-run-app-preview.md)]



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/app-service-mobile-dotnet-backend-ios-get-started-preview/ios-quickstart.png

[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png

[Management Portal]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
 

<!---HONumber=62-->