<properties urlDisplayName="" pageTitle=".NET 및 Azure Active Directory를 사용한 웹 Single Sign-On" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Web Single Sign-On with .NET and Azure Active Directory" authors="" solutions="" manager="terrylan" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/24/2014" ms.author="mbaldwin" />






# .NET 및 Azure Active Directory를 사용한 웹 Single Sign-On

<h2><a name="introduction"></a>소개</h2>

이 자습서에서는 Azure Active Directory를 활용하여 Office 365 고객의 사용자에 대해 Single Sign-On을 사용하도록 설정하는 방법을 보여 줍니다. 다음 방법에 대해 알아봅니다.

* 고객의 테넌트에서 웹 응용 프로그램 프로비전
* WS-Federation을 사용하여 응용 프로그램 보호

<h3>필수 조건</h3>
이 연습에는 다음과 같은 개발 환경 필수 조건이 필요합니다.

* Visual Studio 2010 SP1
* Microsoft .NET Framework 4.0
* [ASP.NET MVC 3]
* [Windows Identity Foundation 1.0 런타임]
* [Windows Identity Foundation 3.5 SDK]
* IIS(인터넷 정보 서비스) 7.5(SSL 사용)
* Windows PowerShell
* [Office 365 PowerShell Commandlets]

<h3>목차</h3>
* [소개][]
* [1단계: ASP.NET MVC 응용 프로그램 만들기][]
* [2단계: 회사 디렉터리 테넌트에서 응용 프로그램 프로비전][]
* [3단계: 직원 로그인을 위해 WS-Federation을 사용하여 응용 프로그램 보호][]
* [요약][]

<h2><a name="createapp"></a>1단계: ASP.NET MVC 응용 프로그램 만들기</h2>
이 단계에서는 보호된 리소스를 나타내는 간단한 ASP.NET MVC 3 응용 프로그램을 만드는 방법을 설명합니다. 이 리소스에 대한 액세스 권한은 자습서의 뒷부분에 설명된 회사의 STS에 의해 관리되는 페더레이션 인증을 통해 부여됩니다.

1. 관리자 권한으로 Visual Studio를 엽니다.
2. **파일** 메뉴에서 **새 프로젝트**를 클릭합니다. 
3. 왼쪽의 템플릿 메뉴에서 **웹**을 선택한 후 **ASP.NET MVC 3 웹 응용 프로그램**을 클릭합니다. 프로젝트의 이름을 *OrgIdFederationSample*로 지정합니다.
4. **새 ASP.NET MVC 3 프로젝트** 대화 상자에서 **빈** 템플릿을 선택한 후 **확인**을 클릭합니다.
5. Visual Studio에서 새 프로젝트가 생성되고 나면 **솔루션 탐색기**에서 **컨트롤러** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**, **컨트롤러**를 차례로 클릭합니다.
6. **컨트롤러 추가** 대화 상자에서 새 컨트롤러의 이름을 *HomeController.cs*로 지정한 후 **추가**를 클릭합니다.
7. **HomeController.cs** 파일이 만들어져 열립니다. 코드 파일에서 ***Index()*** 메서드를 마우스 오른쪽 단추로 클릭하고 **뷰 추가...**를 클릭합니다. 
8. **뷰 추가** 대화 상자에서 **확인**을 클릭합니다. **Home**이라는 새 폴더에 **Index.cshtml**파일이 만들어집니다. 
9. **솔루션 탐색기**에서 **OrgIdFederationSample** 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **속성**을 클릭합니다.
10. 속성 창의 왼쪽 메뉴에서 **웹**을 클릭한 후 **로컬 IIS 웹 서버 사용**을 선택합니다. 프로젝트의 가상 디렉터리를 만들지 묻는 대화 상자가 표시될 수 있습니다. 대화 상자에서 **예**를 클릭합니다.
11. 응용 프로그램을 빌드 및 실행합니다. Home 컨트롤러의 인덱스 페이지가 나타납니다.

