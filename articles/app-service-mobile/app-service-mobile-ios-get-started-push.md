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
ms.openlocfilehash: 7a037cfb411eb3c15f60bb6a8763374d9102bc4e
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027310"
---
# <a name="add-push-notifications-to-your-ios-app"></a>iOS 앱에 푸시 알림 추가

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center은 모바일 앱 개발에 대 한 종단 간 및 통합 서비스의 중심을 지원 합니다. 개발자는 **빌드**, **테스트** 및 **배포** 서비스를 사용 하 여 지속적인 통합 및 배달 파이프라인을 설정할 수 있습니다. 앱이 배포 되 면 개발자는 **분석** 및 **진단** 서비스를 사용 하 여 앱의 상태와 사용 현황을 모니터링 하 고, **푸시** 서비스를 사용 하 여 사용자와 참여할 수 있습니다. 또한 개발자는 **Auth** 를 활용 하 여 사용자 및 **데이터** 서비스를 인증 하 여 클라우드에서 앱 데이터를 유지 하 고 동기화 할 수 있습니다.
> 모바일 응용 프로그램에서 클라우드 서비스를 통합 하려는 경우 현재 App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) 에 등록 하세요.

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
