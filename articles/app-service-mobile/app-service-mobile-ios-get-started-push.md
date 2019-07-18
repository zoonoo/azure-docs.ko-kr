---
title: Azure Mobile Apps를 사용하여 iOS 앱에 푸시 알림 추가
description: Azure Mobile Apps를 사용하여 iOS 앱에 푸시 알림을 보내는 방법에 대해 알아봅니다.
services: app-service\mobile
documentationcenter: ios
manager: crdun
editor: ''
author: elamalani
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 5ab968e88331f888dfcecd2cc30a658b0b0f53ec
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445370"
---
# <a name="add-push-notifications-to-your-ios-app"></a>iOS 앱에 푸시 알림 추가

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center는 모바일 앱 개발을 위한 중앙 새 및 통합 서비스에 투자 합니다. 개발자가 사용할 수 **빌드**를 **테스트** 하 고 **배포** 연속 통합 및 배달 파이프라인을 설정 하는 서비스입니다. 개발자 상태 및 사용 하 여 해당 앱의 사용량을 모니터링할 수 있습니다, 앱을 배포한 후 합니다 **Analytics** 하 고 **진단** , 서비스를 사용 하 여 사용자와 소통 하세요를 **푸시** 서비스입니다. 개발자가 활용할 수도 있습니다 **인증** 해당 사용자를 인증 하 고 **데이터** 유지 하 고 클라우드에 앱 데이터 동기화 서비스. 체크 아웃 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-ios-get-started-push) 지금 합니다.
>

## <a name="overview"></a>개요

이 자습서에서는 푸시 알림을 [iOS 빠른 시작] 프로젝트에 추가하여 레코드가 삽입될 때마다 디바이스에 푸시 알림이 전송됩니다.

다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 푸시 알림 확장 패키지가 필요합니다. 자세한 내용은 [Azure Mobile Apps용 .NET 백 엔드 서버 SDK로 작업](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) 가이드를 참조하세요.

[iOS 시뮬레이터는 푸시 알림을 지원하지 않습니다](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). 실제 iOS 디바이스 및 [Apple 개발자 프로그램 멤버 자격](https://developer.apple.com/programs/ios/)이 필요합니다.

## <a name="configure-hub"></a>알림 허브 구성

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>푸시 알림을 위한 앱 등록

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>푸시 알림을 전송하도록 Azure 구성

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a id="update-server"></a>푸시 알림을 전송하도록 백 엔드 업데이트

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>앱에 푸시 알림 추가

[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>테스트 푸시 알림

[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a id="more"></a>추가 정보

* 템플릿은 유연성을 제공하여 플랫폼간 푸시 및 지역화된 푸시를 보냅니다. [Azure Mobile Apps용 iOS 클라이언트 라이브러리 사용 방법](app-service-mobile-ios-how-to-use-client-library.md#templates) 은 템플릿을 등록하는 방법을 보여 줍니다.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[iOS 빠른 시작]: app-service-mobile-ios-get-started.md
