<properties 
	pageTitle="액세스 제어 사용 방법(.NET) - Azure 기능 가이드" 
	description="웹 앱에 대한 액세스 권한을 받으려고 하는 경우 Azure 응용 프로그램에서 ACS(액세스 제어 서비스)를 사용해 사용자를 인증하는 방법에 대해 알아봅니다." 
	services="active-directory" 
	documentationCenter=".net" 
	authors="msmbaldwin" 
	manager="mbaldwin" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="mbaldwin"/>

# Azure Active Directory 액세스 제어를 사용하여 웹 사용자를 인증하는 방법

이 가이드에서는 Azure Active Directory 액세스 제어(액세스 제어 서비스 또는 ACS라고도 함)를 사용하여 Microsoft, Google, Yahoo 및 Facebook과 같은 ID 공급자의 사용자가 웹 응용 프로그램에 액세스하려고 할 때 사용자를 인증하는 방법을 보여 줍니다.

## <span class="short-header">목차</span>목차

-   [ACS 정의][ACS 정의]
-   [개념][개념]
-   [필수 조건][필수 조건]
-   [액세스 제어 네임스페이스 만들기][액세스 제어 네임스페이스 만들기]
-   [ASP.NET MVC 응용 프로그램 만들기][ASP.NET MVC 응용 프로그램 만들기]
-   [ACS와 웹 응용 프로그램 통합][ACS와 웹 응용 프로그램 통합]
-   [ACS와의 통합 테스트][ACS와의 통합 테스트]
-   [ACS에서 전송된 클레임 보기][ACS에서 전송된 클레임 보기]
-   [ACS 관리 포털에서 응용 프로그램 보기][ACS 관리 포털에서 응용 프로그램 보기]
-   [ID 공급자 추가][ID 공급자 추가]
-   [다음 작업][다음 작업]

## <span class="short-header">ACS 정의</span>ACS 정의

대부분의 개발자는 ID 전문가가 아니며 해당 응용 프로그램과 서비스에 대한 인증 및 권한 부여 메커니즘을 직접 개발하려고 하지 않습니다. ACS는 복잡한 인증 논리를 코드에 추가하지 않고도 웹 응용 프로그램과 서비스에 액세스하려는 사용자를 쉽게 인증할 수 있게 해 주는 Azure 서비스입니다.

ACS에서 사용할 수 있는 기능은 다음과 같습니다.

-   WIF(Windows Identity Foundation)와 통합
-   Microsoft 계정(이전의 Windows Live ID), Google, Yahoo 및 Facebook을 포함하여 인기 있는 웹 IP(ID 공급자) 지원
-   AD FS(Active Directory Federation Services) 2.0 지원
-   ACS 설정에 대한 프로그래밍 방식의 액세스를 제공하는 OData(Open Data Protocol)
     기반 관리 서비스
-   ACS 설정에 대한 관리자 액세스 권한을 허용하는 관리
     포털

ACS에 대한 자세한 내용은 [액세스 제어 서비스 2.0][액세스 제어 서비스 2.0]을 참조하십시오.

## <span class="short-header">개념</span>개념

ACS는 온-프레미스 또는 클라우드에서 실행되는 응용 프로그램에 대한 인증 메커니즘을 만들기 위한 일관된 접근 방식인 클레임 기반 ID 원칙을 기반으로 합니다. 클레임 기반 ID는 응용 프로그램과 서비스가 조직 내부, 다른 조직 및 인터넷의 사용자에 대해 필요한 ID 정보를 얻는 일반적인 방법을 제공합니다.

이 가이드의 작업을 완료하려면 이 가이드에서 사용된 다음 용어와 개념을 이해해야 합니다.

**클라이언트** - 웹 응용 프로그램에 대한 액세스 권한을 얻으려고 하는 브라우저입니다.

**RP(신뢰 당사자) 응용 프로그램** - 해당 웹앱입니다. RP 응용 프로그램은 외부 기관에 인증을 아웃소싱하는 웹 사이트 또는 서비스입니다. ID 표현으로는 RP가 해당 기관을 신뢰한다고 합니다. 이 가이드에서는 ACS를 신뢰하도록 응용 프로그램을 구성하는 방법을 설명합니다.

