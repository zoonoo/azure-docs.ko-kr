<properties linkid="notification-hubs-how-to-guides-howto-notify-users-aspnet" urlDisplayName="Notify Users" pageTitle="Notify Users of your ASP.NET service with Notification Hubs" metaKeywords="" description="Follow this tutorial to register to receive notifications from your ASP.NET service by using Notification Hubs" metaCanonical="" services="notification-hubs" documentationCenter="" title="Notify users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

알림 허브를 통해 사용자에게 알림
================================

[모바일 서비스](/en-us/manage/services/notification-hubs/notify-users "모바일 서비스")[ASP.NET](/en-us/manage/services/notification-hubs/notify-users-aspnet "ASP.NET")

이 자습서에서는 Azure 알림 허브를 사용하여 특정 장치에서 특정 앱 사용자에게 푸시 알림을 보내는 방법을 보여 줍니다. ASP.NET Web API 백 엔드는 클라이언트를 인증하고 알림을 생성하는 데 사용됩니다. 이 자습서는 이전 **알림 허브 시작** 자습서에서 만든 알림 허브를 기반으로 합니다. 알림 등록 코드는 클라이언트에서 백 엔드 서비스로 이동되었습니다. 따라서 클라이언트가 서비스에서 인증된 후에만 등록이 완료됩니다. 또한 알림 허브 자격 증명이 클라이언트 앱에 분산되지 않습니다. 서비스는 등록 중에 요청되는 태그도 제어합니다.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

