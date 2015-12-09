<properties
	pageTitle="Azure 앱 서비스 모바일 앱에서 Windows Runtime 8.1 유니버설 앱 만들기 | Microsoft Azure"
	description="이 자습서에 따라 Azure 모바일 앱 백 엔드를 사용하여 C#, Visual Basic 또는 JavaScript로 Windows 스토어 개발을 시작할 수 있습니다."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="11/16/2015"
	ms.author="glenga"/>

#Windows 앱 만들기

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##개요

이 자습서에서는 범용 Windows 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다. 자세한 내용은 [모바일 앱 정의](app-service-mobile-value-prop.md)를 참조하세요.

[AZURE.INCLUDE [app-service-mobile-windows-universal-get-started](../../includes/app-service-mobile-windows-universal-get-started.md)]

##필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 앱을 가져올 수 있습니다. 이러한 앱은 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 [Azure 무료 체험](http://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* [Visual Studio Community 2013] 이상 버전

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려는 경우 [앱 서비스 체험](https://tryappservice.azure.com/?appServiceName=mobile)으로 이동하세요. 여기서 신용 카드와 약정 없이 앱 서비스에서 수명이 짧은 스타터 모바일 앱을 즉시 만들 수 있습니다.

##새 Azure 모바일 앱 백 엔드 만들기

다음 단계에 따라 새 모바일 앱 백 엔드를 만드세요.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

이제 모바일 클라이언트 응용 프로그램에서 사용할 수 있는 Azure 모바일 앱 백 엔드를 프로비저닝했습니다. 다음으로, 간단한 “할 일 목록” 백 엔드에 대한 서버 프로젝트를 다운로드하고 Azure에 게시합니다.

## 서버 프로젝트 구성

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

##클라이언트 프로젝트 다운로드 및 실행

모바일 앱 백 엔드를 구성하면 새 클라이언트 앱을 만들거나 Azure에 연결할 기존 앱을 수정할 수 있습니다. 이 섹션에서 모바일 앱 백 엔드에 연결하도록 사용자 지정된 범용 Windows 앱 템플릿 프로젝트를 다운로드합니다.

1. 모바일 앱 백 엔드에 대한 **시작** 블레이드로 돌아가서 **새 앱 만들기** > **다운로드**를 클릭한 다음 로컬 컴퓨터에 압축된 프로젝트 파일을 추출합니다.

3. (선택 사항) 서버 프로젝트를 사용하여 솔루션에 범용 Windows 앱 프로젝트를 추가합니다. 이렇게 하면 작업을 수행하려는 경우 동일한 Visual Studio 솔루션에서 앱 및 백 엔드 모두를 디버그하고 테스트할 수 있습니다.

4. 시작 프로젝트인 Windows 스토어 앱을 사용하여 프로젝트를 다시 빌드하고 Windows 스토어 앱을 시작하려면 F5 키를 누릅니다.

5. 앱에서 **Insert a TodoItem** 텍스트 상자에 *자습서 완료*와 같은 의미 있는 텍스트를 입력하고 **저장**을 클릭합니다.

	![](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

	Azure에 호스트된 새 모바일 앱 백 엔드에 POST 요청이 전송됩니다.

6. 디버깅을 중지하고 마우스 오른쪽 단추로 `<your app name>.WindowsPhone` 프로젝트를 클릭하며 **시작 프로젝트로 설정**을 클릭한 다음 F5 키를 다시 누릅니다.

	![](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-wp8.png)

	Windows 앱이 시작된 후 이전 단계에서 저장한 데이터가 모바일 앱에서 로드됩니다.

##다음 단계

* [앱에 인증 추가](app-service-mobile-windows-store-dotnet-get-started-users.md) <br/> ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

* [앱에 푸시 알림 추가](app-service-mobile-windows-store-dotnet-get-started-push.md) <br/>기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_1203_2015-->