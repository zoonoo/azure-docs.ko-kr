1. **channels** 테이블용 insert.js 파일에서 다음 코드 줄을 찾아서 주석으로 처리하거나 삭제한 후 변경 사항을 저장합니다.
   
        sendNotifications(item.channelUri);
   
        function sendNotifications(uri) {
            console.log("Uri: ", uri);
            push.wns.sendToastText01(uri, {
                text1: "Sample toast from sample insert"
            }, {
                success: function (pushResponse) {
                    console.log("Sent push:", pushResponse);
                }
            });
        }
   
    insert.js 파일 변경 사항을 저장하면 새 버전의 스크립트가 모바일 서비스에 업로드 됩니다.
2. 서버 탐색기에서 TodoItem 테이블을 확장하고 insert.js 파일을 열어서 삽입 함수를 다음 코드로 바꾼 후 변경 사항을 저장합니다.
   
        function insert(item, user, request) {
            request.execute({
                success: function() {
                    request.respond();
                    sendNotifications();
                }
            });
   
            function sendNotifications() {
                var channelsTable = tables.getTable('channels');
                channelsTable.read({
                    success: function(devices) {
                        devices.forEach(function(device) {
                            push.wns.sendToastText04(device.channelUri, {
                                text1: item.text
                            }, {
                                success: function(pushResponse) {
                                    console.log("Sent push:", pushResponse);
                                }
                            });
                        });
                    }
                });
            }
        }
   
    이제 새 TodoItem을 삽입하면 등록된 모든 장치에 푸시 알림이 전송됩니다.

<!---HONumber=Oct15_HO3-->