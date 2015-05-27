
1. Visual Studio의 솔루션 탐색기에서 App_Start 폴더를 확장한 다음 WebApiConfig.cs 프로젝트 파일을 엽니다.

2. Register 메서드의 **ConfigOptions** 정의 뒤에 다음 코드 줄을 추가합니다.

        options.PushAuthorization = 
            Microsoft.WindowsAzure.Mobile.Service.Security.AuthorizationLevel.User;
 
	그러면 푸시 알림에 등록하기 전에 사용자 인증이 적용됩니다.

2. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **클래스...**를 클릭합니다.

3. 비어 있는 새 클래스 `PushRegistrationHandler`의 이름을 지정하고 **추가**를 클릭합니다.

4. 코드 페이지의 맨 위에 다음 **using** 문을 추가합니다.

		using System.Threading.Tasks; 
		using System.Web.Http; 
		using System.Web.Http.Controllers; 
		using Microsoft.WindowsAzure.Mobile.Service; 
		using Microsoft.WindowsAzure.Mobile.Service.Notifications; 
		using Microsoft.WindowsAzure.Mobile.Service.Security; 

5. 기존 **PushRegistrationHandler** 클래스를 다음 코드로 바꿉니다.
 
	    public class PushRegistrationHandler : INotificationHandler
	    {
	        public Task Register(ApiServices services, HttpRequestContext context,
            NotificationRegistration registration)
	        {
	            try
	            {
	                // Perform a check here for user ID tags, which are not allowed.
	                if(!ValidateTags(registration))
	                {
	                    throw new InvalidOperationException(
	                        "You cannot supply a tag that is a user ID.");                    
	                }
	
	                // Get the logged-in user.
	                var currentUser = context.Principal as ServiceUser;
	
	                // Add a new tag that is the user ID.
	                registration.Tags.Add(currentUser.Id);
	
	                services.Log.Info("Registered tag for userId: " + currentUser.Id);
	            }
	            catch(Exception ex)
	            {
	                services.Log.Error(ex.ToString());
	            }
	                return Task.FromResult(true);
	        }
	
	        private bool ValidateTags(NotificationRegistration registration)
	        {
	            // Create a regex to search for disallowed tags.
	            System.Text.RegularExpressions.Regex searchTerm =
	            new System.Text.RegularExpressions.Regex(@"facebook:|google:|twitter:|microsoftaccount:",
	                System.Text.RegularExpressions.RegexOptions.IgnoreCase);
	
	            foreach (string tag in registration.Tags)
	            {
	                if (searchTerm.IsMatch(tag))
	                {
	                    return false;
	                }
	            }
	            return true;
	        }
		
	        public Task Unregister(ApiServices services, HttpRequestContext context, 
	            string deviceId)
	        {
	            // This is where you can hook into registration deletion.
	            return Task.FromResult(true);
	        }
	    }

	등록하는 동안 **Register** 메서드가 호출됩니다. 이를 통해 로그인된 사용자의 ID인 태그를 등록에 추가할 수 있습니다. 제공된 태그는 사용자가 다른 사용자의 ID를 등록하지 못하도록 하기 위해 유효성이 확인됩니다. 이 사용자에게 알림이 전송되면 이 장치와 사용자가 등록한 다른 모든 장치에 알림이 수신됩니다.

6. Controllers 폴더를 확장한 후 TodoItemController.cs 프로젝트 파일을 열고 **PostTodoItem** 메서드를 찾아서 **SendAsync**를 호출하는 코드 줄을 다음 코드로 바꿉니다.

        // Get the logged-in user.
		var currentUser = this.User as ServiceUser;
		
		// Use a tag to only send the notification to the logged-in user.
        var result = await Services.Push.SendAsync(message, currentUser.Id);

7. 모바일 서비스 프로젝트를 다시 게시합니다.

이제 서비스에서 사용자 ID 태그를 사용하여 로그인된 사용자가 만든 모든 등록에 푸시 알림(삽입된 항목의 텍스트 포함)을 보냅니다.
 
<!--HONumber=54-->