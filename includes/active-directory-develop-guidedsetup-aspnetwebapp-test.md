## <a name="test-your-code"></a>코드 테스트

Visual Studio에서 응용프로그램을 테스트하려면 **F5** 키를 눌러 프로젝트를 실행합니다. 브라우저가 열리고 http://<span></span>localhost:{port}로 이동합니다. 이 페이지에는 **Microsoft에 로그인** 단추가 표시됩니다. 단추를 선택하여 로그인 프로세스를 시작합니다.

테스트를 실행할 준비가 되면, Microsoft Azure Active Directory(Azure AD) 계정(회사 또는 학교 계정) 또는 개인 Microsoft 계정(<span>live.</span>com 또는 <span>outlook.</span>com)을 사용해 로그인합니다.

![Microsoft로 로그인](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Microsoft 계정으로 로그인](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>응용 프로그램 결과 보기
로그인한 후 사용자는 웹 사이트의 홈 페이지로 리디렉션됩니다. 홈페이지란 Microsoft 응용 프로그램 등록 포털의 응용 프로그램 등록 정보에 지정된 HTTPS URL입니다. 홈 페이지는 로그아웃 링크이며 사용자의 클레임을 보기 위한 링크인 환영 메시지 "Hello \<User>"를 포함합니다. 사용자 클레임에 대한 링크는 앞에서 만든 **Authorize** 컨트롤러로 찾아보기 합니다.

### <a name="browse-to-see-the-users-claims"></a>사용자 클레임 찾아보기
사용자의 클레임을 보려면 인증된 사용자만 사용할 수 있는 컨트롤러 뷰를 찾아보기할 링크를 선택합니다.

#### <a name="view-the-claims-results"></a>클레임 결과 보기
컨트롤러 뷰로 이동 후 사용자를 위한 기본 속성이 담긴 테이블을 확인해야 합니다.

|자산 |값 |설명 |
|---|---|---|
|**name** |사용자의 전체 이름 | 사용자의 이름과 성입니다.
|**사용자 이름** |user<span>@domain.com</span> | 사용자를 식별하는 데 사용하는 사용자 이름입니다.
|**제목** |제목 |웹에서 사용자를 고유하게 식별하는 문자열입니다.|
|**테넌트 ID** |Guid | 사용자의 Azure AD 조직을 고유하게 나타내는 **guid**입니다.|

또한 인증 요청에 있는 모든 클레임 테이블을 확인해야 합니다. 자세한 내용은 [Azure AD ID 토큰에 있는 클레임 목록](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)을 참조하세요.


### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Authorize 특성이 있는 메서드 액세스 테스트(선택 사항)
사용자의 익명 사용자 클레임에 대해 **Authorize** 컨트롤러 액세스를 테스트하려면 다음 단계를 따르세요.
1. 사용자 로그아웃 링크를 선택하고 로그아웃 프로세스를 완료합니다.
2. 브라우저에서 http://<span></span>localhost:{port}/authenticated를 입력하여 **Authorize** 특성으로 보호되는 컨트롤러에 액세스합니다.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>보호된 컨트롤러에 액세스한 후 예상된 결과
보호된 컨트롤러 뷰를 사용하기 위해 인증을 묻는 메시지가 나타납니다.

## <a name="additional-information"></a>추가 정보

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>전체 웹 사이트 보호
**Global.asax** 파일에서 전체 웹 사이트를 보호하려면 **Application_Start** 메서드에서 **AuthorizeAttribute** 특성을 **GlobalFilters** 필터에 추가합니다.

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-sign-in-access-to-your-application"></a>응용 프로그램에 대한 로그인 액세스 제한
기본적으로 outlook.com, live.com 등과 같은 개인 계정에서 응용 프로그램에 로그인할 수 있습니다. 또한 기본적으로 Azure AD와 통합된 조직에서 회사 또는 학교 계정을 사용하여 로그인할 수 있습니다.

응용 프로그램에 대한 사용자 로그인 액세스를 제한하려면 몇 가지 옵션을 사용할 수 있습니다.

#### <a name="restrict-access-to-a-single-organization"></a>단일 조직에 대한 액세스 제한
응용 프로그램에 대한 로그인 액세스를 단일 Azure AD 조직에 있는 사용자 계정으로만 국한할 수 있습니다.
1. **web.config** 파일에서 **테넌트** 매개 변수에 대한 값을 변경합니다. **Common**의 값을 **contoso.onmicrosoft.com**과 같은 조직의 테넌트 이름으로 변경합니다.
2. **OWIN 시작** 클래스에서 **ValidateIssuer** 인수를 **true**로 변경합니다.

#### <a name="restrict-access-to-a-list-of-organizations"></a>조직 목록에 대한 액세스 제한
로그인 액세스를 허용된 조직 목록에 들어 있는 Azure AD 조직에 있는 사용자 계정으로만 국한할 수 있습니다.
1. **web.config** 파일에 있는 **OWIN 시작** 클래스에서 **ValidateIssuer** 인수를 **true**로 설정합니다.
2. **ValidIssuers** 매개 변수의 값을 허용된 조직 목록에 설정합니다.

#### <a name="use-a-custom-method-to-validate-issuers"></a>사용자 지정 메서드를 사용하여 발급자 유효성 검사
**IssuerValidator** 매개 변수를 사용하여 발급자의 유효성을 검사하는 사용자 지정 메서드를 구현할 수 있습니다. 이 매개 변수를 사용하는 방법에 관한 자세한 내용은 MSDN에서 [TokenValidationParameters 클래스](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx)에 관해 알아보세요.

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
