<properties
	pageTitle="Xamarin.Forms을 사용하여 모바일 앱 시작"
	description="이 자습서에 따라 Azure 모바일 앱을 사용하여 Xamarin.Forms 개발을 시작할 수 있습니다."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="normesta"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="08/12/2015"
	ms.author="normesta"/>

#Xamarin.Forms 앱 만들기

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

##개요

이 자습서에서는 Azure 모바일 앱 백 엔드를 사용하여 클라우드 기반 백 엔드 서비스를 Xamarin.Forms 모바일 앱에 추가하는 방법을 보여 줍니다. 새 모바일 앱 백 엔드와 앱 데이터를 Azure에 저장하는 간단한 _할 일 모음_ Xamarin.Forms 앱을 만듭니다.

이 자습서를 완료해야 다른 모든 Xamarin.Forms용 모바일 앱 자습서를 진행할 수 있습니다.

##필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 앱을 가져올 수 있습니다. 이러한 앱은 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 [Azure 무료 체험](http://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
 
* [Visual Studio Community 2013] 이상 버전입니다. Visual Studio 커뮤니티 2013을 설치하는 경우 별도로 [Xamarin]을 설치합니다. Visual Studio 2015를 설치할 때 Xamarin 도구를 설치할 수 있습니다.

* [Xcode] v7.0 이상 및 [Xamarin Studio]가 설치된 Mac입니다.
 
     >[AZURE.NOTE]Visual Studio를 사용하여 Windows 컴퓨터에 앱을 빌드하려면 여전히 네트워크에 연결된 Mac에 액세스해야 합니다.
 
>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 체험](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile)으로 이동합니다. 여기서 앱 서비스에 단기 시작 모바일 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 새 Azure 모바일 앱 백 엔드 만들기

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## 서버 프로젝트 다운로드

1. 사용자 PC에서 [Azure 포털]을 방문합니다. **모두 찾아보기** > **모바일 앱**을 클릭한 다음 방금 만든 모바일 앱 백 엔드를 클릭합니다.

2. 모바일 앱 블레이드에서 **설정**을 클릭하고 **모바일 앱**에서 **빠른 시작** > **Xamarin.Forms**를 클릭합니다.
 
3. **서버 프로젝트 다운로드 및 실행**에서 **다운로드**를 클릭합니다. 귀하의 PC에 압축된 프로젝트 파일을 추출하고 Visual Studio에서 솔루션을 엽니다.
 
## 로컬로 백 엔드 프로젝트를 테스트

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Azure에 서버 프로젝트 게시

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

##Xamarin.Forms 솔루션 다운로드 및 실행

여기서 몇 가지를 선택합니다. Mac에 솔루션을 다운로드하고 Xamarin Studio에서 열 수 있습니다. 또는 Windows 컴퓨터에 솔루션을 다운로드하고 Visual Studio에서 열 수 있습니다. 또한 양쪽 환경 모두를 사용하고 서로 전환할 수 있습니다. 다음을 고려합니다.

* Mac에서 솔루션의 iOS 프로젝트를 쉽게 실행할 수 있습니다. 원하는 경우 Windows 컴퓨터에서 Visual Studio를 사용할 수 있지만 네트워크에 연결된 Mac에 연결해야 하기 때문에 좀 더 복잡합니다. 실행에 관심이 있다면 [Windows에서 Xamarin.iOS 설치]를 참조하세요.
* Mac 또는 Windows 컴퓨터에서 Android 프로젝트를 실행할 수 있습니다.
* Windows 컴퓨터에서 Visual Studio를 사용해야만 Windows 프로젝트를 실행할 수 있습니다.

이 점을 염두하고 시작하겠습니다.

 1. Mac 또는 Windows 컴퓨터의 브라우저 창에서 [Azure 포털]을 엽니다.
 2. **Xamarin.Forms 프로젝트 다운로드 및 실행**에서 **다운로드** 단추를 클릭합니다.

    모바일 앱에 연결된 클라이언트 응용 프로그램을 포함하는 프로젝트를 다운로드합니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

 3. 다운로드한 프로젝트를 추출하고 Xamarin Studio 또는 Visual Studio에서 엽니다.

	![][9]

	![][8]

###iOS 프로젝트 실행

####Xamarin Studio에서

