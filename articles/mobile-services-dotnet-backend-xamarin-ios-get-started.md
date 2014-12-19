<properties urlDisplayName="Get Started with Mobile Services for Xamarin iOS apps" pageTitle="Xamarin iOS 앱용 모바일 서비스 시작 - Azure 모바일 서비스" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Xamarin iOS development" metaCanonical="" services="" documentationCenter="Mobile" title="Get Started with Mobile Services for Xamarin iOS apps" authors="donnam" solutions="" manager="dwrede" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="11/11/2014" ms.author="donnam" />

# <a name="getting-started"> </a>모바일 서비스 시작

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

이 자습서는 Azure 모바일 서비스를 사용하여 Xamarin iOS 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다. 이 자습서에서는 새 모바일 서비스와 새 모바일 서비스에 앱 데이터를 저장하는 간단한 _할 일 모음_ 앱을 둘 다 만듭니다. 생성되는 모바일 서비스에서는 Visual Studio에서 지원되는 .NET 언어를 서버 쪽 비즈니스 논리와 모바일 서비스 관리에 사용합니다. JavaScript에서 서버 쪽 비즈니스 논리를 작성하는 데 사용할 수 있는 모바일 서비스를 만들려면 이 항목의 [JavaScript 백 엔드 버전]을 참조하세요.

>[WACOM.NOTE]이 항목에서는 Azure 관리 포털을 사용하여 새 모바일 서비스 프로젝트를 만드는 방법을 보여 줍니다. Visual Studio 2013 업데이트 2를 사용하면 기존 Visual Studio 솔루션에 새 모바일 서비스 프로젝트를 추가할 수도 있습니다. 자세한 내용은 [빠른 시작: 모바일 서비스 추가(.NET 백 엔드)](http://msdn.microsoft.com/ko-kr/library/windows/apps/dn629482.aspx)

완성된 앱의 스크린샷은 다음과 같습니다.

![][0]


이 자습서를 완료해야 다른 모든 Xamarin iOS 앱용 모바일 서비스 자습서를 진행할 수 있습니다. 

>[WACOM.NOTE]이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 서비스를 사용할 수 있습니다. 이러한 서비스는 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-ios-get-started" target="_blank">Azure 무료 평가판</a>을 참조하세요.<br />이 자습서를 완료하려면 <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>이 필요합니다. 무료 평가판을 이용할 수 있습니다.

## 새 모바일 서비스 만들기

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## 새 Xamarin iOS 앱 만들기

모바일 서비스를 만든 후 관리 포털에서 쉬운 빠른 시작을 따라 모바일 서비스에 연결할 새 앱을 만들거나 기존 앱을 수정할 수 있습니다. 

이 섹션에서는 모바일 서비스에 대한 새로운 Xamarin iOS 앱 및 서비스 프로젝트를 다운로드합니다.

1. 관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.
   
2. 빠른 시작 탭에서 **플랫폼 선택** 아래의 **Xamarin**을 클릭하고 **새 Xamarin 앱 만들기**를 확장합니다.

   	![][6]

   	그러면 모바일 서비스에 연결된 Xamarin iOS 앱을 만들기 위한 쉬운 3단계가 표시됩니다.

  	![][7]

3. 아직 하지 않은 경우 로컬 컴퓨터나 가상 컴퓨터에 <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>을 다운로드하여 설치합니다.  

4. [Xcode] v4.4 이상 버전 및 [Xamarin Studio]를 다운로드하고 설치합니다. 또한 Visual Studio용 Xamarin을 사용할 수도 있습니다.

5. **서비스를 다운로드하고 클라우드에 게시** 아래에서 **iOS**를 선택하고 **다운로드**를 클릭합니다. 

  	모바일 서비스 및 여기에 연결된 샘플 _할 일 모음_ 응용 프로그램 모두를 위한 프로젝트가 포함된 솔루션이 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

6. 게시 프로필을 다운로드하고, 다운로드한 파일을 로컬 컴퓨터에 저장한 다음 저장 위치를 기록해 둡니다.

## 모바일 서비스 테스트

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## 모바일 서비스 게시

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Xamarin iOS 앱 실행

이 자습서의 최종 단계는 새 앱을 빌드하고 실행하는 것입니다.

1. Visual Studio 또는 Xamarin Studio의 모바일 서비스 솔루션 내에서 클라이언트 프로젝트로 이동합니다.

	![][8]

	![][9]

2. **실행** 단추를 눌러 클라이언트 프로젝트를 빌드하고 iPhone 에뮬레이터에서 앱을 시작합니다.

3. 앱에서 _자습서 완료_ 등의 의미 있는 텍스트를 입력한 후 더하기(**+**) 아이콘을 클릭합니다.

	![][10]

	Azure에 호스트된 새 모바일 서비스에 POST 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. TodoItem 테이블에 저장된 항목이 모바일 서비스에서 반환된 후 데이터가 목록에 표시됩니다.

>[WACOM.NOTE]모바일 서비스에 액세스하여 QSTodoService.cs C# 파일에서 데이터를 쿼리 및 삽입하는 코드를 검토할 수 있습니다.

    
## 다음 단계
이제 빠른 시작을 완료했으며 모바일 서비스에서 중요한 추가 작업을 수행하는 방법을 알아보겠습니다. 

* [오프라인 데이터 동기화 시작]
  <br/>오프라인 데이터 동기화를 사용하여 앱의 응답성 및 성능을 강화합니다.

* [인증 시작]
  <br/>ID 공급자를 사용하여 앱 사용자를 인증하는 방법에 대해 알아봅니다.

* [푸시 알림 시작] 
  <br/>매우 기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

* [모바일 서비스 .NET 백 엔드 문제 해결]
  <br/> 모바일 서비스 .NET 백 엔드에서 발생할 수 있는 문제를 진단하고 해결하는 방법에 알아봅니다. 

<!-- Anchors. -->
[모바일 서비스 시작]:#getting-started
[새 모바일 서비스 만들기]:#create-new-service
[다음 단계]:#next-steps



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[오프라인 데이터 동기화 시작]: /ko-kr/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data
[인증 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users
[푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[모바일 서비스 SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript 및 HTML]: mobile-services-win8-javascript/
[관리 포털]: https://manage.windowsazure.com/
[JavaScript 백 엔드 버전]: /ko-kr/documentation/articles/partner-xamarin-mobile-services-ios-get-started
[Visual Studio 2012를 사용하여 모바일 서비스에서 데이터 시작]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-data-vs2012
[모바일 서비스 .NET 백 엔드 문제 해결]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/


[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Windows용 Xamarin]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
