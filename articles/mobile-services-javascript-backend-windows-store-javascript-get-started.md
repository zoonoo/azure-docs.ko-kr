<properties pageTitle="Get Started with Mobile Services for Windows Store apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C# or JavaScript. " metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="hero-article" ms.date="08/18/2014" ms.author="glenga" />

# <a name="getting-started"> </a>모바일 서비스 시작

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

이 자습서에서는 Azure 모바일 서비스를 사용하여 범용 Windows 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다.

이 자습서에서는 새 모바일 서비스와 새 모바일 서비스에 앱 데이터를 저장하는 간단한 *할 일 모음* 앱을 둘 다 만듭니다. 만드는 모바일 서비스는 서버 쪽 비즈니스 논리에 JavaScript를 사용합니다. Visual Studio를 사용하여 지원되는 .NET 언어로 서버 쪽 비즈니스 논리를 작성할 수 있게 해 주는 모바일 서비스를 만들려면 이 토픽의 .NET 백 엔드 버전을 참조하세요.

[WACOM.INCLUDE [mobile-services-windows-universal-get-started](../includes/mobile-services-windows-universal-get-started.md)]

이 자습서를 완료하려면 다음이 필요합니다.

-   활성 Azure 계정. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][Azure 무료 평가판]을 참조하세요.
-   [Visual Studio 2013 Express for Windows][Visual Studio 2013 Express for Windows]

## 새 모바일 서비스 만들기

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## 새 범용 Windows 앱 만들기

모바일 서비스를 만든 후 관리 포털에서 쉬운 빠른 시작을 따라 새 범용 Windows 앱을 만들거나 모바일 서비스에 연결하도록 기존 Windows 스토어 또는 Windows Phone 앱을 수정할 수 있습니다.

이 섹션에서는 모바일 서비스에 연결된 새 범용 Windows 앱을 만듭니다.

1.  관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

2.  빠른 시작 탭에서 **플랫폼 선택** 아래의 **Windows**를 클릭하고 **새 Windows 스토어 앱 만들기**를 확장합니다.

    ![][0]

    모바일 서비스에 연결된 Windows 스토어 앱을 만드는 간편한 세 가지 단계가 표시됩니다.

    ![][1]

3.  아직 수행하지 않은 경우 로컬 컴퓨터나 가상 컴퓨터에 [Visual Studio 2013][Visual Studio 2013 Express for Windows]을 다운로드하여 설치합니다.

4.  **TodoItem 테이블 만들기**를 클릭하여 앱 데이터를 저장할 테이블을 만듭니다.

5.  **앱 다운로드 및 실행**에서 앱의 언어를 선택하고 **다운로드**를 클릭합니다.

    모바일 서비스에 연결된 샘플 *할 일 모음* 응용 프로그램에 대한 프로젝트가 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

## Windows 앱 실행

[WACOM.INCLUDE [mobile-services-javascript-backend-run-app](../includes/mobile-services-javascript-backend-run-app.md)]

> [WACOM.NOTE]모바일 서비스에 액세스하여 데이터를 쿼리 및 삽입하는 코드를 검토할 수 있습니다. 이 코드는 default.js 파일에 있습니다.

## 다음 단계

이제 빠른 시작을 완료했으며 모바일 서비스에서 중요한 추가 작업을 수행하는 방법을 알아보겠습니다.

-   [데이터 시작하기][데이터 시작하기]
    모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아보세요.

-   [인증 시작][인증 시작]
    ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

-   [푸시 알림 시작][푸시 알림 시작]
    기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

범용 Windows 앱에 대한 자세한 내용은 [단일 모바일 서비스에서 여러 장치 플랫폼 지원][단일 모바일 서비스에서 여러 장치 플랫폼 지원]을 참조하세요.



  [Azure 무료 평가판]: http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-kr%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F
  [Visual Studio 2013 Express for Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
  [0]: ./media/mobile-services-javascript-backend-windows-store-javascript-get-started/mobile-portal-quickstart.png
  [1]: ./media/mobile-services-javascript-backend-windows-store-javascript-get-started/mobile-quickstart-steps.png
  [데이터 시작하기]: /ko-kr/documentation/articles/mobile-services-windows-store-javascript-get-started-data
  [인증 시작]: /ko-kr/documentation/articles/mobile-services-windows-store-javascript-get-started-users
  [푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push
  [단일 모바일 서비스에서 여러 장치 플랫폼 지원]: /ko-kr/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs
