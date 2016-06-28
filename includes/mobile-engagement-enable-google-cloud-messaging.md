
###API 키를 가진 Google Cloud Messaging 프로젝트 만들기

>[AZURE.NOTE] 이 절차를 완료하려면 검증된 메일 주소가 포함된 Google 계정이 있어야 합니다. 새 Google 계정을 만들려면 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>으로 이동하세요.

1. [Google 클라우드 콘솔](https://console.developers.google.com/project)로 이동하고 Google 계정 자격 증명으로 로그인합니다.

2. **모든 프로젝트**로 이동한 다음 **프로젝트 만들기**를 클릭합니다.

3. **프로젝트 이름**을 입력하고 **만들기**를 클릭합니다.

4. 프로젝트가 생성되면 긴 숫자 값이 되는 **프로젝트 번호**를 기록해 둡니다. 프로젝트의 **설정**에서 **IAM 및 관리자 섹션** 아래에서 찾을 수 있으며 나중에 필요합니다.
 
	![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)

5. 이제 플랫폼에서 Android 장치에 알림을 전송하는 데 사용되는 Google Cloud Messaging 플랫폼에 대한 키를 만듭니다. **API 관리자** 섹션으로 이동하고 **모바일 API** 아래의 **Google Cloud Messaging**을 클릭합니다.

	![](./media/mobile-engagement-enable-google-cloud-messaging/gcm.png)

6. 다음 페이지에서 **사용** 단추를 클릭합니다. 대시보드는 자격 증명을 만들라는 메시지를 표시합니다. 따라서 **자격 증명으로 이동** 단추를 클릭합니다.

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)

6. 첫 번째 드롭다운 상자에서 **Google Cloud Messaging**을 선택하고 두 번째 드롭다운 상자에서 **웹 서버**를 선택한 후 **필요한 자격 증명**을 클릭합니다.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

7. **프로젝트에 자격 증명 추가** 페이지에서 **API 키 만들기**를 클릭합니다.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)

8. **API key** 값을 기록해 둡니다. 이 API 키 값을 나중에 사용하여 "네이티브 푸시" 섹션에서 구성합니다. 이제 **완료**를 클릭합니다.

<!---HONumber=AcomDC_0622_2016-->