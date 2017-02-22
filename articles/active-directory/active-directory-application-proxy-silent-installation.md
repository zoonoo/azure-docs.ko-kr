---
title: "Azure AD 응용 프로그램 프록시 커넥터 자동 설치 | Microsoft Docs"
description: "Azure AD 응용 프로그램 프록시 커넥터를 무인으로 설치하여 온-프레미스 앱에 대한 보안된 원격 액세스를 제공하는 방법에 대해 설명합니다."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: 3aa1c7f2-fb2a-4693-abd5-95bb53700cbb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 081e45e0256134d692a2da7333ddbaafc7366eaa
ms.openlocfilehash: cf00d47efc613f7bdc152c1b5f0d0830fb44a785


---
# <a name="how-to-silently-install-the-azure-ad-application-proxy-connector"></a>Azure AD 응용 프로그램 프록시 커넥터를 자동으로 설치하는 방법
사용자 인터페이스를 사용하도록 설정되지 않은 Windows Server 또는 여러 Windows 서버에 설치 스크립트를 보낼 수 있습니다. 이 항목에서는 무인 설치가 Azure AD 응용 프로그램 프록시 커넥터를 설치하고 등록할 수 있도록 하는 Windows PowerShell 스크립트를 만드는 방법에 대해 설명합니다.

이 기능은 다음을 수행하는 데 유용 합니다.

* UI 계층이 없는 경우 또는 컴퓨터에 RDP를 수행할 수 없는 경우 컴퓨터에 커넥터를 설치합니다.
* 한 번에 여러 커넥터를 설치하고 등록합니다.
* 커넥터 설치 및 등록을 다른 절차의 일부분으로 통합합니다.
* 커넥터 비트를 포함하지만 등록되지 않은 표준 서버 이미지를 만듭니다.

## <a name="enabling-access"></a>액세스 사용
응용 프로그램 프록시는 네트워크 내부에서 커넥터라고 불리는 간단한 Windows Server 서비스를 설치하여 사용합니다. 응용 프로그램 프록시 커넥터가 작동하려면 전역 관리자 및 암호를 사용하여 Azure AD 디렉터리에 등록되어야 합니다. 일반적으로 이러한 정보는 커넥터 설치 중에 팝업 대화 상자에서 입력됩니다. 또는 Windows PowerShell을 사용하여 자격 증명 개체를 만들어 등록 정보를 입력하거나, 고유한 토큰을 만들고 이 토큰을 사용하여 등록 정보를 입력할 수 있습니다.

## <a name="step-1--install-the-connector-without-registration"></a>1단계: 등록 없이 커넥터 설치
다음과 같이 커넥터를 등록하지 않고 커넥터 MSI를 설치합니다.

1. 명령 프롬프트를 엽니다.
2. 다음 명령을 실행합니다. 여기서 /q는 무인 설치를 의미하며 설치 시 최종 사용자 사용권 계약에 동의할지 묻는 메시지가 표시되지 않습니다.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="step-2-register-the-connector-with-azure-active-directory"></a>2단계: Azure Active Directory에 커넥터 등록
다음 방법 중 하나를 사용하여 이 작업을 수행할 수 있습니다.

* Windows PowerShell 자격 증명 개체를 사용하여 커넥터 등록
* 오프라인으로 만든 토큰을 사용하여 커넥터 등록

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Windows PowerShell 자격 증명 개체를 사용하여 커넥터 등록
1. 다음을 실행하여 Windows PowerShell 자격 증명 개체를 만듭니다. 여기서 \<사용자 이름\> 및 \<암호\>는 디렉터리의 사용자 이름 및 암호로 바꿔야 합니다.
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. **C:\Program Files\Microsoft AAD App Proxy Connector**로 이동하고 PowerShell 자격 증명 개체를 사용하여 스크립트를 실행합니다. 여기서 $cred는 만든 PowerShell 자격 증명 개체의 이름입니다.
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred

### <a name="register-the-connector-using-a-token-created-offline"></a>오프라인으로 만든 토큰을 사용하여 커넥터 등록
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


2. 토큰을 만들었으면 토큰을 사용하여 SecureString을 만듭니다.

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. 다음 Windows PowerShell 명령을 실행하여 \<테넌트 GUID\>를 디렉터리 ID로 대체합니다.

   `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`

## <a name="next-steps"></a>다음 단계 
* [고유한 도메인 이름을 사용하여 응용 프로그램 게시](active-directory-application-proxy-custom-domains.md)
* [Single Sign-On 사용](active-directory-application-proxy-sso-using-kcd.md)
* [응용 프로그램 프록시에서 발생한 문제 해결](active-directory-application-proxy-troubleshoot.md)





<!--HONumber=Feb17_HO1-->


