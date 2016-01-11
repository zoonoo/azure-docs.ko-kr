<properties 
   pageTitle="Visual Studio에서 연결된 서비스를 사용하여 Azure Active Directory 추가 | Microsoft Azure"
   description="Visual Studio 연결된 서비스 추가 대화 상자를 사용하여 Azure Active Directory 추가"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="visual-studio-online"
   ms.date="12/16/2015"
   ms.author="tarcher" />

# Visual Studio에서 연결된 서비스를 사용하여 Azure Active Directory 추가 

##개요
Azure Active Directory(Azure AD)를 사용하여 ASP.NET MVC 웹 응용 프로그램 또는 웹 API 서비스에서 AD 인증을 위한 SSO(Single Sign-on)을 지원할 수 있습니다. Azure AD 인증을 사용하면 사용자가 Azure AD의 자신의 계정을 사용하여 웹 응용 프로그램에 연결할 수 있습니다. Azure AD 인증의 장점으로 웹 응용 프로그램에서 API를 표시하는 경우 향상된 데이터 보안을 들 수 있습니다. Azure AD를 사용하면, 자체 계정 및 사용자 관리를 사용하는 별도 인증 시스템을 관리할 필요가 없습니다.

## 지원되는 프로젝트 형식

연결된 서비스 대화 상자에서 다음 프로젝트 형식으로 Azure AD에 연결할 수 있습니다.

- ASP.NET MVC 프로젝트

- ASP.NET 웹 API 프로젝트


### 연결된 서비스 대화 상자를 사용하여 Azure AD에 연결

1. Azure 계정이 있어야 합니다. Azure 계정이 없으면 [무료 평가판](http://go.microsoft.com/fwlink/?LinkId=518146)에 등록할 수 있습니다.

1. Visual Studio의 프로젝트에서 **참조** 바로 가기 메뉴를 열고 **연결된 서비스 추가**를 선택합니다.
1. **Azure AD 인증**을 선택한 다음 **구성**을 선택합니다.

    ![Azure AD 인증 추가 선택](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. **Azure AD 인증 구성**의 첫 페이지에서 **Azure AD를 사용하여 Single Sign-on 구성**을 확인합니다.

    프로젝트가 다른 인증 구성과 함께 구성된 경우, 마법사는 이전 구성을 사용하지 않다고 설정한 경고를 표시합니다.

    ![마법사에서 Azure AD 구성](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1.  두 번째 페이지의 **도메인** 드롭다운 목록에서 도메인을 선택합니다. 도메인 목록에는 계정 설정 대화 상자에 나열된 계정으로 액세스할 수 있는 모든 도메인을 포함합니다. 대신, 도메인을 찾을 수 없는 경우 mydomain.onmicrosoft.com 등과 같은 도메인 이름을 입력할 수 있습니다. 새 Azure AD 앱을 만들거나 기존 Azure AD 앱의 설정을 사용하는 옵션을 선택할 수 있습니다.

    ![마법사에서 Azure AD 구성](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)


1. 마법사의 세 번째 페이지에서 **디렉터리 데이터 읽기**가 선택되어야 합니다. 마법사는 **클라이언트 암호**를 채웁니다.

    ![마법사에서 Azure AD 구성](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. **마침** 단추를 선택합니다. 대화 상자는 Azure AD 인증을 위한 프로젝트를 사용 가능하도록 필요한 구성 코드 및 참조를 추가합니다. Azure 포털에서 AD 도메인을 확인할 수 있습니다.

1. 다음 단계에 대한 아이디어를 위해 브라우저에 표시되는 시작 페이지 및 프로젝트를 수정한 방법을 보기 위해 변경된 내용 페이지를 검토합니다. 모든 항목이 제대로 작동하는지 확인하려면 수정된 구성 파일 중 하나를 열고 변경된 내용에 언급된 설정이 있는지 확인합니다. 예를 들어, ASP.NET MVC 프로젝트의 기본 web.config에 다음 설정이 추가됩니다.

        <appSettings> 
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:AADInstance" value="https://login.windows.net/" />
            <add key="ida:Domain" value="Your selected domain" />
            <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
            <add key="ida:PostLogoutRedirectUri" value="The default redirect URI from the project" />
        </appSettings>

## 프로젝트를 수정하는 방법

마법사를 실행하면 Visual Studio는 Azure AD 및 연관된 참조를 프로젝트에 추가합니다. 또한 프로젝트의 구성 파일 및 코드 파일이 수정되어 Azure AD용 지원을 추가합니다. Visual Studio가 작성한 특정 수정 내용은 프로젝트 형식에 따라 달라집니다. ASP.NET MVC 프로젝트를 수정하는 방법에 대한 자세한 정보는 [변경된 내용 – MVC 프로젝트](http://go.microsoft.com/fwlink/p/?LinkID=513809)를 참조하세요. 웹 API 프로젝트는 [변경된 내용 - 웹 API 프로젝트](http://go.microsoft.com/fwlink/p/?LinkId=513810)를 참조하세요.

##다음 단계

질문하고 도움 받기.

 - [MSDN 포럼: Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)

 - [Azure AD 설명서](http://azure.microsoft.com/documentation/services/active-directory/)

 - [블로그 게시물: Azure AD에 대한 소개](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)

<!---HONumber=AcomDC_1223_2015-->