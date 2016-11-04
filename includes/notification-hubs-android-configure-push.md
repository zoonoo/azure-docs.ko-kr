

1. [Azure 관리 포털](https://manage.windowsazure.com/)에 로그온하고 화면 맨 아래에 있는 **+새로 만들기**를 클릭합니다.
2. **앱 서비스**, **Service Bus**, **알림 허브**, **빠른 생성**을 차례로 클릭합니다.
3. 알림 허브의 이름을 입력하고 원하는 지역을 선택한 후 **새 알림 허브 만들기**를 클릭합니다.
   
       ![알림 허브 속성 설정](./media/notification-hubs-android-configure-push/notification-hub-create-from-portal2.png)
4. **서비스 버스**에서 방금 만든 네임스페이스(일반적으로 ***알림 허브 이름*-ns**)를 클릭합니다.
5. 네임스페이스에서 위쪽의 **알림 허브** 탭을 클릭한 후 방금 만든 알림 허브를 클릭합니다.
6. 맨 위에 있는 **구성** 탭을 클릭하고 이전 섹션에서 받은 **API 키** 값을 입력한 후 **저장**을 클릭합니다.
   
       ![구성 탭에서 GCM API 키 설정](./media/notification-hubs-android-configure-push/notification-hub-configure-android.png)
7. 맨 위에 있는 **대시보드** 탭을 선택한 후 **연결 문자열 보기**를 클릭합니다. 두 연결 문자열을 기록해 둡니다.

이제 알림 허브가 GCM과 작동하도록 구성되었으며 알림을 받고 푸시 알림을 보내도록 앱을 등록하기 위한 연결 문자열이 있습니다.

<!---HONumber=Oct15_HO3-->