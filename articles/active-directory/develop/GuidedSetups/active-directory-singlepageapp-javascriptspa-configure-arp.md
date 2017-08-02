
## <a name="add-the-applications-registration-information-to-your-app"></a>앱에 응용 프로그램의 등록 정보 추가

이 단계에서는 응용 프로그램 등록 정보의 리디렉션 URL을 구성하고 JavaScript SPA 응용 프로그램에 응용 프로그램 ID를 추가해야 합니다.

### <a name="configure-redirect-url"></a>리디렉션 URL 구성

웹 서버에 따라 index.html 페이지에 대한 URL로 위의 `Redirect URL` 필드를 구성하고 *업데이트*를 클릭합니다.

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url-using-ssl"></a>SSL을 사용하여 리디렉션 URL을 얻기 위한 Visual Studio 지침
> Visual Studio를 사용하는 경우 SSL을 사용하도록 프로젝트를 구성한 후, 아래 지침에 따라 SSL URL을 사용하여 응용 프로그램 등록 정보를 구성합니다.
> 1.    솔루션 탐색기에서 프로젝트를 선택하고 `Properties` 창을 확인합니다([속성] 창이 보이지 않으면 F4 누르기).
> 2.    `SSL Enabled`를 `True`로 변경
> 3.    `SSL URL`의 값을 클립보드로 복사합니다.<br/> ![](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 4.    `Project` 메뉴를 선택하고 `{Project} Properties...` 를 선택합니다({Project}는 프로젝트의 이름임).
> 5.    `Web` 탭 열기
> 6.    `SSL URL`의 값을 `Project Url` 필드에 붙여 넣습니다.
> 7.    또한 `Redirect URL` 필드의 값을 이 페이지 맨 위에 붙여 넣고 *업데이트*를 클릭합니다.


### <a name="configure-your-javascript-spa-application"></a>JavaScript SPA 응용 프로그램 구성

1.  응용 프로그램 등록 정보를 포함하는 `msalconfig.js`라는 파일을 만듭니다. Visual Studio를 사용하는 경우 프로젝트(프로젝트 루트 폴더)를 마우스 오른쪽 단추로 클릭하고 `Add` > `New Item` > `JavaScript File`을 선택합니다. 이름을 `msalconfig.js`로 지정합니다.
2.  `msalconfig.js` 파일에 다음 코드를 추가합니다.

```javascript
var msalconfig = {
    clientID: "[Enter the application Id here]",
    redirectUri: location.origin
};
``` 
