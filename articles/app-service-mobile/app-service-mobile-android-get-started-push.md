---
title: "Azure 모바일 앱을 사용하여 Android 앱에 푸시 알림 추가"
description: "Azure 모바일 앱을 사용하여 Android 앱에 푸시 알림을 보내는 방법에 대해 알아봅니다."
services: app-service\mobile
documentationcenter: android
manager: erikre
editor: 
author: ysxu
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ac50b4986d6abd6f3a4af660560f4252b8c99fdb


---
# <a name="add-push-notifications-to-your-android-app"></a>Android 앱에 푸시 알림 추가
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>개요
이 자습서에서는 푸시 알림을 [Android 빠른 시작] 프로젝트에 추가하여 레코드가 삽입될 때마다 장치에 푸시 알림이 전송됩니다.

다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 푸시 알림 확장 패키지가 필요합니다. 자세한 내용은 [Azure Mobile Apps용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
다음이 필요합니다.

* 프로젝트의 백 엔드에 따른 IDE
  
  * 이 앱에 Node.js 백 엔드가 있는 경우 [Android Studio](https://developer.android.com/sdk/index.html)
  * 이 앱에 .Net 백 엔드가 있는 경우 [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) 이상
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

다음 단계에서는 Google Play Services를 설치합니다. Google Cloud Messaging에는 매니페스트의 **minSdkVersion** 속성이 준수해야 하는 개발 및 테스트에 대한 최소 API 수준 요구 사항이 있습니다.

이전 장치로 테스트할 경우 이 값을 적절하게 설정할 수 있는 최소값을 확인하려면 [Google Play Services SDK 설정]을 참조하세요.

### <a name="add-google-play-services-to-the-project"></a>프로젝트에 Google Play Services 추가
[!INCLUDE [Add Play Services](../../includes/app-service-mobile-add-google-play-services.md)]

### <a name="add-code"></a>코드 추가
[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>게시된 모바일 서비스에 대해 앱 테스트
USB 케이블로 Android 휴대폰을 직접 연결하거나 에뮬레이터에서 가상 장치를 사용하여 앱을 테스트할 수 있습니다.

## <a name="more"></a>추가
<!-- URLs -->
[Android 빠른 시작]: app-service-mobile-android-get-started.md

[Google Play Services SDK 설정]:https://developers.google.com/android/guides/setup



<!--HONumber=Nov16_HO3-->


