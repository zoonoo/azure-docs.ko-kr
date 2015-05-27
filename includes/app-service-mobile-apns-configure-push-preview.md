APNS에 앱을 등록하고 프로젝트를 구성한 후, 다음으로 APNS와 통합되도록 모바일 앱을 구성해야 합니다.

1. Keychain Access에서 **키** 또는 **내 인증서**의 퀵 스타트 앱의 새 인증서를 마우스 오른쪽 단추로 클릭하고 **내보내기**를 클릭한 다음에 파일 이름을 QuickstartPusher로 지정하고 **.p12** 형식을 선택한 후에 **저장**을 클릭합니다.

   	![](./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png)

    내보낸 인증서의 파일 이름과 위치를 적어둡니다.

>[AZURE.NOTE]이 자습서에서는 QuickstartPusher.p12 파일을 만듭니다. 파일 이름과 위치가 다를 수 있습니다.

2. [Azure Preview 포털]에 로그온하고 **찾아보기**, **모바일 앱**을 선택한 다음 앱을 클릭합니다. 푸시 알림 서비스를 클릭합니다.

3. Apple 푸시 알림 서비스에서 **.p12** 파일로 인증서 및 이와 연관된 암호를 업로드하고 원하는 모드를 선택합니다.

앱 서비스 모바일 앱은 이제 APNS와 작동하도록 구성되었습니다.

<!-- URLs. -->
[Azure Preview 포털]: https://portal.azure.com/

<!--HONumber=54-->