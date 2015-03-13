<properties 
	pageTitle="Windows 스토어 앱에 대한 모바일 서비스 시작 | 모바일 개발자 센터" 
	description="이 자습서에 따라 Azure 모바일 서비스를 사용하여 C# 또는 JavaScript로 Windows 스토어 개발을 시작할 수 있습니다." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="hero-article" 
	ms.date="08/18/2014" 
	ms.author="glenga"/>

# <a name="getting-started"> </a>모바일 서비스 시작

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

이 자습서에서는 Azure 모바일 서비스를 사용하여 범용 Windows 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다. 

이 자습서에서는 새 모바일 서비스와 새 모바일 서비스에 앱 데이터를 저장하는 간단한  *To do list* 앱을 둘 다 만듭니다. 만드는 모바일 서비스는 서버 쪽 비즈니스 논리에 JavaScript를 사용합니다. Visual Studio를 사용하여 지원되는 .NET 언어로 서버 쪽 비즈니스 논리를 작성할 수 있게 해 주는 모바일 서비스를 만들려면 이 항목의 .NET 백 엔드 버전을 참조하세요.

[AZURE.INCLUDE [mobile-services-windows-universal-get-started](../includes/mobile-services-windows-universal-get-started.md)]

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-kr%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F)을 참조하세요.
* [Visual Studio 2013 Express for Windows] 

## 새 모바일 서비스 만들기

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## 새 범용 Windows 앱 만들기

모바일 서비스를 만든 후 관리 포털에서 쉬운 퀵 스타트를 따라 새 범용 Windows 앱을 만들거나 모바일 서비스에 연결하도록 기존 Windows 스토어 또는 Windows Phone 앱을 수정할 수 있습니다. 

이 섹션에서는 모바일 서비스에 연결된 새 범용 Windows 앱을 만듭니다.

1.  관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

   
2. 퀵 스타트 탭에서 **플랫폼 선택** 아래의 **Windows**를 클릭하고 **새 Windows 스토어 앱 만들기**를 확장합니다.

   	![](./media/mobile-services-javascript-backend-windows-store-javascript-get-started/mobile-portal-quickstart.png)

   	모바일 서비스에 연결된 Windows 스토어 앱을 만드는 간편한 세 가지 단계가 표시됩니다.

  	![](./media/mobile-services-javascript-backend-windows-store-javascript-get-started/mobile-quickstart-steps.png)

3. 아직 수행하지 않은 경우 로컬 컴퓨터나 가상 컴퓨터에서 [Visual Studio 2013][Visual Studio 2013 Express for Windows]를 다운로드하여 설치합니다.

4. **TodoItem 테이블 만들기**를 클릭하여 앱 데이터를 저장할 테이블을 만듭니다.

5. **앱 다운로드 및 실행**에서 앱의 언어를 선택하고 **다운로드**를 클릭합니다. 

  	모바일 서비스에 연결된 샘플  *To do list* 응용 프로그램에 대한 프로젝트가 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

## Windows 앱 실행

[AZURE.INCLUDE [mobile-services-javascript-backend-run-app](../includes/mobile-services-javascript-backend-run-app.md)]

>[AZURE.NOTE]모바일 서비스에 액세스하는 코드를 검토하여 데이터를 쿼리 및 삽입할 수 있습니다(default.js 파일에 있음).

## 다음 단계
이제 퀵 스타트를 완료했으며 모바일 서비스에서 중요한 추가 작업을 수행하는 방법을 알아보겠습니다. 

* [데이터 시작]
  <br/>모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아봅니다.

* [인증 시작](영문)
  <br/>ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

* [푸시 알림 시작] 
  <br/>앱에 기본적인 푸시 알림을 보내는 방법을 알아봅니다.

범용 Windows 앱에 대한 자세한 내용은 [단일 모바일 서비스에서 여러 장치 플랫폼 지원](/ko-kr/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs)을 참조하세요.

<!-- Anchors. -->
[모바일 서비스 시작]:#getting-started
[새 모바일 서비스 만들기]:#create-new-service
[모바일 서비스 인스턴스 정의]:#define-mobile-service-instance
[다음 단계]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[데이터 시작]: /ko-kr/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-data
[인증 시작](영문): /ko-kr/documentation/articles/mobile-services-windows-store-javascript-get-started-users
[푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push
[Visual Studio 2013 Express for Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[모바일 서비스 SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[관리 포털]: https://manage.windowsazure.com/


<!--HONumber=42-->
