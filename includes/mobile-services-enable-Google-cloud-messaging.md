

**참고**

이 항목의 절차를 완료하려면 검증된 전자 메일 주소가 포함된 Google 계정이 있어야 합니다. 새 Google 계정을 만들려면 [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302)으로 이동하십시오.

1.  [Google 클라우드 콘솔](http://cloud.google.com/console)(영문) 웹 사이트로 이동하고 Google 계정 자격 증명을 사용하여 로그인한 후 **CREATE PROJECT**를 클릭합니다.

  ![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)   

    **참고**

    기존 프로젝트가 이미 있는 경우 로그인하면 **Projects** 페이지로 이동됩니다. Dashboard에서 새 프로젝트를 만들려면 **API Project**를 확장하고 **Other projects** 아래의 **Create...**를 클릭한 후 프로젝트 이름을 입력하고 **Create project**를 클릭합니다.

2.  프로젝트 이름을 입력하고 서비스 약관에 동의한 후 **Create**를 클릭합니다. 요청한 SMS 인증을 수행하고 **Create**를 다시 클릭합니다.

3.  **Projects** 섹션에 있는 프로젝트 번호를 기록해 둡니다.

    이 자습서 뒷부분에서 이 값을 클라이언트의 PROJECT\_ID 변수로 설정합니다.

4.  왼쪽 열에서 **APIs & auth**를 클릭한 다음 아래로 스크롤하여 **Google Cloud Messaging for Android**를 사용하도록 토글을 클릭하고 서비스 약관에 동의합니다.

  ![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5.  **Credentials**를 클릭한 다음 **CREATE NEW KEY**를 클릭합니다.

  ![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6.  **Create a new key**에서 **Server key**를 클릭합니다. 다음 창에서 **Create**를 클릭합니다.

  ![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7.  **API key** 값을 기록해 둡니다.

  ![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png) 