<h2><a name="provisionapp"></a>2단계: 회사 디렉터리 테넌트에서 응용 프로그램 프로비전</h2>
이 단계에서는 Azure Active Directory 고객의 관리자가 해당 테넌트에서 MVC 응용 프로그램을 프로비전하고 Single Sign-On을 구성하는 방법을 설명합니다. 이 단계가 완료된 후 회사의 직원은 해당 Office 365 계정을 사용하여 웹 응용 프로그램에 인증할 수 있습니다.

프로비전 프로세스는 응용 프로그램의 새 서비스 사용자를 만드는 것으로 시작됩니다. 서비스 사용자는 Azure Active Directory에서 디렉터리에 응용 프로그램을 등록하고 인증하는 데 사용됩니다.

1. 아직 수행하지 않은 경우 Office 365 PowerShell Commandlet을 다운로드하여 설치합니다.
2. **시작** 메뉴에서 **Windows PowerShell용 Microsoft Online Services 모듈** 콘솔을 실행합니다. 이 콘솔은 서비스 사용자 만들기 및 수정과 같은 Office 365 테넌트에 대한 특성 구성을 위한 명령줄 환경을 제공합니다.
3. 필수 **MSOnlineExtended** 모듈을 가져오려면 다음 명령을 입력하고 Enter 키를 누릅니다.

		Import-Module MSOnlineExtended -Force
4. Office 365 디렉터리에 연결하려면 회사의 관리자 자격 증명을 제공해야 합니다. 다음 명령을 입력하고 Enter 키를 누른 후 프롬프트에 자격 증명을 입력합니다.

		Connect-MsolService
5. 이제 응용 프로그램의 새 서비스 사용자를 만듭니다. 다음 명령을 입력하고 Enter 키를 누릅니다.

		New-MsolServicePrincipal -ServicePrincipalNames @("OrgIdFederationSample/localhost") -DisplayName "Federation Sample Website" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 
이 단계에서는 다음과 유사한 정보가 출력됩니다.

		The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
		DisplayName           : Federation Sample Website
		ServicePrincipalNames : {OrgIdFederationSample/localhost}
		ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
		AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
		TrustedForDelegation  : False
		AccountEnabled        : True
		KeyType               : Symmetric
		KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
		StartDate             : 12/01/2012 08:00:00 a.m.
		EndDate               : 12/01/2013 08:00:00 a.m.
		Usage                 : Verify 
	<div class="dev-callout"><strong>참고</strong><p>이 출력을 저장해야 하며, 특히 생성된 대칭 키를 저장해야 합니다. 이 키는 서비스 사용자를 만드는 동안에만 표시되며 나중에는 이 키를 검색할 수 없습니다. 다른 값은 Graph API를 사용하여 디렉터리에서 정보를 읽고 쓰는 데 필요합니다.</p></div>

6. 마지막 단계는 응용 프로그램의 릴레이 URL을 설정하는 것입니다. 릴레이 URL은 응답이 다음과 같은 인증 시도로 전송되는 위치입니다. 다음 명령을 입력하고 Enter 키를 누릅니다.

		$replyUrl = New-MsolServicePrincipalAddresses -Address "https://localhost/OrgIdFederationSample" 

		Set-MsolServicePrincipal -AppPrincipalId "7829c758-2bef-43df-a685-717089474505" -Addresses $replyUrl 
	
이제 디렉터리에서 웹 응용 프로그램이 프로비전되어 회사 직원이 이를 웹 Single Sign-On에 사용할 수 있습니다.

<h2><a name="protectapp"></a>3단계: 직원 로그인을 위해 WS-Federation을 사용하여 응용 프로그램 보호</h2>
이 단계에서는 WIF(Windows Identity Foundation)를 사용하는 페더레이션 로그인에 대한 지원을 추가하는 방법을 보여 줍니다. 또한 로그인 페이지를 추가하고 응용 프로그램과 디렉터리 테넌트 간에 트러스트를 구성합니다.

