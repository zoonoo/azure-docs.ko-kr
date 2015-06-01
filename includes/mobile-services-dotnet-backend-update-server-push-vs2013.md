이 단계를 수행하여 앱에 푸시 알림을 보내는 새 사용자 지정 [ApiController][ApiController]를 만듭니다. [TableController][TableController] 또는 백 엔드 서비스의 다른 위치에서 같은 코드를 구현할 수 있습니다.

1.  Visual Studio 솔루션 탐색기에서 모바일 서비스 프로젝트의 Controllers 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 확장한 후 **새 스캐폴드 항목**을 클릭합니다.

    그러면 스캐폴드 추가 대화 상자가 표시됩니다.

2.  **Azure 모바일 서비스**를 확장하고 **Azure 모바일 서비스 사용자 지정 컨트롤러**를 클릭한 후 **추가**를 클릭하고 **컨트롤러 이름**을 `NotifyAllUsersController`로 입력한 후 **추가**를 다시 클릭합니다.

    ![Web API 스캐폴드 추가 대화 상자][Web API 스캐폴드 추가 대화 상자]

    그러면 이름이 **NotifyAllUsersController**인 빈 컨트롤러 클래스가 새로 생성됩니다.

3.  새 NotifyAllUsersController.cs 프로젝트 파일에서 다음 **using** 문을 추가합니다.

        using Newtonsoft.Json.Linq;
        using System.Threading.Tasks;

4.  컨트롤러에 대해 POST 메서드를 구현하는 다음 코드를 추가합니다.

        public async Task<bool> Post(JObject data)
        {
            try
            {
                // Define the XML paylod for a WNS native toast notification 
                // that contains the value supplied in the POST request.
                string wnsToast = 
                    string.Format("<?xml version="1.0" encoding="utf-8"?>" +
                    "<toast><visual><binding template="ToastText01">" + 
                    "<text id="1">{0}</text></binding></visual></toast>", 
                    data.GetValue("toast").Value<string>());

                // Define the WNS native toast with the payload string.
                WindowsPushMessage message = new WindowsPushMessage();
                message.XmlPayload = wnsToast;

                // Send the toast notification.
                await Services.Push.SendAsync(message);
                return true;
            }
            catch (Exception e)
            {
                Services.Log.Error(e.ToString());
            }
            return false;
        }

    > [WACOM.NOTE]이 POST 메서드는 응용 프로그램 키를 소유한 모든 클라이언트가 호출할 수 있으므로 안전하지 않습니다. 끝점을 보호하려면 인증이 필요하도록 메서드나 클래스에 `[AuthorizeLevel(AuthorizationLevel.User)]` 특성을 적용합니다.

  [ApiController]: http://go.microsoft.com/fwlink/p/?LinkId=512673
  [TableController]: http://msdn.microsoft.com/library/azure/dn643359.aspx
  [Web API 스캐폴드 추가 대화 상자]: ./media/mobile-services-dotnet-backend-update-server-push-vs2013/add-custom-api-controller.png
