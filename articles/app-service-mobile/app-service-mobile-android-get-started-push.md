---
title: Mobile Apps를 사용하여 Android 앱에 푸시 알림 추가 | Microsoft Docs
description: Mobile Apps를 사용하여 Android 앱에 푸시 알림을 보내는 방법에 대해 알아봅니다.
services: app-service\mobile
documentationcenter: android
manager: crdun
editor: ''
author: elamalani
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 539859ba73c8a26d6f7e8f25b9e7453d987a52bd
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389021"
---
# <a name="add-push-notifications-to-your-android-app"></a>Android 앱에 푸시 알림 추가

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center는 모바일 앱 개발의 중심인 엔드투엔드 통합 서비스를 지원합니다. 개발자는 **빌드**, **테스트** 및 **배포** 서비스를 사용하여 지속적인 통합 및 업데이트 파이프라인을 설정할 수 있습니다. 앱이 배포되면 개발자는 **분석** 및 **진단** 서비스를 사용하여 앱의 상태와 사용 현황을 모니터링하고, **푸시** 서비스를 사용하여 사용자와 소통할 수 있습니다. 또한 개발자는 **인증** 서비스를 사용하여 사용자를 인증하고, **데이터** 서비스를 사용하여 클라우드에서 애플리케이션 데이터를 유지하고 동기화할 수도 있습니다.
>
> 모바일 애플리케이션에서 클라우드 서비스를 통합하려면 지금 [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc)에 등록하세요.

## <a name="overview"></a>개요

이 자습서에서는 푸시 알림을 [Android 빠른 시작] 프로젝트에 추가하여 레코드가 삽입될 때마다 디바이스에 푸시 알림이 전송됩니다.

다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 푸시 알림 확장 패키지가 필요합니다. 자세한 내용은 [Azure Mobile Apps용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

## <a name="prerequisites"></a>선행 조건

다음이 필요합니다.

* 프로젝트의 백 엔드에 따른 IDE

  * 이 앱에 Node.js 백 엔드가 있는 경우 [Android Studio](https://developer.android.com/sdk/index.html)
  * 이 앱에 Microsoft .Net 백 엔드가 있는 경우 [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) 이상
* Firebase Cloud Messaging의 경우 Android 2.3 이상, Google Repository 개정 27 이상 및 Google Play Services 9.0.2 이상
* [Android 빠른 시작]을 완료합니다.

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Firebase Cloud Messaging을 지원하는 프로젝트 만들기

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>알림 허브 구성

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>푸시 알림을 전송하도록 Azure 구성

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>서버 프로젝트에 푸시 알림 사용

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>앱에 푸시 알림 추가

이 섹션에서는 푸시 알림을 처리하도록 클라이언트 Android 앱을 업데이트합니다.

### <a name="verify-android-sdk-version"></a>Android SDK 버전 확인

[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

다음 단계에서는 Google Play Services를 설치합니다. Firebase Cloud Messaging에는 매니페스트의 **minSdkVersion** 속성이 준수해야 하는 개발 및 테스트의 최소 API 수준 요구 사항이 있습니다.

오래된 디바이스로 테스트하는 경우 [Android 프로젝트에 Firebase 추가]에서 이 값의 허용되는 최소값과 제대로 설정하는 방법을 참조하세요.

### <a name="add-firebase-cloud-messaging-to-the-project"></a>프로젝트에 Firebase Cloud Messaging 추가

[!INCLUDE [Add Firebase Cloud Messaging](../../includes/app-service-mobile-add-firebase-cloud-messaging.md)]

### <a name="add-code"></a>코드 추가

[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>게시된 모바일 서비스에 대해 앱 테스트

USB 케이블로 Android 휴대폰을 직접 연결하거나 에뮬레이터에서 가상 디바이스를 사용하여 앱을 테스트할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서를 완료했으므로 다음 자습서 중 하나를 계속하는 것을 고려해보세요.

* [Android 앱에 인증 추가](app-service-mobile-android-get-started-users.md).
  지원되는 ID 공급자를 사용하여 Android의 할 일 모음 빠른 시작 프로젝트에 인증을 추가하는 방법을 알아봅니다.
* [Android 앱에 대해 오프라인 동기화를 사용합니다](app-service-mobile-android-get-started-offline-data.md).
  Mobile Apps 백 엔드를 사용하여 앱에 오프라인 지원을 추가하는 방법을 알아봅니다. 오프라인 동기화를 사용하면 사용자는 네트워크에 연결되어 있지 않을 때도 모바일 앱&mdash;데이터 보기, 추가 또는 수정&mdash;과 같은 상호 작용을 수행할 수 있습니다.

<!-- URLs -->
[Android 빠른 시작]: app-service-mobile-android-get-started.md
[Android 프로젝트에 Firebase 추가]: https://firebase.google.com/docs/android/setup