1. Visual Studio에서 **OrgIdFederationSample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **"STS 참조 추가..."**를 선택합니다. 이 상황에 맞는 메뉴 옵션은 WIF SDK 설치 시 추가되었습니다.
2. **페더레이션 유틸리티** 대화 상자의 **응용 프로그램 URII**에 다음 형식으로 URI를 제공해야 합니다.

		spn:<Your AppPrincipalId>@<Your Directory Domain>

	**spn**은 URI가 서비스 사용자 이름임을 지정하고, **Your AppPrincpalId**는 서비스 사용자를 만들 때 생성된 **AppPrincipalId** GUID 값이며, **Your Directory Domain**은 디렉터리 테넌트의 *.onmicrosoft.com 도메인입니다. 이 샘플 응용 프로그램의 경우 전체 응용 프로그램 URI 값은 아래에 표시된 것처럼 지정됩니다.

		spn:7829c758-2bef-43df-a685-717089474505@awesomecomputers.onmicrosoft.com

	응용 프로그램 URI를 입력한 후 **다음**을 클릭합니다.

3. "응용 프로그램이 보안 https 연결에서 호스트되지 않습니다."라는 경고 대화 상자가 나타납니다. 이 문제는 페더레이션 유틸리티가 **spn:** 형식을 인식하지 못하기 때문에 발생하지만 응용 프로그램에서는 보안 HTTPS 연결을 계속 사용합니다. **예**를 클릭하여 계속합니다.

4. 페더레이션 유틸리티의 다음 페이지에서 **기존 STS 사용**을 선택한 후 **STS WS-Federation 메타데이터 문서 위치**에 WS-Federation 메타데이터 문서의 URL을 입력합니다. 이 URL은 다음과 같은 형식으로 지정됩니다.

		https://accounts.accesscontrol.windows.net/<Domain Name or Tenant ID>/FederationMetadata/2007-06/FederationMetadata.xml 

	이 응용 프로그램의 경우 WS-Federation 메타데이터 위치는 아래에 표시된 것처럼 지정됩니다.

		https://accounts.accesscontrol.windows.net/fabrikam.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml 

	메타데이터 위치를 입력한 후 **다음**을 클릭합니다.

5. 페더레이션 유틸리티의 다음 페이지에서 **Disable certificate chain validation**을 선택한 후 **다음**을 클릭합니다.

6. 페더레이션 유틸리티의 다음 페이지에서 **암호화 안 함**을 선택한 후 **다음**을 클릭합니다.

7. 페더레이션 유틸리티의 다음 페이지에 STS에서 제공하는 클레임이 표시됩니다. 클레임을 검토한 후 **다음**을 클릭합니다.

8. 이제 개발 환경에 대해 SSL을 지원하도록 IIS(인터넷 정보 서비스)를 구성합니다. IIS 관리자를 열려면 **실행** 프롬프트에 *inetmgr*을 입력하면 됩니다. 

9. IIS 관리자의 왼쪽 메뉴에서 **사이트** 폴더를 확장한 후 **기본 웹 사이트 홈**을 클릭합니다. 오른쪽의 **작업** 메뉴에서 **바인딩...**을 클릭합니다.

10. **사이트 바인딩** 대화 상자에서 **추가**를 클릭합니다. **사이트 바인딩 추가** 대화 상자에서 **유형** 드롭다운을 ***https***로 변경하고 **SSL 인증서**에서 **IIS Express 개발 인증서**를 선택합니다. **확인**을 클릭합니다.

11. IIS 관리자의 왼쪽 메뉴에서 **응용 프로그램 풀**을 클릭한 후 목록에서 **ASP.NET v4.0** 항목을 선택하고 오른쪽의 **작업** 메뉴에서 **고급 설정**을 클릭합니다.

12. **고급 설정** 대화 상자에서 **사용자 프로필 로드** 속성을 **true**로 설정합니다. **확인**을 클릭합니다.

