
## <a name="test-your-code"></a>코드 테스트

Visual Studio를 사용하는 경우 `F5` 키를 눌러 프로젝트를 실행합니다. 브라우저가 열리고 *http://localhost:{port}*로 이동합니다. 이 페이지에는 *Call Microsoft Graph API*(Microsoft Graph API 호출) 단추가 표시됩니다.

Visual Studio를 사용하지 않는 경우에는 웹 서버가 시작되었고 JavaScript 웹 응용 프로그램이 포함된 폴더가 웹 서버에 구성되어 있는지 확인합니다. 브라우저를 열고 *http://localhost:{port}/path*를 입력합니다. 여기서 *port*는 웹 서버가 수신 대기 중인 포트이고 *path*는 index.html의 경로입니다.

*Call Microsoft Graph API*(Microsoft Graph API 호출) 단추를 클릭합니다. 이 단추를 처음 클릭하는 경우 사용자에게 로그인하라는 팝업 창이 표시됩니다.
 
![샘플 스크린샷](media/active-directory-singlepageapp-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="consent"></a>동의
응용 프로그램에 처음으로 로그인하면 아래와 유사한 동의 화면이 표시됩니다. 여기서 명시적으로 동의해야 합니다.

 ![동의 화면](media/active-directory-singlepageapp-javascriptspa-test/javascriptspaconsent.png)

Microsoft Graph API를 쿼리하므로 다른 동의 페이지가 표시될 수 있습니다. 이는 응용 프로그램에서 요청한 범위마다 동의가 필요한 *동적 동의* 때문입니다. 이 가이드에서 생성하는 응용 프로그램 예제의 경우 *user.read* 범위가 제공되므로 동의해야만 이 응용 프로그램이 사용자 프로필을 읽을 수 있습니다.

> [!IMPORTANT]
> 현재 *msal* javascript의 알려진 문제로 인해 Chrome 및 Firefox와 같은 브라우저에서 팝업 차단을 사용하지 않도록 설정해야만 *동적 동의* 화면이 제대로 작동할 수 있습니다. 사용자가 처음으로 Microsoft Graph API를 호출하는 경우 `acquireTokenPopup`를 사용하여 팝업 차단을 사용하지 않도록 설정해야 합니다.

### <a name="expected-results"></a>예상 결과
Microsoft Graph API 호출에서 반환된 사용자 프로필 정보가 표시되어야 합니다.
 
 ![결과](media/active-directory-singlepageapp-javascriptspa-test/javascriptsparesults.png)

*액세스 토큰* 및 *ID Token Claims*(ID 토큰 클레임) 상자에서 가져온 토큰에 대한 기본 정보도 볼 수 있습니다.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>범위 및 위임된 권한에 대한 자세한 내용

Microsoft Graph API는 `user.read` 범위가 있어야만 사용자 프로필을 읽을 수 있습니다. 이 범위는 Microsoft 등록 포털에서 등록된 모든 응용 프로그램에서 기본적으로 자동 추가됩니다. 일부 다른 Microsoft Graph용 API와 백 엔드 서버용 사용자 지정 API에는 추가 범위가 필요할 수 있습니다. 예를 들어 Microsoft Graph의 경우 사용자 일정을 나열하려면 `Calendars.Read` 범위가 필요합니다. 응용 프로그램의 컨텍스트에서 사용자 일정에 액세스하려면 응용 프로그램 등록 정보에 `Calendars.Read` 위임 권한을 추가한 다음 `acquireTokenSilent` 호출에 `Calendars.Read` 범위를 추가해야 합니다. 범위 수를 늘리면 사용자에게 추가 동의를 요청하는 메시지가 표시될 수 있습니다.

백 엔드 API에 범위가 필요 없는 경우(권장되지 않음) `acquireTokenSilent` 및/또는 `acquireTokenPopup` 호출에서 `clientId`를 범위로 사용할 수 있습니다.

<!--end-collapse-->
