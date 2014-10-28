마지막으로 TodoItem 테이블에서 삽입 작업에 등록된 스크립트를 업데이트하여 알림을 보내야 합니다.

1.  **TodoItem**을 클릭하고 **스크립트**를 클릭한 후 **삽입**을 선택합니다.

    ![][]

2.  삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

        function insert(item, user, request) {
        // Define a payload for the Windows Store toast notification.
        var payload = '<?xml version="1.0" encoding="utf-8"?><toast><visual>' +    
            '<binding template="ToastText01">  <text id="1">' +
            item.text + '</text></binding></visual></toast>';

        request.execute({
            success: function() {
                // If the insert succeeds, send a notification.
                push.wns.send(null, payload, 'wns/toast', {
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

    이 삽입 스크립트는 성공적으로 삽입한 후 모든 Windows 스토어 앱 등록에 삽입된 항목의 텍스트가 포함된 푸시 알림을 보냅니다.

  []: ./media/mobile-services-javascript-update-script-notification-hubs/mobile-insert-script-push2.png