-   [인증을 통해 ASP.NET 응용 프로그램 만들기](#create-application)
-   [알림을 등록하도록 ASP.NET 응용 프로그램 업데이트](#register-notification)
-   [로그인하고 등록을 요청하도록 앱 업데이트](#update-app)

필수 조건
---------

-   Visual Studio 2012. Visual Studio Express 2012 for Web과 Visual Studio Express 2012 for Windows 8을 모두 사용하여 각각 ASP.NET 응용 프로그램 및 Windows 스토어 앱을 만들 수도 있습니다.
-   이 자습서는 **알림 허브 시작**에서 만든 앱과 알림 허브를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 **알림 허브 시작**([Windows 스토어 C\#](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet)/[iOS](/en-us/manage/services/notification-hubs/get-started-notification-hubs-ios)/[Android](/en-us/manage/services/notification-hubs/get-started-notification-hubs-android)) 자습서를 완료해야 합니다.

**참고**

이 자습서에서 만드는 ASP.NET Web API 프로젝트는 로컬 컴퓨터에서 실행됩니다. ASP.NET Web API 프로젝트를 Azure에 게시할 수도 있습니다. 자세한 내용은 [ASP.NET Web API 및 SQL 데이터베이스를 사용하여 모바일 지원 REST 서비스 만들기](/en-us/develop/net/tutorials/rest-service-using-web-api/)를 참조하십시오.

ASP.NET 앱 만들기인증을 통해 ASP.NET 응용 프로그램 만들기
---------------------------------------------------------

먼저 ASP.NET Web API 응용 프로그램을 만듭니다. 이 백 엔드 서비스는 클라이언트를 인증하고, 인증된 사용자를 대신하여 푸시 알림을 등록하고, 알림을 보냅니다.

1.  Visual Studio 또는 Visual Studio Express 2012 for Web에서 **파일**을 클릭하고 **파일 메뉴에서 새 프로젝트를 클릭한 후 **템플릿**, **Visual C\#**을 차례로 확장하고 **웹** 및 **ASP.NET MVC 4 웹 응용 프로그램**을 클릭한 후 이름으로 *NotificationService*를 입력하고 **확인**을 클릭합니다.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-create-mvc-app.png)

2.  **새 ASP.NET MVC 프로젝트** 대화 상자에서 **비어 있음**을 클릭한 후 **확인**을 클릭합니다.

    그러면 ASP.NET MVC 프로젝트가 만들어집니다.

3.  솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **클래스**를 클릭하고 `AuthenticationTestHandler`를 입력한 후 **추가**를 클릭합니다.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-create-aspnet-class.png)

    그러면 **AuthenticationTestHandler** 클래스의 코드 파일이 프로젝트에 추가됩니다.

4.  새 AuthenticationTestHandler.cs 프로젝트 파일을 열고 클래스 정의를 다음 코드로 바꿉니다.

         using System;
         using System.Collections.Generic;
         using System.Linq;
         using System.Net;
         using System.Net.Http;
         using System.Threading;
         using System.Threading.Tasks;
         using System.Security.Principal;
         using System.Text;
         using System.Web;
            
         namespace NotificationService
         {
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
                     } else return Unauthorised();
            
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
         } 

    **보안 정보**

    **AuthenticationTestHandler** 클래스는 진정한 인증을 제공하지 않습니다. 이 클래스는 기본 인증과 비슷한 동작을 하고 원칙을 반환하는 목적으로만 사용됩니다. 알림 허브 등록을 만드는 데 사용자 이름이 필요합니다. 위의 구현은 보안이 설정되지 않습니다. 프로덕션 응용 프로그램 및 서비스에 보안 인증 메커니즘을 구현해야 합니다.

5.  **App\_Start** 폴더를 확장하고 WebApiConfig.cs 파일을 연 후 **Register** 메서드 끝에 다음 코드 줄을 추가합니다.

         config.MessageHandlers.Add(new AuthenticationTestHandler());

    그러면 ASP.NET 응용 프로그램에 대한 요청에 Authorization 헤더가 포함됩니다.

이제 사용자 이름을 제공받기 위한 모의 인증 체계로 기본 응용 프로그램을 만들었습니다.

알림 등록알림을 등록하도록 ASP.NET 응용 프로그램 업데이트
---------------------------------------------------------

다음 단계는 새 **등록** 컨트롤러를 만들어 알림 허브에 대한 등록 논리를 ASP.NET 응용 프로그램에 추가하는 것입니다.

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그인하고 **Service Bus**, 네임스페이스, **알림 허브**를 차례로 클릭한 후 알림 허브를 선택하고 **연결 정보**를 클릭합니다.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-select-hub-connection.png)

2.  알림 허브 이름에 주의하고 **DefaultFullSharedAccessSignature**의 연결 문자열을 복사합니다.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-connection-strings.png)

    이 연결 문자열과 알림 허브 이름을 함께 사용하여 알림을 등록하고 보냅니다.

3.  **솔루션 탐색기**에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 선택합니다.

4.  왼쪽 창에서 **온라인** 범주를 선택하고 `WindowsAzure.ServiceBus`를 검색한 후 **Azure 서비스 버스** 패키지에서 **설치**를 클릭하고 사용권 계약에 동의합니다.

	![](./media/notification-hubs-aspnet-notify-users/notification-hub-add-nuget-package.png) 

	그러면 프로젝트에 Microsoft.ServiceBus.dll 어셈블리가 추가됩니다.

1.  솔루션 탐색기에서 **컨트롤러** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **컨트롤러...**를 클릭하고 **컨트롤러 이름**으로 `RegisterController`를 입력한 후 **빈 API 컨트롤러**를 선택하고 **추가**를 클릭합니다.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-add-register-controller2.png)

    그러면 프로젝트에 컨트롤러 클래스가 추가됩니다. 이 컨트롤러는 호출될 경우 장치를 알림 허브에 등록하는 작업을 수행합니다.

2.  새 RegisterController.cs 프로젝트 파일을 열고 다음 **using** 문을 추가합니다.

         using Microsoft.ServiceBus.Notifications;
         using Newtonsoft.Json.Linq;
         using System.Threading.Tasks;
         using System.Web;

3.  다음 코드를 새 RegisterController 클래스에 추가합니다.

         // Define the Notification Hubs client.
         private NotificationHubClient hubClient;

         // Create the client in the constructor.
         public RegisterController()
         {
             var cn = "<FULL_SAS_CONNECTION_STRING>";
             hubClient = NotificationHubClient
                 .CreateClientFromConnectionString(cn, "<NOTIFICATION_HUB_NAME>");
         }

