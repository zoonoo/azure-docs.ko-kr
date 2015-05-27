

로컬 모바일 서비스에 대해 클라이언트 앱을 테스트한 후 이 자습서의 마지막 단계는 모바일 서비스를 Azure에 게시하고 Live 서비스에 대해 앱을 실행하는 것입니다.

>[AZURE.NOTE]이 절차는 Visual Studio 도구를 사용하여 모바일 서비스를 게시하는 방법을 보여 줍니다. 원본 제어를 사용하여 .NET 백 엔드 모바일 서비스를 게시할 수도 있습니다. 자세한 내용은 [소스 제어에 프로젝트 코드 저장](../articles/mobile-services-dotnet-backend-store-code-source-control.md)을 참조하세요.

1. 솔루션 탐색기에서 모바일 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭한 다음, **웹 게시** 대화 상자에서 **Azure 모바일 서비스**를 클릭합니다.

2. Azure 계정 자격 증명으로 로그인하고, **기존 모바일 서비스**에서 서비스를 선택하고 **확인**을 클릭합니다. Visual Studio는 Azure에서 직접 게시 설정을 다운로드합니다.

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-select-service.png)

3. **연결 유효성 검사**를 클릭하여 게시가 올바르게 구성되었는지 확인한 다음 **게시**를 클릭합니다.

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-2.png)

	게시가 성공하면 이때 Azure에서 모바일 서비스가 실행 중이라는 확인 페이지가 다시 표시됩니다.

<!--HONumber=54-->