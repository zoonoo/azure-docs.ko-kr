<properties
	pageTitle="Xamarin Android 앱용 Azure 모바일 앱 시작"
	description="이 자습서에 따라 Azure 모바일 앱을 사용하여 Xamarin Android 개발을 시작할 수 있습니다."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/12/2015"
	ms.author="normesta"/>

#Xamarin.Android 앱 만들기

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]
 
##개요

이 자습서는 Azure 모바일 앱 백 엔드를 사용하여 Xamarin Android 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다. 새 모바일 앱 백 엔드와 앱 데이터를 Azure에 저장하는 간단한 _할 일 모음_ Xamarin.Andorid 앱을 만듭니다.

완성된 앱의 스크린샷은 다음과 같습니다.

![][0]

이 자습서를 완료해야 다른 모든 Xamarin Android 앱용 모바일 앱 자습서를 진행할 수 있습니다.
 
##필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 앱을 가져올 수 있습니다. 이러한 앱은 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 [Azure 무료 체험](http://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
 
* [Visual Studio Community 2013] 이상 버전입니다. Visual Studio 커뮤니티 2013을 설치하는 경우 별도로 [Xamarin]을 설치합니다. Visual Studio 2015를 설치할 때 Xamarin 도구를 설치할 수 있습니다.
 
>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 체험](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile)으로 이동합니다. 여기서 앱 서비스에 단기 시작 모바일 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.


## 새 Azure 모바일 앱 백 엔드 만들기

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## 서버 프로젝트 다운로드

1. 사용자 PC에서 [Azure 포털]을 방문합니다. **모두 찾아보기** > **모바일 앱**을 클릭한 다음 방금 만든 모바일 앱 백 엔드를 클릭합니다.

2. 모바일 앱 블레이드에서 **설정**을 클릭하고 **모바일 앱**에서 **빠른 시작** > **Xamarin.Android**를 클릭합니다.
 
3. **서버 프로젝트 다운로드 및 실행**에서 **다운로드**를 클릭합니다. 귀하의 PC에 압축된 프로젝트 파일을 추출하고 Visual Studio에서 솔루션을 엽니다.
 
## 로컬로 백 엔드 프로젝트를 테스트

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Azure에 서버 프로젝트 게시

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Xamarin.Android 앱 다운로드 및 실행

1. **Xamarin.Android 프로젝트 다운로드 및 실행**에서 **다운로드** 단추를 클릭합니다.

  	모바일 앱에 연결된 클라이언트 응용 프로그램을 포함하는 프로젝트를 다운로드합니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

	![][8]

	![][9]

2. **F5** 키를 눌러 프로젝트를 빌드하고 앱을 시작합니다.

3. 앱에서 _자습서 완료_ 등의 의미 있는 텍스트를 입력한 후 **추가** 단추를 클릭합니다.

	![][10]

	Azure에 호스트된 새 모바일 앱 백 엔드에 POST 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. TodoItem 테이블에 저장된 항목이 모바일 앱 백 엔드에서 반환된 후 데이터가 목록에 나타납니다.

	> [AZURE.NOTE]모바일 앱 백 엔드에 액세스하여 데이터를 쿼리 및 삽입하는 코드를 검토할 수 있습니다. 이 코드는 ToDoActivity.cs C# 파일에 있습니다.

##다음 단계

* [앱에 인증 추가](app-service-mobile-dotnet-backend-xamarin-android-get-started-users-preview.md) <br/>ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.


<!-- Images. -->
[0]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure 포털]: https://azure.portal.com/
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
 
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!----HONumber=Sept15_HO1-->