4.  *`<NOTIFICATION_HUB_NAME>`* 및 *`<FULL_SAS_CONNECTION_STRING>`*을 알림 허브의 값으로 바꾸도록 생성자의 코드를 업데이트한 후 **저장**을 클릭합니다.

    **참고**

    *`<FULL_SAS_CONNECTION_STRING>`*에 **DefaultFullSharedAccessSignature**가 사용되는지 확인합니다. 이 클레임을 통해 Web API가 등록을 만들고 업데이트할 수 있습니다.

5.  다음 Post 메서드 코드를 RegisterController 클래스에 추가합니다.

         public async Task<RegistrationDescription> Post([FromBody]JObject registrationCall)
         {
             // Get the registration info that we need from the request. 
             var platform = registrationCall["platform"].ToString();
             var installationId = registrationCall["instId"].ToString();
             var channelUri = registrationCall["channelUri"] != null 
         
                 registrationCall["channelUri"].ToString() : null;
             var deviceToken = registrationCall["deviceToken"] != null 
         
                 registrationCall["deviceToken"].ToString() : null;       
             var userName = HttpContext.Current.User.Identity.Name;

             // Get registrations for the current installation ID.
             var regsForInstId = await hubClient.GetRegistrationsByTagAsync(installationId, 100);

             bool updated = false;
             bool firstRegistration = true;
             RegistrationDescription registration = null;

             // Check for existing registrations.
             foreach (var registrationDescription in regsForInstId)
             {
                 if (firstRegistration)
                 {

    		 // Update the tags.

                     registrationDescription.Tags = new HashSet<string>() { installationId, userName };

                     // We need to handle each platform separately.
                     switch (platform)
                     {
                         case "windows":
                             var winReg = registrationDescription as WindowsRegistrationDescription;
                             winReg.ChannelUri = new Uri(channelUri);
                             registration = await hubClient.UpdateRegistrationAsync(winReg);                            
                             break;
                         case "ios":
                             var iosReg = registrationDescription as AppleRegistrationDescription;
                             iosReg.DeviceToken = deviceToken;
                             registration = await hubClient.UpdateRegistrationAsync(iosReg);
                             break;
                     }
                     updated = true;
                     firstRegistration = false;
                 }
                 else
                 {
                     // We shouldn't have any extra registrations; delete if we do.
                     await hubClient.DeleteRegistrationAsync(registrationDescription);
                 }
             }

             // Create a new registration.
             if (!updated)
             {
                 switch (platform)
                 {
                     case "windows":
                         registration = await hubClient.CreateWindowsNativeRegistrationAsync(channelUri, 
                             new string[] { installationId, userName });
                         break;
                     case "ios":
                         registration = await hubClient.CreateAppleNativeRegistrationAsync(deviceToken, 
                             new string[] { installationId, userName });
                         break;
                 }
             }

             // Send out a test notification.
             sendNotification(string.Format("Test notification for {0}", userName), userName);

             return registration;
         }

    이 코드는 POST 요청에 의해 호출되며 메시지 본문에서 플랫폼 및 장치 ID 정보를 가져옵니다. 이 데이터는 요청 헤더의 설치 ID 및 로그인한 사용자의 사용자 ID와 함께 등록을 업데이트하는 데 사용됩니다. 등록이 없으면 새 등록이 만들어집니다. 이 등록은 사용자 ID 및 설치 ID로 태그가 지정됩니다.

6.  다음 sendNotification 메서드를 추가합니다.

        // Basic implementation that sends a not ification to Windows Store and iOS app clients.
        private async Task sendNotification(string notificationText, string tag)
        {
            try
            {
                // Create notifications for both Windows Store and iOS platforms.
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                    notificationText + "</text></binding></visual></toast>";
                var alert = "{\"aps\":{\"alert\":\"" + notificationText + 
                    "\"}, \"inAppMessage\":\"" + notificationText + "\"}";

                // Send a notification to the logged-in user on both platforms.
                await hubClient.SendWindowsNativeNotificationAsync(toast, tag);
                await hubClient.SendAppleNativeNotificationAsync(alert, tag);
            }
            catch(ArgumentException ex)
            {
                // This is expected when an APNS registration doesn't exist.
                Console.WriteLine(ex.Message);
            }
        }

    등록이 완료되면 이 메서드가 호출되어 즉시 알림이 전송됩니다.

