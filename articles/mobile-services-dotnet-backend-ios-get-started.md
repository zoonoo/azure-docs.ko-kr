<properties pageTitle="Get Started with Azure Mobile Services for iOS apps" metaKeywords="Azure iOS application, mobile service iOS, getting started Azure iOS" description="Follow this tutorial to get started using Azure Mobile Services for iOS development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# <a name="getting-started"> </a>모바일 서비스 시작

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

이 자습서는 Azure 모바일 서비스를 사용하여 iOS 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다. 이 자습서에서는 새 모바일 서비스와 새 모바일 서비스에 앱 데이터를 저장하는 간단한 *할 일 모음* 앱을 둘 다 만듭니다. 생성되는 모바일 서비스에서는 Visual Studio에서 지원되는 .NET 언어를 서버 쪽 비즈니스 논리와 모바일 서비스 관리에 사용합니다. JavaScript에서 서버 쪽 비즈니스 논리를 작성할 수 있게 해 주는 모바일 서비스를 만들려면 이 항목의 [JavaScript 백 엔드 버전][JavaScript 백 엔드 버전]을 참조하세요.

완성된 앱의 스크린샷은 다음과 같습니다.

![][0]

이 자습서를 완료하려면 XCode 4.5 및 iOS 5.0 이상 버전이 필요합니다.

<div class="dev-callout"><strong>참고</strong> <p>이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 서비스를 사용할 수 있습니다. 이러한 서비스는 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.</p></div>

## <a name="create-new-service"> </a>새 모바일 서비스 만들기

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## 로컬 컴퓨터에 모바일 서비스 다운로드

모바일 서비스를 만들었으므로 이제 로컬 컴퓨터 또는 가상 컴퓨터에서 실행할 수 있는 개인 설정된 모바일 서비스 프로젝트를 다운로드합니다.

1.  방금 만든 모바일 서비스를 클릭하고 빠른 시작 탭에서 **플랫폼 선택** 아래의 **iOS**를 클릭하고 **새 iOS 앱 만들기**를 확장합니다.

    ![][1]

2.  아직 하지 않은 경우 Visual Studio Professional 2013 이상 버전을 다운로드하여 설치합니다.

3.  **서비스를 다운로드하고 클라우드에 게시** 아래의 **다운로드**를 클릭합니다.

    모바일 서비스를 구현하는 Visual Studio 프로젝트가 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

4.  또한 게시 프로필을 다운로드하고, 다운로드한 파일을 로컬 컴퓨터에 저장한 다음 저장 위치를 기록해 둡니다.

## 모바일 서비스 테스트

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## 모바일 서비스 게시

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## 새 iOS 앱 만들기

이 섹션에서는 모바일 서비스에 연결된 새 iOS 앱을 만듭니다.

1.  관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

2.  빠른 시작 탭에서 **플랫폼 선택** 아래의 **iOS**를 클릭하고 **새 iOS 앱 만들기**를 확장합니다.

3.  아직 하지 않은 경우 [Xcode][Xcode] v4.4 이상 버전을 다운로드하여 설치합니다.

4.  **Download and run your app** 아래에서 **다운로드**를 클릭합니다.

    모바일 서비스 iOS SDK와 함께 모바일 서비스에 연결된 샘플 *할 일 모음* 응용 프로그램에 대한 프로젝트가 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

## 새 iOS 앱 실행

[WACOM.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

Azure에서 실행 중인 모바일 서비스에 대해 새 클라이언트 앱을 실행하는 방법을 보여 줍니다. 로컬 컴퓨터에서 실행 중인 모바일 서비스로 iOS 앱을 테스트하려면 먼저 iOS 개발 컴퓨터에서 액세스할 수 있도록 웹 서버와 방화벽을 구성해야 합니다. 자세한 내용은 [로컬 모바일 서비스에 연결할 수 있도록 로컬 웹 서버 구성][로컬 모바일 서비스에 연결할 수 있도록 로컬 웹 서버 구성]을 참조하십시오.

## <a name="next-steps"> </a>다음 단계

이제 빠른 시작을 완료했으며 모바일 서비스에서 중요한 추가 작업을 수행하는 방법을 알아보겠습니다.

-   [데이터 시작하기][데이터 시작하기]
    모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아보세요.

-   [오프라인 데이터 동기화 시작][오프라인 데이터 동기화 시작]
    오프라인 데이터 동기화를 활용하여 응답성과 견고성이 뛰어난 앱을 제작하는 방법을 살펴보십시오.

-   [인증 시작][인증 시작]
    ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

-   [푸시 알림 시작][푸시 알림 시작]
    기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

-   [모바일 서비스 .NET 백 엔드 문제 해결][모바일 서비스 .NET 백 엔드 문제 해결]
     모바일 서비스 .NET 백 엔드에서 발생할 수 있는 문제를 진단 및 해결하는 방법을 알아보십시오.

 
 


  [JavaScript 백 엔드 버전]: /ko-kr/documentation/articles/mobile-services-ios-get-started
  [0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
  [1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png
  [Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [로컬 모바일 서비스에 연결할 수 있도록 로컬 웹 서버 구성]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express
  [데이터 시작하기]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
  [오프라인 데이터 동기화 시작]: /ko-kr/documentation/articles/mobile-services-ios-get-started-offline-data
  [인증 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
  [푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push
  [모바일 서비스 .NET 백 엔드 문제 해결]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
