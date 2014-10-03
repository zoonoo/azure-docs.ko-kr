기본적으로 모바일 서비스 리소스에 대한 모든 요청은 리소스에 대한 액세스가 엄격하게 보안되지 않는 응용 프로그램 키를 제공하는 클라이언트에게로 제한되어 있습니다. 리소스를 보안하려면 인증된 클라이언트만 액세스를 허용해야 합니다.

1.  Visual Studio에서 해당 모바일 서비스가 포함된 프로젝트를 엽니다.

2.  솔루션 탐색기에서 컨트롤러 폴더를 확장하고 TodoItemController.cs 프로젝트 파일을 엽니다.

    **TodoItemController** 클래스는 TodoItem 테이블에 대한 데이터 액세스를 구현합니다.

3.  코드 페이지의 맨 위에 다음 `using` 문을 추가합니다.

        using Microsoft.WindowsAzure.Mobile.Service.Security;

4.  **TodoItemController** 클래스에 다음 AuthorizeLevel 특성을 적용합니다.

        [AuthorizeLevel(AuthorizationLevel.User)] 

    그러면 **TodoItem** 테이블에 대한 모든 작업에 인증된 사용자가 필요합니다.

    > [WACOM.NOTE]개별 메서드에 AuthorizeLevel 특성을 적용하여 컨트롤러에 표시된 메서드에서 특정 권한 수준을 설정합니다.

5.  로컬로 인증을 디버그하려는 경우 App\_Start 폴더를 확장하고 WebApiConfig.cs 프로젝트 파일을 연 다음 **Register** 메서드에 다음 코드를 추가합니다.

        config.SetIsHosted(true);

    이렇게 하면 로컬 모바일 서비스 프로젝트가 AuthorizeLevel 설정을 유지하며 Azure에서 호스트 중인 것처럼 실행됩니다. 이 설정이 없는 경우 *localhost*에 대한 모든 HTTP 요청은 AuthorizeLevel 설정에도 불구하고 인증 없이 허용됩니다.

6.  서비스 프로젝트를 다시 게시합니다.



