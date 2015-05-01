
1. 관리 포털에서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다. 
 
2. **todoitem**에서 **스크립트** 탭을 클릭하고 **삽입**을 선택합니다.
   
   	**TodoItem** 테이블에 삽입 시 호출되는 함수가 표시됩니다.

3. 삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

		function insert(item, user, request) {
		// Define a simple payload for a GCM notification.
	    var payload = {
	        "data": {
	            "message": item.text
	        }
	    };		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		        push.gcm.send(null, payload, {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse, payload);
		                request.respond();
		                },              
		            error: function (pushResponse) {
		                console.log("Error Sending push:", pushResponse);
		                request.respond(500, { error: pushResponse });
		                }
		            });
		        },
		    error: function(err) {
		        console.log("request.execute error", err)
		        request.respond();
		    }
		  });
		}

   	이 코드는 새 삽입 스크립트를 등록하며, 삽입 성공 후 [gcm 개체](http://go.microsoft.com/fwlink/p/?LinkId=282645)를 사용하여 등록된 모든 장치에 푸시 알림을 보냅니다. 


<!--HONumber=52-->