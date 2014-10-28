<properties linkid="Deploy_a_Secure_ASP.NET_Web_Forms_App_with_Membership_OAuth_and_SQL_Database_to_an_Azure_Web_Site" pageTitle="Deploy a Secure ASP.NET Web Forms App with Membership, OAuth, and SQL Database to an Azure Website" metaKeywords="Azure WebForms Hello World Tutorial, Azure Getting Started with WebForms Tutorial, Azure C# WebForms Hello World Tutorial, SQL Azure C# WebForms Tutorial" description="This tutorial shows you how to build a secure ASP.NET 4.5 Web Forms web app that incorporates a SQL Database and deploy the application to Azure." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Deploy a secure ASP.NET 4.5 Web Forms app to an Azure Website" authors="erikre"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="erikre"></tags>

# Azure 웹 사이트에 멤버 자격, OAuth 및 SQL 데이터베이스를 포함한 보안 ASP.NET Web Forms 앱 배포

이 자습서에서는 SQL 데이터베이스를 통합하는 보안 ASP.NET 4.5 Web Forms 웹 앱을 빌드하고 응용 프로그램을 Azure에 배포하는 방법을 보여 줍니다.

> [WACOM.NOTE]
> 이 자습서의 MVC 버전에 대해서는 [Azure 웹 사이트에 멤버 자격, OAuth 및 SQL 데이터베이스를 포함한 보안 ASP.NET MVC 5 앱 배포][Azure 웹 사이트에 멤버 자격, OAuth 및 SQL 데이터베이스를 포함한 보안 ASP.NET MVC 5 앱 배포]를 참조하세요.

Azure 계정은 무료로 개설할 수 있으며, Visual Studio 2013이 아직 없는 경우 SDK에서 Web Express용 Visual Studio 2013를 자동으로 설치합니다. Azure용 개발을 무료로 시작할 수 있습니다.

이 자습서에서는 이전에 Microsoft Azure를 사용한 경험이 없다고 가정합니다. 이 자습서를 완료하면 클라우드에서 클라우드 데이터베이스를 사용하는 웹 응용 프로그램을 실행할 수 있습니다.

다음 내용을 배웁니다.

-   ASP.NET 4.5 Web Forms 프로젝트를 만들고 Azure 웹 사이트에 게시하는 방법
-   OAuth 및 ASP.NET 멤버 자격을 사용하여 응용 프로그램 보안을 유지하는 방법
-   멤버 자격 및 응용 프로그램 데이터 모두에 단일 데이터베이스를 사용하는 방법
-   Web Forms 스캐폴딩을 사용하여 데이터를 수정할 수 있도록 하는 웹 페이지를 만드는 방법
-   새 멤버 자격 API를 사용하여 사용자와 역할을 추가하는 방법
-   SQL 데이터베이스를 사용하여 Azure에 데이터를 저장하는 방법

ASP.NET 4.5 Web Forms에서 빌드되고 데이터베이스 액세스에 Entity Framework를 사용하는 간단한 연락처 목록 웹 응용 프로그램을 빌드합니다. 다음 이미지는 데이터베이스에 대한 읽기 및 쓰기 권한을 허용하는 Web Forms 페이지를 보여 줍니다.

![연락처 - 편집 페이지][연락처 - 편집 페이지]

> [WACOM.NOTE]
> 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 [MSDN 구독자 혜택을 활성화][MSDN 구독자 혜택을 활성화]하거나 [무료 평가판을 등록][무료 평가판을 등록]할 수 있습니다.

이 자습서에는 다음 섹션이 포함되어 있습니다.

-   [개발 환경 설정][개발 환경 설정]
-   [Azure 환경 설정][Azure 환경 설정]
-   [ASP.NET Web Forms 응용 프로그램 만들기][ASP.NET Web Forms 응용 프로그램 만들기]
-   [응용 프로그램에 데이터베이스 추가][응용 프로그램에 데이터베이스 추가]
-   [프로젝트에 SSL 사용][프로젝트에 SSL 사용]
-   [OAuth 2.0 공급자 추가][OAuth 2.0 공급자 추가]
-   [멤버 자격 API를 사용하여 액세스 제한][멤버 자격 API를 사용하여 액세스 제한]
-   [Azure에 데이터베이스와 함께 응용 프로그램 배포][Azure에 데이터베이스와 함께 응용 프로그램 배포]
-   [데이터베이스 검토][데이터베이스 검토]
-   [다음 단계][다음 단계]

## 개발 환경 설정

시작하려면 Visual Studio 2013 및 Azure SDK for .NET을 설치하여 개발 환경 설정을 설정합니다.

1.  아직 설치하지 않았다면 [Visual Studio 2013][Visual Studio 2013]을 설치합니다.
2.  [Azure SDK for Visual Studio 2013][Azure SDK for Visual Studio 2013]을 설치합니다. 이 자습서에서는, Azure SDK for Visual Studio 2013를 설치하려면 먼저 Visual Studio 2013이 설치되어 있어야 합니다.

    > [WACOM.NOTE]
    > 사용자 컴퓨터에 SDK 종속성이 얼마나 있었는지에 따라 SDK를 설치하는 시간이 몇 분에서 30분 또는 그 이상이 될 수 있습니다.

3.  실행 가능한 설치 프로그램을 실행할지 또는 저장할지 묻는 메시지가 표시되면 **실행**을 클릭합니다.
4.  **웹 플랫폼 설치 관리자** 창에서 **설치**를 클릭하여 설치를 계속합니다.
    ![웹 플랫폼 설치 관리자][웹 플랫폼 설치 관리자]

    > [WACOM.NOTE]
    > SDK를 이미 설치한 경우 0개의 항목이 설치됩니다. 설치할 항목의 수는 **웹 플랫폼 설치 관리자** 창의 오른쪽 아래에 표시됩니다.

5.  **Visual Studio 업데이트 2**를 아직 설치하지 않았다면 **[Visual Studio 2013 업데이트 2][Visual Studio 2013 업데이트 2]** 이상을 다운로드하여 설치합니다.

    > [WACOM.NOTE]
    > Goggle OAuth 2.0을 사용하고 경고 없이 SSL을 로컬로 사용하려면 Visual Studio 2013 업데이트 2 이상을 설치해야 합니다. 또한 Web Forms 스캐폴딩을 사용하려면 업데이트 2가 필요합니다.

설치가 완료되면 개발을 시작하는 데 필요한 내용이 모두 준비된 것입니다.

## Azure 환경 설정

이 섹션에서는 Azure에서 Azure 웹 사이트 및 SQL 데이터베이스를 만들어 Azure 환경을 설정합니다.

### Azure에서 웹 사이트 및 SQL 데이터베이스 만들기

이 자습서에서 Azure 웹 사이트는 공유 호스팅 환경에서 실행되므로 다른 Azure 웹 사이트와 공유되는 VM(가상 컴퓨터)에서 실행됩니다. 공유 호스팅 환경은 클라우드를 시작하는 저비용 방법입니다. 나중에 웹 트래픽이 증가하면 응용 프로그램이 전용 VM에서 실행되어 요구에 맞게 확장될 수 있습니다. 더 복잡한 아키텍처가 필요한 경우 Azure 클라우드 서비스로 마이그레이션할 수 있습니다. 클라우드 서비스는 요구에 따라 구성할 수 있는 전용 VM에서 실행됩니다.

Azure SQL 데이터베이스는 SQL Server 기술로 구축된 클라우드 기반의 관계형 데이터베이스 서비스입니다. SQL Server에서 작동하는 도구와 응용 프로그램은 SQL 데이터베이스에서도 작동합니다.

1.  [Azure 관리 포털][Azure 관리 포털]의 왼쪽 탭에서 **웹 사이트**를 클릭한 다음 **새로 만들기**를 클릭합니다.   
    ![웹 플랫폼 설치 관리자][1]
2.  **웹 사이트**를 클릭한 다음 **사용자 지정 만들기**를 클릭합니다.
    ![사용자 지정 만들기][사용자 지정 만들기]
    **새 웹 사이트 - 사용자 지정 만들기** 마법사가 열립니다.

