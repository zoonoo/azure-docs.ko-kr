APNS에 앱을 등록하고 프로젝트를 구성했으면 APNS와 통합되도록 모바일 서비스를 구성해야 합니다.

1. Keychain Access에서 **Keys** 또는 **My Certificates**의 quickstart 앱 새 인증서를 마우스 오른쪽 단추로 클릭하고 **Export**를 클릭하고 파일의 이름을 QuickstartPusher로 지정하고 **.p12** 형식을 선택한 다음 **Save**를 클릭합니다.

   	![](./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png)

  내보낸 인증서의 파일 이름과 위치를 적어둡니다.

>[WACOM.NOTE] 이 자습서에서는 QuickstartPusher.p12 파일을 만듭니다. 파일 이름과 위치가 다를 수 있습니다.

2. [Azure 관리 포털]에 로그온하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

   	![](./media/mobile-services-apns-configure-push/mobile-services-selection.png)

3. **푸시** 탭을 클릭하고 **업로드**를 클릭합니다.

   	![](./media/mobile-services-apns-configure-push/mobile-push-tab-ios.png)

	인증서 업로드 대화 상자가 표시됩니다.

4. **파일**을 클릭하고 내보낸 인증서 QuickstartPusher.p12 파일을 선택하고 **암호**를 입력하고 올바른 **모드**를 선택했는지 확인하고(Dev/Sandbox 또는 Prod/Production) 확인 아이콘을 클릭한 다음 **저장**을 클릭합니다.

   	![](./media/mobile-services-apns-configure-push/mobile-push-tab-ios-upload.png)

    > [WACOM.NOTE] 이 자습서에서는 개발 인증서를 사용합니다.

모바일 서비스가 이제 APNS와 작동하도록 구성됩니다.

<!-- URLs. -->
[Azure 관리 포털]: https://manage.windowsazure.com/