**토큰** - 사용자는 RP 응용 프로그램이 신뢰하는 기관에서 발급된 유효한 토큰을 제공하여 RP 응용 프로그램에 대한 액세스 권한을 얻습니다. 클라이언트가 인증될 때 발급되는 보안 데이터 모음입니다. 사용자 이름 또는 연령과 같은 인증된 사용자의 특성이거나 사용자 역할의 식별자인 클레임 집합이 포함되어 있습니다. 토큰은 디지털 서명되므로 발급자를 식별할 수 있으며 해당 콘텐츠를 변경할 수 없습니다.

**IP(ID 공급자)** - 사용자 ID를 인증하고 Microsoft 계정(Windows Live ID), Facebook, Google, Twitter 및 Active Directory와 같은 보안 토큰을 발급하는 기관입니다. ACS가 IP를 신뢰하도록 구성된 경우 IP에서 발급하는 토큰을 수락하고 유효성을 검사합니다. ACS에서 동시에 여러 IP를 신뢰할 수 있으므로 응용 프로그램이 ACS를 신뢰하는 경우 ACS에서 신뢰하는 모든 IP에 의해 대신 인증되는 옵션을 사용자에게 제공할 수 있습니다.

**FP(페더레이션 공급자)** - IP(ID 공급자)는 사용자 정보를 직접 보유하고 해당 자격 증명을 통해 사용자를 인증하며 사용자에 대한 클레임을 발급합니다. FP(페더레이션 공급자)는 다른 종류의 기관입니다. 사용자를 직접 인증하는 대신 FP는 인증을 조정합니다. 신뢰 당사자 응용 프로그램과 하나 이상의 IP 사이에서 중간자 역할을 합니다. ACS는 FP(페더레이션 공급자)입니다.

**ACS 규칙 엔진** - 클레임 변환 규칙은 RP에서 사용할 수 있도록 신뢰된 IP이 토큰에 포함된 클레임을 변환합니다. ACS에는 RP에 지정한 클레임 변환 규칙을 적용하는 규칙 엔진이 포함되어 있습니다.

**액세스 제어 네임스페이스** - 응용 프로그램 내에서 ACS 리소스의 주소를 지정하기 위한 고유 범위를 제공합니다. 네임스페이스에는 신뢰하는 IP, 서비스하려는 RP 응용 프로그램, 들어오는 토큰에 적용할 규칙 등의 설정이 포함되어 있으며 응용 프로그램과 개발자가 ACS와 통신하는 데 사용하는 끝점을 표시합니다.

다음 그림은 웹 응용 프로그램에서 ACS 인증이 작동하는 방식을 보여 줍니다.

![][0]

1.  클라이언트(이 경우 브라우저)가 RP의 페이지를 요청합니다.
2.  요청이 아직 인증되지 않았으므로 RP가 사용자를 신뢰하는 기관,
     즉 ACS로 리디렉션합니다. ACS는 이 RP에 대해
    지정된 IP 선택 항목을 사용자에게 제공합니다. 사용자가
    적절한 IP를 선택합니다.
3.  클라이언트가 IP의 인증 페이지로 이동하고 사용자에게 로그온하라는
    메시지를 표시합니다.
4.  클라이언트가 인증된 후(예: ID 자격 증명이 입력됨) IP가
    보안 토큰을 발급합니다.
5.  보안 토큰을 발급한 후 IP가 발급된 보안 토큰을 ACS에 보내도록 클라이언트에 지시합니다.
6.  ACS가 IP에 의해 발급된 보안 토큰의 유효성을 검사하고 이
    토큰에 포함된 ID 클레임을 ACS 규칙 엔진에 입력한 다음
    출력 ID 클레임을 계산하고 이러한 출력 클레임이 포함된 새 보안
    토큰을 발급합니다.
7.  ACS가 발급된 보안 토큰을 RP에 보내도록 클라이언트에 지시합니다. RP가 보안 토큰에 있는 서명의 유효성을 검사하고 응용 프로그램 비즈니스 논리에서 사용하기 위해 클레임을 추출한 다음 원래 요청된 페이지를 반환합니다.

## <span class="short-header">필수 조건</span>필수 조건

이 가이드의 작업을 완료하려면 다음이 필요합니다.

-   Azure 구독
-   Microsoft Visual Studio 2012
-   Visual Studio 2012용 ID 및 액세스 도구(다운로드하려면 [ID 및 액세스 도구][ID 및 액세스 도구] 참조)

