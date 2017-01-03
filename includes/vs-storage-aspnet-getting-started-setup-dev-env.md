## <a name="set-up-the-development-environment"></a>개발 환경 설정

이 섹션에서는 ASP.NET MVC 앱 만들기, 연결된 서비스 연결 추가, 컨트롤러 추가 및 필수 네임스페이스 지시문 지정을 포함한 개발 환경 설정에 대해 설명합니다.

### <a name="create-an-aspnet-mvc-app-project"></a>ASP.NET MVC 앱 프로젝트 만들기

1. Visual Studio를 엽니다.

1. 기본 메뉴에서 **파일->새로 만들기->프로젝트**를 차례로 선택합니다.

1. **새 프로젝트** 대화 상자에서 다음 그림에서 강조 표시한 대로 옵션을 지정합니다.

    ![ASP.NET 프로젝트 만들기](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. **확인**을 선택합니다.

1. **새 ASP.NET 프로젝트** 대화 상자에서 다음 그림에서 강조 표시한 대로 옵션을 지정합니다.

    ![MVC 지정](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

1. **확인**을 선택합니다.

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>연결된 서비스를 사용하여 Azure 저장소 계정 연결

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가->연결된 서비스**를 차례로 선택합니다.

1. **연결된 서비스 추가** 대화 상자에서 **Azure Storage**를 선택한 다음 **구성**을 선택합니다.

    ![연결된 서비스 대화 상자](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. **Azure Storage** 대화 상자에서 작업하려는 Azure 저장소 계정을 선택하고 **추가**를 선택합니다.


<!--HONumber=Dec16_HO3-->


