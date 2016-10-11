<properties
	pageTitle="Xamarin.iOS 앱용 Azure 앱 서비스 모바일 앱 시작 | Microsoft Azure"
	description="이 자습서에 따라 모바일 앱을 사용하여 Xamarin.iOS 개발을 시작할 수 있습니다."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="10/01/2016"
	ms.author="adrianha"/>


#Xamarin.iOS 앱 만들기

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##개요

이 자습서에서는 Azure 모바일 앱 백 엔드를 사용하여 클라우드 기반 백 엔드 서비스를 Xamarin.iOS 모바일 앱에 추가하는 방법을 보여 줍니다. 새 모바일 앱 백 엔드와 앱 데이터를 Azure에 저장하는 간단한 _할 일 모음_ Xamarin.iOS 앱을 만듭니다.

이 자습서를 완료해야 Azure 앱 서비스에서 모바일 앱 기능을 사용하는 방법에 대한 다른 모든 Xamarin.iOS 자습서를 진행할 수 있습니다.

##필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 앱을 가져올 수 있습니다. 이러한 앱은 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하십시오.

* Xamarin이 포함된 Visual Studio입니다. 지침은 [Visual Studio 및 Xamarin을 위한 설치 및 설정](https://msdn.microsoft.com/library/mt613162.aspx)을 참조하세요.

* Xcode v7.0 이상 및 Xamarin Studio Community가 설치된 Mac입니다. [Visual Studio 및 Xamarin을 위한 설정 및 설치](https://msdn.microsoft.com/library/mt613162.aspx) 및 [Mac 사용자를 위한 설정, 설치 및 유효성 검사](https://msdn.microsoft.com/library/mt488770.aspx)(MSDN)를 참조하세요.

>[AZURE.NOTE] Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려는 경우 [앱 서비스 평가](https://tryappservice.azure.com/?appServiceName=mobile)로 이동하세요. 여기서 신용 카드와 약정 없이 앱 서비스에서 수명이 짧은 스타터 모바일 앱을 즉시 만들 수 있습니다.

## 새 Azure 모바일 앱 백 엔드 만들기

다음 단계에 따라 새 모바일 앱 백 엔드를 만드세요.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## 서버 프로젝트 구성

이제 모바일 클라이언트 응용 프로그램에서 사용할 수 있는 Azure 모바일 앱 백 엔드를 프로비저닝했습니다. 다음으로, 간단한 “할 일 목록” 백 엔드에 대한 서버 프로젝트를 다운로드하고 Azure에 게시합니다.

아래 단계에 따라 Node.js 또는 .NET 백 엔드를 사용하도록 서버 프로젝트를 구성합니다.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## Xamarin.iOS 앱 다운로드 및 실행

1. 브라우저 창에서 [Azure 포털]을 엽니다.

2. 모바일 앱의 설정 블레이드에서 **시작** > **Xamarin.iOS**를 클릭합니다. 3단계 아래에서 **새 앱 만들기**가 선택되어 있지 않으면 클릭합니다. 그런 다음 **다운로드** 단추를 클릭합니다.

  	모바일 앱에 연결된 클라이언트 응용 프로그램을 포함하는 프로젝트를 다운로드합니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

3. 다운로드한 프로젝트를 추출하고 Xamarin Studio(또는 Visual Studio)에서 엽니다.

	![][9]

	![][8]

4. F5 키를 눌러 프로젝트를 빌드하고 iPhone 에뮬레이터에서 앱을 시작합니다.

5. 앱에서 _Learn Xamarin_과 같은 의미 있는 텍스트를 입력한 후 더하기(**+**) 아이콘을 클릭합니다.

	![][10]

	Azure에 호스트된 새 모바일 앱 백 엔드에 POST 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. TodoItem 테이블에 저장된 항목이 모바일 앱 백 엔드에서 반환된 후 데이터가 목록에 표시됩니다.

>[AZURE.NOTE]모바일 앱 백 엔드에 액세스하여 QSTodoService.cs C# 파일에서 데이터를 쿼리 및 삽입하는 코드를 검토할 수 있습니다.

##다음 단계

* [앱에 인증 추가](app-service-mobile-xamarin-ios-get-started-users.md) <br/>ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

* [앱에 푸시 알림 추가](app-service-mobile-xamarin-ios-get-started-push.md) <br/>기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Azure 포털]: https://portal.azure.com/

<!---HONumber=AcomDC_1005_2016-->