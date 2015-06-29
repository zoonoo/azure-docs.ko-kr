

기본적으로 모바일 서비스 리소스에 대한 모든 요청은 리소스에 대한 액세스가 엄격하게 보안되지 않는 응용 프로그램 키를 제공하는 클라이언트에게로 제한되어 있습니다. 리소스를 보안하려면 인증된 클라이언트만 액세스를 허용해야 합니다.

1. Visual Studio에서 모바일 서비스 프로젝트를 열고 Controllers 폴더를 확장한 다음 **TodoItemController.cs**를 엽니다. **TodoItemController** 클래스는 TodoItem 테이블에 대한 데이터 액세스를 구현합니다. 다음 `using` 문을 추가합니다.

		using Microsoft.WindowsAzure.Mobile.Service.Security;

2. **TodoItemController** 클래스에 다음 _AuthorizeLevel_ 특성을 적용합니다. 그러면 _TodoItem_ 테이블에 대한 모든 작업에 인증된 사용자가 필요합니다.

		[AuthorizeLevel(AuthorizationLevel.User)]

	>[AZURE.NOTE]개별 메서드에 AuthorizeLevel 특성을 적용하여 컨트롤러에 표시된 메서드에서 특정 권한 수준을 설정합니다.

3. (선택 사항) 로컬로 인증을 디버그하려는 경우 `App_Start` 폴더를 확장하고 **WebApiConfig.cs** 프로젝트 파일을 연 다음 **Register** 메서드에 다음 코드를 추가합니다.

		config.SetIsHosted(true);

	이렇게 하면 로컬 모바일 서비스 프로젝트가 *AuthorizeLevel* 설정을 유지하며 Azure에서 호스트 중인 것처럼 실행됩니다. 이 설정이 없는 경우 localhost에 대한 모든 HTTP 요청은 *AuthorizeLevel* 설정에도 불구하고 인증 없이 허용됩니다. 자체 호스팅된 모드를 사용하도록 설정하면 로컬 응용 프로그램 키에 대한 값을 설정해야 할 수도 있습니다.

4. (선택 사항) Web.config 프로젝트 파일에서 `MS_ApplicationKey` 앱 설정에 대한 문자열 값을 설정합니다. 이는 서비스를 로컬로 실행할 대 API 도움말 페이지를 테스트하는 데 사용하는 암호(사용자 이름 없음)입니다.

	>[AZURE.NOTE]이 문자열 값은 Azure의 라이브 사이트에 의해 사용되지 않으며, 실제 응용 프로그램을 사용할 필요가 없습니다. 유효한 문자열 값이 동작합니다.
 
4. 프로젝트를 다시 게시합니다.

<!---HONumber=58_postMigration-->