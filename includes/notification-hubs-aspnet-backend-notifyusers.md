## WebAPI 프로젝트 만들기

새 ASP.NET WebAPI 백 엔드는 이후 섹션에서 만들어지며 다음 세 가지 주요 목적이 있습니다.

1. **클라이언트 인증**: 나중에 클라이언트 요청을 인증하고 사용자를 요청과 연결하는 메시지 처리기가 추가될 예정입니다.
2. **클라이언트 알림 등록**: 나중에 클라이언트 장치에서 알림을 받을 수 있도록 새 등록을 처리하는 컨트롤러를 추가할 예정입니다. 인증된 사용자 이름은 [태그](https://msdn.microsoft.com/library/azure/dn530749.aspx)로 등록에 자동으로 추가됩니다.
3. **클라이언트로 알림 보내기**: 나중에 사용자가 태그와 연결된 장치 및 클라이언트로 보안 푸시를 트리거할 수 있는 방법을 제공하는 컨트롤러를 추가할 예정입니다. 

다음 단계에서는 새 ASP.NET WebAPI 백 엔드를 만드는 방법을 보여 줍니다.


> [AZURE.NOTE] **중요**: 이 자습서를 시작하기 전에 최신 버전의 NuGet 패키지 관리자를 설치했는지 확인하세요. 확인하려면 Visual Studio를 시작합니다. **도구** 메뉴에서 **확장 및 업데이트**를 클릭합니다. **Visual Studio 2013용 NuGet 패키지 관리자**를 검색하고 버전이 2.8.50313.46 이상인지 확인합니다. 그렇지 않은 경우 제거한 다음, NuGet 패키지 관리자를 다시 설치합니다.
> 
> ![][B4]

> [AZURE.NOTE] 웹 사이트 배포를 위해 Visual Studio [Azure SDK](https://azure.microsoft.com/downloads/)를 설치했는지 확인합니다.

1. Visual Studio 또는 Visual Studio Express를 시작합니다. **서버 탐색기**를 클릭하고 Azure 계정에 로그인합니다. 계정에 웹 사이트 리소스를 만들려면 Visual Studio에 로그인해야 합니다.
2. Visual Studio에서 **파일**을 클릭한 후 **새로 만들기**, **프로젝트**를 클릭하고 **템플릿**, **Visual C#**을 확장한 다음 **웹** 및 **ASP.NET 웹 응용프로그램**을 클릭하고 **AppBackend**라는 이름을 입력한 후 **확인**을 클릭합니다. 
	
	![][B1]

3. **새 ASP.NET MVC 프로젝트** 대화 상자에서 **웹 API**를 클릭한 다음, **확인**을 클릭합니다.

	![][B2]

4. **Microsoft Azure 웹 앱** 대화 상자에서 구독 및 이미 만들어둔 **앱 서비스 계획**을 선택합니다. **새 앱 서비스 계획 만들기**를 선택하고 대화 상자에서 만들 수도 있습니다. 이 자습서를 위해 데이터베이스는 필요하지 않습니다. 앱 서비스 계획을 선택한 후 **확인**을 클릭하여 프로젝트를 만듭니다.

	![][B5]



## WebAPI 백 엔드에 클라이언트 인증

이 섹션에서는 새 백 엔드에 대해 **AuthenticationTestHandler**라는 새 메시지 처리기 클래스를 만듭니다. 이 클래스는 [DelegatingHandler](https://msdn.microsoft.com/library/system.net.http.delegatinghandler.aspx)에서 파생되며 백 엔드로 들어오는 모든 요청을 처리할 수 있도록 메시지 처리기로 추가됩니다.



1. 솔루션 탐색기에서 **AppBackend** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**, **클래스**를 차례로 클릭합니다. 새 클래스의 이름을 **AuthenticationTestHandler.cs**로 지정하고 **추가**를 클릭하여 클래스를 생성합니다. 이 클래스는 간단히 하기 위해 *기본 인증*을 사용하여 사용자를 인증하는 데 사용됩니다. 앱은 모든 인증 체계를 사용할 수 있습니다.

2. AuthenticationTestHandler.cs에 다음 `using` 문을 추가합니다.

        using System.Net.Http;
        using System.Threading;
        using System.Security.Principal;
        using System.Net;
        using System.Web;

3. AuthenticationTestHandler.cs에서 `AuthenticationTestHandler` 클래스 정의를 다음으로 바꿉니다.

	이 처리기는 다음 세 조건이 모두 참일 때 요청에 권한을 부여합니다. * 요청이 *권한 부여* 헤더에 포함되었습니다. * 요청이 *기본* 인증을 사용합니다. * 사용자 이름 문자열 및 암호 문자열이 동일한 문자열입니다.

	그렇지 않으면 요청이 거부됩니다. 이는 실제 인증 및 권한 부여 방법이 아닙니다. 이 자습서를 위한 매우 간단한 예제일 뿐입니다.

	요청 메시지가 `AuthenticationTestHandler`에 의해 인증되고 권한이 부여되면 [HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.current.aspx)의 현재 요청에 기본 인증 사용자가 연결됩니다. HttpContext의 사용자 정보는 나중에 다른 컨트롤러(RegisterController)에서 알림 등록 요청에 [태그](https://msdn.microsoft.com/library/azure/dn530749.aspx)를 추가하는 데 사용됩니다.

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
	                else return Unauthorized();
	            }
	            else return Unauthorized();
	
	            return base.SendAsync(request, cancellationToken);
	        }
	
	        private bool verifyUserAndPwd(string user, string password)
	        {
	            // This is not a real authentication scheme.
	            return user == password;
	        }
	
	        private Task<HttpResponseMessage> Unauthorized()
	        {
	            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
	            var tsc = new TaskCompletionSource<HttpResponseMessage>();
	            tsc.SetResult(response);
	            return tsc.Task;
	        }
	    }

	> [AZURE.NOTE] **보안 정보**: `AuthenticationTestHandler` 클래스는 진정한 의미의 인증을 제공하지 않습니다. 이 클래스는 기본 인증과 비슷한 동작을 하고 보안이 안전하지 않습니다. 프로덕션 응용프로그램 및 서비스에 보안 인증 메커니즘을 구현해야 합니다.

4. **App\_Start/WebApiConfig.cs** 클래스의 `Register` 메서드 끝에 다음 코드를 추가하여 메시지 처리기를 등록합니다.

		config.MessageHandlers.Add(new AuthenticationTestHandler());

5. 변경 내용을 저장합니다.

## WebAPI 백 엔드를 사용하여 알림 등록

이 섹션에서는 알림 허브에 클라이언트 라이브러리를 사용하여 알림을 위한 사용자 및 장치 등록 요청을 처리하는 새 컨트롤러를 WebAPI 백 엔드에 추가합니다. 이 컨트롤러는 `AuthenticationTestHandler`에 의해 인증되고 HttpContext에 연결된 사용자에 대한 사용자 태그를 추가합니다. 태그의 문자열 형식은 `"username:<actual username>"`입니다.


 

1. 솔루션 탐색기에서 **AppBackend**프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리**를 클릭합니다.

2. 왼쪽에서 **온라인**을 클릭하고 **검색** 상자에서 **Microsoft.Azure.NotificationHubs**를 검색합니다.

3. 결과 목록에서 **Microsoft Azure 알림 허브**를 클릭한 다음 **설치**를 클릭합니다. 설치를 완료한 다음, NuGet 패키지 관리자 창을 닫습니다.

	그러면 <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet 패키지</a>를 사용하는 Azure 알림 허브 SDK에 대한 참조가 추가됩니다.

4. 이제 전송할 다른 보안 알림을 나타내는 새 클래스 파일을 만듭니다. 완전한 구현에서 알림은 데이터베이스에 저장됩니다. 여기서는 단순화를 위해 메모리에 알림을 저장합니다. 솔루션 탐색기에서 **Models** 폴더를 마우스 오른쪽 단추로 클릭한 후 **추가**, **클래스**를 차례로 클릭합니다. 새 클래스 이름을 **Notifications.cs**로 지정한 후 **추가**를 클릭하여 클래스를 생성합니다.

	![][B6]

5. Notifications.cs에서 파일의 맨 위에 `using` 문을 추가합니다.

        using Microsoft.Azure.NotificationHubs;

6. `Notifications` 클래스 정의를 다음으로 바꾸고 두 개의 자리 표시자를 알림 허브에 대한 연결 문자열(모든 권한 사용) 및 허브 이름([Azure 클래식 포털](http://manage.windowsazure.com)에서 제공)으로 바꿉니다.

		public class Notifications
        {
            public static Notifications Instance = new Notifications();
        
            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>", 
																			 "<hub name>");
            }
        }



7. 다음으로 **RegisterController**라는 새 컨트롤러를 만듭니다. 솔루션 탐색기에서 **Controllers** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**와 **컨트롤러**를 차례로 클릭합니다. **웹 API 2 컨트롤러 -- 비어 있음** 항목을 클릭한 다음 **추가**를 클릭합니다. 새 클래스 이름을 **RegisterController**로 지정한 다음 **추가**를 다시 클릭하여 컨트롤러를 생성합니다.

	![][B7]

	![][B8]

8. RegiterController.cs에 다음 `using` 문을 추가합니다.

        using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.NotificationHubs.Messaging;
        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;

9. 다음 코드를 `RegisterController` 클래스 정의 내에 추가합니다. 이 코드에서 HttpContext에 연결된 사용자에 대한 사용자 태그를 추가합니다. 이 사용자는 추가한 메시지 필터 `AuthenticationTestHandler`에 의해 인증되고 HttpContext에 연결되었습니다. 또한 선택적인 검사를 추가하여 사용자에게 요청된 태그에 등록할 수 있는 권한이 있는지 확인합니다.

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
            string newRegistrationId = null;
            
            // make sure there are no existing registrations for this push handle (used for iOS and Android)
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

            if (newRegistrationId == null) 
				newRegistrationId = await hub.CreateRegistrationIdAsync();

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

10. 변경 내용을 저장합니다.

## WebAPI 백 엔드에서 알림 보내기

이 섹션에서는 클라이언트 장치가 ASP.NET WebAPI 백 엔드에서 Azure 알림 허브 서비스 관리 라이브러리를 사용하여 사용자 이름 태그에 따라 알림을 보낼 수 있는 방법을 제공하는 새 컨트롤러를 추가합니다.


1. **NotificationsController**라는 다른 새 컨트롤러를 만듭니다. 이전 섹션에서 **RegisterController**를 만들 때와 동일한 방법으로 만듭니다.

2. NotificationsController.cs에 다음 `using` 문을 추가합니다.

        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;

3. **NotificationsController** 클래스에 다음 메서드를 추가합니다.

	이 코드는 PNS(Platform Notification Service) `pns` 매개 변수를 기반으로 알림 유형을 보냅니다. `to_tag` 값은 메시지에서 *사용자 이름* 태그를 지정하는 데 사용됩니다. 이 태그는 활성 알림 허브 등록의 사용자 이름 태그와 일치해야 합니다. 알림 메시지는 POST 요청의 본문에서 가져오고 대상 PNS에 맞게 형식이 지정됩니다.

	알림을 수신하기 위해 지원되는 장치가 사용하는 플랫폼 알림 서비스(PNS)에 따라 다른 형식을 사용하여 다양한 알림을 지원합니다. 예를 들어 Windows 장치에서 다른 PNS에서 직접 지원되지 않는 [WNS로 알림](https://msdn.microsoft.com/library/windows/apps/br230849.aspx)을 사용할 수 있습니다. 따라서 백 엔드는 알림을 지원하려는 장치의 PNS에 지원되는 알림으로 포맷해야 합니다. 그런 다음 [NotificationHubClient 클래스](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.notificationhubclient_methods.aspx)에서 적절한 전송 API를 사용합니다.

        public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag)
        {
            var user = HttpContext.Current.User.Identity.Name;
            string[] userTag = new string[2];
            userTag[0] = "username:" + to_tag;
            userTag[1] = "from:" + user;

            Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;
            HttpStatusCode ret = HttpStatusCode.InternalServerError;

            switch (pns.ToLower())
            {
                case "wns":
                    // Windows 8.1 / Windows Phone 8.1
                    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + 
                                "From " + user + ": " + message + "</text></binding></visual></toast>";
                    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
                    break;
                case "apns":
                    // iOS
                    var alert = "{"aps":{"alert":"" + "From " + user + ": " + message + ""}}";
                    outcome = await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);
                    break;
                case "gcm":
                    // Android
                    var notif = "{ "data" : {"message":"" + "From " + user + ": " + message + ""}}";
                    outcome = await Notifications.Instance.Hub.SendGcmNativeNotificationAsync(notif, userTag);
                    break;
            }

            if (outcome != null)
            {
                if (!((outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Abandoned) ||
                    (outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Unknown)))
                {
                    ret = HttpStatusCode.OK;
                }
            }

            return Request.CreateResponse(ret);
        }


4. **F5** 키를 눌러 응용프로그램을 실행하고 지금까지 작업의 정확성을 확인합니다. 앱은 웹 브라우저를 시작하고 ASP.NET 홈페이지를 표시합니다.

##새 WebAPI 백엔드 게시

1. 이제 모든 장치에서 액세스할 수 있도록 이 앱을 Azure 웹 사이트에 배포합니다. **AppBackend** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

2. 게시 대상으로 **Microsoft Azure 웹앱**을 선택합니다.

    ![][B15]

3. Azure 계정으로 로그인하고 기존 또는 새로운 웹 앱을 선택합니다.

    ![][B16]

4. **연결** 탭의 **대상 URL** 속성을 기록합니다. 이 자습서의 뒷부분에서 이 URL을 *백 엔드 끝점*이라고 합니다. **게시**를 클릭합니다.

    ![][B18]


[B1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[B2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[B3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[B4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[B5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[B6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[B7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[B8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[B14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users15.PNG
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG

<!---HONumber=AcomDC_0128_2016-->