
1. 다시 모바일 앱 백 엔드 설정에서 **모바일**까지 아래로 스크롤하고 **시작** > 클라이언트 플랫폼을 클릭합니다. 

2. **테이블 API 만들**에서 **백 엔드 언어**를 **C#** 또는 **Node.js** 중 하나로 선택합니다.

	+ **Node.js 백 엔드**(포털을 통해): 승인을 허용하고 **TodoItem 테이블 만들기**를 클릭합니다. 데이터베이스에 새 *TodoItem* 테이블이 만들어집니다.
	 
		>[AZURE.IMPORTANT]기존 앱 백 엔드를 Node.js에 전환하면 모든 사이트 콘텐츠에 덮어쓰게 됩니다.

	+ **.NET 백 엔드(C#)**: **다운로드**를 클릭하고 로컬 컴퓨터에 압축된 프로젝트 파일을 추출하며 Visual Studio에서 솔루션을 열고 NuGet 패키지를 복원하도록 프로젝트를 빌드한 다음 Azure에 프로젝트를 배포합니다. .NET 백 엔드 서버 프로젝트를 Azure에 배포하는 방법을 알아보려면 [Azure 앱 서비스에서 ASP.NET 웹앱 만들기](../articles/app-service-web/web-sites-dotnet-get-started.md#deploy-the-project-to-the-web-app)의 *웹앱 프로젝트 배포* 섹션을 참조하세요. 앱 서비스에서 모바일 앱 백 엔드는 웹앱과 동등합니다.
	 
모바일 앱 백 엔드는 클라이언트 앱과 함께 사용할 준비가 되었습니다.

<!---HONumber=AcomDC_1203_2015-->