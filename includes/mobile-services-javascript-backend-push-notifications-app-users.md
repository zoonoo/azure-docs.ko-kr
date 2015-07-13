
1. [Azure 관리 포털]에 로그온하여 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다.

2. **푸시** 탭을 클릭하고 **사용 권한**에서 **인증된 사용자만**을 선택한 후 **스크립트 편집**을 클릭합니다.
	
	이를 통해 푸시 알림 등록 콜백 함수를 사용자 지정할 수 있습니다. Git를 사용하여 소스 코드를 편집하는 경우 `.\service\extensions\push.js`에서 이와 동일한 등록 함수를 찾을 수 있습니다.

3. 기존 **register** 함수를 다음 코드로 바꿉니다.

		exports.register = function (registration, registrationContext, done) {   
		    // Get the ID of the logged-in user.
			var userId = registrationContext.user.userId;    
		    
			// Perform a check here for any disallowed tags.
			if (!validateTags(registration))
			{
				// Return a service error when the client tries 
		        // to set a user ID tag, which is not allowed.		
				done("You cannot supply a tag that is a user ID");		
			}
			else{
				// Add a new tag that is the user ID.
				registration.tags.push(userId);
				
				// Complete the callback as normal.
				done();
			}
		};
		
		function validateTags(registration){
		    for(var i = 0; i < registration.tags.length; i++) { 
		        console.log(registration.tags[i]);           
				if (registration.tags[i]
				.search(/facebook:|twitter:|google:|microsoft:/i) !== -1){
					return false;
				}
				return true;
			}
		}

	그러면 로그인된 사용자의 ID인 태그가 등록에 추가됩니다. 제공된 태그는 사용자가 다른 사용자의 ID를 등록하지 못하도록 하기 위해 유효성이 확인됩니다. 이 사용자에게 알림이 전송되면 이 장치와 사용자가 등록한 다른 모든 장치에 알림이 수신됩니다.

4. 뒤로 화살표를 클릭하고 **데이터** 탭, **TodoItem**, **스크립트**를 차례로 클릭한 후 **삽입**을 선택합니다.

<!---HONumber=July15_HO1-->