3.  마법사의 **웹 사이트 만들기** 단계에서 응용 프로그램의 고유 URL로 사용할 문자열을 **URL** 상자에 입력합니다. 전체 URL은 여기에 입력한 문자열과 텍스트 상자 옆에 표시되는 접미사로 구성됩니다. 이 그림에서는 이미 사용되고 있는 URL을 보여 주므로 **다른 URL을 선택해야** 합니다.
    ![연락처 - 새 웹 사이트 만들기][연락처 - 새 웹 사이트 만들기]
4.  웹 호스팅 계획 드롭다운 목록에서 가장 가까운 지역을 선택합니다. 이 설정은 VM이 실행되는 데이터 센터를 지정합니다.
5.  **데이터베이스** 드롭다운 목록에서 **무료 20MB SQL 데이터베이스 만들기**를 선택합니다.
6.  **DB Connection String Name** 상자에 기본값 *DefaultConnection*을 그대로 둡니다.
7.  상자의 아래에 있는 화살표를 클릭합니다.
    마법사가 **데이터베이스 설정 지정** 단계로 이동합니다.
8.  **이름** 상자에 *`ContactDB`*를 입력합니다.  
    ![데이터베이스 설정][데이터베이스 설정]
9.  **서버** 상자에서 **새 SQL 데이터베이스** 서버를 선택합니다.
    또는 이전에 SQL Server를 만든 경우 드롭다운 컨트롤에서 SQL Server를 선택할 수 있습니다.
10. **지역**에서 웹 사이트를 만든 지역과 동일한 지역을 설정합니다.
11. 관리자 **로그인 이름** 및 **암호**를 입력합니다.
    **새 SQL 데이터베이스 서버**를 선택한 경우 여기서 기존 이름과 암호를 입력하지 않고 나중에 데이터베이스에 액세스할 때 사용하기 위해 지금 정의하는 새 이름과 암호를 입력합니다. 이전에 만든 SQL Server를 선택한 경우 이전에 만든 SQL Server 계정 이름의 암호를 묻는 메시지가 표시됩니다. 이 자습서에서는 **고급** 확인란을 선택하지 않습니다.
12. 확인란 오른쪽 맨 아래에 있는 확인 표시를 클릭하여 마쳤음을 표시합니다.

**Azure 관리 포털**이 **웹 사이트** 페이지로 돌아가고 **상태** 열에 사이트를 만드는 중이라고 표시됩니다. 잠시(일반적으로 1분 미만) 후에 **상태** 열에 사이트를 만들었다고 표시됩니다. 왼쪽의 탐색 모음에서 계정에 보유한 사이트 수가 **웹 사이트** 아이콘 옆에 표시되고 데이터베이스 수가 **SQL 데이터베이스** 아이콘 옆에 표시됩니다.

## ASP.NET Web Forms 응용 프로그램 만들기

Azure 웹 사이트를 만들었지만 아직 콘텐츠가 없습니다. 다음 단계에서는 Azure에 게시할 Visual Studio 웹 앱을 만듭니다.

### 프로젝트 만들기

1.  Visual Studio의 **파일** 메뉴에서 **새 프로젝트**를 선택합니다.  
    ![파일 메뉴 - 새 프로젝트][파일 메뉴 - 새 프로젝트]
2.  왼쪽에서 **템플릿** -\> **Visual C#** -\> **웹** 템플릿 그룹을 선택합니다.
3.  가운데 열에서 **ASP.NET 웹 응용 프로그램** 템플릿을 선택합니다.
4.  프로젝트 *ContactManager* 이름을 입력하고 **확인**을 클릭합니다.
    ![새 프로젝트 대화 상자][새 프로젝트 대화 상자]

    > [WACOM.NOTE]
    > 이 자습서의 프로젝트 이름은 **ContactManager**입니다. 자습서 시리즈 전체에 제공된 코드가 예상대로 작동하도록 정확한 프로젝트 이름을 사용하는 것이 좋습니다.

5.  **새 ASP.NET 프로젝트** 대화 상자에서 **Web Forms** 템플릿을 선택합니다. **Host in the cloud** 확인란을 선택한 경우 선택을 취소하고 **확인**을 클릭합니다.
    ![새 ASP.NET 프로젝트 대화 상자][새 ASP.NET 프로젝트 대화 상자]
    Web Forms 응용 프로그램이 생성됩니다.

    ### 마스터 페이지 업데이트

    ASP.NET Web Forms에서 마스터 페이지를 통해 응용 프로그램의 페이지에 대한 일관된 레이아웃을 만들 수 있습니다. 단일 마스터 페이지는 응용 프로그램의 모든 페이지(또는 페이지 그룹)에 대해 원하는 모양과 느낌 및 표준 동작을 정의합니다. 그런 다음 표시할 콘텐츠를 포함하는 개별 콘텐츠 페이지를 만들 수 있습니다. 사용자가 콘텐츠 페이지를 요청하면 ASP.NET이 마스터 페이지에 해당 페이지를 병합하여 콘텐츠 페이지의 콘텐츠와 마스터 페이지의 레이아웃을 결합하는 출력을 생성합니다.
    새로운 사이트에서는 응용 프로그램 이름 및 링크를 업데이트해야 합니다. 이 링크는 연락처 세부 정보를 표시하는 페이지를 가리킵니다. 이렇게 변경하려면 마스터 페이지에서 HTML을 수정합니다.

6.  **솔루션 탐색기**에서 *Site.Master* 페이지를 찾아서 엽니다.
7.  페이지가 **디자인** 보기에 있는 경우 **소스** 보기로 전환합니다.
8.  노랑으로 강조 표시된 마크업을 수정하거나 추가하여 마스터 페이지를 업데이트합니다.

``` prettyprint
<%@ Master Language="C#" AutoEventWireup="true" CodeBehind="Site.master.cs" Inherits="ContactManager.SiteMaster" %>

<!DOCTYPE html>

<html lang="en">
<head runat="server">
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title><%: Page.Title %> - Contact Manager</title>

    <asp:PlaceHolder runat="server">
        <%: Scripts.Render("~/bundles/modernizr") %>
    </asp:PlaceHolder>
    <webopt:bundlereference runat="server" path="~/Content/css" />
    <link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />

</head>
<body>
    <form runat="server">
        <asp:ScriptManager runat="server">
            <Scripts>
                <%--To learn more about bundling scripts in ScriptManager see http://go.microsoft.com/fwlink/?LinkID=301884 --%>
                <%--Framework Scripts--%>
                <asp:ScriptReference Name="MsAjaxBundle" />
                <asp:ScriptReference Name="jquery" />
                <asp:ScriptReference Name="bootstrap" />
                <asp:ScriptReference Name="respond" />
                <asp:ScriptReference Name="WebForms.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebForms.js" />
                <asp:ScriptReference Name="WebUIValidation.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebUIValidation.js" />
                <asp:ScriptReference Name="MenuStandards.js" Assembly="System.Web" Path="~/Scripts/WebForms/MenuStandards.js" />
                <asp:ScriptReference Name="GridView.js" Assembly="System.Web" Path="~/Scripts/WebForms/GridView.js" />
                <asp:ScriptReference Name="DetailsView.js" Assembly="System.Web" Path="~/Scripts/WebForms/DetailsView.js" />
                <asp:ScriptReference Name="TreeView.js" Assembly="System.Web" Path="~/Scripts/WebForms/TreeView.js" />
                <asp:ScriptReference Name="WebParts.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebParts.js" />
                <asp:ScriptReference Name="Focus.js" Assembly="System.Web" Path="~/Scripts/WebForms/Focus.js" />
                <asp:ScriptReference Name="WebFormsBundle" />
                <%--Site Scripts--%>
            </Scripts>
        </asp:ScriptManager>

        <div class="navbar navbar-inverse navbar-fixed-top">
            <div class="container">
                <div class="navbar-header">
                    <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                    </button>
                    <a class="navbar-brand" runat="server" id="ContactDemoLink" href="~/Contacts/Default.aspx">연락처 데모</a>
                </div>
                <div class="navbar-collapse collapse">
                    <ul class="nav navbar-nav">
                        <li><a runat="server" href="~/">Home</a></li>
                        <li><a runat="server" href="~/About">About</a></li>
                        <li><a runat="server" href="~/Contact">Contact</a></li>
                    </ul>
                    <asp:LoginView runat="server" ViewStateMode="Disabled">
                        <AnonymousTemplate>
                            <ul class="nav navbar-nav navbar-right">
                                <li><a runat="server" href="~/Account/Register">Register</a></li>
                                <li><a runat="server" href="~/Account/Login">Log in</a></li>
                            </ul>
                        </AnonymousTemplate>
                        <LoggedInTemplate>
                            <ul class="nav navbar-nav navbar-right">
                                <li><a runat="server" href="~/Account/Manage" title="Manage your account">Hello, <%: Context.User.Identity.GetUserName()  %> !</a></li>
                                <li>
                                    <asp:LoginStatus runat="server" LogoutAction="Redirect" LogoutText="Log off" LogoutPageUrl="~/" OnLoggingOut="Unnamed_LoggingOut" />
                                </li>
                            </ul>
                        </LoggedInTemplate>
                    </asp:LoginView>
                </div>
            </div>
        </div>
        <div class="container body-content">
            <asp:ContentPlaceHolder ID="MainContent" runat="server">
            </asp:ContentPlaceHolder>
            <hr />
            <footer>
                <p>© <%: DateTime.Now.Year %> - Contact Manager</p>
            </footer>
        </div>
    </form>
</body>
</html>
```

