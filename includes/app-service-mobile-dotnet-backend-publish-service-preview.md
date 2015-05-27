로컬 모바일 앱에 대해 클라이언트 앱을 테스트한 후 이 자습서의 마지막 단계는 모바일 앱 백엔드를 Azure에 게시하고 Live 서비스에 대해 앱을 실행하는 것입니다.

> [AZURE.NOTE] 이 절차는 Visual Studio 도구를 사용하여 모바일 앱 백엔드를 게시하는 방법을 보여줍니다. 소스 제어를 사용하여.NET 백엔드를 게시할 수도 있습니다.

1. 솔루션 탐색기에서 모바일 앱 코드 프로젝트("서비스"가 추가된 사용자 앱 이름)를 마우스 오른쪽 단추로 누르고 **게시**를 선택합니다. 

	![앱 코드 프로젝트에서 게시 선택](./media/app-service-mobile-dotnet-backend-publish-service-preview/dotnet-project-publish.png)

2. **웹 게시** 대화 상자에서 **Azure 모바일 앱**을 게시 대상으로 선택합니다. 나타나는 대화 상자에서 모바일 앱 이름에 "code"가 추가되는 기존 모바일 앱을 선택합니다.

    ![게시할 기존 웹앱 선택](./media/app-service-mobile-dotnet-backend-publish-service-preview/mobile-quickstart-publish-select-service.png)

3. **연결 유효성 검사**를 클릭하여 게시가 올바르게 구성되었는지 확인한 다음 **게시**를 클릭합니다.

	![게시 설정 마법사 마지막 페이지](./media/app-service-mobile-dotnet-backend-publish-service-preview/dotnet-publish-settings.png)

   게시가 성공된 후 모바일 앱 백엔드가 작동하고 Azure에서 실행되는 확인 페이지가 나타납니다. Visual Studio 출력 창에도 성공을 표시합니다.

<!--HONumber=54-->