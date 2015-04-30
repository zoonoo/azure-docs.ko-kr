<properties 
	pageTitle="AD FS 인증을 통해 Azure 앱 서비스에서 .NET MVC 웹 앱 만들기" 
	description="Azure 앱 서비스에서 온-프레미스 STS로 인증하는 ASP.NET MVC 기간 업무 응용 프로그램을 만드는 방법에 대해 알아봅니다. 이 자습서에서는 AD FS를 온-프레미스 STS로 사용합니다." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="04/09/2015" 
	ms.author="cephalin"/>

# AD FS 인증을 통해 Azure 앱 서비스에서 .NET MVC 웹 앱 만들기

이 문서에서는 [Azure 앱 서비스 웹 앱](http://go.microsoft.com/fwlink/?LinkId=529714)에서 온-프레미스 [Active Directory Federation Services](http://technet.microsoft.com/library/hh831502.aspx)를 ID 공급자로 사용하는 ASP.NET MVC 기간 업무 응용 프로그램을 만드는 방법에 대해 알아봅니다. 이 시나리오는 Azure 앱 서비스 웹 앱에서 기간 업무 응용 프로그램을 만들 때 조직의 규정에 따라 모든 데이터를 온사이트에 저장해야 하는 경우에 적용될 수 있습니다.

>[AZURE.NOTE] Azure 앱 서비스 웹 앱에 대한 다른 엔터프라이즈 인증 및 권한 부여 옵션의 개요는 [Azure 앱 서비스에서 인증을 위해 Active Directory 사용](web-sites-authentication-authorization.md)을 참조하세요.

<a name="bkmk_build"></a>
## 빌드할 내용 ##

Azure 앱 서비스 웹 앱에서 다음 기능이 있는 기본 ASP.NET 응용 프로그램을 빌드합니다.

- AD FS에 대해 사용자 인증
- `[Authorize]`를 사용하여 다른 동작에 대해 사용자에게 권한 부여
- Visual Studio에서의 디버깅 및 앱 서비스 웹 앱에 게시 둘 다에 대한 정적 구성(한 번 구성하여 언제든 디버그 및 게시)  

<a name="bkmk_need"></a>
## 필요한 사항 ##

[AZURE.INCLUDE [free-trial-note](../includes/free-trial-note.md)]

>[AZURE.NOTE] Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 응용 프로그램을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

이 자습서를 완료하려면 다음 항목이 필요합니다.

- 온-프레미스 AD FS 배포(이 문서에 사용된 테스트 랩의 전체 연습은 [테스트 랩: Azure VM에서 AD FS를 사용하는 독립 실행형 STS(테스트 전용)](TODO) 참조)
- AD FS 관리에서 신뢰 당사자 트러스트를 만들 수 있는 권한
- Visual Studio 2013
- [Azure SDK 2.5.1](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) 이상

<a name="bkmk_sample"></a>
## 기간 업무 템플릿에 예제 응용 프로그램 사용 ##

이 자습서의 예제 응용 프로그램[WebApp-WSFederation-DotNet)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet)은 Azure Active Directory 팀이 만들었습니다. AD FS는 WS-Federation을 지원하므로 이를 템플릿으로 사용하여 새 기간 업무 응용 프로그램을 손쉽게 만들 수 있습니다. 제공되는 기능은 다음과 같습니다.

