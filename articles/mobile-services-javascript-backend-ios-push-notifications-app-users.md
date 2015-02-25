<properties pageTitle="인증된 사용자에게 푸시 알림 보내기" description="특정 사용자에게 푸시 알림을 보내는 방법에 대해 알아봅니다." services="mobile-services, notification-hubs" documentationCenter="ios" authors="krisragh" manager="dwrede" editor=""/>


<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh"/>

# 인증된 사용자에게 푸시 알림 보내기

[AZURE.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

이 항목에서는 등록된 모든 iOS 장치에서 인증된 사용자에게 푸시 알림을 보내는 방법을 보여 줍니다. 이전의 [푸시 알림][푸시 알림 시작] 자습서와 달리, 이 자습서에서는 먼저 사용자가 인증된 후에야 iOS 클라이언트가 푸시 알림을 받기 위해 알림 허브에 등록할 수 있도록 모바일 서비스를 변경합니다. 할당된 사용자 ID에 따라 태그를 추가하도록 등록도 수정됩니다. 마지막으로 서버 스크립트는 모든 등록 대신 인증된 사용자에게만 알림을 보내도록 업데이트됩니다.

이 자습서에서는 다음 프로세스를 단계별로 안내합니다.

+ [등록에 인증이 필요하도록 서비스 업데이트]
+ [등록 전에 로그인하도록 앱 업데이트]
+ [앱 테스트]

##필수 조건

이 자습서를 시작하려면 먼저 다음 모바일 서비스 자습서를 완료해야 합니다.

+ [인증 시작]<br/>TodoList 샘플 앱에 로그인 요구 사항을 추가합니다.

+ [푸시 알림 시작]<br/>알림 허브를 사용하여 푸시 알림에 맞게 TodoList 샘플 앱을 구성합니다.

두 자습서를 모두 완료한 다음에는 인증되지 않은 사용자가 모바일 서비스의 푸시 알림을 등록할 수 없도록 방지할 수 있습니다.

##<a name="register"></a>등록에 인증이 필요하도록 서비스 업데이트

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

<ol start="5"><li><p>삽입 함수를 다음의 코드로 바꾼 후 <strong>저장</strong>을 클릭합니다.</p>
<pre><code>function insert(item, user, request) {

        function insert(item, user, request) {
            request.execute();
            setTimeout(function() {
                push.apns.send(null, {
                    alert: "Alert: " + item.text,
                    payload: {
                        "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

}</code></pre>

<p>이 삽입 스크립트는 사용자 ID 태그를 사용하여 삽입된 항목의 텍스트가 포함된 푸시 알림을 로그인된 사용자가 만든 모든 Windows Phone(MPNS) 앱 등록으로 전송합니다.</p></li></ol>


##<a name="update-app"></a>등록 전에 로그인하도록 앱 업데이트

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../includes/mobile-services-ios-push-notifications-app-users-login.md)]

##<a name="test"></a>앱 테스트

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]



<!-- Anchors. -->
[등록에 인증이 필요하도록 서비스 업데이트]: #register
[등록 전에 로그인하도록 앱 업데이트]: #update-app
[앱 테스트]: #test
[다음 단계]:#next-steps


<!-- URLs. -->
[인증 시작]: /ko-kr/documentation/articles/mobile-services-ios-get-started-users/
[푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/

[Azure 관리 포털]: https://manage.windowsazure.com/
[모바일 서비스 .NET 방법 개념 참조]: /ko-kr/develop/mobile/how-to-guides/work-with-net-client-library

[23]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png


<!--HONumber=42-->
