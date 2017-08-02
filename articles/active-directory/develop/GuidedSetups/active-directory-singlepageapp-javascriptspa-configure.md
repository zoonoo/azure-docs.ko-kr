
### <a name="create-an-application-express"></a>응용 프로그램(Express) 만들기
이제 Microsoft 응용 프로그램 등록 포털에서 응용 프로그램을 등록해야 합니다.

1.  [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure)을 통해 응용 프로그램을 등록합니다.
2.  응용 프로그램 이름과 메일을 입력합니다.
3.  *안내식 설정* 옵션이 선택되어 있는지 확인합니다.
4.  지침에 따라 응용 프로그램 ID를 가져와 코드에 붙여넣습니다.

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>솔루션에 응용 프로그램 등록 정보 추가(고급)

1. [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app)로 이동해 응용 프로그램을 등록합니다.
2. 응용 프로그램 이름과 메일을 입력합니다. 
3. *안내식 설정* 옵션이 선택 취소되어 있는지 확인합니다.
4.  `Add Platform`를 클릭한 다음 `Web`을 선택합니다.
5. 리디렉션 URL을 응용 프로그램에 추가합니다. 리디렉션 URL은 웹 서버를 기준으로 하는 `index.html` 페이지의 URL입니다.
6. 페이지 맨 아래에 있는 *저장*

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url-using-ssl"></a>SSL을 사용하여 리디렉션 URL을 얻기 위한 Visual Studio 지침
> Visual Studio를 사용하는 경우 SSL을 사용하도록 프로젝트를 구성한 후, 아래 지침에 따라 SSL URL을 사용하여 응용 프로그램 등록 정보를 구성합니다.
> 1.    솔루션 탐색기에서 프로젝트를 선택하고 `Properties` 창을 확인합니다([속성] 창이 보이지 않으면 F4 누르기).
> 2.    `SSL Enabled`를 `True`로 변경
> 3.    `SSL URL`의 값을 클립보드로 복사합니다.<br/> ![](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 4.    `Project` 메뉴를 선택하고 `{Project} Properties...` 를 선택합니다({Project}는 프로젝트의 이름임).
> 5.    `Web` 탭 열기
> 6.    `SSL URL`의 값을 `Project Url` 필드에 붙여 넣습니다.
> 7.    응용 프로그램 등록 포털로 다시 돌아간 후 `Redirect URL`의 값을 리디렉션 URL로 붙여 넣고 *저장*을 클릭합니다.


#### <a name="configure-your-javascript-spa-application"></a>JavaScript SPA 응용 프로그램 구성

1.  응용 프로그램 등록 정보를 포함하는 `msalconfig.js`라는 파일을 만듭니다. Visual Studio를 사용하는 경우 프로젝트(프로젝트 루트 폴더)를 마우스 오른쪽 단추로 클릭하고 `Add` > `New Item` > `JavaScript File`을 선택합니다. 이름을 `msalconfig.js`로 지정합니다.
2.  `msalconfig.js` 파일에 다음 코드를 추가합니다.

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
<ol start="3">
<li>
<code>Enter_the_Application_Id_here</code>를 방금 등록한 응용 프로그램 ID로 바꿉니다.
</li>
</ol>