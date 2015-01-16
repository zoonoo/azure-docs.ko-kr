<properties urlDisplayName="Notify Users xplat aspnet" pageTitle="알림 허브를 통해 사용자에게 크로스 플랫폼 알림 보내기(ASP.NET)" metaKeywords="" description="알림 허브 템플릿을 사용하여 모든 플랫폼을 대상으로 하는 플랫폼 중립적인 알림을 단일 요청으로 보내는 방법을 보여 줍니다." metaCanonical="" services="notification-hubs" documentationCenter="" title="Send cross-platform notifications to users with Notification Hubs" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="dotnet" ms.topic="article" ms.date="11/22/2014" ms.author="glenga" />
# 알림 허브를 통해 사용자에게 크로스 플랫폼 알림 보내기


이전 자습서인 [알림 허브를 통해 사용자에게 알림]에서는 인증된 특정 사용자가 등록한 모든 장치에 알림을 푸시하는 방법을 배웠습니다. 해당 자습서에서는 지원되는 각 클라이언트 플랫폼에 알림을 보내기 위해 여러 요청이 필요했습니다. 알림 허브는 특정 장치가 알림을 받는 방법을 지정할 수 있는 템플릿을 지원합니다. 이 경우 크로스 플랫폼 알림 전송이 간소화됩니다. 이 항목에서는 템플릿을 사용하여 단일 요청으로 모든 플랫폼을 대상으로 하는, 플랫폼을 알 수 없는 알림을 보내는 방법을 보여 줍니다. 템플릿에 대한 자세한 내용은 [Azure 알림 허브 개요][Templates]를 참조하세요.

<div class="dev-callout"><b>참고</b>
	<p>알림 허브를 사용하면 장치가 동일한 태그로 여러 템플릿을 등록할 수 있습니다. 이 경우 해당 태그를 대상으로 들어오는 메시지가 있으면 각 템플릿에 대해 하나씩 여러 개의 알림이 장치에 전달됩니다. 이런 방식으로 Windows 스토어 앱에 알림 메시지와 배지 둘 다로 표시하는 등 여러 시각적 알림에 동일한 메시지를 표시할 수 있습니다.</p>
</div>

템플릿을 사용하여 크로스 플랫폼 알림을 보내려면 다음 단계를 따르세요.

1. Visual Studio의 솔루션 탐색기에서 **컨트롤러** 폴더를 확장한 다음 RegisterController.cs 파일을 엽니다. 

2. **Post** 메서드에서 새 등록을 만드는 코드 블록을 찾아서 `switch` 내용을 다음 코드로 바꿉니다.

		switch (deviceUpdate.Platform)
        {
            case "mpns":
                var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>$(message)</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
                registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "wns":

                registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "apns":
                var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{\"data\":{\"msg\":\"$(message)\"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }
	
	이 코드는 플랫폼 특정 메서드를 호출하여 기본 등록이 아니라 템플릿 등록을 만듭니다. 템플릿 등록은 기본 등록에서 파생되므로 기존 등록을 수정할 필요는 없습니다.

3. **알림** 컨트롤러에서 **sendNotification** 메서드를 다음 코드로 바꿉니다.

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;

            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);   

            return Request.CreateResponse(HttpStatusCode.OK);
        }

	이 코드는 네이티브 페이로드를 지정할 필요 없이 동시에 모든 플랫폼에 알림을 보냅니다. 알림 허브는 등록된 템플릿에 지정된 대로 올바른 페이로드를 작성하고 제공된 _태그_ 값을 가진 모든 장치에 전달합니다.

4. WebApi 백 엔드 프로젝트를 다시 게시합니다.

5. 클라이언트 앱을 다시 실행하고 등록이 성공했는지 확인합니다.

6. (옵션) 클라이언트 앱을 두 번째 장치에 배포한 후 앱을 실행합니다. 

	각 장치에 알림이 표시됩니다.

## 다음 단계

이 자습서를 마쳤습니다. 이제 다음 항목에서 알림 허브 및 템플릿에 대해 자세히 알아보세요.

+ **[알림 허브를 사용하여 속보 보내기]** <br/>다른 템플릿 사용 시나리오를 보여 줍니다. 

+  **[Azure 알림 허브 개요][Templates]**<br/>개요 항목에는 템플릿에 대한 세부 정보가 포함되어 있습니다.

+  **[Windows 스토어에 대한 알림 허브 방법]**<br/>템플릿 표현 언어 참조가 포함되어 있습니다.



<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[사용자에게 푸시 알림 보내기(ASP.NET)]: /ko-kr/manage/services/notification-hubs/notify-users-aspnet
[사용자에게 푸시 모바일 서비스]: /ko-kr/manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[관리 포털]: https://manage.windowsazure.com/
[알림 허브를 사용하여 속보 보내기]: /ko-kr/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Azure 알림 허브]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[알림 허브를 통해 사용자에게 알림]: /ko-kr/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/
[템플릿]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Windows 스토어에 대한 알림 허브 방법]: http://msdn.microsoft.com/ko-kr/library/windowsazure/jj927172.aspx

<!--HONumber=35.1-->
