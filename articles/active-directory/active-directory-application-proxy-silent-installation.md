<properties
	pageTitle="Azure AD 응용 프로그램 프록시 커넥터를 자동으로 설치하는 방법 | Microsoft Azure"
	description="Azure AD 응용 프로그램 프록시 커넥터를 자동으로 설치하여 온-프레미스 앱에 대한 보안된 원격 액세스를 제공하는 방법에 대해 설명합니다."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="steven.powell"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/19/2015"
	ms.author="rkarlin"/>

# Azure AD 응용 프로그램 프록시 커넥터를 자동으로 설치하는 방법

사용자 인터페이스를 사용하도록 설정되지 않은 Windows Server 또는 여러 Windows 서버에 설치 스크립트를 보낼 수 있습니다. 이 항목에서는 무인 설치를 사용하도록 설정하고 Azure AD 응용 프로그램 프록시 커넥터를 설치 및 등록하는 Windows PowerShell 스크립트를 만드는 방법에 대해 설명합니다.

## 액세스 사용
응용 프로그램 프록시는 네트워크 내부에서 커넥터라고 불리는 간단한 Windows Server 서비스를 설치하여 사용합니다. 응용 프로그램 프록시 커넥터가 작동하려면 전역 관리자 및 암호를 사용하여 Azure AD 디렉터리에 등록되어야 합니다. 일반적으로 이러한 등록 정보는 커넥터 설치 중에 팝업 대화 상자에서 입력됩니다. 또는 Windows PowerShell을 사용하여 자격 증명 개체를 만들어 등록 정보를 입력하거나, 고유한 토큰을 만들고 이 토큰을 사용하여 등록 정보를 입력할 수 있습니다.

## 1단계: 등록 없이 커넥터 설치


다음과 같이 커넥터를 등록하지 않고 커넥터 MSI를 설치합니다.


1. 명령 프롬프트를 엽니다.
2. 다음 명령을 실행합니다. 여기서 /q는 무인 설치를 의미하며 설치 시 최종 사용자 사용권 계약에 동의할지 묻는 메시지가 표시되지 않습니다.

        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## 2단계: Azure Active Directory에 커넥터 등록
다음 방법 중 하나를 사용하여 이 작업을 수행할 수 있습니다.


- Windows PowerShell 자격 증명 개체를 사용하여 커넥터 등록
- 오프라인으로 만든 토큰을 사용하여 커넥터 등록

### Windows PowerShell 자격 증명 개체를 사용하여 커넥터 등록


1. 다음을 실행하여 Windows PowerShell 자격 증명 개체를 만듭니다. 여기서 "사용자 이름" 및 "암호"는 디렉터리의 사용자 이름 및 암호로 바꿔야 합니다.

        $User = "<username>" 
        $PlainPassword = '<password>' 
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force 
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword 
    
2. PowerShell 자격 증명 개체를 사용하여 **C:\\Program Files\\Microsoft AAD App Proxy Connector**로 이동하고 스크립트를 실행합니다. 여기서 $cred는 만든 PowerShell 자격 증명 개체의 이름입니다.

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred 


### 오프라인으로 만든 토큰을 사용하여 커넥터 등록

1. 코드 조각의 값을 사용하여 AuthenticationContext 클래스를 사용하는 오프라인 토큰을 만듭니다.

        
        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.windows.net/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }





2. 토큰을 만들었으면 토큰을 사용하여 SecureString을 만듭니다. <br> `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`
3. 다음 Windows PowerShell 명령을 실행합니다. 여기서 SecureToken은 위에서 만든 토큰의 이름이고 tenantID는 테넌트의 GUID입니다.<br> `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`



## 다음 작업
응용 프로그램 프록시를 사용하여 수행할 수 있는 작업은 많습니다.


- [고유한 도메인 이름을 사용하여 응용 프로그램 게시](active-directory-application-proxy-custom-domains.md)
- [Single Sign-On 사용](active-directory-application-proxy-sso-using-kcd.md)
- [클레임 인식 응용 프로그램으로 작업](active-directory-application-proxy-claims-aware-apps.md)
- [조건부 액세스 사용](active-directory-application-proxy-conditional-access.md)


### 응용 프로그램 프록시에 대해 자세히 알아보기
- [온라인 도움말에서 살펴보기](active-directory-application-proxy-enable.md)
- [응용 프로그램 프록시 블로그 확인](http://blogs.technet.com/b/applicationproxyblog/)
- [Channel 9에서 비디오 시청](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## 추가 리소스
* [조직으로 Azure 등록](sign-up-organization.md)
* [Azure ID](fundamentals-identity.md)

<!---HONumber=Oct15_HO4-->