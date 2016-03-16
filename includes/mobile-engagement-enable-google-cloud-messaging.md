>[AZURE.NOTE]이 절차를 완료하려면 검증된 메일 주소가 포함된 Google 계정이 있어야 합니다. 새 Google 계정을 만들려면 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>으로 이동하세요.

1. [Google 클라우드 콘솔](https://console.developers.google.com/project)로 이동하고 Google 계정 자격 증명을 사용하여 로그인한 후 **프로젝트 만들기**를 클릭합니다.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project.png)

   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project-2.png)

2. 프로젝트 이름을 입력하고 서비스 약관에 동의한 후 **Create**를 클릭합니다. 요청된 경우 SMS 확인을 수행하고 **Create**를 다시 클릭합니다.

3. **Projects** 섹션에 있는 프로젝트 번호를 기록해 둡니다. Android 매니페스트 파일에 채우기 위해 자습서의 뒷 부분에서 필요합니다.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)

4. 왼쪽 열에서 **APIs & auth**를 확장하고 **APIs**를 클릭한 다음 아래로 스크롤하여 **Cloud Messaging for Android**를 클릭합니다. 다음 페이지에서 **Enable API**를 클릭하고 서비스의 조건에 동의합니다.

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-gcm-2.png)

5. **자격 증명**을 클릭한 다음 **자격 증명 추가**->**API 키**를 클릭합니다.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

6. **Create a new key**에서 **Server key**를 클릭합니다. 다음 창에서 **Create**를 클릭합니다.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)


   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key6.png)

7. **API key** 값을 기록해 둡니다. 이 API 키 값을 나중에 사용하여 "네이티브 푸시" 섹션에서 구성합니다.

<!---HONumber=Nov15_HO1-->