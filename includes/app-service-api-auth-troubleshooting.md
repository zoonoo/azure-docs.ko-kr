대부분의 시간 인증 오류는 잘못되었거나 일관되지 않은 구성 설정으로 인해 발생합니다. 확인할 사항에 대한 몇 가지 특정 제안 사항은 다음과 같습니다.

* **저장** 단추를 누르지 않은 곳은 없는지 확인합니다. 자주 있는 일이며 이로 인해 포털 페이지에서 올바른 값을 기대하지만 Azure 환경 또는 Azure AD 응용 프로그램에서 실제로 저장되지 않은 결과가 발생합니다.
* Azure 포털의 **응용 프로그램 설정** 블레이드에서 구성된 설정의 경우 설정을 입력할 때 올바른 API 앱 또는 웹앱을 선택했는지 확인합니다. 또한 두 섹션의 형식이 비슷해서 **연결 문자열**이 아니라 **앱 설정**으로 설정을 입력하였는지 확인합니다.
* JavaScript 프런트 엔드에 대한 인증을 위해 다시 매니페스트를 다운로드하여 `oauth2AllowImplicitFlow`가 성공적으로 `true`로 변경되었는지 확인합니다.
* 다음에서 URL을 구성하는 데 HTTPS를 사용했는지 확인합니다.
  
  * 프로젝트 코드
  * CORS
  * 각 API 앱 및 웹앱에 대한 Azure 환경 앱 설정
  * Azure AD 응용 프로그램 설정
    
    포털에서 API 앱의 URL을 복사하는 경우 `http://`가 흔하며 `https://`는 수동으로 변경해야 합니다.
* 모든 코드 변경이 성공적으로 배포되었는지 확인합니다. 예를 들어 다중 프로젝트 솔루션에서 프로젝트의 코드를 변경하고 변경 내용을 배포하려고 할 때 실수로 다른 코드 중 하나를 선택할 수 있습니다.
* 브라우저에서 HTTP URL이 아닌 HTTPS URL을 사용하는지 확인합니다. 기본적으로 Visual Studio는 HTTP URL이 포함된 게시 프로필을 생성하며 프로젝트를 배포한 후 브라우저에서 열립니다.
* JavaScript 프런트 엔드에 대한 인증을 위해 JavaScript 코드가 호출하는 API 앱에 CORS가 올바르게 구성되어 있는지 확인합니다. 문제가 CORS와 관련이 있는지 잘 모르겠으면 "*"를 다음 원래의 URL로 사용해 봅니다.
* JavaScript 프런트 엔드의 경우 브라우저의 개발자 도구 콘솔 탭을 열어 자세한 오류 정보를 얻고 네트워크에서 HTTP 요청을 검사합니다. 그러나 콘솔 오류 메시지가 잘못될 수 있습니다. CORS 오류를 나타내는 메시지를 받는 경우 실제 문제는 인증일 수 있습니다. 이 경우에 일시적으로 사용할 수 없는 인증으로 앱을 실행했는지 확인할 수 있습니다.
* .NET API 앱의 경우 [customErrors 모드를 Off](../articles/app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview)로 설정하여 오류 메시지에서 가능한 많은 정보를 확인할 수 있도록 합니다.
* .NET API 앱에서 [원격 디버깅 세션](../articles/app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)을 시작하고 ADAL을 사용하여 전달자 토큰을 획득하는 코드 또는 예상되는 서비스 주체 ID에 대한 클레임을 검사하는 코드에 전달되는 변수 값을 검사합니다. 코드가 많은 다양한 원본에서 구성 값을 선택할 수 있으므로 이러한 방식으로 재미를 찾을 수 있습니다. 예를 들어 `ida:ClientId`를 `ida:ClientID`로 잘못 입력한 경우 Azure 앱 서비스 환경 설정을 구성할 때 코드가 Azure 앱 서비스 환경 설정을 무시하고 Web.config 파일에서 찾는 `ida:ClientId` 값을 가져올 수 있습니다. 
* 일반 Internet Explorer 창에서 작동하지 않으면 기존 로그인이 방해가 될 수 있으니 InPrivate을 시도하고 Chrome 또는 Firefox를 시도해 보세요.

<!---HONumber=AcomDC_0309_2016-->