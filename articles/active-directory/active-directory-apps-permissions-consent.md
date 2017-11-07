---
title: "Azure Active Directory에서 앱, 사용 권한 및 동의 | Microsoft Docs"
description: "Azure AD Connect는 온-프레미스 디렉터리와 Azure Active Directory를 통합니다. 이렇게 하면 Azure AD와 통합된 Office 365, Azure 및 SaaS 응용 프로그램에 대한 공통 ID를 제공할 수 있습니다."
keywords: "Azure AD 소개, 앱, Azure AD Connect 정의, active directory 설치"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 25897cc4-7687-49b6-b0d5-71f51302b6b1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/31/2017
ms.author: billmath
ms.reviewer: jesakowi
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 6f6baf5e1538fb280a899065c64ca5688473c04a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="apps-permissions-and-consent-in-azure-active-directory"></a>Azure Active Directory에서 앱, 사용 권한 및 동의
Azure Active Directory 내에서 디렉터리에 응용 프로그램을 추가할 수 있습니다.  응용 프로그램은 응용 프로그램 유형에 따라 다를 수 있습니다.  클래식 포털에서 응용 프로그램을 보려면 디렉터리와 응용 프로그램을 선택합니다.

![](media/active-directory-apps-permissions-consent/apps1.png)

> [!IMPORTANT]
> 이 문서에서 참조되는 Azure 클래식 포털을 사용하는 대신 Azure Portal에서 [Azure AD 관리 센터](https://aad.portal.azure.com)를 사용하여 Azure AD를 관리하는 것이 좋습니다.

## <a name="types-of-apps"></a>앱 형식

1. **단일 테넌트 앱** </br>
    - **단일 테넌트 앱** - LOB(기간 업무) 앱이라고도 합니다. 조직 내 누군가가 자신의 앱을 개발하고 조직의 사용자가 앱에 로그인할 수 있도록 하는 경우에 해당합니다.
    ![](media/active-directory-apps-permissions-consent/apps2.png)
    - **앱 프록시 앱** - Azure AD 앱 프록시를 통해 온-프레미스 응용 프로그램을 노출하는 경우 앱 프록시 서비스 외에도 단일 테넌트 앱이 사용자의 테넌트에 등록됩니다. 이 앱은 모든 클라우드 상호 작용(예: 인증)에 대한 온-프레미스 응용 프로그램을 나타냅니다. (앱 프록시에는 Azure AD Basic 이상이 필요)


2. **다중 테넌트 앱**
    - **다른 사용자가 동의할 수 있는 다중 테넌트 앱** - "조직에서 개발하는 단일 테넌트 앱"과 유사합니다. 앱 자체에 있는 논리 외에도 주요 차이점은 다른 테넌트의 사용자가 동의하고 앱에 로그인할 수 있다는 점입니다.</br>
    ![](media/active-directory-apps-permissions-consent/apps4.png)
    - **Contoso에서 동의할 수 있고 다른 사용자가 개발하는 다중 테넌트 앱**. (또는 간략히 "동의한 앱") "조직이 개발하는 다중 테넌트 앱"의 다른 면입니다. 다른 조직에서 다중 테넌트 앱을 개발하는 경우 조직의 사용자는 앱에 동의하고 로그인할 수 있습니다.
    - **Microsoft 자사 앱** - Microsoft 서비스를 나타내는 앱입니다. 사용자가 서비스에 등록했다는 사실로 동의가 이루어집니다. 간혹 앱에 대한 액세스 관련 정책을 설정할 때 자주 사용되는 특정 자사 앱에 대해 특수한 UX 및 논리가 있습니다.</br>
    ![](media/active-directory-apps-permissions-consent/apps3.png)
    - **사전 통합된 앱** - Azure AD 앱 갤러리에서 사용 가능한 앱으로, 사용자 디렉터리에 추가하여 인기 SaaS 앱에 single sign-on(일부 경우, 프로비전)을 제공할 수 있습니다.
    - **Azure AD single sign-on**: SAML 2.0 또는 OpenID Connect와 같은 지원되는 로그인 프로토콜을 통해 Azure AD와 통합할 수 있는 앱에 대한 "실제" SSO입니다. 마법사에서 설정하는 과정을 안내해 줍니다.
    - **암호 single sign-on**: Azure AD는 앱에 대한 사용자의 자격 증명을 안전하게 저장하고 자격 증명은 Azure AD 앱 액세스 브라우저 확장 기능으로 로그인 양식에 "삽입"됩니다. “암호 보관”이라고도 합니다.

## <a name="permissions"></a>권한

앱이 등록되면 앱 등록을 수행하는 사용자(즉, 개발자)가 앱에 액세스하는 데 필요한 권한과 리소스를 정의합니다. 리소스 자체는 다른 앱으로 정의됩니다. 예를 들어, 메일 읽기 프로그램 앱을 작성 중인 사용자는 앱에 “Office 365 Exchange Online” 리소스에 대한 “로그인한 사용자로 사서함 액세스” 권한이 필요하다는 것을 명시합니다.
    
![](media/active-directory-apps-permissions-consent/apps6.png)

한 앱(클라이언트)에서 다른 앱(리소스)으로부터 특정 권한을 요청하려면 리소스 앱 개발자가 존재하는 권한을 정의합니다 이 예제에서 “Office 365 Exchange Online” 리소스 앱의 소유자인 Microsoft는 “로그인한 사용자로 사서함 액세스”라는 권한을 정의했습니다.

권한을 정의할 경우 앱 개발자는 권한에 동의할 수 있는지 또는 관리자 동의가 필요한지 여부를 정의해야 합니다. 이를 통해 개발자는, 사용자는 중요도가 낮은 권한만 필요한 앱에 동의할 수 있도록 하고 관리자는 중요도가 높은 권한에 동의하도록 할 수 있습니다. 예를 들어 “Azure Active Directory” 리소스 앱은 제한된 읽기 전용 권한만 요청하면서 사용자가 앱에 동의할 수 있도록 정의되었습니다.  그러나 관리자 동의에는 전체 읽기 권한 및 전체 쓰기 권한이 필요합니다.

네이티브 클라이언트는 인증되지 않으므로 네이티브 클라이언트 앱으로 정의된 앱은 위임된 권한만 요청할 수 있습니다. 따라서 토큰을 획득할 때는 실제 관련된 사용자여야 합니다. Web Apps 및 Web API(기밀 클라이언트)는 액세스 토큰을 가져올 때 항상 Azure AD로 인증해야 합니다. 또한 앱 전용 권한을 요청할 가능성이 있음을 의미합니다. 예를 들어 한 백 엔드 서비스에서 다른 백 엔드 서비스에 인증해야 하는 경우가 있습니다. 앱 전용 권한을 요청하는 응용 프로그램은 관리자의 동의를 항상 필요로 합니다.

요약:



- 앱(클라이언트)은 다른 앱(리소스)에 대해 필요한 권한을 명시합니다.
- 앱(리소스)은 다른 앱(클라이언트)에 노출되는 권한을 명시합니다.
- 권한은 앱 전용 권한이거나 위임된 권한일 수 있습니다.
- 위임된 권한은 “사용자 동의 허용” 또는 “관리자 동의 필요”로 표시할 수 있습니다.
- 앱은 리소스에 대한 권한이 필요함을 선언하면 클라이언트로 동작하고 노출되는 권한을 선언하면 리소스로 동작할 수 있습니다.

## <a name="controls"></a>컨트롤

이러한 모든 동작에 사용할 수 있는 다양한 관리 컨트롤 목록은 다음과 같습니다. 관리 컨트롤은 클래식 포털에서 해당 디렉터리의 구성에서 액세스할 수 있습니다.

![](media/active-directory-apps-permissions-consent/apps7.png)

Azure Portal의 **관리**, **사용자 설정**에서는 다음을 수행할 수 있습니다.

![](media/active-directory-apps-permissions-consent/apps11.png)



- 사용자가 앱에 동의할 수 있는지 여부를 제어할 수 있습니다.

클래식 포털에서 **사용자가 자신의 데이터에 액세스할 수 있는 권한을 응용 프로그램에게 부여할 수 있음**
![](media/active-directory-apps-permissions-consent/apps8.png)을 선택합니다.

Azure Portal에서 **사용자가 앱이 데이터에 액세스하도록 허용할 수 있음**을 선택합니다.
![](media/active-directory-apps-permissions-consent/apps12.png)



- 사용자가 자신의 단일 테넌트 LOB 앱을 등록할 수 있는지 여부를 제어할 수 있습니다. 클래식 포털에서 **사용자가 통합 응용 프로그램을 추가할 수 있음**
![](media/active-directory-apps-permissions-consent/apps9.png)을 선택합니다.

Azure Portal에서 **사용자가 앱이 데이터에 액세스하도록 허용할 수 있음**을 선택합니다.
![](media/active-directory-apps-permissions-consent/apps13.png)

>[!NOTE]
>사용자가 단일 테넌트 LOB 앱을 등록할 수 있도록 하더라도 등록 가능한 항목에는 제한이 있습니다.  
>예를 들어 디렉터리 관리자가 아닌 개발자는 다음 제한이 있습니다.
>
>- 사용자는 단일 테넌트 앱을 다중 테넌트 앱으로 만들 수 없습니다.
>- 단일 테넌트 LOB 앱을 등록할 때 사용자는 앱 전용 권한을 다른 앱에 요청할 수 없습니다.
>- 단일 테넌트 LOB 앱을 등록할 때 사용자는 위임된 권한을 다른 앱에 요청할 수 없습니다(해당 권한에 관리자 동의가 필요한 경우).
>- 사용자는 자신의 소유자가 아닌 앱을 변경할 수 없습니다.



- 사용자가 스스로 암호 SSO(즉, “암호 보관”)를 사용하는 사전 통합된 앱을 추가할 수 있는지 여부를 제어할 수 있습니다. ![](media/active-directory-apps-permissions-consent/apps10.png)



- 응용 프로그램에 액세스할 수 있는 조건(즉, 조건부 액세스)을 제어할 수 있습니다. 이 내용은 클라이언트 앱과 리소스 앱 모두에 해당합니다. 따라서 조건부 액세스 정책을 설정했다면 컴퓨터에서 호환되는 “Office 365 Exchange Online” 앱에만 액세스할 수 있는 것입니다.  이 정책은 사용자가 Exchange Online에 대한 권한을 요청하는 클라이언트 앱을 사용하려고 시도할 때에도 시작됩니다.



- 동의한 앱과 사용된 권한이 표시됩니다.

1.  사용자가 앱에 동의하면 ServicePrincipal 개체가 테넌트에 생성됩니다. ServicePrincipal 생성은 감사 보고서에 포함됩니다.
2.  사용자 로그인 활동 보고서를 통해 사용자가 로그인한 앱을 알 수 있습니다. 

## <a name="example"></a>예

예를 들어 테넌트의 사용자가 “FabrikamMail for Office 365” 앱에 로그인한 경우를 살펴 보겠습니다. “FabrikamMail”은 “Fabrikam, Inc.”에서 게시한 Android용 메일 읽기 프로그램 앱입니다. “Contoso에서 동의할 수 있고 다른 사용자가 개발하는 다중 테넌트 앱”에 해당합니다.

사용자가 동의할 수 있는 경우 처음으로 로그인하면 동의 확인 메시지가 표시됩니다. ![](media/active-directory-apps-permissions-consent/apps14.png)

“사서함 액세스”는 “Office 365 Exchange Online”(즉, Exchange)에 의해 노출되는 “로그인한 사용자로 사서함 액세스” 권한에 대해 사용자에게 표시되는 동의 문자열입니다.

Office 365에 등록할 때 추가된 Exchange(리소스)에서 ServicePrincipal 개체를 조회하여 권한을 확인할 수 있습니다. 다양한 옵션 및 구성을 기록하는 데 사용되는 테넌트에 있는 앱의 "인스턴스"인 ServicePrincipal 개체를 생각할 수 있습니다.  PowerShell에서 `Get-AzureADServicePrincipal`을 사용하여 이를 확인할 수 있습니다.

    PS C:\> Get-AzureADServicePrincipal -ObjectId 383f7b97-6754-4d3d-9474-3908ebcba1c6 | fl *
    
    DeletionTimeStamp         : 
    ObjectId                  : 383f7b97-6754-4d3d-9474-3908ebcba1c6
    ObjectType                : ServicePrincipal
    AccountEnabled            : True
    AppDisplayName            : Office 365 Exchange Online
    AppId                     : 00000002-0000-0ff1-ce00-000000000000
    AppOwnerTenantId          : 
    AppRoleAssignmentRequired : False
    AppRoles                  : {...}
    DisplayName               : Microsoft.Exchange
    ErrorUrl                  : 
    Homepage                  : 
    KeyCredentials            : {}
    LogoutUrl                 : 
    Oauth2Permissions         : {...
                                , class OAuth2Permission {
                                  AdminConsentDescription : Allows the app to have the same access to mailboxes as the signed-in user via Exchange Web Services.
                                  AdminConsentDisplayName : Access mailboxes as the signed-in user via Exchange Web Services
                                  Id                      : 3b5f3d61-589b-4a3c-a359-5dd4b5ee5bd5
                                  IsEnabled               : True
                                  Type                    : User
                                  UserConsentDescription  : Allows the app full access to your mailboxes on your behalf.
                                  UserConsentDisplayName  : Access your mailboxes
                                  Value                   : full_access_as_user
                                },
                                ...}
    PasswordCredentials       : {}
    PublisherName             : 
    ReplyUrl                  : 
    SamlMetadataUrl           : 
    ServicePrincipalNames     : {00000002-0000-0ff1-ce00-000000000000/outlook.office365.com, 00000002-0000-0ff1-ce00-000000000000/mail.office365.com, 00000002-0000-0ff1-ce00-000000000000/outlook.com, 
                                00000002-0000-0ff1-ce00-000000000000/*.outlook.com...}
    Tags                      : {}

동의는 사용자가 “동의함”을 클릭하면 시작됩니다. 먼저 “FabrikamMail for Office 365”에 대한 ServicePrincipal 개체가 테넌트에 생성됩니다. ServicePrincipal은 다음과 같이 표시됩니다.

    PS C:\> Get-AzureADServicePrincipal -SearchString "FabrikamMail for Office 365" | fl *
    
    DeletionTimeStamp         : 
    ObjectId                  : a8b16333-851d-42e8-acd2-eac155849b37
    ObjectType                : ServicePrincipal
    AccountEnabled            : True
    AppDisplayName            : FabrikamMail for Office 365
    AppId                     : aba7c072-2267-4031-8960-e7a2db6e0590
    AppOwnerTenantId          : 4a4076e0-a70f-41c6-b819-6f9c4a86df89
    AppRoleAssignmentRequired : False
    AppRoles                  : {}
    DisplayName               : FabrikamMail for Office 365
    ErrorUrl                  : 
    Homepage                  : 
    KeyCredentials            : {}
    LogoutUrl                 : 
    Oauth2Permissions         : {}
    PasswordCredentials       : {}
    PublisherName             : Fabrikam, Inc.
    ReplyUrl                  : 
    SamlMetadataUrl           : 
    ServicePrincipalNames     : {aba7c072-2267-4031-8960-e7a2db6e0590}
    Tags                      : {WindowsAzureActiveDirectoryIntegratedApp}

앱에 동의하면 다음 사이에 Oauth2PermissionGrant 링크가 생성됩니다.
  
- 사용자 개체
- 클라이언트 앱 ServicePrincipalName(SPN)
- 리소스 앱 ServicePrincipalName(SPN)
- 리소스 그룹에서 권한.  

FabrikamMail의 경우 다음과 같이 나타납니다.

    PS C:\> Get-AzureADUserOAuth2PermissionGrant -ObjectId ddiggle@aadpremiumlab.onmicrosoft.com | fl *
    
    ClientId    : a8b16333-851d-42e8-acd2-eac155849b37
    ConsentType : Principal
    ExpiryTime  : 05/15/2017 07:02:39 AM
    ObjectId    : M2OxqB2F6EKs0urBVYSbN5d7PzhUZz1NlH25COvLocbJjoxkUFfRQauryBKwBWet
    PrincipalId : 648c8ec9-5750-41d1-abab-c812b00567ad
    ResourceId  : 383f7b97-6754-4d3d-9474-3908ebcba1c6
    Scope       : full_access_as_user
    StartTime   : 01/01/0001 12:00:00 AM

(**ClientId**는 FabrikamMail의 서비스 주체 개체 ID(방금 생성)이고, **PrincipalId**는 사용자 개체 ID(동의한 사용자의)이며, **ResourceId**는 Exchange의 서비스 주체 개체 ID입니다. 범위는 동의한 Exchange에서의 권한입니다.)

사용자가 동의할 수 없는 경우 권한이 필요하다는 화면이 표시됩니다.

