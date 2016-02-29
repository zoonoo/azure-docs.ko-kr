<properties
    pageTitle="Azure 앱 서비스 모바일 앱에서 Cordova 앱 만들기 | Microsoft Azure"
    description="이 자습서에 따라 Azure 모바일 앱 백 엔드를 사용하여 Apache Cordova 개발을 시작할 수 있습니다."
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="ggailey777"
    editor=""
    tags=""
    keywords="cordova,javascript,모바일,클라이언트" />

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="adrianha"/>

#Apache Cordova 앱 만들기

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## 개요

이 자습서에서는 Azure 모바일 앱 백 엔드를 사용하여 클라우드 기반 백 엔드 서비스를 Apache Cordova 모바일 앱에 추가하는 방법을 보여 줍니다. 새 모바일 앱 백 엔드와 앱 데이터를 Azure에 저장하는 간단한 _할 일 목록_ Apache Cordova 앱을 만듭니다.

이 자습서를 완료해야 Azure 앱 서비스에서 모바일 앱 기능을 사용하는 방법에 대한 다른 모든 Apache Cordova 자습서를 진행할 수 있습니다.

## 필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* [Visual Studio Community 2015] 이상이 설치된 PC
* [Visual Studio Tools for Apache Cordova].
* [활성 Azure 계정](https://azure.microsoft.com/pricing/free-trial/)

Visual Studio를 바이패스하고 직접 Apache Cordova 명령줄을 사용할 수도 있습니다. 이 옵션은 Mac 컴퓨터에서 자습서를 완료하는 경우 유용합니다. 명령줄을 사용하여 Apache Cordova 클라이언트 응용 프로그램을 컴파일하는 작업은 이 자습서에서 다루지 않습니다.

## 새 Azure 모바일 앱 백 엔드 만들기

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## 서버 프로젝트 구성

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Apache Cordova 앱 다운로드 및 실행

[AZURE.INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## 다음 단계

이제 이 빠른 시작 자습서를 완료했으므로 다음 자습서 중 하나로 이동합니다.

* Apache Cordova 앱에 [인증을 추가]합니다.
* Apache Cordova 앱에 [푸시 알림을 추가]합니다.

Azure 앱 서비스의 주요 개념에 대해 자세히 알아봅니다.

* [인증]
* [푸시 알림]

SDK 사용 방법을 알아봅니다.

* [ASP.NET 서버 SDK]
* [Node.js 서버 SDK]

<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/ko-KR/features/cordova-vs.aspx
[인증을 추가]: app-service-mobile-cordova-get-started-users.md
[푸시 알림을 추가]: app-service-mobile-cordova-get-started-push.md
[인증]: app-service-mobile-auth.md
[푸시 알림]: ../notification-hubs/notification-hubs-overview.md
[ASP.NET 서버 SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js 서버 SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0218_2016-->