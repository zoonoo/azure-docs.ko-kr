
1. Mac에서 [Azure 포털]을 방문합니다. **모두 찾아보기** > **Mobile Apps** > 방금 만든 백 엔드를 클릭합니다. 모바일 앱 설정에서 **빠른 시작** > **iOS(Objective-C)** 를 클릭합니다. Swift를 원하는 경우 **빠른 시작** > **iOS(Swift)** 를 대신 클릭합니다. **iOS 프로젝트 다운로드 및 실행** 아래에서 **다운로드**를 클릭합니다. 그러면 사전 구성된 앱을 백 엔드에 연결하기 위한 전체 Xcode 프로젝트가 다운로드됩니다. Xcode를 사용하여 프로젝트를 엽니다.
2. **실행** 단추를 눌러 프로젝트를 빌드하고 iOS 시뮬레이터에서 앱을 시작합니다.
3. 앱에서 *Complete the tutorial* 등의 의미 있는 텍스트를 입력한 후 더하기(**+**) 아이콘을 클릭합니다. 그러면 이전에 배포한 Azure 백 엔드에 POST 요청을 보냅니다. 요청의 백 엔드 데이터가 TodoItem SQL 테이블로 삽입되고 새로 저장된 항목에 대한 정보를 모바일 앱으로 다시 반환합니다. 모바일 앱이 이 데이터를 목록에 표시합니다. 

   ![iOS에서 실행 중인 빠른 시작 앱](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure 포털]: https://portal.azure.com/
