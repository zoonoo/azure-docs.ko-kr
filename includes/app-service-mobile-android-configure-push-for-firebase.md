
1. [Azure 포털](https://portal.azure.com/)에서 **모두 찾아보기** > **App Services** > Mobile App 백 엔드를 클릭합니다. **설정** 아래에서 **App Service 푸시**를 클릭한 후 알림 허브 이름을 클릭합니다.
2. **Google(GCM)**로 이동하고, 이전 절차에서 Firebase로부터 얻은 **Server Key** 값을 입력한 다음 **저장**을 클릭합니다.
   
    ![포털에서 GCM API 키 설정하기](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

이제 Mobile App 백 엔드는 알림 허브를 사용하는 Android 장치에서 실행 중인 앱에 Firebase Cloud Messaging을 사용하여 푸시 알림을 보내도록 구성됩니다.

<!-- URLs. -->


<!-- images -->


<!--HONumber=Nov16_HO3-->


