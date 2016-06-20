
###API 키를 가진 Google Cloud Messaging 프로젝트 만들기

>[AZURE.NOTE] 이 절차를 완료하려면 검증된 메일 주소가 포함된 Google 계정이 있어야 합니다. 새 Google 계정을 만들려면 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>으로 이동하세요.

1. [Google 클라우드 콘솔](https://console.developers.google.com/project)로 이동하고 Google 계정 자격 증명으로 로그인합니다.

2. **프로젝트로 이동**을 클릭하고 **프로젝트 만들기**를 클릭합니다.
<!--
   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project.png)

   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project-2.png)   
-->
3. 프로젝트 이름을 입력합니다.

4. **프로젝트 이름** 텍스트 상자 아래에 표시되는 프로젝트 번호를 적어둡니다. Android 매니페스트 파일에 채우기 위해 자습서의 뒷 부분에서 필요합니다. ![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)
5. **만들기**를 클릭합니다.

6. 왼쪽 열에서 **개요**가 선택되어 있는지 확인하고 모바일 API에서 **Google Cloud Messaging**을 클릭합니다. 다음 페이지에서 **사용**을 클릭합니다.

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)
<!--
	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-gcm-2.png)
-->
7. 다음 페이지에서 **자격 증명으로 이동**을 클릭하고 그 다음 페이지의 첫 번째 드롭다운 상자에서 **Google Cloud Messaging**을 선택하고 두 번째 드롭다운 상자에서 **웹 서버**를 선택한 후 **필요한 자격 증명**을 클릭합니다.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

8. **프로젝트에 자격 증명 추가** 페이지에서 **API 키 만들기**를 클릭합니다.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)
<!--
   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key6.png)
-->
9. **API key** 값을 기록해 둡니다. 이 API 키 값을 나중에 사용하여 "네이티브 푸시" 섹션에서 구성합니다. 이제 **완료**를 클릭합니다.

<!---HONumber=AcomDC_0608_2016-->