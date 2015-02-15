## WebAPI 프로젝트 만들기

아래 단계에 따라 새 ASP.NET WebAPI 백 엔드를 만들어 클라이언트를 인증하고 알림을 생성하거나 이전 프로젝트 또는 [인증된 사용자에게 푸시 알림 보내기](http://azure.microsoft.com/ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/) 자습서의 기존 백 엔드를 수정합니다.

> [AZURE.NOTE] **중요**: 이 자습서를 시작하기 전에 최신 버전의 NuGet 패키지 관리자를 설치했는지 확인하세요. 확인하려면 Visual Studio를 시작합니다. **도구** 메뉴에서 **확장 및 업데이트**를 클릭합니다. **Visual Studio 2013용 NuGet 패키지 관리자**를 검색하고 버전이 2.8.50313.46 이상인지 확인합니다. 그렇지 않은 경우 제거한 다음, NuGet 패키지 관리자를 다시 설치합니다.
> 
> ![][4]

> [AZURE.NOTE] 웹 사이트 배포를 위해 Visual Studio [Azure SDK](http://azure.microsoft.com/ko-kr/downloads/)를 설치했는지 확인합니다.

1. Visual Studio 또는 Visual Studio Express를 시작합니다.
2. Visual Studio에서 **파일**을 클릭한 후 **새로 만들기**, **프로젝트**를 클릭하고 **템플릿**, **Visual C#**을 확장한 다음 **웹** 및 **ASP.NET 웹 응용 프로그램**을 클릭하고 **AppBackend**라는 이름을 입력한 후 **확인**을 클릭합니다. 
	
	![][1]

3. **새 ASP.NET 프로젝트** 대화 상자에서 **웹 API**를 클릭한 다음, **확인**을 클릭합니다.

	![][2]

4. **Azure 사이트 구성** 대화 상자에서 이 프로젝트에 사용할 구독, 지역 및 데이터베이스를 선택합니다. 그런 다음 **확인**을 클릭하여 프로젝트를 만듭니다.

	![][5]

5. 솔루션 탐색기에서 **AppBackend** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리**를 클릭합니다.

6. 왼쪽에서 **온라인**을 클릭하고 **검색** 상자에 **servicebus**를 입력합니다.

7. 결과 목록에서 **Microsoft Azure 서비스 버스**를 클릭한 다음 **설치**를 클릭합니다. 설치를 완료한 다음, NuGet 패키지 관리자 창을 닫습니다.

	![][14]

8. 이제 **Notifications.cs** 클래스를 새로 만듭니다. 솔루션 탐색기로 이동하고 **Models** 폴더를 마우스 오른쪽 단추로 클릭한 다음 **추가**와 **클래스**를 차례로 클릭합니다. 새 클래스 이름을 **Notifications.cs**로 지정한 후 **추가**를 클릭하여 클래스를 생성합니다. 이 모듈은 전송할 다른 보안 알림을 나타냅니다. 완전한 구현에서 알림은 데이터베이스에 저장됩니다. 여기서는 단순화를 위해 메모리에 알림을 저장합니다.

	![][6]

9. Notifications.cs에서 파일의 맨 위에 `using` 문을 추가합니다.

        using Microsoft.ServiceBus.Notifications;

10. 그런 다음 `Notifications` 클래스 정의를 다음으로 바꾸고 두 개의 자리 표시자를 알림 허브에 대한 연결 문자열(모든 권한 사용) 및 허브 이름([Azure 관리 포털](http://manage.windowsazure.com)에서 얻을 수 있음)으로 바꿉니다.

		public class Notifications
        {
            public static Notifications Instance = new Notifications();
        
            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}", "{hub name}");
            }
        }

11. 그런 다음 **AuthenticationTestHandler.cs** 클래스를 새로 만듭니다. 솔루션 탐색기에서 **AppBackend** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**와 **클래스**를 차례로 클릭합니다. 새 클래스의 이름을 **AuthenticationTestHandler.cs**로 지정하고 **추가**를 클릭하여 클래스를 생성합니다. 이 클래스는 *Basic Authentication*을 사용하여 사용자를 인증하는 데 사용됩니다. 앱은 모든 인증 체계를 사용할 수 있습니다.

12. AuthenticationTestHandler.cs에 다음 `using` 문을 추가합니다.

        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Threading;
        using System.Text;
        using System.Security.Principal;
        using System.Net;

13. AuthenticationTestHandler.cs에서 `AuthenticationTestHandler` 클래스 정의를 다음으로 바꿉니다.

		public class AuthenticationTestHandler : DelegatingHandler
	    {
	        protected override Task<HttpResponseMessage> SendAsync(
	        HttpRequestMessage request, CancellationToken cancellationToken)
	        {
	            var authorizationHeader = request.Headers.GetValues("Authorization").First();
	
	            if (authorizationHeader != null && authorizationHeader
	                .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
	            {
	                string authorizationUserAndPwdBase64 =
	                    authorizationHeader.Substring("Basic ".Length);
	                string authorizationUserAndPwd = Encoding.Default
	                    .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
	                string user = authorizationUserAndPwd.Split(':')[0];
	                string password = authorizationUserAndPwd.Split(':')[1];
	
	                if (verifyUserAndPwd(user, password))
	                {
	                    // Attach the new principal object to the current HttpContext object
	                    HttpContext.Current.User =
	                        new GenericPrincipal(new GenericIdentity(user), new string[0]);
	                    System.Threading.Thread.CurrentPrincipal =
	                        System.Web.HttpContext.Current.User;
	                }
	                else return Unauthorised();
	            }
	            else return Unauthorised();
	
	            return base.SendAsync(request, cancellationToken);
	        }
	
	        private bool verifyUserAndPwd(string user, string password)
	        {
	            // This is not a real authentication scheme.
	            return user == password;
	        }
	
	        private Task<HttpResponseMessage> Unauthorised()
	        {
	            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
	            var tsc = new TaskCompletionSource<HttpResponseMessage>();
	            tsc.SetResult(response);
	            return tsc.Task;
	        }
	    }

	> [AZURE.NOTE] **보안 정보**:  `AuthenticationTestHandler` 클래스는 진정한 의미의 인증을 제공하지 않습니다. 이 클래스는 기본 인증과 비슷한 동작을 하고 보안이 안전하지 않습니다. 프로덕션 응용 프로그램 및 서비스에 보안 인증 메커니즘을 구현해야 합니다.				

14. **App_Start/WebApiConfig.cs** 클래스의 `Register` 메서드 끝에 다음 코드를 추가합니다.

		config.MessageHandlers.Add(new AuthenticationTestHandler());

15. 그런 다음 **RegisterController** 컨트롤러를 새로 만듭니다. 솔루션 탐색기에서 **Controllers** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**와 **컨트롤러**를 차례로 클릭합니다. **웹 API 2 컨트롤러 -- 비어 있음** 항목을 클릭한 다음 **추가**를 클릭합니다. 새 클래스 이름을 **RegisterController**로 지정한 다음 **추가**를 다시 클릭하여 컨트롤러를 생성합니다.

	![][7]

	![][8]

16. RegiterController.cs에 다음 `using` 문을 추가합니다.

        using Microsoft.ServiceBus.Notifications;
        using AppBackend.Models;
        using System.Threading.Tasks;
        using Microsoft.ServiceBus.Messaging;
        using System.Web;

17. 다음 코드를 `RegisterController` 클래스 정의 내에 추가합니다. 이 코드에서, 처리기를 통해 인증된 사용자의 사용자 태그를 추가합니다. 또한 선택적인 검사를 추가하여 사용자에게 요청된 태그에 등록할 수 있는 권한이 있는지 확인합니다.

		private NotificationHubClient hub;

        public RegisterController()
        {
            hub = Notifications.Instance.Hub;
        }

        public class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }

        // POST api/register
        // This creates a registration id
        public async Task<string> Post(string handle = null)
        {
            // make sure there are no existing registrations for this push handle (used for iOS and Android)
            string newRegistrationId = null;
            
            if (handle != null)
            {
                var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);

                foreach (RegistrationDescription registration in registrations)
                {
                    if (newRegistrationId == null)
                    {
                        newRegistrationId = registration.RegistrationId;
                    }
                    else
                    {
                        await hub.DeleteRegistrationAsync(registration);
                    }
                }
            }

            if (newRegistrationId == null) newRegistrationId = await hub.CreateRegistrationIdAsync();

            return newRegistrationId;
        }

        // PUT api/register/5
        // This creates or updates a registration (with provided channelURI) at the specified id
        public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
        {
            RegistrationDescription registration = null;
            switch (deviceUpdate.Platform)
            {
                case "mpns":
                    registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "wns":
                    registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "apns":
                    registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "gcm":
                    registration = new GcmRegistrationDescription(deviceUpdate.Handle);
                    break;
                default:
                    throw new HttpResponseException(HttpStatusCode.BadRequest);
            }

            registration.RegistrationId = id;
            var username = HttpContext.Current.User.Identity.Name;

            // add check if user is allowed to add these tags
            registration.Tags = new HashSet<string>(deviceUpdate.Tags);
            registration.Tags.Add("username:" + username);

            try
            {
                await hub.CreateOrUpdateRegistrationAsync(registration);
            }
            catch (MessagingException e)
            {
                ReturnGoneIfHubResponseIsGone(e);
            }

            return Request.CreateResponse(HttpStatusCode.OK);
        }

        // DELETE api/register/5
        public async Task<HttpResponseMessage> Delete(string id)
        {
            await hub.DeleteRegistrationAsync(id);
            return Request.CreateResponse(HttpStatusCode.OK);
        }

        private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
        {
            var webex = e.InnerException as WebException;
            if (webex.Status == WebExceptionStatus.ProtocolError)
            {
                var response = (HttpWebResponse)webex.Response;
                if (response.StatusCode == HttpStatusCode.Gone)
                    throw new HttpRequestException(HttpStatusCode.Gone.ToString());
            }
        }

18. **RegisterController**를 만든 방법에 따라 **NotificationsController** 컨트롤러를 새로 만듭니다. 이 구성 요소는 장치에서 알림을 안전하게 검색할 수 있는 방법을 표시하며, 사용자가 자신의 장치로 보안 푸시를 트리거할 수 있는 방법을 제공합니다. 알림 허브로 알림을 보낼 때에는 알림의 ID만 포함된 원시 알림(실제 메시지 없음)을 보냅니다.

19. NotificationsController.cs에 다음 `using` 문을 추가합니다.

        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;

20. **NotificationsController** 클래스 정의 내에 다음 코드를 추가하고 작업하지 않는 플랫폼에 대한 코드 조각을 주석으로 처리합니다.

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:"+user;


            // windows
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello, " + user + "</text></binding></visual></toast>";
            await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);


            // apns
            var alert = "{\"aps\":{\"alert\":\"Hello\"}}";
            await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);


            // gcm
            var notif = "{ \"data\" : {\"msg\":\"Hello\"}}";
            await Notifications.Instance.Hub.SendGcmNativeNotificationAsync(notif, userTag);


            return Request.CreateResponse(HttpStatusCode.OK);
        }

21. **F5** 키를 눌러 응용 프로그램을 실행하고 지금까지 작업의 정확성을 확인합니다. 앱은 웹 브라우저를 시작하고 ASP.NET 홈페이지를 표시합니다. 

22. 이제 모든 장치에서 액세스할 수 있도록 이 앱을 Azure 웹 사이트에 배포합니다. **AppBackend** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

23. Azure 웹 사이트를 게시 대상으로 선택합니다.

    ![][B15]

24. Azure 계정으로 로그인하고 기존 또는 새로운 웹 사이트를 선택합니다.

    ![][B16]

25. **연결** 탭의 **대상 URL** 속성을 기록합니다. 이 자습서의 뒷부분에서 이 URL을 *backend endpoint*이라고 합니다. **게시**를 클릭합니다.

    ![][B18]


[1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users15.PNG
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG

<!--HONumber=42-->
