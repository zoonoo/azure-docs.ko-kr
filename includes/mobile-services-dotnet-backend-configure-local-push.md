
Azure에 게시하기 전에 선택적으로 로컬 컴퓨터 또는 VM에서 실행 중인 모바일 서비스로 푸시 알림을 테스트할 수 있습니다. 그렇게 하려면 web.config 파일의 앱에 사용되는 알림 허브에 대한 정보를 설정해야 합니다. 이 정보는 알림 허브에 연결하기 위해 로컬로 실행 중인 경우에만 사용되며 Azure에 게시될 때에는 무시됩니다.

1. 모바일 서비스의 **게시** 탭으로 돌아와 **알림 허브** 링크를 클릭합니다.

	![](./media/mobile-services-dotnet-backend-configure-local-push/link-to-notification-hub.png)

	모바일 서비스에 사용되는 알림 허브로 이동합니다.

2. 알림 허브 페이지에서 알림 허브의 이름을 기록한 다음, **연결 문자열 보기**를 클릭합니다.

	![](./media/mobile-services-dotnet-backend-configure-local-push/notification-hub-page.png)

3. **Access 연결 정보**에서 **DefaultFullSharedAccessSignature** 연결 문자열을 복사합니다.

	![](./media/mobile-services-dotnet-backend-configure-local-push/notification-hub-connection-string.png)

4. Visual Studio의 모바일 서비스 프로젝트에서, 서비스에 대한 Web.config 파일을 열고 **connectionStrings**에서, **MS_NotificationHubConnectionString**에 대한 연결 문자열을 이전 단계의 연결 문자열로 바꿉니다.

5. **appSettings**에서, **MS_NotificationHubName** 앱 설정의 값을 알림 허브의 이름으로 바꿉니다.

이제 모바일 서비스 프로젝트는 로컬로 실행될 때 Azure에서 알림 허브에 연결되도록 구성됩니다. Azure에서 실행할 때 이러한 Web.config 프로젝트 설정이 포털 설정에 의해 재정의되므로 포털과 동일한 알림 허브 이름 및 연결 문자열을 사용하는 것이 중요합니다.

<!---HONumber=July15_HO2-->