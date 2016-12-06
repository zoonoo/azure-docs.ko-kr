App Service Mobile Apps에서는 [Notification Hubs]를 사용하여 푸시를 보내므로 모바일 앱에 대해 알림 허브가 구성됩니다.

1. [Azure Portal]에서 **찾아보기** > **App Services**로 이동하고 앱 백 엔드를 클릭합니다. **설정** 아래에서 **App Service 푸시**를 클릭합니다.
2. **구성**을 클릭하고 알림 허브를 구성합니다. 허브를 만들거나 기존 허브에 연결할 수 있습니다.
   
    ![](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

이제 알림 허브를 모바일 앱 백 엔드에 연결했습니다. 나중에 장치에 푸시하는 PNS(플랫폼 알림 시스템)에 연결하도록 이 알림 허브를 구성합니다.

[Azure Portal]: https://portal.azure.com/
[Notification Hubs]: https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-push-notification-overview/

<!--HONumber=Nov16_HO3-->


