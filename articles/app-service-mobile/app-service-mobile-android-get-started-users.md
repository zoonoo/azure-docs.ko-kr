---
title: "Mobile App을 사용하여 Android에 인증 추가 | Microsoft Docs"
description: "Azure 앱 서비스에서 모바일 앱을 사용하여 Google, Facebook, Twitter, Microsoft를 비롯한 다양한 ID 공급자를 통해 Android 앱의 사용자를 인증하는 방법을 알아봅니다."
services: app-service\mobile
documentationcenter: android
author: ysxu
manager: erikre
editor: 
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3caf6edb1e3a33a451ec8773b1e14fe0c95734a1


---
# <a name="add-authentication-to-your-android-app"></a>Android 앱에 인증 추가
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>요약
이 자습서에서는 지원되는 ID 공급자를 사용하여 Android의 할 일 모음 빠른 시작 프로젝트에 인증을 추가합니다. 이 자습서는 [모바일 앱 시작] 자습서를 기반으로 하며 이를 먼저 완료해야 합니다.

## <a name="a-nameregisteraregister-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>인증을 위해 앱 등록 및 앱 서비스 구성
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="a-namepermissionsarestrict-permissions-to-authenticated-users"></a><a name="permissions"></a>사용 권한을 인증된 사용자로 제한
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* Android Studio에서 [모바일 앱 시작] 자습서를 완료한 프로젝트를 엽니다. **실행** 메뉴에서 **앱 실행**을 클릭하여 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다.
  
     이 예외는 앱이 인증되지 않은 사용자로 백 엔드에 액세스하려고 시도하지만 *할 일 모음* 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

다음으로 앱을 업데이트하여 모바일 앱 백 엔드에서 리소스를 요청하기 전에 사용자를 인증하도록 합니다.

## <a name="add-authentication-to-the-app"></a>앱에 인증 추가
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="a-namecache-tokensacache-authentication-tokens-on-the-client"></a><a name="cache-tokens"></a>클라이언트에 인증 토큰 캐시
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>다음 단계
이 기본 인증 자습서를 완료했으므로 다음 자습서 중 하나를 계속하는 것을 고려해보세요.

* [Android 앱에 푸시 알림 추가](app-service-mobile-android-get-started-push.md) Azure Notification Hubs를 사용하는 Mobile App 백 엔드를 구성하여 푸시 알림을 보내는 방법을 알아봅니다.
* [Android 앱에 오프라인 동기화 사용](app-service-mobile-android-get-started-offline-data.md) Mobile App 백 엔드를 사용하여 앱에 오프라인 지원을 추가하는 방법을 알아봅니다. 오프라인 동기화를 사용하면 최종 사용자는 네트워크에 연결되어 있지 않을 때도 모바일 앱 &mdash;데이터 보기, 추가 또는 수정&mdash;과 같은 상호 작용을 수행할 수 있습니다.

<!-- Anchors. -->
[인증을 위해 앱 등록 및 모바일 서비스 구성]: #register
[테이블 사용 권한을 인증된 사용자로 제한]: #permissions
[앱에 인증 추가]: #add-authentication
[클라이언트에 인증 토큰 저장]: #cache-tokens
[만료된 토큰 새로 고침]: #refresh-tokens
[다음 단계]:#next-steps


<!-- URLs. -->
[모바일 앱 시작]: app-service-mobile-android-get-started.md



<!--HONumber=Nov16_HO3-->


