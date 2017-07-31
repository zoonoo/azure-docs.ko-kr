
1. **App Services**를 클릭하고, 모바일 앱 백 엔드를 선택하고, **빠른 시작** > 클라이언트 플랫폼(iOS, Android, Xamarin, Cordova)을 차례로 클릭합니다.

![모바일 앱 빠른 시작이 강조 표시된 Azure 포털][quickstart]

2. 데이터베이스 연결이 구성되지 않은 경우 하나의 데이터 연결을 만들어야 합니다.

![Mobile Apps가 있는 Azure Portal - BD에 연결][connect]

  * 새 SQL Database 및 서버를 만듭니다.

  ![Mobile Apps가 있는 Azure Portal - 새 BD 및 서버 만들기][server]

  * 데이터 연결을 성공적으로 만들 때까지 기다립니다.

  ![Mobile Apps가 있는 Azure Portal - 데이터 연결 만들기에 대한 알림][notification]

  * 데이터 연결이 성공해야 합니다.

  ![Mobile Apps가 있는 Azure Portal - 데이터 연결 만들기에 대한 알림][already-connection]

3. **2.  테이블 API 만들기** 아래에서 **백 엔드 언어**로 Node.js를 선택합니다. 승인을 허용하고 **TodoItem 테이블 만들기**를 클릭합니다. 데이터베이스의 테이블에 새 *TodoItem* 을 만듭니다. 기존 백 엔드를 Node.js로 전환하면 모든 콘텐츠를 덮어쓰게 되다는 점에 유의해야 합니다. 대신 .NET 백 엔드를 만들려면 [이 지침을 따르세요][instructions].

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
