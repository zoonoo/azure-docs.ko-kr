>[AZURE.NOTE]이 절차를 완료하려면 검증된 메일 주소가 포함된 Google 계정이 있어야 합니다. 새 Google 계정을 만들려면 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>으로 이동하세요.


1. [Google 클라우드 콘솔](https://console.developers.google.com/project)로 이동하고 Google 계정 자격 증명을 사용하여 로그인한 후 **프로젝트 만들기**를 클릭합니다.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)

	>[AZURE.NOTE]기존 프로젝트가 이미 있는 경우 로그인하면 <strong>Projects</strong> 페이지로 이동됩니다. Dashboard에서 새 프로젝트를 만들려면 <strong>API Project</strong>를 확장하고 <strong>Other projects</strong> 아래의 <strong>Create...</strong>를 클릭한 후 프로젝트 이름을 입력하고 <strong>Create project</strong>를 클릭합니다.

2. 프로젝트 이름을 입력하고 서비스 약관에 동의한 후 **Create**를 클릭합니다. 요청된 경우 SMS 확인을 수행하고 **Create**를 다시 클릭합니다.

3. **Projects** 섹션에 있는 프로젝트 번호를 기록해 둡니다.

	이 자습서 뒷부분에서 이 값을 클라이언트의 PROJECT\_ID 변수로 설정합니다.

4. 왼쪽 열에서 **APIs & auth**를 확장하고 **APIs**를 클릭한 다음 아래로 스크롤하여 **Cloud Messaging for Android**를 클릭합니다. 다음 페이지에서 **Enable API**를 클릭하고 서비스의 조건에 동의합니다.

	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5. **자격 증명**을 클릭한 다음 **자격 증명 추가**->**API 키**를 클릭합니다.


   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6. **Create a new key**에서 **Server key**를 클릭합니다. 다음 창에서 **Create**를 클릭합니다.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key5.png)


   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key6.png)

7. **API key** 값을 기록해 둡니다.


	이 API 키를 사용하여 Azure 에서 GCM에 인증하고 앱 대신 푸시 알림을 보낼 수 있게 합니다.

<!---HONumber=Nov15_HO1-->