13. Visual Studio의 **솔루션 탐색기**에서 프로젝트의 루트에서 **Web.config** 파일을 엽니다. 

14. **Web.config** 파일에서 **wsFederation** 섹션을 찾아 서비스 사용자를 만들 때 지정한 **$replyUrl** 변수와 동일한 값으로 **reply** 특성을 추가합니다. 예를 들면 다음과 같습니다.

		<wsFederation passiveRedirectEnabled="true" issuer="https://accounts.accesscontrol.windows.net/v2/wsfederation" realm="spn: 7829c758-2bef-43df-a685-717089474505" requireHttps="false" reply="https://localhost/OrgIdFederationSample" /> 

15. **system.web** 섹션 내부에 **httpRuntime** 노드를 추가하고 **requestValidationMode** 특성을 **2.0**으로 설정합니다. 예를 들면 다음과 같습니다.

		<system.web>
			<httpRuntime requestValidationMode="2.0" /> 
			...

	**Web.config** 파일을 저장합니다.

16. 이제 웹 응용 프로그램에 대해 인증을 사용하도록 설정했으므로 **인덱스** 페이지가 인증된 사용자의 클레임을 표시하도록 수정되어야 합니다. **뷰** 폴더의 **홈** 폴더에 있는 **Index.cshtml** 파일을 엽니다.

17. **Index.cshtml** 파일에 다음 코드 조각을 추가하고 이 파일을 저장합니다.

		<p>
			@if (User.Identity.IsAuthenticated)
			{
			<ul>
				@foreach (string claim in ((Microsoft.IdentityModel.Claims.IClaimsIdentity)this.User.Identity).Claims.Select(c => c.ClaimType + " : " + c.Value))
				{
					<li>@claim</li>
				}
			</ul>
			}
		</p> 

18. 변경 내용을 **Index.cshtml** 파일에 저장한 후 **F5** 키를 눌러 응용 프로그램을 실행합니다. 디렉터리 테넌트 자격 증명을 사용하여 로그인할 수 있는 Office 365 ID 공급자 페이지로 리디렉션됩니다(예: *john.doe@awesomecomputers.onmicrosoft.com*).

19. 자격 증명을 사용하여 로그인하고 나면 계정의 클레임이 표시되는 홈 컨트롤러의 인덱스 페이지로 리디렉션됩니다. 여기에 Azure Active Directory에서 제공하는 Single Sign-On을 사용하여 응용 프로그램에 인증된 사용자가 표시됩니다.

<h2><a name="summary"></a>요약</h2>
이 자습서에서는 Azure Active Directory의 Single Sign-On 기능을 사용하여 단일 테넌트 응용 프로그램을 만들고 구성하는 방법을 살펴보았습니다. 또한 [Azure Active Directory를 사용하여 다중 테넌트 클라우드 응용 프로그램 개발](영문) 자습서를 참조하여 Azure Active Directory용 다중 테넌트 응용 프로그램을 만들 수 있습니다.

[소개]: #introduction
[1단계: ASP.NET MVC 응용 프로그램 만들기]: #createapp
[2단계: 회사 디렉터리 테넌트에서 응용 프로그램 프로비전]: #provisionapp
[3단계: 직원 로그인을 위해 WS-Federation을 사용하여 응용 프로그램 보호]: #protectapp
[요약]: #summary
[Azure Active Directory를 사용하여 다중 테넌트 클라우드 응용 프로그램 개발]: http://g.microsoftonline.com/0AX00en/121
[Windows Identity Foundation 3.5 SDK]: http://www.microsoft.com/ko-kr/download/details.aspx?id=4451
[Windows Identity Foundation 1.0 런타임]: http://www.microsoft.com/ko-kr/download/details.aspx?id=17331
[Office 365 Powershell Commandlets]: http://onlinehelp.microsoft.com/ko-kr/office365-enterprises/ff652560.aspx
[ASP.NET MVC 3]: http://www.microsoft.com/ko-kr/download/details.aspx?id=4211


<!--HONumber=35.1-->