## <span class="short-header">액세스 제어 네임스페이스 만들기</span>액세스 제어 네임스페이스 만들기

Azure에서 Active Directory 액세스 제어를 사용하려면 액세스 제어 네임스페이스를 만듭니다. 네임스페이스는 응용 프로그램 내에서 ACS
리소스의 주소를 지정하기 위한 고유 범위를 제공합니다.

1.  [Azure 관리 포털][Azure 관리 포털](<https://manage.WindowsAzure.com>)에 로그인합니다.

2.  **Active Directory**를 클릭합니다.

    ![][1]

3.  새 액세스 제어 네임스페이스를 만들려면 **새로 만들기**를 클릭합니다. **앱 서비스** 및 **액세스 제어**가 선택됩니다. **빠른 생성**을 클릭합니다.

    ![][2]

4.  네임스페이스의 이름을 입력합니다. Azure에서 이름이 고유한지 검증합니다.

5.  네임스페이스가 사용되는 지역을 선택합니다. 최상의 성능을 얻으려면 응용 프로그램을 배포할 지역을 사용하고 **만들기**를 클릭합니다.

Azure에서 네임스페이스를 만들고 활성화합니다.

## <span class="short-header">ASP.NET MVC 응용 프로그램 만들기</span>ASP.NET MVC 응용 프로그램 만들기

이 단계에서는 ASP.NET MVC 응용 프로그램을 만듭니다. 이후 단계에서 이 간단한 Web Forms 응용 프로그램을 ACS와 통합하겠습니다.

1.  Visual Studio 2012 또는 Visual Studio Express for Web 2012를 시작합니다(이전 버전의 Visual Studio는 이 자습서에 작동하지 않음).
2.  **파일**을 클릭한 후 **새 프로젝트**를 클릭합니다.
3.  Visual C#/웹 템플릿을 선택한 후 **ASP.NET MVC 4 웹 응용 프로그램**을 선택합니다.

    이 가이드에서는 MVC 응용 프로그램을 사용하지만 모든 웹 응용 프로그램 유형을 이 작업에 사용할 수 있습니다.

    ![][3]

4.  **이름**에 **MvcACS**를 입력하고 **확인**을 클릭합니다.
5.  다음 대화 상자에서 **인터넷 응용 프로그램**을 선택하고 **확인**을 클릭합니다.
6.  *Views\\Shared\_LoginPartial.cshtml* 파일을 편집하고 파일 내용을 다음 코드로 바꿉니다.

        @if (Request.IsAuthenticated)
        {
            string name = "Null ID";
            if (!String.IsNullOrEmpty(User.Identity.Name))
            {
                name = User.Identity.Name;
            }    
            <text>
            Hello, @Html.ActionLink(name, "Manage", "Account", routeValues: null, htmlAttributes: new { @class = "username", title = "Manage" })!
                    @using (Html.BeginForm("LogOff", "Account", FormMethod.Post, new { id = "logoutForm" }))
                    {
                        @Html.AntiForgeryToken()
                        <a href="javascript:document.getElementById('logoutForm').submit()">Log off</a>
                    }
            </text>
        }
        else
        {
            <ul>
                <li>@Html.ActionLink("Register", "Register", "Account", routeValues: null, htmlAttributes: new { id = "registerLink" })</li>
                <li>@Html.ActionLink("Log in", "Login", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
            </ul>
        }

현재 ACS에서 User.Identity.Name을 설정하지 않으므로 위와 같이 변경해야 합니다.

1.  F5 키를 눌러 응용 프로그램을 실행합니다. 기본 ASP.NET MVC 응용 프로그램이 웹 브라우저에 나타납니다.

## <span class="short-header">ACS와 웹 응용 프로그램 통합</span>ACS와 웹 응용 프로그램 통합

이 작업에서는 ACS와 ASP.NET 웹 응용 프로그램을 통합합니다.

1.  솔루션 탐색기에서 MvcACS 프로젝트를 마우스 오른쪽 단추로 클릭하고 **ID 및 액세스**를 선택합니다.

    **ID 및 액세스** 옵션이 상황에 맞는 메뉴에 표시되지 않는 경우 ID 및 액세스 도구를 설치합니다. 자세한 내용은 [ID 및 액세스 도구][ID 및 액세스 도구](영문)를 참조하십시오.

    ![][4]

2.  **공급자** 탭에서 **Azure 액세스 제어 서비스 사용**을 선택합니다.

    ![][5]

3.  **구성** 링크를 클릭합니다.

    ![][6]

    Visual Studio에서 액세스 제어 네임스페이스에 대한 정보를 요청합니다. 앞에서 만든 네임스페이스 이름을 입력합니다(위 이미지에서는 Test지만 다른 네임스페이스를 사용함). Azure 관리 포털로 돌아가서 대칭 키를 가져옵니다.

    ![][7]

4.  Azure 관리 포털에서 액세스 제어 네임스페이스를 클릭한 후 **관리**를 클릭합니다.

    ![][8]

5.  **관리 서비스**를 클릭한 후 **관리 클라이언트**를 클릭합니다.

    ![][9]

6.  **대칭 키**, **키 표시**를 차례로 클릭하고 키 값을 복사합니다. 그러 다음 **취소**를 클릭하여 변경하지 않고 관리 클라이언트 편집 페이지를 종료합니다.

    ![][10]

7.  Visual Studio에서 **네임스페이스에 대한 관리 키 입력** 필드에 키를 붙여 넣고 **관리 키 저장**을 클릭한 후 **확인**을 클릭합니다.

    ![][11]

    Visual Studio에서 네임스페이스 정보를 사용하여 ACS 관리 포털에 연결한 후 ID 공급자, 영역, 반환 URL 등의 네임스페이스 설정을 가져옵니다.

8.  **Windows Live ID**(Microsoft 계정)를 선택하고 확인을 클릭합니다.

    ![][12]

## <span class="short-header">ACS와의 통합 테스트</span>ACS와의 통합 테스트

이 작업에서는 RP 응용 프로그램과 ACS의 통합을 테스트하는 방법을 설명합니다.

-   Visual Studio에서 F5 키를 눌러 앱을 실행합니다.

응용 프로그램이 ACS와 통합되었으며 Windows Live ID(Microsoft 계정)를 선택한 경우 기본 ASP.NET Web Forms 응용 프로그램을 여는 대신 브라우저가 Microsoft 계정에 대한 로그인 페이지로 리디렉션됩니다. 유효한 사용자 이름과 암호로 로그인하면 MvcACS 응용 프로그램으로 리디렉션됩니다.

![][13]

축하합니다. ASP.NET 웹 응용 프로그램과 ACS를 통합했습니다. 이제 ACS에서 Microsoft 계정 자격 증명을 사용하여 사용자 인증을 처리합니다.

## <a name="bkmk_viewClaims"></a>ACS에서 전송된 클레임 보기

이 섹션에서는 ACS에서 전송된 클레임을 보기 위해 응용 프로그램을 수정합니다. ID 및 액세스 도구는 IP의 모든 클레임을 해당 응용 프로그램으로 전달하는 규칙 그룹을 만들었습니다. ID 공급자는 각기 다른 클레임을 보냅니다.

1.  *Controllers\\HomeController.cs* 파일을 엽니다. **System.Threading**에 대한 **using** 문을 추가합니다.

    using System.Threading;

2.  HomeController 클래스에서 *Claims* 메서드를 추가합니다.

        public ActionResult Claims()
        {
        ViewBag.Message = "Your claims page.";

        ViewBag.ClaimsIdentity = Thread.CurrentPrincipal.Identity;

        return View();

        }

3.  *Claims* 메서드를 마우스 오른쪽 단추로 클릭하고 **보기 추가**를 선택합니다.

![][14]

1.  **추가**를 클릭합니다.

2.  *Views\\Home\\Claims.cshtml* 파일 내용을 다음 코드로 바꿉니다.

        @{
            ViewBag.Title = "Claims";
        }
        <hgroup class="title">
            <h1>@ViewBag.Title.</h1>
            <h2>@ViewBag.Message</h2>
        </hgroup>
        <h3>Values from Identity</h3>
        <table>
            <tr>
                <td>
                    IsAuthenticated: 
                </td>
                <td>
                    @ViewBag.ClaimsIdentity.IsAuthenticated 
                </td>
            </tr>
            <tr>
                <td>
                    Name: 
                </td>        
                <td>
                    @ViewBag.ClaimsIdentity.Name
                </td>        
            </tr>
        </table>
        <h3>Claims from ClaimsIdentity</h3>
        <table>
            <tr>
                <th>
                    Claim Type
                </th>
                <th>
                    Claim Value
                </th>
            </tr>
                @foreach (System.Security.Claims.Claim claim in ViewBag.ClaimsIdentity.Claims ) {
            <tr>
                <td>
                    @claim.Type
                </td>
                <td>
                    @claim.Value
                </td>
            </tr>
        }
        </table>

3.  응용 프로그램을 실행하고 *Claims* 메서드로 이동합니다.

![][15]

응용 프로그램에서 클레임을 사용하는 방법에 대한 자세한 내용은 [Windows Identity Foundation 라이브러리][Windows Identity Foundation 라이브러리]를 참조하십시오.

## <a name="bkmk_VP"></a>ACS 관리 포털에서 앱 보기

Visual Studio의 ID 및 액세스 도구는 해당 응용 프로그램과 ACS를 자동으로 통합합니다.

Use Azure Access Control 옵션을 선택하고 응용 프로그램을 실행하면 ID 및 액세스 제어 도구가 해당 응용 프로그램을 신뢰 당사자로 추가하고 선택한 ID 공급자를 사용하도록 구성한 다음 응용 프로그램에 대한 기본 클레임 변환 규칙을 생성 및 선택합니다.

ACS 관리 포털에서 이러한 구성 설정을 검토 및 변경할 수 있습니다. 포털에서 변경 내용을 검토하려면 다음 단계를 따르십시오.

1.  Windows [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.

2.  **Active Directory**를 클릭합니다.

    ![][8]

3.  액세스 제어 네임스페이스를 선택하고 **관리**를 클릭합니다. ACS 관리 포털이 열립니다.

    ![][16]

4.  **신뢰 당사자 응용 프로그램**을 클릭합니다.

    새 MvcACS 응용 프로그램이 신뢰 당사자 응용 프로그램 목록에 나타납니다. 영역은 자동으로 응용 프로그램 기본 페이지로 설정됩니다.

    ![][17]

5.  **MvcACS**를 클릭합니다.

    신뢰 당사자 응용 프로그램 편집 페이지에는 MvcACS 웹 응용 프로그램에 대한 구성 설정이 포함되어 있습니다. 이 페이지에서 설정을 변경한 후 저장하면 변경 내용이 응용 프로그램에 즉시 적용됩니다.

    ![][18]

6.  페이지 아래로 스크롤하여 ID 공급자, 클레임 변환 규칙 등 MvcACS 응용 프로그램에 대한 나머지 구성 설정을 확인합니다.

    ![][19]

다음 섹션에서는 얼마나 쉽게 작업을 수행할 수 있는지 보여 주기 위해 ACS 관리 포털의 기능을 사용하여 웹 응용 프로그램을 변경하겠습니다.

## <span class="short-header">ID 공급자 추가</span>ID 공급자 추가

ACS 관리 포털을 사용하여 MvcACS 응용 프로그램의 인증을 변경합니다. 이 예제에서는 Google을 MvcACS의 ID 공급자로 추가하겠습니다.

1.  **ID 공급자**(탐색 메뉴)를 클릭한 후 **추가**를 클릭합니다.

    ![][20]

2.  **Google**을 클릭한 후 **다음**을 클릭합니다. 기본적으로 MvcACS 앱 확인란이 선택되어 있습니다.

    ![][21]

3.  저장을 클릭합니다.

    ![][22]

완료되었습니다. Visual Studio로 돌아가서 MvcACS 앱 프로젝트를 열고 **ID 및 액세스**를 클릭하면 도구에 Windows Live ID 및 Google ID 공급자가 둘 다 나열됩니다.

![][23]

응용 프로그램을 실행하면 결과를 확인할 수 있습니다. 응용 프로그램이 ID 공급자를 두 개 이상 지원하는 경우 사용자 브라우저가 먼저 ACS에 호스트된 페이지로 이동되며, 사용자에게 ID 공급자를 선택하라는 메시지가 표시됩니다.

![][24]

사용자가 ID 공급자를 선택하면 브라우저가 ID 공급자 로그인 페이지로 이동합니다.

## <span class="short-header">다음 작업</span>다음 작업

ACS와 통합된 웹 응용 프로그램을 만들었습니다. 그러나 이것은 시작일 뿐입니다. 이 시나리오를 확장할 수 있습니다.

예를 들어 이 RP에 ID 공급자를 더 추가하거나 Active Directory 도메인 서비스 등의 엔터프라이즈 디렉터리에 등록된 사용자가 웹 응용 프로그램에 로그온하도록 허용할 수 있습니다.

응용 프로그램 비즈니스 논리에서 처리하기 위해 응용 프로그램으로 전송되는 클레임을 결정하는 규칙을 네임스페이스에 추가할 수도 있습니다.

ACS 기능을 자세히 살펴보고 추가 시나리오를 실험하려면 [액세스 제어 서비스 2.0][액세스 제어 서비스 2.0](영문)을 참조하십시오.

  [ACS 정의]: #what-is
  [개념]: #concepts
  [필수 조건]: #pre
  [액세스 제어 네임스페이스 만들기]: #create-namespace
  [ASP.NET MVC 응용 프로그램 만들기]: #create-web-app
  [ACS와 웹 응용 프로그램 통합]: #Identity-Access
  [ACS와의 통합 테스트]: #Test-ACS
  [ACS에서 전송된 클레임 보기]: #bkmk_viewClaims
  [ACS 관리 포털에서 응용 프로그램 보기]: #bkmk_VP
  [ID 공급자 추가]: #add-IP
  [다음 작업]: #whats-next
  [액세스 제어 서비스 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
  [0]: ./media/active-directory-dotnet-how-to-use-access-control/acs-01.png
  [ID 및 액세스 도구]: http://go.microsoft.com/fwlink/?LinkID=245849
  [Azure 관리 포털]: http://manage.WindowsAzure.com
  [1]: ./media/active-directory-dotnet-how-to-use-access-control/acsCreateNamespace.png
  [2]: ./media/active-directory-dotnet-how-to-use-access-control/acsQuickCreate.png
  [3]: ./media/active-directory-dotnet-how-to-use-access-control/rzMvc.png
  [4]: ./media/active-directory-dotnet-how-to-use-access-control/rzIA.png
  [5]: ./media/active-directory-dotnet-how-to-use-access-control/rzPT.png
  [6]: ./media/active-directory-dotnet-how-to-use-access-control/rzC.png
  [7]: ./media/active-directory-dotnet-how-to-use-access-control/acsConfigAcsNamespace.png
  [8]: ./media/active-directory-dotnet-how-to-use-access-control/acsClickManage.png
  [9]: ./media/active-directory-dotnet-how-to-use-access-control/acsManagementService.png
  [10]: ./media/active-directory-dotnet-how-to-use-access-control/acsShowKey.png
  [11]: ./media/active-directory-dotnet-how-to-use-access-control/acsConfigAcsNamespace2.png
  [12]: ./media/active-directory-dotnet-how-to-use-access-control/acsIdAndAccess1.png
  [13]: ./media/active-directory-dotnet-how-to-use-access-control/acsMSFTAcct.png
  [14]: ./media/active-directory-dotnet-how-to-use-access-control/rzAv.png
  [15]: ./media/active-directory-dotnet-how-to-use-access-control/rzCl.png
  [Windows Identity Foundation 라이브러리]: http://msdn.microsoft.com/library/hh377151.aspx
  [16]: ./media/active-directory-dotnet-how-to-use-access-control/acsACSPortal.png
  [17]: ./media/active-directory-dotnet-how-to-use-access-control/acsRPPage.png
  [18]: ./media/active-directory-dotnet-how-to-use-access-control/acsEdit-RP.png
  [19]: ./media/active-directory-dotnet-how-to-use-access-control/acsEdit-RP2.png
  [20]: ./media/active-directory-dotnet-how-to-use-access-control/acsAdd-Idp.png
  [21]: ./media/active-directory-dotnet-how-to-use-access-control/acsAdd-Google.png
  [22]: ./media/active-directory-dotnet-how-to-use-access-control/acsSave-Google.png
  [23]: ./media/active-directory-dotnet-how-to-use-access-control/acsIdAndA-after.png
  [24]: ./media/active-directory-dotnet-how-to-use-access-control/acsSignIn.png

<!--HONumber=46--> 