이제 **알림 허브 시작** 자습서를 완료할 때 만든 클라이언트 앱을 업데이트합니다.

앱 업데이트로그인하고 등록을 요청하도록 앱 업데이트
---------------------------------------------------

**알림 허브 시작** 자습서를 완료할 때 만든 앱은 알림 허브에서 직접 등록을 요청합니다. 클라이언트 앱에서 이 등록 코드를 제거하고 ASP.NET Web API 응용 프로그램의 새 Register API에 대한 호출로 바꿉니다.

1.  F5 키를 눌러 ASP.NET 응용 프로그램을 시작하고 기본 페이지를 로드합니다.

    브라우저가 표시되면 요청된 사이트의 호스트 이름을 기록해 둡니다. 클라이언트 앱을 업데이트할 때 이 루트 URL이 필요합니다.

    **참고**

    로컬 IIS 웹 서버 또는 Visual Studio 개발 서버를 사용 중인 경우 포트 번호도 지정해야 합니다. 이 응용 프로그램에서 기본 페이지를 구현하지 않았으므로 404 오류가 반환됩니다.

2.  클라이언트 플랫폼에 따라 다음 **ASP.NET Web API를 사용하여 푸시 알림에 현재 사용자 등록** 버전 중 하나의 단계를 따릅니다.

    -   [Windows 스토어 C\# 버전](/en-us/manage/services/notification-hubs/register-users-aspnet-dotnet)
    -   [iOS 버전](/en-us/manage/services/notification-hubs/howto-register-user-with-aspnet-ios)

3.  업데이트된 앱을 실행하고 사용자 이름 및 암호에 대해 동일한 문자열을 사용하여 서비스에 로그인한 후 알림에 할당된 등록 ID가 표시되는지 확인합니다.

    푸시 알림도 받게 됩니다.

    **참고**

    알림이 전송되도록 요청된 플랫폼에 대한 등록이 없는 경우 백 엔드에서 오류가 발생합니다. 이 경우 이 오류를 무시해도 됩니다. 이 상황을 방지하기 위한 템플릿 사용 방법을 보려면 [알림 허브를 통해 사용자에게 플랫폼 간 알림 보내기](/en-us/manage/services/notification-hubs/notify-users-xplat-aspnet)(영문)를 참조하십시오.

4.  (옵션) 클라이언트 앱을 두 번째 장치에 배포한 후 앱을 실행하고 텍스트를 삽입합니다.

    각 장치에 알림이 표시됩니다.

다음 단계
---------

이제 이 자습서를 완료했으므로 다음 자습서 완료를 고려하십시오.

-   **알림 허브를 사용하여 뉴스 속보 보내기([Windows 스토어 C\#](/en-us/manage/services/notification-hubs/breaking-news-dotnet)(영문)/[iOS](/en-us/manage/services/notification-hubs/breaking-news-dotnet)(영문))**
    이 플랫폼별 자습서는 태그를 사용하여 사용자가 관심 있는 알림 유형을 구독할 수 있도록 하는 방법을 보여 줍니다.

-   **[알림 허브를 통해 사용자에게 플랫폼 간 알림 보내기](/en-us/manage/services/notification-hubs/notify-users-xplat-aspnet)(영문)**
    이 자습서는 플랫폼별 템플릿을 사용하여 알림을 등록하도록 현재 **알림 허브를 통해 사용자에게 알림** 자습서를 확장합니다. 이를 통해 서버 쪽 코드의 단일 메서드에서 알림을 보낼 수 있습니다.

알림 허브에 대한 자세한 내용은 [Azure 알림 허브](http://msdn.microsoft.com/ko-kr/library/windowsazure/jj927170.aspx)를 참조하십시오.