이 자습서의 뒷부분에서 Web Forms 스캐폴딩을 추가합니다. 스캐폴딩이 위의 “연락처 데모” 링크에서 참조하는 페이지를 만듭니다.

### 로컬에서 응용 프로그램 실행

1.  **솔루션 탐색기**에서 *Default.aspx* 페이지를 마우스 오른쪽 단추로 클릭하고 **시작 페이지로 설정**을 선택합니다.
2.  이제 **CTRL+F5** 키를 눌러 응용 프로그램을 실행합니다.  
    응용 프로그램 기본 페이지가 기본 브라우저 창에 표시됩니다.  
    ![연락처 - 새 웹 사이트 만들기][2]

Azure에 배포할 응용 프로그램을 만들기 위해 지금 수행해야 하는 작업은 이것뿐입니다. 뒷부분에서, 데이터베이스 기능뿐 아니라 필요한 페이지를 추가하여 연락처 정보를 표시하고 편집합니다.

### Azure에 응용 프로그램 배포

응용 프로그램을 로컬로 만들어서 실행했으므로 Azure에 응용 프로그램을 배포하겠습니다.

1.  Visual Studio의 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 컨텍스트 메뉴에서 **게시**를 선택합니다.
    ![게시 선택][게시 선택]
    **웹 게시** 대화 상자가 표시됩니다.

2.  **웹 게시** 대화 상자의 **프로필** 탭에서 **Azure 웹 사이트**를 클릭합니다.
    ![웹 게시 대화 상자][웹 게시 대화 상자]
3.  아직 로그인하지 않은 경우 **기존 웹 사이트 선택** 대화 상자에서 **로그인** 단추를 클릭합니다. 로그인한 후에는 이 자습서의 첫 번째 부분에서 만든 웹 사이트를 선택합니다. **확인**을 클릭하여 계속합니다.  
    ![기존 웹 사이트 선택 대화 상자][기존 웹 사이트 선택 대화 상자]  
    Visual Studio는 게시 설정을 다운로드합니다.
4.  **웹 게시** 대화 상자에서 **게시**를 클릭합니다.  
    ![웹 게시 대화 상자][3]  
    Visual Studio 내 **Web Publish Activity** 창에 전반적인 게시 상태가 표시됩니다.  
    ![Web Publishing Activity][Web Publishing Activity]

만든 응용 프로그램이 이제 클라우드에서 실행되고 있습니다. 다음에 Visual Studio에서 응용 프로그램을 배포할 때는 변경된(또는 새) 파일만 배포됩니다.
 ![브라우저의 앱][브라우저의 앱]

> [WACOM.NOTE]
> 이미 구현된 웹 사이트에 게시하는 동안 오류가 발생하는 경우 새 파일을 추가하기 전에 위치를 지울 수 있습니다.
> 
> 응용 프로그램을 다시 게시하지만 **웹 게시** 대화 상자에서 **설정** 탭을 선택합니다. 그런 다음 구성을 **디버그**로 설정하고 **Remove additional files at destination** 옵션을 선택합니다. **게시**를 선택하여 응용 프로그램을 다시 배포합니다.
>  ![웹 게시 대화 상자][4]

## 응용 프로그램에 데이터베이스 추가

이제 Web Forms 응용 프로그램을 업데이트하여 연락처를 표시 및 업데이트하고 기본 데이터베이스에 데이터를 저장하는 기능을 추가하겠습니다. Web Forms 프로젝트를 만들 때 데이터베이스도 자동으로 생성되었습니다. 응용 프로그램은 Entity Framework를 사용하여 데이터베이스에 액세스하며 데이터베이스에서 데이터를 읽고 업데이트합니다.

### 데이터 모델 클래스 추가

먼저 코드로 간단한 데이터 모델을 만듭니다. 이 데이터 모델은 `Contacts`라는 클래스에 포함됩니다. `Contacts` 클래스 이름은 Web Forms 템플릿으로 생성된 Contact.aspx.cs 파일에 들어 있는 `Contact` 클래스와의 이름 충돌을 방지하기 위해 선택했습니다.

1.  **솔루션 탐색기**에서 *Models* 폴더를 마우스 오른쪽 단추로 클릭한 다음 **추가** -\> **클래스**를 선택합니다.
    ![클래스 선택][클래스 선택]
    **새 항목 추가** 대화 상자가 표시됩니다.

2.  이 새 클래스의 이름을 *Contacts.cs*로 지정합니다.
    ![새 항목 추가 대화 상자][새 항목 추가 대화 상자]
3.  기본 코드를 다음 코드로 바꿉니다.

    ``` prettyprint
    using System.ComponentModel.DataAnnotations;using System.Globalization;
    namespace ContactManager.Models{ public class Contacts { [ScaffoldColumn(false)][Key] public int ContactId { get; set; } public string Name { get; set; } public string Address { get; set; } public string City { get; set; } public string State { get; set; } public string Zip { get; set; } [DataType(DataType.EmailAddress)] public string Email { get; set; } }}
    ```

    </p>

**Contacts** 클래스는 각 연락처에 대해 저장할 데이터와 데이터베이스에 필요한 기본 키인 `ContactID`를 정의합니다. **Contacts** 클래스는 표시되는 연락처 데이터를 나타냅니다. Contacts 개체의 각 인스턴스는 관계형 데이터베이스 테이블 내 행에 해당하며 Contacts 클래스의 각 속성은 관계형 데이터베이스 테이블 내의 열에 매핑됩니다. 이 자습서의 뒷부분에서 데이터베이스에 포함된 연락처 데이터를 검토합니다.

### Web Forms 스캐폴딩

위에서 **Contacts** 모델을 만들었습니다. 이제 Web Forms 스캐폴더를 사용하여 이 데이터 작업을 할 때 사용되는 *List*, *Insert*, *Edit* 및 *Delete* 페이지를 생성할 수 있습니다. Web Forms 스캐폴더는 Entity Framework, Bootstrap 및 Dynamic Data를 사용합니다. 기본적으로 Web Forms 스캐폴더는 프로젝트에 확장으로 설치되어 Visual Studio 2013을 사용할 때 프로젝트 템플릿의 일부로 사용됩니다.

다음 단계를 수행하여 Web Forms 스캐폴더를 사용할 수 있습니다.

1.  Visual Studio의 메뉴 표시줄에서 **도구** -\> **확장 및 업데이트**를 선택합니다.
    **확장 및 업데이트** 대화 상자가 표시됩니다.
