이 섹션에서는 Visual Studio를 사용하여 IIS Express의 개발용 워크스테이션에서 로컬로 모바일 서비스를 호스트합니다. 그런 다음 앱과 백 엔드 서비스를 테스트합니다.

1.  Visual Studio에서 F7 키를 누르거나 **빌드** 메뉴에서 **솔루션 빌드**를 클릭하여 Windows 스토어 앱과 모바일 서비스를 모두 빌드합니다. Visual Studio의 출력 창에서 두 프로젝트가 오류 없이 빌드되는지 확인합니다.

2.  Visual Studio에서 F5 키를 누르거나 **디버그** 메뉴에서 **디버깅 시작**을 클릭하여 IIS Express에서 로컬로 앱을 실행하고 모바일 서비스를 호스트합니다.

3.  새 todoitem의 텍스트를 입력합니다. 그런 다음 **Save**를 클릭합니다. IIS Express에서 로컬로 호스트한 모바일 서비스에서 만든 데이터베이스에 새 todoItem이 삽입됩니다.

    ![][0]

4.  항목 중 하나의 확인란을 클릭하여 완료로 표시합니다.

    ![][1]

5.  Visual Studio에서 서버 탐색기를 열고 데이터 연결을 확장하여 백 엔드 서비스에 대해 만든 데이터베이스에서 변경 사항을 확인합니다. **MS\_TableConnectionString** 아래에서 TodoItems 테이블을 마우스 오른쪽 단추로 클릭하고 **테이블 데이터 표시**를 클릭합니다.

    ![][2]

  [0]: ./media/mobile-services-dotnet-backend-test-local-service-data/new-local-todoitem.png
  [1]: ./media/mobile-services-dotnet-backend-test-local-service-data/local-item-checked.png
  [2]: ./media/mobile-services-dotnet-backend-test-local-service-data/vs-show-local-table-data.png