1. iOS 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 클릭합니다.
2. **실행** 메뉴에서 **디버깅 시작**을 클릭하여 프로젝트를 빌드하고 iPhone 에뮬레이터에서 앱을 시작합니다.

####Visual Studio에서
1. iOS 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 클릭합니다.
2. **빌드** 메뉴에서 **구성 관리자**를 클릭합니다. 
3. **구성 관리자** 대화 상자에서 iOS 프로젝트의 **빌드** 및 **배포** 확인란을 선택합니다.
4. **F5** 키를 눌러 프로젝트를 빌드하고 iPhone 에뮬레이터에서 앱을 시작합니다.

앱에서 _Learn Xamarin_와 같은 의미 있는 텍스트를 입력한 후 더하기(**+**) 아이콘을 클릭합니다.

![][10]

Azure에 호스트된 새 모바일 앱 백 엔드에 POST 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. TodoItem 테이블에 저장된 항목이 모바일 앱 백 엔드에서 반환된 후 데이터가 목록에 표시됩니다.

> [AZURE.NOTE]솔루션의 이식 가능한 클래스 라이브러리 프로젝트의 ToDoActivity.cs C# 파일에서 모바일 앱 백 엔드에 액세스하는 코드를 찾을 수 있습니다.

###Android 프로젝트 실행

####Xamarin Studio에서

1. Android 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 클릭합니다.
2. **실행** 메뉴에서 **디버깅 시작**을 클릭하여 프로젝트를 빌드하고 Android 에뮬레이터에서 앱을 시작합니다.

####Visual Studio에서
1. Android 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 클릭합니다.
4. **빌드** 메뉴에서 **구성 관리자**를 클릭합니다. 
5. **구성 관리자** 대화 상자에서 Android 프로젝트의 **빌드** 및 **배포** 확인란을 선택합니다.
6. **F5** 키를 눌러 프로젝트를 빌드하고 Android 에뮬레이터에서 앱을 시작합니다.

앱에서 _Learn Xamarin_와 같은 의미 있는 텍스트를 입력한 후 더하기(**+**) 아이콘을 클릭합니다.

![][11]

Azure에 호스트된 새 모바일 앱 백 엔드에 POST 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. TodoItem 테이블에 저장된 항목이 모바일 앱 백 엔드에서 반환된 후 데이터가 목록에 표시됩니다.

> [AZURE.NOTE]솔루션의 이식 가능한 클래스 라이브러리 프로젝트의 ToDoActivity.cs C# 파일에서 모바일 앱 백 엔드에 액세스하는 코드를 찾을 수 있습니다.


###Windows 프로젝트 실행

####Visual Studio에서
1. Windows 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 클릭합니다.
4. **빌드** 메뉴에서 **구성 관리자**를 클릭합니다. 
5. **구성 관리자** 대화 상자에서 선택한 Windows 프로젝트의 **빌드** 및 **배포** 확인란을 선택합니다.
6. **F5** 키를 눌러 프로젝트를 빌드하고 Windows 에뮬레이터에서 앱을 시작합니다.

앱에서 _Learn Xamarin_와 같은 의미 있는 텍스트를 입력한 후 더하기(**+**) 아이콘을 클릭합니다.
	
Azure에 호스트된 새 모바일 앱 백 엔드에 POST 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. TodoItem 테이블에 저장된 항목이 모바일 앱 백 엔드에서 반환된 후 데이터가 목록에 표시됩니다.

![][12]
	
> [AZURE.NOTE]솔루션의 이식 가능한 클래스 라이브러리 프로젝트의 ToDoActivity.cs C# 파일에서 모바일 앱 백 엔드에 액세스하는 코드를 찾을 수 있습니다.

<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Get started with offline data sync]: app-service-mobile-xamarin-ios-get-started-offline-data-preview.md
[Get started with authentication]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-users.md
[Get started with push notifications]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure 포털]: https://portal.azure.com/
[JavaScript backend version]: ../partner-xamarin-mobile-services-ios-get-started.md
[Get started with data in app services using Visual Studio 2012]: ../app-service-mobile-windows-store-dotnet-get-started-data-vs2012-preview.md
[Troubleshoot a mobile app .NET backend]: ../app-service-mobile-dotnet-backend-how-to-troubleshoot-preview.md


[Xamarin Studio]: http://xamarin.com/download
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
[Windows에서 Xamarin.iOS 설치]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
 

<!---HONumber=August15_HO8-->