2.  대화 상자의 왼쪽 창에서 **온라인** -\> **Visual Studio 갤러리** -\> **도구** -\> **스캐폴딩**을 선택합니다.
3.  목록에 'Web Forms 스캐폴딩'이 나타나지 않으면 대화 상자의 오른쪽에 있는 검색 상자에 'Web Forms 스캐폴딩'을 입력합니다.
4.  Web Forms 스캐폴더를 아직 설치하지 않은 경우 **다운로드**를 선택하여 'Web Forms 스캐폴딩'을 설치합니다. 필요한 경우 Visual Studio를 다시 시작합니다. 요청 시 프로젝트 변경 내용을 저장합니다.  
    ![확장 및 업데이트 대화 상자][확장 및 업데이트 대화 상자]
5.  프로젝트를 빌드합니다(**Ctrl+Shift+B**).
    스캐폴딩 메커니즘을 사용하기 전에 프로젝트를 빌드해야 합니다.
6.  **솔루션 탐색기**에서 *project* 폴더를 마우스 오른쪽 단추로 클릭한 다음 **추가** -\> **New Scaffolded Item**를 선택합니다.
    **스캐폴드 추가** 대화 상자가 표시됩니다.
7.  왼쪽 창에서 **Web Forms**를 선택하고 가운데 페이지에서 **Web Forms Pages using Entity Framework**를 선택합니다. 그런 다음 **추가**를 클릭합니다.
    ![스캐폴드 추가 대화 상자][스캐폴드 추가 대화 상자]
    **Web Forms 페이지 추가** 대화 상자가 표시됩니다.

8.  **Web Forms 페이지 추가** 대화 상자에서 **모델 클래스**를 `Contact (ContactManager.Models)`로 설정합니다. **데이터 컨텍스트 클래스**를 `ApplicationDbContext (ContactManager.Models)`로 설정합니다. 그런 다음 **추가**를 클릭합니다.
    ![Web Forms 페이지 추가 대화 상자][Web Forms 페이지 추가 대화 상자]

Web Forms 스캐폴더에서 *Default.aspx*, *Delete.aspx*, *Edit.aspx* 및 *Insert.aspx* 페이지를 포함하는 새 폴더를 추가합니다. 또한 *EntityTemplates* 폴더 및 *FieldTemplates* 폴더를 포함하는 *DynamicData* 폴더도 만듭니다. `ApplicationDbContext`가 멤버 자격 데이터베이스 및 연락처 데이터 모두에 사용됩니다.

### 데이터 모델을 사용하도록 응용 프로그램 구성

다음 작업은 만든 데이터 모델에 따라 데이터베이스를 만들기 위해 Code First 마이그레이션(영문) 기능을 사용하도록 설정하는 것입니다. 또한 샘플 데이터 및 데이터 이니셜라이저도 추가합니다.

1.  **도구** 메뉴에서 **NuGet 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 선택합니다.  
    ![Web Forms 페이지 추가 대화 상자][5]
2.  패키지 관리자 콘솔 창에서 다음 명령을 입력합니다.

    ``` prettyprint
     enable-migrations
    ```

    enable-migrations 명령은 *Migrations* 폴더를 만들고 해당 폴더에 *Configuration.cs* 파일을 넣습니다. 이 파일을 편집하여 데이터베이스를 시드하고 데이터 마이그레이션을 구성할 수 있습니다.

3.  **패키지 관리자 콘솔** 창에서 다음 명령을 입력합니다.

    ``` prettyprint
     add-migration Initial
    ```

    `add-migration Initial` 명령은 *Migrations* 폴더에 데이터베이스를 만드는 <date_stamp>Initial이라는 파일을 생성합니다. 첫 번째 매개 변수(Initial)는 임의이며 파일 이름을 만드는 데 사용됩니다. **솔루션 탐색기**에서 새 클래스 파일을 볼 수 있습니다. `Initial` 클래스의 `Up` 메서드는 `Contact` 테이블을 만들고 이전 상태로 돌아가려는 경우 사용되는 `Down` 메서드는 테이블을 삭제합니다.

4.  *Migrations\\Configuration.cs* 파일을 엽니다.
5.  다음 네임스페이스를 추가합니다.

    ``` prettyprint
    using ContactManager.Models;
    ```

6.  `Seed` 메서드를 다음 코드로 바꿉니다.

    ``` prettyprint
    protected override void Seed(ContactManager.Models.ApplicationDbContext context){ context.Contacts.AddOrUpdate(p => p.Name, new Contacts { ContactId = 1, Name = "Ivan Irons", Address = "One Microsoft Way", City = "Redmond", State = "WA", Zip = "10999", Email = "ivani@wideworldimporters.com", }, new Contacts { ContactId = 2, Name = "Brent Scholl", Address = "5678 1st Ave W", City = "Redmond", State = "WA", Zip = "10999", Email = "brents@wideworldimporters.com", }, new Contacts { ContactId = 3, Name = "Terrell Bettis", Address = "9012 State St", City = "Redmond", State = "WA", Zip = "10999", Email = "terrellb@wideworldimporters.com", }, new Contacts { ContactId = 4, Name = "Jo Cooper", Address = "3456 Maple St", City = "Redmond", State = "WA", Zip = "10999", Email = "joc@wideworldimporters.com", }, new Contacts { ContactId = 5, Name = "Ines Burnett", Address = "7890 2nd Ave E", City = "Redmond", State = "WA", Zip = "10999", Email = "inesb@wideworldimporters.com", } );}
    ```

    이 코드는 연락처 정보를 사용하여 데이터베이스를 초기화(시드)합니다. 데이터베이스 시드에 대한 자세한 내용은 [EF(Entity Framework) DB 시드 및 디버그][EF(Entity Framework) DB 시드 및 디버그](영문)를 참조하세요.

7.  **패키지 관리자 콘솔**에서 다음 명령을 입력합니다.

    ``` prettyprint
    update-database
    ```

    `update-database`는 데이터베이스를 만드는 첫 번째 마이그레이션을 실행합니다. 기본적으로 데이터베이스는 SQL Server Express LocalDB 데이터베이스로 생성됩니다.
    ![패키지 관리자 콘솔][패키지 관리자 콘솔]

### 로컬로 응용 프로그램 실행 및 데이터 표시

이제 응용 프로그램을 실행하여 연락처를 보는 방법을 알아봅니다.

1.  먼저 프로젝트를 빌드합니다(**Ctrl+Shift+B**).
2.  **Ctrl+F5** 키를 눌러 응용 프로그램을 실행합니다.
    브라우저가 열리고 *Default.aspx* 페이지를 표시합니다.
3.  페이지의 맨 위에 있는 **Contact Demo** 링크를 선택하여 *Contact List* 페이지를 표시합니다.  
    ![Contacts List 페이지][Contacts List 페이지]

## 프로젝트에 SSL 사용

SSL(Secure Sockets Layer)은 웹 서버 및 웹 클라이언트가 암호화를 사용하여 더욱 안전하게 통신할 수 있도록 정의된 프로토콜입니다. SSL을 사용하지 않으면 네트워크에 실제로 액세스하여 패킷을 스니핑하는 누군가에게 클라이언트와 서버 간에 전송된 데이터가 노출됩니다. 또한 몇 가지 일반적인 인증 체계는 일반 HTTP를 통해서는 보호되지 않습니다. 특히, 기본 인증 및 폼 인증은 암호화되지 않은 자격 증명을 보냅니다. 보안을 위해서 인증 체계는 SSL을 사용해야 합니다.

1.  **솔루션 탐색기**에서, **ContactManager** 프로젝트를 클릭한 다음 **F4** 키를 눌러 **속성** 창을 표시합니다.
2.  **SSL 사용**을 `true`로 변경합니다.
3.  나중에 사용할 수 있도록 **SSL URL**을 복사합니다.
    이전에 SSL 웹 사이트를 만든 적이 없다면 SSL URL은 `https://localhost:44300/`이 됩니다(아래 참조).
    ![프로젝트 속성][프로젝트 속성]
4.  **솔루션 탐색기**에서 **Contact Manager** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
5.  왼쪽 탭에서 **웹**을 클릭합니다.
6.  앞서 저장한 **SSL URL**을 사용하도록 **프로젝트 URL**을 변경합니다.  
    ![프로젝트 웹 속성][프로젝트 웹 속성]
