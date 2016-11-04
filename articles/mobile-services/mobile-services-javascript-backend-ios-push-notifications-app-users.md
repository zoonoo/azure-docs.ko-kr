---
title: iOS에서 인증된 사용자에게 푸시 알림 보내기(JavaScript 백 엔드)
description: 특정 사용자에게 푸시 알림을 보내는 방법에 대해 알아보기
services: mobile-services,notification-hubs
documentationcenter: ios
author: krisragh
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/21/2016
ms.author: krisragh

---
# 인증된 사용자에게 푸시 알림 보내기
[!INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> 이 항목에 해당하는 모바일 앱 버전은 [방법: 인증된 사용자에게 태그를 사용하여 푸시 알림 보내기](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#push-user)를 참조하세요.
> 
> 

이 항목에서는 iOS에서 인증된 사용자에게 푸시 알림을 보내는 방법에 대해 배웁니다. 이 자습서를 시작하기 전에 [인증 시작] 및 [푸시 알림 시작]을 먼저 완료합니다.

이 자습서에서는 인증된 사용자만 해당 알림을 보내기 위해 먼저 인증하고, 푸시 알림에 대한 알림 허브에 등록하고, 서버 스크립트를 업데이트하도록 사용자에게 요청합니다.

## <a name="register"></a>등록에 인증이 필요하도록 서비스 업데이트
[!INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

`insert` 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다. 이 삽입 스크립트는 사용자 ID 태그를 사용하여 로그인된 사용자의 모든 iOS 앱 등록에 푸시 알림을 보냅니다.

```
// Get the ID of the logged-in user.
var userId = user.userId;

function insert(item, user, request) {
    request.execute();
    setTimeout(function() {
        push.apns.send(userId, {
            alert: "Alert: " + item.text,
            payload: {
                "Hey, a new item arrived: '" + item.text + "'"
            }
        });
    }, 2500);
}
```

## <a name="update-app"></a>등록 전에 로그인하도록 앱 업데이트
[!INCLUDE [mobile-services-ios-push-notifications-app-users-login](../../includes/mobile-services-ios-push-notifications-app-users-login.md)]

## <a name="test"></a>앱 테스트
[!INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]

<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[인증 시작]: mobile-services-ios-get-started-users.md
[푸시 알림 시작]: mobile-services-javascript-backend-ios-get-started-push.md
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-ios-how-to-use-client-library.md

<!---HONumber=AcomDC_0727_2016-->