<properties 
	pageTitle="인증된 범용 Windows 앱 사용자에게 푸시 알림을 보냅니다." 
	description="Azure 모바일 서비스에서 범용 Windows C# 앱의 특정 사용자에게 푸시 알림을 보내는 방법에 대해 알아봅니다." 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/22/2015" 
	ms.author="glenga"/>

# 인증된 사용자에게 푸시 알림 보내기

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

##개요
이 항목에서는 등록된 모든 장치에서 인증된 사용자에게 푸시 알림을 보내는 방법을 보여 줍니다. 이전의 [앱에 푸시 알림 추가] 자습서와 달리, 이 자습서에서는 먼저 사용자가 인증된 후에야 클라이언트가 푸시 알림을 받기 위해 알림 허브에 등록할 수 있도록 모바일 서비스를 변경합니다. 할당된 사용자 ID에 따라 태그를 추가하도록 등록도 수정됩니다. 마지막으로 서버 스크립트는 모든 등록 대신 인증된 사용자에게만 알림을 보내도록 업데이트됩니다.

이 자습서에서는 다음 프로세스를 단계별로 안내합니다.

1. [등록에 인증이 필요하도록 서비스 업데이트]
2. [등록 전에 로그인하도록 앱 업데이트]
3. [앱 테스트]
 
이 자습서에서는 Windows 스토어 및 Windows Phone 스토어 앱이 모두 지원됩니다.

##필수 조건 

이 자습서를 시작하려면 먼저 다음 모바일 서비스 자습서를 완료해야 합니다.

+ [앱에 인증 추가]<br/>TodoList 샘플 앱에 로그인 요구 사항을 추가합니다.

+ [앱에 푸시 알림 추가]<br/>알림 허브를 사용하여 푸시 알림에 맞게 TodoList 샘플 앱을 구성합니다.

두 자습서를 모두 완료한 다음에는 인증되지 않은 사용자가 모바일 서비스의 푸시 알림을 등록할 수 없도록 방지할 수 있습니다.

##<a name="register"></a>등록에 인증이 필요하도록 서비스 업데이트

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

&nbsp;&nbsp;5. 삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

	function insert(item, user, request) {
    // Define a payload for the Windows Store toast notification.
    var payload = '<?xml version="1.0" encoding="utf-8"?><toast><visual>' +    
    '<binding template="ToastText01"><text id="1">' +
    item.text + '</text></binding></visual></toast>';

    // Get the ID of the logged-in user.
    var userId = user.userId;		

    request.execute({
        success: function() {
            // If the insert succeeds, send a notification to all devices 
	    	// registered to the logged-in user as a tag.
            	push.wns.send(userId, payload, 'wns/toast', {
                success: function(pushResponse) {
                    console.log("Sent push:", pushResponse);
	    			request.respond();
                    },              
                    error: function (pushResponse) {
                            console.log("Error Sending push:", pushResponse);
	    				request.respond(500, { error: pushResponse });
                        }
                    });
                }
            });
	}

&nbsp;&nbsp;이 삽입 스크립트는 사용자 ID 태그를 사용하여 삽입된 항목의 텍스트가 포함된 푸시 알림을 로그인된 사용자가 만든 모든 Windows 스토어 앱 등록으로 전송합니다.

##<a name="update-app"></a>등록 전에 로그인하도록 앱 업데이트

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-push-notifications-app-users](../../includes/mobile-services-windows-store-dotnet-push-notifications-app-users.md)]

##<a name="test"></a>앱 테스트

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../../includes/mobile-services-windows-test-push-users.md)]

<!-- Anchors. -->
[등록에 인증이 필요하도록 서비스 업데이트]: #register
[등록 전에 로그인하도록 앱 업데이트]: #update-app
[앱 테스트]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[앱에 인증 추가]: ../mobile-services-windows-store-dotnet-get-started-users.md
[앱에 푸시 알림 추가]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md

[Azure 관리 포털]: https://manage.windowsazure.com/
 

<!---HONumber=Oct15_HO3-->