7.  **CTRL+S** 키를 눌러 페이지를 저장합니다.
8.  **Ctrl+F5** 키를 눌러 응용 프로그램을 실행합니다.
    Visual Studio에서 SSL 경고를 표시하지 않도록 하는 옵션을 표시합니다.
9.  **예**를 클릭하여 IIS Express SSL 인증서를 신뢰하고 계속합니다.
    ![IIS Express SSL 인증서 정보][IIS Express SSL 인증서 정보]
    보안 경고가 표시됩니다.

10. **예**를 클릭하여 localhost에 인증서를 설치합니다.
    ![보안 경고 대화 상자][보안 경고 대화 상자]
    브라우저 창이 표시됩니다.

로컬로 SSL을 사용하는 웹 응용 프로그램을 쉽게 테스트할 수 있습니다.

## OAuth 2.0 공급자 추가

ASP.NET Web Forms는 멤버 자격 및 인증을 위해 개선된 옵션을 제공합니다. 이러한 개선 사항에는 OAuth가 포함됩니다. OAuth는 웹, 모바일 및 데스크톱 응용 프로그램에서 간단한 표준 메서드로 보안 권한 부여를 허용하는 개방형 프로토콜입니다. ASP.NET MVC 인터넷 템플릿은 OAuth를 사용하여 Facebook, Twitter, Google 및 Microsoft를 인증 공급자로 표시합니다. 이 자습서에서는 인증 공급자로 Google만 사용하지만 다른 공급자를 사용하도록 코드를 쉽게 수정할 수 있습니다. 다른 공급자를 구현하는 단계도 이 자습서에 표시되는 단계와 매우 비슷합니다.

자습서에서는 인증 외에도 역할을 사용하여 권한 부여를 구현합니다. `canEdit` 역할에 추가한 사용자만 연락처 생성, 편집 또는 삭제하는 데이터를 변경할 수 있습니다.

다음 단계를 통해 Google 인증 공급자를 추가할 수 있습니다.

1.  *App\_Start\\Startup.Auth.cs* 파일을 엽니다.
2.  메서드가 다음과 같이 나타나도록 `app.UseGoogleAuthentication()` 메서드에서 주석 문자를 제거합니다.

    ``` prettyprint
     app.UseGoogleAuthentication(new GoogleOAuth2AuthenticationOptions() { ClientId = "", ClientSecret = "" });
    ```

3.  [Google Developers Console][Google Developers Console]로 이동합니다. Google 개발자 전자 메일 계정(gmail.com)으로 로그인해야 합니다. Google 계정이 없는 경우 **가입하기** 링크를 선택합니다.
    이제 **Google Developers Console**이 표시됩니다.
    ![Google Developers Console][6]

4.  **Create Project** 단추를 클릭하여 프로젝트 이름 및 ID(기본값 사용 가능)를 입력합니다. 그런 다음 **agreement checkbox**와 **Create** 단추를 차례로 클릭합니다.
    ![Google - 새 프로젝트][Google - 새 프로젝트]
    잠시 후에 새 프로젝트가 생성되고 브라우저에서 새 프로젝트 페이지를 표시합니다.
5.  왼쪽 탭에서 **APIs & auth**를 클릭한 다음 **Credentials**를 클릭합니다.
6.  **OAuth** 아래에서 **Create New Client ID**를 클릭합니다.
    **Create Client ID** 대화 상자가 표시됩니다.
    ![Google - Create Client ID][Google - Create Client ID]