- [WS-Federation](http://msdn.microsoft.com/library/bb498017.aspx)을 사용하여 온-프레미스 AD FS 배포로 인증
- 로그인 및 로그아웃 기능
- [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana)(Windows Identity Foundation, 즉 WIF 대신)을 사용합니다. 이는 ASP.NET의 미래이며 WIF보다 인증 및 권한 부여 설정이 훨씬 간편합니다.

<a name="bkmk_setup"></a>
## 샘플 응용 프로그램 설정 ##

2.	[WebApp-WSFederation-DotNet](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet)에서 로컬 디렉터리에 예제 솔루션을 복제하거나 다운로드합니다.

	> [AZURE.NOTE] [README.md](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet/blob/master/README.md)의 지침에는 Azure Active Directory를 사용하여 응용 프로그램을 설정하는 방법이 나와 있지만, 이 자습서에서는 AD FS를 사용하여 설정하므로 여기의 단계를 대신 따르세요.

3.	솔루션을 열고 **솔루션 탐색기**에서 Controllers\AccountController.cs를 엽니다.

	코드가 단순히 인증 챌린지를 실행하여 WS-Federation을 통해 사용자를 인증하는 것을 볼 수 있습니다. 모든 인증은 App_Start\Startup.Auth.cs에서 구성됩니다.

4.  App_Start\Startup.Auth.cs를 엽니다.  `ConfigureAuth` 메서드에서 다음 줄을 기록해 둡니다.

        app.UseWsFederationAuthentication(
            new WsFederationAuthenticationOptions
            {
                Wtrealm = realm,
                MetadataAddress = metadata                                      
            });

	OWIN 환경에서 이는 실제로 WS-Federation 인증을 구성하는 데 필요한 최소 코드입니다. 이는 Web.config의 여러 곳에 XML이 주입되는 WIF보다 훨씬 단순하고 세련된 코드입니다. 필요한 정보는 RP(신뢰 당사자)의 식별자와 AD FS 서비스의 메타데이터 파일 URL뿐입니다. 예를 들면 다음과 같습니다.

	-	RP 식별자: `https://contoso.com/MyLOBApp`
	-	메타데이터 주소: `http://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

5.	App_Start\Startup.Auth.cs에서 아래에 강조 표시된 대로 정적 문자열 정의를 변경합니다.  
	<pre class="prettyprint">
	private static string realm = ConfigurationManager.AppSettings["ida:<mark>RPIdentifier</mark>"];
    <mark><del>private static string aadInstance = ConfigurationManager.AppSettings["ida:AADInstance"];</del></mark>
    <mark><del>private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];</del></mark>
    <mark><del>private static string metadata = string.Format("{0}/{1}/federationmetadata/2007-06/federationmetadata.xml", aadInstance, tenant);</del></mark>
    <mark>private static string metadata = string.Format("https://{0}/federationmetadata/2007-06/federationmetadata.xml", ConfigurationManager.AppSettings["ida:ADFS"]);</mark>

    <mark><del>string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant);</del></mark>
    </pre>

6.	이제 Web.config를 적절히 변경합니다. Web.config를 열고 아래에 강조 표시된 대로 응용 프로그램 설정을 수정합니다.  
	<pre class="prettyprint">
	&lt;appSettings&gt;
	  &lt;add key="webpages:Version" value="3.0.0.0" /&gt;
	  &lt;add key="webpages:Enabled" value="false" /&gt;
	  &lt;add key="ClientValidationEnabled" value="true" /&gt;
	  &lt;add key="UnobtrusiveJavaScriptEnabled" value="true" /&gt;
	  <mark><del>&lt;add key="ida:Wtrealm" value="[Enter the App ID URI of WebApp-WSFederation-DotNet https://contoso.onmicrosoft.com/WebApp-WSFederation-DotNet]" /&gt;</del></mark>
	  <mark><del>&lt;add key="ida:AADInstance" value="https://login.windows.net" /&gt;</del></mark>
	  <mark><del>&lt;add key="ida:Tenant" value="[Enter tenant name, e.g. contoso.onmicrosoft.com]" /&gt;</del></mark>
	  <mark>&lt;add key="ida:RPIdentifier" value="[Enter the relying party identifier as configured in AD FS, e.g. https://localhost:44320/]" /&gt;</mark>
	  <mark>&lt;add key="ida:ADFS" value="[Enter the FQDN of AD FS service, e.g. adfs.contoso.com]" /&gt;</mark>

	&lt;/appSettings&gt;
	</pre>

	각 환경을 기반으로 키 값을 입력합니다.

7.	응용 프로그램을 빌드하여 오류가 없는지 확인합니다.

이것으로 끝입니다. 이제 예제 응용 프로그램이 AD FS에서 작동할 준비가 완료되었습니다. 나중에 AD FS에서 이 응용 프로그램과의 RP 트러스트를 구성해야 합니다.

<a name="bkmk_deploy"></a>
## Azure 앱 서비스 웹 앱에 샘플 응용 프로그램 배포

여기에서는 디버그 환경을 유지하는 동안 앱 서비스 웹 앱에서 웹 앱에 응용 프로그램을 게시합니다. AD FS와의 RP 트러스트가 설정되기 전에 응용 프로그램을 게시할 것이므로 인증은 아직 작동하지 않습니다. 하지만 이렇게 한 경우 나중에 RP 트러스트를 구성하는 데에도 사용할 수 있는 웹 앱 URL이 생성됩니다.

1. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

	![](./media/web-sites-dotnet-lob-application-adfs/01-publish-website.png)

2. **Microsoft Azure 웹 앱**을 선택합니다.
3. Azure에 로그인하지 않은 경우 **로그인**을 클릭하고 Azure 구독에 대한 Microsoft 계정을 사용하여 로그인합니다.
4. 로그인되면 **새로 만들기**를 클릭하여 새 웹 앱을 만듭니다.
5. 모든 필수 필드를 입력합니다. 온-프레미스 데이터에는 나중에 연결할 것이므로 이 웹 앱에 대한 데이터베이스는 만들지 않습니다.

	![](./media/web-sites-dotnet-lob-application-adfs/02-create-website.png)

6. **만들기**를 클릭합니다. 웹 앱이 만들어지면 웹 게시 대화 상자가 열립니다.
7. **대상 URL**에서 **http**를 **https**로 변경합니다. 전체 URL을 텍스트 편집기에 복사합니다. 이 URL은 나중에 사용합니다. 그런 다음 **게시**를 클릭합니다.

	![](./media/web-sites-dotnet-lob-application-adfs/03-destination-url.png)

11. Visual Studio,에서 프로젝트의 **Web.Release.config**를 엽니다. 다음 XML을 `<configuration>` 태그에 삽입하고 키 값을 게시 웹 앱의 URL로 바꿉니다.  
	<pre class="prettyprint">
&lt;appSettings&gt;
   &lt;add key="ida:RPIdentifier" value="<mark>[e.g. https://mylobapp.azurewebsites.net/]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" /&gt;
&lt;/appSettings&gt;</pre>

작업을 마치면 RP 식별자 두 개가 프로젝트에 구성됩니다. 하나는 Visual Studio의 디버그 환경에 대한 RP 식별자이고, 나머지 하나는 Azure에 게시된 웹 앱에 대한 RP 식별자입니다. AD FS에서 두 환경 각각에 대한 RP 트러스트를 설정합니다. 디버그하는 동안 Web.config의 응용 프로그램 설정을 통해 **디버그** 구성이 AD FS와 작동하도록 설정되며, 게시된 경우(기본적으로 **릴리스** 구성이 게시됨) 변환된 Web.config가 업로드되어 응용 프로그램 설정 변경 사항이 Web.Release.config에 포함됩니다.

Azure에서 게시된 웹 앱을 디버거에 연결하려는 경우(즉, 게시된 웹 앱에서 코드의 디버그 기호를 업로드해야 함) Web.Release.config의 앱 설정을 사용하는 고유한 사용자 지정 Web.config 변환(예: Web.AzureDebug.config)을 통해 Azure 디버깅을 위한 디버그 구성의 복제본을 만들 수 있습니다. 이렇게 하면 여러 환경에서 정적 구성을 유지 관리할 수 있습니다.

<a name="bkmk_rptrusts"></a>
## AD FS 관리에서 신뢰 당사자 트러스트 구성 ##

이제 실제로 AD FS를 사용하여 예제 응용 프로그램을 인증하려면 먼저 AD FS 관리에서 RP 트러스트를 구성해야 합니다. 각각 디버그 환경과 게시된 웹 앱에 대한 별도의 RP 트러스트 두 개를 설정해야 합니다.

> [AZURE.NOTE] 두 환경 모두에 대해 아래의 단계를 반복해야 합니다.

4.	AD FS 서버에서 AD FS 관리 권한이 있는 자격 증명으로 로그인합니다.
5.	AD FS 관리를 엽니다. **AD FS\Trusted Relationships\Relying Party Trusts**를 마우스 오른쪽 단추로 클릭하고 **신뢰 당사자 트러스트 추가**를 선택합니다.

	![](./media/web-sites-dotnet-lob-application-adfs/1-add-rptrust.png)

5.	**데이터 원본 선택 페이지**에서 **신뢰 당사자에 대한 데이터를 수동으로 입력**을 선택합니다. 

	![](./media/web-sites-dotnet-lob-application-adfs/2-enter-rp-manually.png)

6.	**표시 이름 지정** 페이지에서 응용 프로그램의 표시 이름을 입력하고 **다음**을 클릭합니다.
7.	**프로토콜 선택** 페이지에서 **다음**을 클릭합니다.
8.	**인증서 구성** 페이지에서 **다음**을 클릭합니다.

	> [AZURE.NOTE] HTTPS를 이미 사용하고 있으므로 암호화된 토큰은 선택 사항입니다. 이 페이지에서 AD FS의 토큰을 암호화하려면 토큰 암호 해독 논리도 코드에 추가해야 합니다. 자세한 내용은 [OWIN WS-Federation 미들웨어를 수동으로 구성하고 암호화된 토큰 적용](http://chris.59north.com/post/2014/08/21/Manually-configuring-OWIN-WS-Federation-middleware-and-accepting-encrypted-tokens.aspx)을 참조하세요.
  
5.	다음 단계를 진행하기 전에 Visual Studio 프로젝트에서 한 가지 정보를 확인해야 합니다. 프로젝트 속성에서 응용 프로그램의 **SSL URL**을 기록해 둡니다. 

	![](./media/web-sites-dotnet-lob-application-adfs/3-ssl-url.png)

6.	AD FS 관리로 돌아와 **신뢰 당사자 트러스트 추가 마법사**의 **URL 구성** 페이지에서 **WS-Federation Passive 프로토콜 지원**을 선택하고 이전 단계에서 기록해 둔 Visual Studio 프로젝트의 SSL URL을 입력합니다. 그런 후 **다음**을 클릭합니다.

	![](./media/web-sites-dotnet-lob-application-adfs/4-configure-url.png)

	> [AZURE.NOTE] URL은 인증에 성공한 후 클라이언트를 전송할 위치를 지정합니다. 디버그 환경의 경우 <code>https://localhost:&lt;port&gt;/</code>여야 합니다. 게시된 웹 앱의 경우 웹 앱 URL이어야 합니다.

7.	**식별자 구성** 페이지에서 프로젝트 SSL URL이 이미 나열되어 있는지 확인하고 **다음**을 클릭합니다. 기본값을 선택하여 마법사의 끝까지 **다음**을 클릭합니다.

	> [AZURE.NOTE] Visual Studio 프로젝트의 App_Start\Startup.Auth.cs에서 이 식별자는 페더레이션 인증 중에 <code>WsFederationAuthenticationOptions.Wtrealm</code> 값과 일치합니다. 기본적으로 이전 단계의 응용 프로그램 URL이 RP 식별자로 추가됩니다.

8.	이제 AD FS에서 프로젝트에 대한 RP 응용 프로그램 구성을 마쳤습니다. 다음으로, 응용 프로그램에 필요한 클레임을 보내도록 이 응용 프로그램을 구성합니다. 마법사가 종료되면 즉시 시작할 수 있도록 **클레임 규칙 편집** 대화 상자가 기본적으로 열립니다. 최소한 다음 클레임을 구성해 보겠습니다(스키마는 괄호 안에 포함).

	-	이름 (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name) - ASP.NET에서 `User.Identity.Name`을 하이드레이션하는 데 사용됩니다.
	-	사용자 계정 이름(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn) - 조직에서 사용자를 고유하게 식별하는 데 사용됩니다.
	-	역할로서의 그룹 멤버 자격 (http://schemas.microsoft.com/ws/2008/06/identity/claims/role) - `[Authorize(Roles="role1, role2,...")]` 장식과 함께 컨트롤러/작업에 대한 권한을 부여하는 데 사용됩니다. 실제로 이는 역할 권한 부여에 가장 적합한 접근 방식이 아닐 수도 있습니다. 특히 AD 사용자가 SAML 토큰에서 수백 개의 역할 클레임으로 변환되는 수백 개의 보안 그룹에 정기적으로 속하는 경우에는 더욱 그렇습니다. 이 접근 방식의 대안은 특정 그룹에 속한 사용자의 멤버 자격에 따라 단일 역할 클레임을 조건부로 보내는 것입니다. 하지만 이 자습서에서는 이 접근 방식을 단순한 형태로 제공합니다.
	-	이름 ID(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier) - 위조 방지 유효성 검사에 사용될 수 있습니다. 위조 방지 유효성 검사와 함께 사용하는 방법에 대한 정보는 [Azure Active Directory 인증으로 Azure 앱 서비스에서 .NET MVC 웹 앱 만들기](web-sites-dotnet-lob-application-azure-ad.md#bkmk_crud)의 **예제 응용 프로그램에 기간 업무 기능 추가** 섹션을 참조하세요.

	> [AZURE.NOTE] 응용 프로그램에 대해 구성해야 하는 클레임 유형은 응용 프로그램의 요구 사항에 따라 결정됩니다. 예를 들어, Azure Active Directory 응용 프로그램(즉, RP 트러스트)에서 지원되는 클레임 목록은 [지원되는 토큰 및 클레임 유형](http://msdn.microsoft.com/library/azure/dn195587.aspx)을 참조하세요.

8.	클레임 규칙 편집 대화 상자에서 **규칙 추가**를 클릭합니다.
9.	아래에 표시된 대로 이름, UPN 및 역할 클레임을 구성하고 **마침**을 클릭합니다.

	![](./media/web-sites-dotnet-lob-application-adfs/5-ldap-claims.png)

	그런 다음 [SAML 어설션의 이름 식별자](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx)에 설명된 단계를 사용하여 임시 이름 ID 클레임을 만듭니다.

9.	**규칙 추가**를 다시 클릭합니다.
10.	**사용자 지정 규칙을 사용하여 클레임 보내기**를 선택하고 **다음**을 클릭합니다.
11.	다음 규칙 언어를 **사용자 지정 규칙** 상자에 붙여 넣고 규칙 이름을 **세션별 식별자**로 지정한 후 **마침**을 클릭합니다.  
	<pre class="prettyprint">
	c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] &amp;&amp;
	c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant"]
		add(
			store = "_OpaqueIdStore",
			types = ("<mark>http://contoso.com/internal/sessionid</mark>"),
			query = "{0};{1};{2};{3};{4}",
			param = "useEntropy",
			param = c1.Value,
			param = c1.OriginalIssuer,
			param = "",
			param = c2.Value);
	</pre>

	사용자 지정 규칙은 다음과 같습니다.

	![](./media/web-sites-dotnet-lob-application-adfs/6-per-session-identifier.png)

9.	**규칙 추가**를 다시 클릭합니다.
10.	**들어오는 클레임 변환**을 선택하고 **다음**을 클릭합니다.
11.	아래에 표시된 대로 규칙을 구성(사용자 지정 규칙에서 만든 클레임 유형 사용)하고 **마침**을 클릭합니다.

	![](./media/web-sites-dotnet-lob-application-adfs/7-transient-name-id.png)

	위의 임시 이름 ID 클레임 단계에 자세한 내용은 [SAML 어설션의 이름 식별자](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx)를 참조하세요.

12.	**클레임 규칙 편집** 대화 상자에서 **적용**을 클릭합니다. 이제 아래 스크린샷처럼 표시됩니다.

	![](./media/web-sites-dotnet-lob-application-adfs/8-all-claim-rules.png)

	> [AZURE.NOTE] 즉, 디버그 환경과 게시된 웹 앱 둘 다에 대해 이러한 단계를 반복해야 합니다.

<a name="bkmk_test"></a>
## 응용 프로그램에 대한 페더레이션 인증 테스트

AD FS에 대해 응용 프로그램의 인증 논리를 테스트할 준비가 완료되었습니다. 이 AD FS 실습 환경에는 AD(Active Directory)의 테스트 그룹에 속한 테스트 사용자가 있습니다.

![](./media/web-sites-dotnet-lob-application-adfs/10-test-user-and-group.png)

디버거에서 인증을 테스트하려면 `F5` 키를 누르기만 하면 됩니다. 게시된 웹 앱에서 인증을 테스트하려면 해당 URL로 이동합니다.

웹 응용 프로그램이 로드되면 **로그인**을 클릭합니다. AD FS에서 선택한 인증 방법에 따라 로그인 대화 상자 또는 AD FS에서 제공하는 로그인 페이지가 열립니다. 다음은 Internet Explorer 11의 화면 예입니다.

![](./media/web-sites-dotnet-lob-application-adfs/9-test-debugging.png)

AD FS 배포의 AD 도메인에 속한 사용자로 로그인하면 가장자리에 **안녕하세요, <사용자 이름>님!**이 표시된 홈 페이지가 표시됩니다. 예를 들면 다음과 같습니다.

![](./media/web-sites-dotnet-lob-application-adfs/11-test-debugging-success.png)

지금까지 다음 방법에 성공했습니다.

- 응용 프로그램에서 성공적으로 AD FS에 연결하고 AD FS 데이터베이스에서 일치하는 RP 식별자를 찾았습니다.
- AD FS가 AD 사용자를 성공적으로 인증하고 응용 프로그램의 홈 페이지로 돌아갑니다.
- 가장자리에 사용자 이름이 표시되어 있다는 사실로 알 수 있듯이, AD FS에서 이름 클레임(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)을 응용 프로그램으로 성공적으로 전송했습니다. 

이름 클레임이 누락된 경우 **안녕하세요, 님!**이 표시됩니다. Views\Shared\_LoginPartial.cshtml을 보면 `User.Identity.Name`을 사용하여 사용자 이름을 표시한다는 것을 알 수 있습니다. 앞서 설명한 바와 같이, ASP.NET은 인증된 사용자의 이름 클레임(SAML 토큰에서 사용할 수 있는 경우)으로 이 속성을 하이드레이션합니다. AD FS에서 전송된 모든 클레임을 보려면 Controllers\HomeController.cs의 Index 작업 메서드에 중단점을 둡니다. 사용자가 인증되고 나면 `System.Security.Claims.Current.Claims` 컬렉션을 조사합니다.

![](./media/web-sites-dotnet-lob-application-adfs/12-test-debugging-all-claims.png) 

<a name="bkmk_authorize"></a>
## 사용자에게 특정 컨트롤러 또는 작업에 대한 권한 부여

RP 트러스트 구성에서 그룹 멤버 자격을 역할 클레임으로 포함했기 때문에 이제 컨트롤러 및 작업에 대한 `[Authorize(Roles="...")]` 장식에서 직접 이를 사용할 수 있습니다. CRUD(만들기-읽기-업데이트-삭제) 패턴이 있는 LOB(기간 업무) 응용 프로그램에서는 특정 역할에 각 작업에 대한 액세스 권한을 부여할 수 있습니다. 지금은 기존 Home 컨트롤러에서 이 기능을 사용해 보기만 합니다.

1. Controllers\HomeController.cs를 엽니다.
2. 인증된 사용자의 보안 그룹 멤버 자격을 사용하여 아래와 유사하게 `About` 및 `Contact` 작업 메서드를 장식합니다.  
	<pre class="prettyprint">
    <mark>[Authorize(Roles="Test Group")]</mark>
    public ActionResult About()
    {
        ViewBag.Message = "Your application description page.";

        return View();
    }

    <mark>[Authorize(Roles="Domain Admins")]</mark>
    public ActionResult Contact()
    {
        ViewBag.Message = "Your contact page.";

        return View();
    }
	</pre>

	이 자습서의 AD FS 실습 환경에서는 **테스트 사용자**를 **테스트 그룹**에 추가했기 때문에 테스트 그룹을 사용하여 `About`에 대한 권한 부여를 테스트합니다.  `Contact`의경우 **테스트 사용자**가 속해 있지 않은 **Domain Admins**의 부정적인 사례를 테스트합니다.

3. `F5` 키를 눌러 디버거를 시작하고 로그인한 후 **About**을 클릭합니다. 이제 인증된 사용자에게 해당 작업에 대한 권한이 부여된 경우 `~/About/Index` 페이지가 성공적으로 표시되어야 합니다.
4. **Contact**를 클릭합니다(이 자습서에서는 **테스트 사용자**에게 작업에 대한 권한이 부여되지 않음). 그러나 브라우저가 AD FS로 리디렉션되므로 다음 메시지가 표시됩니다.

	![](./media/web-sites-dotnet-lob-application-adfs/13-authorize-adfs-error.png)

	AD FS 서버의 이벤트 뷰어에서 이 오류를 조사하면 다음 예외 메시지를 확인할 수 있습니다.  
	<pre class="prettyprint">
	Microsoft.IdentityServer.Web.InvalidRequestException: MSIS7042: <mark>The same client browser session has made '6' requests in the last '11' seconds.</mark> Contact your administrator for details.
	   at Microsoft.IdentityServer.Web.Protocols.PassiveProtocolHandler.UpdateLoopDetectionCookie(WrappedHttpListenerContext context)
	   at Microsoft.IdentityServer.Web.Protocols.WSFederation.WSFederationProtocolHandler.SendSignInResponse(WSFederationContext context, MSISSignInResponse response)
	   at Microsoft.IdentityServer.Web.PassiveProtocolListener.ProcessProtocolRequest(ProtocolContext protocolContext, PassiveProtocolHandler protocolHandler)
	   at Microsoft.IdentityServer.Web.PassiveProtocolListener.OnGetContext(WrappedHttpListenerContext context)
	</pre>

	이 오류가 발생한 이유는 사용자의 역할에 권한이 부여되지 않은 경우 기본적으로 MVC에서 401 권한 없음을 반환하기 때문입니다. 이 경우 ID 공급자(AD FS)에 대한 재인증 요청이 트리거됩니다. 사용자가 이미 인증되었으므로 AD FS가 동일한 페이지로 돌아가고 또 다른 401이 발생하여 리디렉션 루프가 생성됩니다. 단순한 논리로 AuthorizeAttribute의 `HandleUnauthorizedRequest` 메서드를 재정의하여 리디렉션 루프를 계속하는 대신 의미 있는 내용을 표시할 수 있습니다.

5. 프로젝트에서 AuthorizeAttribute.cs라는 파일을 만들어 아래 코드를 붙여 넣습니다.

		using System;
		using System.Web.Mvc;
		using System.Web.Routing;
		
		namespace WebApp_WSFederation_DotNet
		{
		    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
		    public class AuthorizeAttribute : System.Web.Mvc.AuthorizeAttribute
		    {
		        protected override void HandleUnauthorizedRequest(AuthorizationContext filterContext)
		        {
		            if (filterContext.HttpContext.Request.IsAuthenticated)
		            {
		                filterContext.Result = new System.Web.Mvc.HttpStatusCodeResult((int)System.Net.HttpStatusCode.Forbidden);
		            }
		            else
		            {
		                base.HandleUnauthorizedRequest(filterContext);
		            }
		        }
		    }
		}

	재정의 코드는 authenticated-but-unauthorized의 경우 HTTP 401(권한 없음) 대신 HTTP 403(사용 권한 없음)을 보냅니다.

6. `F5` 키를 눌러 디버거를 다시 실행합니다. 이제 **Contact**를 클릭하면 보다 자세한(중요하지는 않음) 오류 메시지가 표시됩니다.

	![](./media/web-sites-dotnet-lob-application-adfs/14-unauthorized-forbidden.png)

7. 응용 프로그램을 Azure 앱 서비스 웹 앱에 다시 게시하고 라이브 응용 프로그램의 동작을 테스트합니다.

<a name="bkmk_data"></a>
## 온-프레미스 데이터 연결

Azure Active Directory 대신 AD FS를 사용하여 LOB(기간 업무) 응용 프로그램을 구현하는 이유는 조직의 데이터를 오프-프레미스로 유지하는 경우의 규정 준수 문제 때문입니다. 이는 Azure의 웹 앱이 웹 앱에 대한 데이터 계층으로 [SQL 데이터베이스](/services/sql-database/) 사용이 허용되지 않았기 때문에 온-프레미스 데이터베이스에 액세스해야 한다는 뜻입니다.

Azure 앱 서비스 웹 앱은 다음의 두 가지 방법으로 온-프레미스 데이터베이스 액세스를 지원합니다. [하이브리드 연결](integration-hybrid-connection-overview.md) 및 [가상 네트워크](web-sites-integrate-with-vnet.md). 자세한 내용은 [Azure 앱 서비스 웹 앱에서 VNET 통합 및 하이브리드 연결 사용](http://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/)을 참조하세요.

<a name="bkmk_resources"></a>
## 추가 리소스

- [SSL 및 Authorize 특성을 사용하여 응용 프로그램 보호](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute)
- [Azure 앱 서비스에서 인증을 위해 Active Directory를 사용합니다.](web-sites-authentication-authorization.md)
- [Azure Active Directory 인증을 통해 Azure 앱 서비스에서 .NET MVC 웹 앱 만들기](web-sites-dotnet-lob-application-azure-ad.md)
- [Visual Studio 2013의 ASP.NET에서 온-프레미스 조직 인증 옵션(ADFS) 사용](http://www.cloudidentity.com/blog/2014/02/12/use-the-on-premises-organizational-authentication-option-adfs-with-asp-net-in-visual-studio-2013/)
- [Vittorio Bertocci의 블로그](http://blogs.msdn.com/b/vbertocci/)
- [WIF에서 Katana로 VS2013 웹 프로젝트 마이그레이션](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/)
- [Active Directory Federation Services 개요](http://technet.microsoft.com/library/hh831502.aspx)
- [WS-Federation 1.1 사양](http://download.boulder.ibm.com/ibmdl/pub/software/dw/specs/ws-fed/WS-Federation-V1-1B.pdf?S_TACT=105AGX04&S_CMP=LP)

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 다음을 참조하세요. [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)
* 이전 포털에서 새 포털로의 변경에 대한 지침은 다음을 참조하세요. [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=52-->