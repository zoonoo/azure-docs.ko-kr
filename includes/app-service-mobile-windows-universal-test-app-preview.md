
Visual Studio로 돌아가서 공유 코드 클라이언트 앱 프로젝트를 선택합니다(이름이 `<your app name>.Shared`와 같음)

1. **App.xaml** 파일을 확장한 다음 **App.xaml.cs** 파일을 엽니다. localhost URL로 초기화된 `MobileService` 구성원의 선언을 찾습니다. 이 선언(`CTRL+K,CTRL+C` 사용)을 주석 처리하고 호스트된 서비스에 연결하는 선언(`CTRL+K,CTRL+U`)의 주석 처리를 제거합니다.

        // This MobileServiceClient has been configured to communicate with your local
        // test project for debugging purposes.
        //public static MobileServiceClient MobileService = new MobileServiceClient(
        //    "http://localhost:58454"
        //);

        // This MobileServiceClient has been configured to communicate with the Azure Mobile Service and
        // Azure Gateway using the application key. You're all set to start working with your Mobile Service!
        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://mymobileapp-code.azurewebsites.net",
            "https://myresourcegroupgateway.azurewebsites.net/Microsoft.Azure.AppService.ApiApps.Gateway",
            "XXXX-APPLICATION-KEY-XXXXX"
        );

2. 기본 시작 프로젝트에 있어야 하며 프로젝트를 다시 빌드하고 Windows 스토어 앱을 시작하려면 F5 키를 누릅니다.

2. 앱에서 **Insert a TodoItem**에 *자습서 완료*와 같은 의미 있는 텍스트를 입력하고 **저장**을 클릭합니다.

	![](./media/app-service-mobile-windows-universal-test-app-preview/mobile-quickstart-startup.png)

	Azure에 호스트된 새 모바일 앱 백 엔드에 POST 요청이 전송됩니다.

3. 디버깅을 중지하고 Windows Phone 스토어 앱에 범용 Windows 솔루션에서 프로젝트를 구성하여 기본 시작 프로젝트를 변경하고(`<your app name>.WindowsPhone` 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 클릭) 다시 F5 키를 누릅니다.

	![](./media/app-service-mobile-windows-universal-test-app-preview/mobile-quickstart-completed-wp8.png)

	Windows 앱이 시작된 후 이전 단계에서 저장한 데이터가 모바일 앱에서 로드됩니다.

<!---HONumber=62-->