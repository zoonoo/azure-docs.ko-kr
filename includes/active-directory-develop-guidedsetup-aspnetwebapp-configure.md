
## <a name="create-an-application-express"></a>응용 프로그램(Express) 만들기
이제 *Microsoft 응용 프로그램 등록 포털*에서 등용 프로그램을 등록해야 합니다.
1. [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure)을 통해 응용 프로그램을 등록합니다.
2.  응용 프로그램 이름과 메일을 입력합니다.
3.  안내식 설정 옵션이 선택되어 있는지 확인합니다.
4.  지침에 따라 응용 프로그램에 리디렉션 URL 추가

## <a name="add-your-application-registration-information-to-your-solution-advanced"></a>솔루션에 응용 프로그램 등록 정보 추가(고급)
이제 *Microsoft 응용 프로그램 등록 포털*에서 등용 프로그램을 등록해야 합니다.
1. [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app)로 이동해 응용 프로그램을 등록합니다.
2. 응용 프로그램 이름과 메일을 입력합니다. 
3.  안내식 설정 옵션이 선택 취소되어 있는지 확인합니다.
4.  `Add Platform`를 클릭한 다음 `Web`을 선택합니다.
5.  Visual Studio로 돌아가 솔루션 탐색기에서 프로젝트를 선택하고 [속성] 창을 확인합니다([속성] 창이 보이지 않으면 F4 누르기).
6.  SSL 사용을 `True`로 변경
7.  SSL URL을 복사하고 등록 포털의 리디렉션 URL 목록에서 리디렉션 URL 목록에 이 URL을 추가합니다.<br/><br/>![프로젝트 속성](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
8.  다음 내용을 루트 폴더에 있는 `web.config`의 `configuration\appSettings` 섹션 아래에 추가합니다.

```xml
<add key="ClientId" value="Enter_the_Application_Id_here" />
<add key="redirectUri" value="Enter_the_Redirect_URL_here" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
<!-- Workaround for Docs conversion bug -->
<ol start="9">
<li>
`ClientId`를 방금 등록한 응용 프로그램 ID로 바꿉니다.
</li>
<li>
`redirectUri`를 프로젝트의 SSL URL로 바꿉니다.
</li>
</ol>
<!-- End Docs -->