7.  **Create Client ID** 대화 상자에서 응용 프로그램 유형에 기본값인 **Web application**을 그대로 사용합니다.
8.  **Authorized JavaScript Origins**를 이 자습서의 앞부분에서 사용한 SSL URL로 설정합니다(다른 SSL 프로젝트를 만들지 않았다면 **https://localhost:44300/**).  
    이 URL이 응용 프로그램의 원점입니다. 이 샘플의 경우 localhost 테스트 URL만 입력합니다. 그러나 실제로는 localhost 및 프로덕션을 담당할 URL을 여러 개 입력할 수 있습니다.

9.  **Authorized Redirect URI**를 다음으로 설정합니다.

    ``` prettyprint
    https://localhost:44300/signin-google
    ```

    이 값은 ASP.NET OAuth 사용자가 Google OAuth 서버와 통신하는 데 사용하는 URI입니다. 위에서 사용한 SSL URL을 기억해 두세요(다른 SSL 프로젝트를 만들지 않았다면 **https://localhost:44300/**).

10. **Create Client ID** 단추를 클릭합니다.
11. Visual Studio에서 **AppId** 및 **App Secret**을 복사하여 *Startup.Auth.cs* 페이지의 `UseGoogleAuthentication` 메서드에 붙여넣어 이 메서드를 업데이트합니다. 아래 표시된 **AppId** 및 **App Secret** 값은 샘플이며 작동하지 않습니다.
	<pre class="prettyprint">  
	using System;
	using Microsoft.AspNet.Identity;
	using Microsoft.AspNet.Identity.EntityFramework;
	using Microsoft.AspNet.Identity.Owin;
	using Microsoft.Owin;
	using Microsoft.Owin.Security.Cookies;
	using Microsoft.Owin.Security.DataProtection;
	using Microsoft.Owin.Security.Google;
	using Owin;
	using ContactManager.Models;
	
	namespace ContactManager
	{
	    public partial class Startup {
	
	        // For more information on configuring authentication, please visit http://go.microsoft.com/fwlink/?LinkId=301883
	        public void ConfigureAuth(IAppBuilder app)
	        {
	            // Configure the db context and user manager to use a single instance per request
	            app.CreatePerOwinContext(ApplicationDbContext.Create);
	            app.CreatePerOwinContext<ApplicationUserManager>(ApplicationUserManager.Create);
	
	            // Enable the application to use a cookie to store information for the signed in user
	            // and to use a cookie to temporarily store information about a user logging in with a third party login provider
	            // Configure the sign in cookie
	            app.UseCookieAuthentication(new CookieAuthenticationOptions
	            {
	                AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
	                LoginPath = new PathString("/Account/Login"),
	                Provider = new CookieAuthenticationProvider
	                {
	                    OnValidateIdentity = SecurityStampValidator.OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
	                        validateInterval: TimeSpan.FromMinutes(20),
	                        regenerateIdentity: (manager, user) => user.GenerateUserIdentityAsync(manager))
	                }
	            });
	            // Use a cookie to temporarily store information about a user logging in with a third party login provider
	            app.UseExternalSignInCookie(DefaultAuthenticationTypes.ExternalCookie);
	
	            // Uncomment the following lines to enable logging in with third party login providers
	            //app.UseMicrosoftAccountAuthentication(
	            //    clientId: "",
	            //    clientSecret: "");
	
	            //app.UseTwitterAuthentication(
	            //   consumerKey: "",
	            //   consumerSecret: "");
	
	            //app.UseFacebookAuthentication(
	            //   appId: "",
	            //   appSecret: "");
	
	            app.UseGoogleAuthentication(new GoogleOAuth2AuthenticationOptions()
	            {
	                ClientId = "<mark>000000000000.apps.googleusercontent.com</mark>",
	                ClientSecret = "<mark>00000000000</mark>"
	            });
	        }
	    }
	}
	</pre>  
12. **CTRL+F5** 키를 눌러 응용 프로그램을 빌드 및 실행합니다. **로그인** 링크를 클릭합니다.
13. **Use another service to log in**에서 **Google** 단추를 클릭합니다.  
    ![로그인][로그인]
14. 자격 증명을 입력해야 하는 경우 자격 증명을 입력할 Google 사이트로 리디렉션됩니다.  
    ![Google - 로그인][Google - 로그인]
15. 자격 증명을 입력하면 방금 만든 웹 응용 프로그램에 사용 권한을 부여하는 메시지가 표시됩니다.  
    ![프로젝트 기본 서비스 계정][프로젝트 기본 서비스 계정]
16. **Accept**를 클릭합니다. 이제 Google 계정을 등록할 수 있는 **ContactManager** 응용 프로그램의 **Register** 페이지로 다시 리디렉션됩니다.  
    ![Google 계정 등록][Google 계정 등록]
17. Gmail 계정에 사용되는 로컬 전자 메일 등록 이름을 변경할 수 있지만 일반적으로 기본 전자 메일 별칭(즉, 인증에 사용하는 별칭)을 그대로 사용합니다. **Log in**을 클릭합니다.

## 멤버 자격 API를 사용하여 액세스 제한

ASP.NET ID는 ASP.NET 웹 응용 프로그램을 빌드할 때 인증에 사용되는 멤버 자격 시스템입니다. 이 시스템을 사용하면 사용자 관련 프로필 데이터를 응용 프로그램 데이터와 쉽게 통합할 수 있습니다. 또한 ASP.NET ID를 통해 응용 프로그램의 사용자 프로필에 대한 지속성 모델을 선택할 수 있습니다. *NoSQL* 데이터 저장소(예: Azure 저장소 테이블)를 비롯하여 SQL Server 데이터베이스나 다른 데이터 저장소에 데이터를 저장할 수 있습니다.

기본 ASP.NET Web Forms 템플릿을 사용하여 응용 프로그램이 실행될 때 즉시 사용할 수 있는 멤버 자격 기능을 기본 제공했습니다. 이제 ASP.NET ID를 사용하여 관리자 역할을 추가하고 해당 역할에 사용자를 할당하겠습니다. 그런 다음 관리자 폴더와 해당 폴더의 페이지(연락처 정보를 수정하는 데 사용되는 페이지)에 대한 액세스 권한을 제한하는 방법에 대해 알아보겠습니다.

### 관리자 추가

ASP.NET ID를 사용하여 관리자 역할을 추가하고 해당 역할에 사용자를 할당할 수 있습니다.

1.  **솔루션 편집기**에서 *Migrations* 폴더의 *Configuration.cs* 파일을 엽니다.
2.  `ContactManger.Migrations` 네임스페이스에 다음 `using` 문을 추가합니다.

    ``` prettyprint
    using Microsoft.AspNet.Identity;using Microsoft.AspNet.Identity.EntityFramework;
    ```

3.  `Seed` 메서드 뒤의 `Configuration` 클래스에 다음 `AddUserAndRole` 메서드를 추가합니다.

    ``` prettyprint
     public void AddUserAndRole(ContactManager.Models.ApplicationDbContext context) { IdentityResult IdRoleResult; IdentityResult IdUserResult;
        var roleStore = new RoleStore&lt;IdentityRole&gt;(context);
        var roleMgr = new RoleManager&lt;IdentityRole&gt;(roleStore);

        if (!roleMgr.RoleExists(&quot;canEdit&quot;))
        {
            IdRoleResult = roleMgr.Create(new IdentityRole { Name = &quot;canEdit&quot; });
        }

        //var userStore = new UserStore&lt;ApplicationUser&gt;(context);
        //var userMgr = new UserManager&lt;ApplicationUser&gt;(userStore);
        var userMgr = new UserManager&lt;ApplicationUser&gt;(new UserStore&lt;ApplicationUser&gt;(context));

        var appUser = new ApplicationUser
        {
            UserName = &quot;canEditUser@wideworldimporters.com&quot;,
            Email = &quot;canEditUser@wideworldimporters.com&quot;
        };
        IdUserResult = userMgr.Create(appUser, &quot;Pa$$word1&quot;);

        if (!userMgr.IsInRole(userMgr.FindByEmail(&quot;canEditUser@wideworldimporters.com&quot;).Id, &quot;canEdit&quot;))
        {
          //  IdUserResult = userMgr.AddToRole(appUser.Id, &quot;canEdit&quot;);
            IdUserResult = userMgr.AddToRole(userMgr.FindByEmail(&quot;canEditUser@wideworldimporters.com&quot;).Id, &quot;canEdit&quot;);
        }
    }
    ```

    </pre>
4.  `Seed` 메서드 시작을 포함하여 `AddUserAndRole` 메서드에서 호출을 추가합니다. `Seed` 메서드의 시작 부분만 표시됩니다.

    ``` prettyprint
     protected override void Seed(ContactManager.Models.ApplicationDbContext context) { AddUserAndRole(context);
    ```

5.  모든 변경 내용을 저장한 후 **패키지 관리자 콘솔**에서 다음 명령을 실행합니다.

    ``` prettyprint
    Update-Database
    ```

    이 코드는 새로운 `canEdit` 역할을 만들고 <canEditUser@wideworldimporters.com> 메일 계정이 있는 새로운 로컬 사용자를 만듭니다. 그런 다음 이 코드는 <canEditUser@wideworldimporters.com>을 `canEdit` 역할에 추가합니다. 자세한 내용은 [ASP.NET ID][ASP.NET ID](영문) 리소스 페이지를 참조하세요.

    </p>

### 관리 폴더에 대한 액세스 제한

**ContactManager** 샘플 응용 프로그램을 통해 익명의 사용자와 로그인한 사용자는 연락처를 볼 수 있습니다. 그러나 이 섹션을 완료한 후에는 "canEdit" 역할에 할당되어 있는 로그인한 사용자만 연락처를 수정할 수 있습니다.

이제 "canEdit" 역할에 할당된 사용자만 액세스할 수 있는 *Admin* 폴더를 만들겠습니다.

1.  **솔루션 편집기**에서, *Contacts* 폴더에 하위 폴더를 추가하고 새 폴더의 이름을 *Admin*으로 지정합니다.
2.  *Contacts* 폴더에서 *Contacts/Admin* 폴더로 다음 파일을 이동합니다.
	- *Delete.aspx* 및\* Delete.aspx.cs\*
	- *Edit.aspx* 및\* Edit.aspx.cs\*
	- *Insert.aspx* 및\* Insert.aspx.cs\*

3.  *Insert.aspx*, *Edit.aspx* 및 *Delete.aspx*에 연결되는 페이지 참조 앞에 "Admin/"을 추가하여 *Contacts/Default.aspx*에서 링크 참조를 업데이트합니다.

    ``` prettyprint
    <%@ Page Title="ContactsList" Language="C#" MasterPageFile="~/Site.Master" CodeBehind="Default.aspx.cs" Inherits="ContactManager.Contacts.Default" ViewStateMode="Disabled" %><%@ Register TagPrefix="FriendlyUrls" Namespace="Microsoft.AspNet.FriendlyUrls" %>
    <asp:Content runat="server" ContentPlaceHolderID="MainContent"> <h2>Contacts List</h2> <p> <asp:HyperLink runat="server" NavigateUrl="Admin/Insert.aspx" Text="Create new" /> </p> <div> <asp:ListView runat="server" DataKeyNames="ContactId" ItemType="ContactManager.Models.Contacts" AutoGenerateColumns="false" AllowPaging="true" AllowSorting="true" SelectMethod="GetData"> <EmptyDataTemplate> There are no entries found for Contacts </EmptyDataTemplate> <LayoutTemplate> <table class="table"> <thead> <tr> <th>Name</th> <th>Address</th> <th>City</th> <th>State</th> <th>Zip</th> <th>Email</th> <th> </th> </tr> </thead> <tbody> <tr runat="server" id="itemPlaceholder" /> </tbody> </table> </LayoutTemplate> <ItemTemplate> <tr> <td> <asp:DynamicControl runat="server" DataField="Name" ID="Name" Mode="ReadOnly" /> </td> <td> <asp:DynamicControl runat="server" DataField="Address" ID="Address" Mode="ReadOnly" /> </td> <td> <asp:DynamicControl runat="server" DataField="City" ID="City" Mode="ReadOnly" /> </td> <td> <asp:DynamicControl runat="server" DataField="State" ID="State" Mode="ReadOnly" /> </td> <td> <asp:DynamicControl runat="server" DataField="Zip" ID="Zip" Mode="ReadOnly" /> </td> <td> <asp:DynamicControl runat="server" DataField="Email" ID="Email" Mode="ReadOnly" /> </td> <td> <a href="Admin/Edit.aspx?ContactId=<%#: Item.ContactId%>">Edit</a> | <a href="Admin/Delete.aspx?ContactId=<%#: Item.ContactId%>">Delete</a> </td> </tr> </ItemTemplate> </asp:ListView> </div></asp:Content>
    ```

4.  다음 3개 파일에서 `Response.Redirect("Default.aspx")` 코드의 `Response.Redirect("~/Contacts/Default.aspx")`에 대한 6개의 참조를 업데이트합니다.

    -   *Delete.aspx.cs*
    -   *Edit.aspx.cs*
    -   *Insert.aspx.cs*

    이제 연락처 데이터를 표시하고 업데이트할 때 모든 링크가 올바르게 작동합니다.

5.  *Admin* 폴더에 대한 액세스 권한을 제한하려면 **솔루션 탐색기**에서 *Admin* 폴더를 마우스 오른쪽 단추로 클릭하고 **새 항목 추가**를 선택합니다.
6.  Visual C# 웹 템플릿 목록의 가운데 목록에서 **웹 구성 파일**을 선택하고 *Web.config*의 기본 이름을 그대로 사용한 다음 **추가**를 선택합니다.
7.  *Web.config* 파일의 기존 XML 콘텐츠를 다음으로 바꿉니다.

    ``` prettyprint
    <?xml version="1.0"?><configuration> <system.web> <authorization> <allow roles="canEdit"/> <deny users="*"/> </authorization> </system.web></configuration>
    ```

8.  *Web.config* 파일을 저장합니다.
    *Web.config* 파일은 "canEdit" 역할에 할당된 사용자만 *Admin* 폴더에 포함된 페이지에 액세스할 수 있도록 지정합니다.

"canEdit" 역할에 속하지 않은 사용자가 데이터를 수정하려고 하면 *로그인* 페이지로 리디렉션됩니다.

## Azure에 데이터베이스와 함께 응용 프로그램 배포

이제 웹 응용 프로그램을 완료했으므로 Azure에 게시할 수 있습니다.

### 응용 프로그램 게시

1.  Visual Studio에서 프로젝트를 빌드합니다(**Ctrl+Shift+B**).
2.  **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.  
    ![게시 메뉴 옵션][게시 메뉴 옵션]  
    **웹 게시** 대화 상자가 표시됩니다.  
    ![웹 게시 대화 상자][7]
3.  **프로필** 탭에서 **Azure 웹 사이트**를 아직 선택하지 않은 경우 게시 대상으로 선택합니다.  
    ![웹 게시 대화 상자][8]
4.  아직 로그인하지 않은 경우 **로그인**을 클릭합니다.
5.  **기존 웹 사이트** 드롭다운 상자에서 이 자습서의 앞부분에서 만든 기존 웹 사이트를 선택하고 **확인** 단추를 클릭합니다.  
    ![기존 웹 사이트 선택 대화 상자][9]
    프로필을 변경하라는 메시지가 표시되면 **예**를 선택합니다.
6.  **설정** 탭을 클릭합니다.  
    ![기존 웹 사이트 선택 대화 상자][10]
7.  **구성** 드롭다운 상자를 **디버그**로 설정합니다.
8.  **ApplicationDbContext** 옆에 있는 **아래쪽 화살표** 아이콘을 클릭하고 **ContactDB**로 설정합니다.
9.  **Execute Code First Migrations** 확인란을 선택합니다.

    > [WACOM.NOTE]
    > 이 예제에서는 응용 프로그램을 처음 게시할 때에만 이 확인란을 선택합니다. 이런 방식으로 *Configuration.cs* 파일의 *Seed* 메서드는 한 번만 호출됩니다.

10. 그런 다음 **게시**를 클릭합니다.
    응용 프로그램이 Azure에 게시됩니다.

    > [WACOM.NOTE]
    > 게시 프로필을 만든 후 Visual Studio를 닫았다가 다시 연 경우 드롭다운 목록에 연결 문자열이 표시되지 않을 수도 있습니다. 이 경우 앞에서 만든 게시 프로필을 편집하는 대신 이전과 동일한 방식으로 새 게시 프로필을 만든 다음 **설정** 탭에서 이러한 단계를 따르세요.

### Azure에서 응용 프로그램 검토

1.  브라우저에서 **Contact Demo** 링크를 클릭합니다.
    연락처 목록이 표시됩니다.
    ![브라우저에 나열된 연락처][브라우저에 나열된 연락처]

2.  **Contacts List** 페이지에서 **Create new**를 선택합니다.
    ![브라우저에 나열된 연락처][11]
    연락처를 수정할 수 있는 계정으로 로그인하지 않았으므로 **Login** 페이지로 리디렉션됩니다.
3.  아래 전자 메일 및 암호를 입력한 후 **Log in** 단추를 클릭합니다.
    **전자 메일**: `canEditUser@wideworldimporters.com`
    **암호**: `Pa$$word1`
    ![로그인 페이지][로그인 페이지]

4.  각 필드에 대해 새 데이터를 입력한 다음 **Insert** 단추를 누릅니다.
    ![새 연락처 추가 페이지][새 연락처 추가 페이지]
    *EditContactList.aspx* 페이지가 새로운 레코드와 함께 표시됩니다.
    ![Add New Contact Page][Add New Contact Page]

5.  **Log off** 링크를 선택합니다.

### 응용 프로그램 중지

다른 사람이 샘플 응용 프로그램을 등록하고 사용하지 못하도록 웹 사이트를 중지하겠습니다.

1.  Visual Studio의 **보기 메뉴**에서 **서버 탐색기**를 선택합니다.
2.  **서버 탐색기**에서 **웹 사이트**로 이동합니다.
3.  각 웹 사이트 인스턴스를 마우스 오른쪽 단추로 클릭하고 **웹 사이트 중지**를 선택합니다.
    ![웹 사이트 중지 메뉴 항목][웹 사이트 중지 메뉴 항목]

    또는 Microsoft Azure 관리 포털에서 웹 사이트를 선택하고 페이지 맨 아래에 있는 **중지** 아이콘을 클릭합니다.
    ![새 연락처 추가 페이지][12]

## 데이터베이스 검토

데이터베이스를 직접 보고 수정하는 방법을 아는 것이 중요합니다. 데이터베이스에 대한 직접 작업 방법을 알면 데이터베이스의 데이터를 확인하고 각 테이블에 데이터를 저장하는 방법도 이해할 수 있습니다.

### SQL Azure DB 검사

1.  Visual Studio에서 **서버 탐색기**를 열고 **ContactDB**로 이동합니다.
2.  **ContactDB**를 마우스 오른쪽 단추로 클릭하고 **SQL Server 객체 탐색기에서 열기**를 선택합니다.  
    ![SQL Server 객체 탐색기에서 열기 메뉴 항목][SQL Server 객체 탐색기에서 열기 메뉴 항목]
3.  **방화벽 규칙 추가** 대화 상자가 표시되면 **방화벽 규칙 추가**를 선택합니다.

    > [WACOM.NOTE]
    >  **SQL 데이터베이스**를 확장할 수 없고 Visual Studio에서 **ContactDB**를 볼 수 없는 경우 아래 지침에 따라 방화벽 포트나 포트 범위를 열어야 합니다. 그렇게 하려면 [MVC 자습서][Azure 웹 사이트에 멤버 자격, OAuth 및 SQL 데이터베이스를 포함한 보안 ASP.NET MVC 5 앱 배포](영문) 끝에 있는 **Azure 방화벽 규칙 설정**의 지침을 따르세요. 또는 로컬로 응용 프로그램을 빌드 및 실행하고, 이 응용 프로그램에 데이터를 추가하여 로컬 데이터베이스의 데이터를 검토할 수 있습니다(Visual Studio에서 **CTRL+F5** 키를 누름).

4.  **서버에 연결** 대화 상자가 표시되면 이 자습서의 앞부분에서 만든 **암호**를 입력하고 **연결** 단추를 누릅니다.

    > [WACOM.NOTE]
    > 암호가 기억나지 않는 경우 로컬 프로젝트 파일에서 찾을 수 있습니다. **솔루션 탐색기**에서 *Properties* 폴더와 *PublishProfiles* 폴더를 차례로 확장합니다. *contactmanager.pubxml* 파일(사용자의 파일 이름은 다를 수 있음)을 엽니다. 파일에서 게시 암호를 검색합니다.

5.  **contactDB** 데이터베이스를 확장한 다음 **Tables**를 확장합니다.
6.  **dbo.AspNetUsers** 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기**를 선택합니다.  
    ![데이터 보기 메뉴 항목][데이터 보기 메뉴 항목]  
    <canEditUser@contoso.com> 사용자
    와 연결된 데이터를 볼 수 있습니다.  
    ![ContactManager 창][ContactManager 창]

### 데이터베이스를 편집하여 관리자 역할에 사용자 추가

자습서 앞부분에서는 코드를 사용하여 canEdit 역할에 사용자를 추가했습니다. 대체 방법은 멤버 자격 테이블의 데이터를 직접 조작하는 것입니다. 다음 단계에서는 이 대체 방법을 사용하여 역할에 사용자를 추가하는 방법을 보여 줍니다.

1.  **SQL Server 객체 탐색기**에서 **dbo.AspNetUserRoles**를 마우스 오른쪽 단추로 클릭하고 **데이터 보기**를 선택합니다.
    ![AspNetUserRoles 데이터][AspNetUserRoles 데이터]
2.  *RoleId*를 복사하여 빈(새) 행에 붙여 넣습니다.  
    ![AspNetUserRoles 데이터][13]
3.  **dbo.AspNetUsers** 테이블에서 역할에 넣을 사용자를 찾고 사용자의 *Id*를 복사합니다.
4.  복사한 *Id*를 **AspNetUserRoles** 테이블의 새 행에 있는 **UserId** 필드로 붙여넣습니다.

> [WACOM.NOTE]
> 
> 사용자 및 역할 관리를 도와줄 도구를 개발 중입니다.

## 다음 단계

ASP.NET Web Forms에 대해 자세한 내용은 ASP.NET 웹 사이트의 [ASP.NET Web Forms 알아보기][ASP.NET Web Forms 알아보기](영문) 및 [Microsoft Azure 자습서 및 가이드][Microsoft Azure 자습서 및 가이드]를 참조하세요.

이 자습서는 Rick Anderson(Twitter [@RickAndMSFT][@RickAndMSFT])이 Tom Dykstra 및 Barry Dorrans(Twitter [@blowdart][@blowdart])의 도움을 받아 작성한 MVC 자습서인 [Azure 웹 사이트에 멤버 자격, OAuth 및 SQL 데이터베이스를 포함한 보안 ASP.NET MVC 5 앱 배포][Azure 웹 사이트에 멤버 자격, OAuth 및 SQL 데이터베이스를 포함한 보안 ASP.NET MVC 5 앱 배포](영문) 내용을 바탕으로 합니다.

자습서 자체뿐 아니라 설명된 제품과 관련해서 마음에 드는 사항이나 바라는 개선 사항에 대한 의견을 남겨주세요. 사용자 의견은 개선 사항의 우선 순위를 지정하는 데 도움이 됩니다. [코드 사용 방법 보기][코드 사용 방법 보기]에서 새 항목을 요청하고 투표할 수도 있습니다.

  [Azure 웹 사이트에 멤버 자격, OAuth 및 SQL 데이터베이스를 포함한 보안 ASP.NET MVC 5 앱 배포]: http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
  [연락처 - 편집 페이지]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms00.png
  [MSDN 구독자 혜택을 활성화]: /ko-kr/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [무료 평가판을 등록]: /ko-kr/pricing/free-trial/?WT.mc_id=A261C142F
  [개발 환경 설정]: #set-up-the-development-environment
  [Azure 환경 설정]: #Set-up-the-Azure-environment
  [ASP.NET Web Forms 응용 프로그램 만들기]: #Create-an-ASP.NET-Web-Forms-Application
  [응용 프로그램에 데이터베이스 추가]: #Add-a-Database-to-the-Application
  [프로젝트에 SSL 사용]: #Enable-SSL-for-the-Project
  [OAuth 2.0 공급자 추가]: #Add-an-OAuth-2.0-Provider
  [멤버 자격 API를 사용하여 액세스 제한]: #Use-the-Membership-API-to-Restrict-Access
  [Azure에 데이터베이스와 함께 응용 프로그램 배포]: #Deploy-the-Application-with-the-Database-to-Azure
  [데이터베이스 검토]: #Review-the-Database
  [다음 단계]: #Next-Steps
  [Visual Studio 2013]: http://go.microsoft.com/fwlink/?LinkId=306566
  [Azure SDK for Visual Studio 2013]: http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409
  [웹 플랫폼 설치 관리자]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-01.png
  [Visual Studio 2013 업데이트 2]: http://www.microsoft.com/download/details.aspx?id=42666
  [Azure 관리 포털]: https://manage.windowsazure.com/
  [1]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-02.png
  [사용자 지정 만들기]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-03.png
  [연락처 - 새 웹 사이트 만들기]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-04.png
  [데이터베이스 설정]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-05.png
  [파일 메뉴 - 새 프로젝트]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms01.png
  [새 프로젝트 대화 상자]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms02.png
  [새 ASP.NET 프로젝트 대화 상자]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms03.png
  [2]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms04.png
  [게시 선택]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms05.png
  [웹 게시 대화 상자]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms06.png
  [기존 웹 사이트 선택 대화 상자]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms07.png
  [3]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms08.png
  [Web Publishing Activity]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms09.png
  [브라우저의 앱]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms10.png
  [4]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms11.png
  [클래스 선택]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms12.png
  [새 항목 추가 대화 상자]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13.png
  [확장 및 업데이트 대화 상자]: ./media/web-sites-dotnet-web-forms-secure/ExtensionsAndUpdatesDB.png
  [스캐폴드 추가 대화 상자]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13a.png
  [Web Forms 페이지 추가 대화 상자]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13b.png
  [5]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13c.png
  [EF(Entity Framework) DB 시드 및 디버그]: http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx
  [패키지 관리자 콘솔]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13d.png
  [Contacts List 페이지]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms17.png
  [프로젝트 속성]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms18.png
  [프로젝트 웹 속성]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms19.png
  [IIS Express SSL 인증서 정보]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms20.png
  [보안 경고 대화 상자]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21.png
  [Google Developers Console]: https://console.developers.google.com/
  [6]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21a.png
  [Google - 새 프로젝트]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21b.png
  [Google - Create Client ID]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21c.png
  [로그인]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21d.png
  [Google - 로그인]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21e.png
  [프로젝트 기본 서비스 계정]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21f.png
  [Google 계정 등록]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21g.png
  [ASP.NET ID]: http://www.asp.net/identity
  [게시 메뉴 옵션]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms22.png
  [7]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms22a.png
  [8]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms23.png
  [9]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms25.png
  [10]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms26.png
  [브라우저에 나열된 연락처]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms27.png
  [11]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms29.png
  [로그인 페이지]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms28.png
  [새 연락처 추가 페이지]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms30.png
  [Add New Contact Page]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms31.png
  [웹 사이트 중지 메뉴 항목]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms26a.png
  [12]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms26b.png
  [SQL Server 객체 탐색기에서 열기 메뉴 항목]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms32.png
  [데이터 보기 메뉴 항목]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms34.png
  [ContactManager 창]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms35.png
  [AspNetUserRoles 데이터]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms36.png
  [13]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms37.png
  [ASP.NET Web Forms 알아보기]: http://www.asp.net/web-forms
  [Microsoft Azure 자습서 및 가이드]: http://azure.microsoft.com/ko-kr/documentation/services/web-sites/#net
  [@RickAndMSFT]: https://twitter.com/RickAndMSFT
  [@blowdart]: https://twitter.com/blowdart
  [코드 사용 방법 보기]: http://aspnet.uservoice.com/forums/228522-show-me-how-with-code
