
1. [Azure Portal]을 방문합니다. **모두 찾아보기** > **Mobile Apps** > 방금 만든 백 엔드를 클릭합니다. 모바일 앱 설정에서 **빠른 시작** > **Android**를 클릭합니다. **클라이언트 응용 프로그램 구성** 아래에서 **다운로드**를 클릭합니다. 그러면 사전 구성된 앱을 백 엔드에 연결하기 위한 전체 Android 프로젝트가 다운로드됩니다. 
2. **Android Studio**에서 **프로젝트(Eclipse ADT, Gradle 등) 가져오기**를 사용하여 프로젝트를 엽니다. 모든 JDK 오류를 방지하려면 이 가져오기를 선택해야 합니다.
3. **'앱' 실행** 단추를 눌러 프로젝트를 빌드하고 Android 시뮬레이터에서 앱을 시작합니다.
4. 앱에서 *자습서 완료* 등의 의미 있는 텍스트를 입력한 후 '추가' 단추를 클릭합니다. 그러면 이전에 배포한 Azure 백 엔드에 POST 요청을 보냅니다. 요청의 백 엔드 데이터가 TodoItem SQL 테이블로 삽입되고 새로 저장된 항목에 대한 정보를 모바일 앱으로 다시 반환합니다. 모바일 앱이 이 데이터를 목록에 표시합니다. 
   
    ![](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)

[Azure Portal]: https://portal.azure.com/
