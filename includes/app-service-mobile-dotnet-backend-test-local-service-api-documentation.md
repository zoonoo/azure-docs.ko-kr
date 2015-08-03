
1. Visual Studio의 솔루션 탐색기에서 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **디버그** 상황에 맞는 메뉴에서 **새 인스턴스 시작**을 클릭합니다.

    Visual Studio에서 서비스용 기본 웹 페이지가 열립니다. Visual Studio는 기본적으로 IIS Express에서 로컬로 모바일 앱 백 엔드를 호스트합니다.

2. Windows 작업 표시줄에서 IIS Express의 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 모바일 앱 백 엔드가 시작되었는지 확인합니다.

	 ![작업 표시줄에서 모바일 서비스 확인](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/iis-express-tray.png)

3. .NET 백 엔드 시작 페이지에서 **체험**을 클릭합니다.

    그러면 표시되는 API 설명서 페이지에서 모바일 앱을 테스트할 수 있습니다.

	>[AZURE.NOTE]프로젝트를 로컬로 실행할 때는 인증하지 않아도 이 페이지에 액세스할 수 있습니다. 그러나 Azure에서 실행할 때는 이 페이지에 액세스하려면 응용 프로그램 키를 암호로 입력해야 합니다(사용자 이름은 없음).

4. **GET 테이블/TodoItem** 링크를 클릭합니다.

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-api-documentation-page.png)
   	
	그러면 API의 GET 응답 페이지가 표시됩니다.

5. **체험**을 클릭한 후 **보내기**를 클릭합니다.
 
	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-todoitems.png)

	그러면 TodoItem 테이블의 모든 행을 반환하는 GET 요청을 로컬 모바일 앱 백 엔드로 보냅니다. 테이블은 이니셜라이저가 시드하므로 응답 메시지 본문에서는 TodoItem 개체 두 개가 반환됩니다. 이니셜라이저에 대한 자세한 내용은 [.NET 백 엔드 모바일 서비스에 대한 데이터 모델 변경 방법](../articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)을 참조하세요.

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-response.png)

<!---HONumber=July15_HO4-->