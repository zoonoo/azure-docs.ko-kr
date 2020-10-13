---
title: 게시자 확인 문제 해결 - Microsoft ID 플랫폼 | Azure
description: Microsoft Graph Api를 호출 하 여 Microsoft id 플랫폼에 대 한 게시자 확인 문제를 해결 하는 방법을 설명 합니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 3c5084ff770f27438c85b7bc57cef0145182abb4
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91873152"
---
# <a name="troubleshoot-publisher-verification"></a>게시자 확인 문제 해결
프로세스를 완료할 수 없거나 예기치 않은 동작이 [게시자 확인](publisher-verification-overview.md)에 발생 하는 경우 오류를 수신 하거나 예기치 않은 동작을 볼 수 있는 경우 다음을 수행 하 여 시작 해야 합니다. 

1. [요구 사항](publisher-verification-overview.md#requirements)을 검토하고 모두 충족되었는지 확인합니다.

1. 지침을 검토하여 [앱을 게시자 확인으로 표시](mark-app-as-publisher-verified.md)하고 모든 단계가 성공적으로 수행되었는지 확인합니다.

1. [일반적인 문제](#common-issues)의 목록을 검토합니다.

1. [Graph Explorer](#making-microsoft-graph-api-calls)를 통해 요청을 재현하여 추가 정보를 수집하고 UI에서 문제를 제외합니다.

## <a name="common-issues"></a>일반적인 문제
프로세스 중에 발생할 수 있는 몇 가지 일반적인 문제는 다음과 같습니다. 

- **내 MPN ID(Microsoft 파트너 네트워크 ID)를 모르거나 또는 계정에 대한 기본 연락처를 알 수 없습니다.** 
    1. [MPN 등록 페이지](https://partner.microsoft.com/dashboard/account/v3/enrollment/joinnow/basicpartnernetwork/new)로 이동합니다.
    1. 조직의 기본 Azure AD 테넌트에서 사용자 계정으로 로그인합니다. 
    1. MPN 계정이 이미 있으면 이 계정이 인식되어 계정에 추가됩니다. 
    1. MPN ID 및 기본 계정 연락처가 나열되는 [파트너 프로필 페이지](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile)로 이동합니다.

- **Azure AD 전역 관리자 (회사 관리자 또는 테 넌 트 관리자 라고도 함)가 누구 인지 알 수 없는 경우 어떻게 찾을 수 있나요? 응용 프로그램 관리자 또는 클라우드 응용 프로그램 관리자는 어떻게 되나요?**
    1. 조직의 기본 테넌트에서 사용자 계정을 사용하여 [Azure AD 포털](https://aad.portal.azure.com)에 로그인합니다.
    1. [역할 관리](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)로 이동합니다.
    1. 원하는 관리자 역할을 클릭 합니다.
    1. 해당 역할에 할당된 사용자의 목록이 표시됩니다.

- **내 MPN 계정에 대한 관리자가 누구인지 알 수 없습니다.** [MPN 사용자 관리 페이지](https://partner.microsoft.com/pcv/users)로 이동하고, 사용자 목록을 필터링하여 다양한 관리 역할에 속한 사용자를 확인합니다.

- **MPN ID가 잘못되었거나 액세스할 수 없다는 오류가 발생합니다.**
    1. [파트너 프로필](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile)로 이동하여 다음을 확인합니다. 
        - MPN ID가 올바릅니다. 
        - 오류 또는 "보류 중인 작업"이 표시되지 않으며, 법적 비즈니스 프로필 및 파트너 정보 아래에서 확인 상태가 모두 "권한 있음" 또는 "성공"으로 표시됩니다.
    1. [MPN 테넌트 관리 페이지](https://partner.microsoft.com/dashboard/account/v3/tenantmanagement)로 이동하여 앱이 등록되어 있고 사용자 계정으로 서명하는 테넌트가 연결된 테넌트 목록에 있는지 확인합니다. 추가 테 넌 트를 추가 해야 하는 경우 [여기](https://docs.microsoft.com/partner-center/multi-tenant-account)에 설명 된 지침을 따르세요. 추가 하는 모든 테 넌 트의 모든 전역 관리자에 게는 파트너 센터 계정에 대 한 전역 관리자 권한이 부여 됩니다.
    1. [MPN 사용자 관리 페이지로](https://partner.microsoft.com/pcv/users) 이동 하 여 로그인 하는 사용자가 전역 관리자, MPN 관리자 또는 계정 관리자 인지 확인 합니다. 파트너 센터에서 사용자를 역할에 추가 해야 하는 경우 [여기](https://docs.microsoft.com/partner-center/create-user-accounts-and-set-permissions)에 설명 된 지침을 따르세요.

- **Azure AD 포털에 로그인하면 등록된 앱이 표시되지 않습니다. 그 이유는 무엇일까요?** 
    앱 등록은이 테 넌 트의 다른 사용자 계정, 개인/소비자 계정 또는 다른 테 넌 트를 사용 하 여 만들어졌을 수 있습니다. 앱 등록을 만든 테넌트에서 올바른 계정으로 로그인했는지 확인합니다.

- **Multi-factor authentication과 관련 된 오류가 발생 합니다. 제가 뭘 해야 하나요?** 
    [Multi-factor authentication](../fundamentals/concept-fundamentals-mfa-get-started.md) 을 사용 하도록 설정 하 고이 시나리오에 대해 로그인 하는 사용자에 대해 **필수** 를 확인 하세요. 예를 들어 MFA는 다음과 같을 수 있습니다.
    - 로그인 하는 사용자에 대해 항상 필요
    - [Azure 관리에 필요](../conditional-access/howto-conditional-access-policy-azure-management.md)합니다.
    - 로그인 하는 데 사용 하는 [관리자 유형에 필요](../conditional-access/howto-conditional-access-policy-admin-mfa.md) 합니다.

## <a name="making-microsoft-graph-api-calls"></a>Microsoft Graph API 호출 

문제가 있지만 UI에 표시되는 내용에 따라 이유를 이해할 수 없는 경우 Microsoft Graph 호출을 통해 앱 등록 포털에서 수행할 수 있는 것과 동일한 작업을 수행하여 문제 해결을 추가로 수행하는 것이 유용할 수 있습니다.

이러한 요청을 수행하는 가장 쉬운 방법은 [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)를 사용하는 것입니다. [Postman](https://www.postman.com/) 또는 PowerShell을 사용하여 [웹 요청을 호출](/powershell/module/microsoft.powershell.utility/invoke-webrequest)하는 것과 같은 다른 옵션을 고려할 수도 있습니다.  

Microsoft Graph를 사용하여 앱의 확인된 게시자를 설정하거나 해제하고 이러한 작업 중 하나를 수행한 후 결과를 확인할 수 있습니다. 결과는 앱 등록에 해당하는 [애플리케이션](/graph/api/resources/application) 개체와 해당 앱에서 인스턴스화된 [서비스 주체](/graph/api/resources/serviceprincipal) 모두에서 볼 수 있습니다. 이러한 개체 간의 관계에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조하세요.  

몇 가지 유용한 요청의 예제는 다음과 같습니다.  

### <a name="set-verified-publisher"></a>확인된 게시자 설정 

요청

```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/setVerifiedPublisher 

{ 

    "verifiedPublisherId": "12345678" 

} 
```
 
응답 
```
204 No Content 
```
> [!NOTE]
> *verifiedPublisherID*는 MPN ID입니다. 

### <a name="unset-verified-publisher"></a>확인된 게시자 설정 해제 

요청:  
```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/unsetVerifiedPublisher 
```
 
응답 
```
204 No Content 
```
### <a name="get-verified-publisher-info-from-application"></a>애플리케이션에서 확인된 게시자 정보 가져오기 
 
```
GET https://graph.microsoft.com/v1.0/applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec 

HTTP/1.1 200 OK 

{ 
    "id": "0cd04273-0d11-4e62-9eb3-5c3971a7cbec", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

### <a name="get-verified-publisher-info-from-service-principal"></a>서비스 주체에서 확인된 게시자 정보 가져오기 
```
GET https://graph.microsoft.com/v1.0/servicePrincipals/010422a7-4d77-4f40-9335-b81ef5c23dd4 

HTTP/1.1 200 OK 

{ 
    "id": "010422a7-4d77-4f40-9335-b81ef5c22dd4", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

## <a name="error-reference"></a>오류 참조 

Microsoft Graph를 사용하여 문제를 해결하거나 앱 등록 포털에서 프로세스를 진행하는 경우 받을 수 있는 잠재적인 오류 코드의 목록은 다음과 같습니다.

### <a name="mpnaccountnotfoundornoaccess"></a>MPNAccountNotFoundOrNoAccess     

제공한 MPN ID(<MPNID>)가 없거나 이에 액세스할 수 없습니다. 유효한 MPN ID를 제공하고 다시 시도하세요.
    
로그인 한 사용자가 파트너 센터에서 MPN 계정에 대 한 적절 한 역할의 멤버가 아닌 경우 가장 일반적으로 발생 하는 항목입니다. 자세한 내용은 [요구 사항](publisher-verification-overview.md#requirements) 역할 목록 및 [일반적인 문제](#common-issues) 를 참조 하세요. 앱이 등록 된 테 넌 트가 MPN 계정에 추가 되지 않았거나 잘못 된 MPN ID로 인해 발생할 수도 있습니다.

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound     

제공한 MPN ID(<MPNID>)가 잘못되었습니다. 유효한 MPN ID를 제공하고 다시 시도하세요.
    
가장 일반적으로는 PLA (파트너 위치 계정)에 해당 하는 MPN ID가 제공 될 때 발생 합니다. 파트너 전역 계정만 지원 됩니다. 자세한 내용은 [파트너 센터 계정 구조](/partner-center/account-structure) 를 참조 하세요.

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid    

제공한 MPN ID(<MPNID>)가 잘못되었습니다. 유효한 MPN ID를 제공하고 다시 시도하세요.
    
잘못 된 MPN ID가 제공 되는 경우 가장 일반적으로 발생 합니다.

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted  

제공한 MPN ID(<MPNID>)에서 심사 프로세스를 완료하지 않았습니다. 파트너 센터에서 이 프로세스를 완료하고 다시 시도하세요. 
    
MPN 계정이 [확인](/partner-center/verification-responses) 프로세스를 완료 하지 않은 경우에 가장 일반적으로 발생 합니다.

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount  

제공한 MPN ID(<MPNID>)가 잘못되었습니다. 유효한 MPN ID를 제공하고 다시 시도하세요. 
   
잘못 된 MPN ID가 제공 되는 경우 가장 일반적으로 발생 합니다.

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount    

제공한 MPN ID(<MPNID>)가 잘못되었습니다. 유효한 MPN ID를 제공하고 다시 시도하세요.
    
잘못 된 MPN ID가 제공 되는 경우 가장 일반적으로 발생 합니다.

### <a name="applicationnotfound"></a>ApplicationNotFound  

대상 애플리케이션(<AppId>)을 찾을 수 없습니다. 유효한 애플리케이션 ID를 제공하고 다시 시도하세요.
    
가장 일반적으로는 Graph API을 통해 확인을 수행 하 고 제공 된 응용 프로그램의 id가 올바르지 않은 경우에 발생 합니다. 참고-응용 프로그램의 id는 AppId/ClientId가 아니라 제공 되어야 합니다.

### <a name="b2ctenantnotallowed"></a>B2CTenantNotAllowed  

이 기능은 Azure AD B2C 테넌트에서 지원되지 않습니다. 

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed    

이 기능은 이메일로 확인된 테넌트에서 지원되지 않습니다. 

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication   

대상 응용 프로그램 ( \<AppId\> )에는 게시자 도메인 집합이 있어야 합니다. 게시자 도메인을 설정하고 다시 시도하세요.

앱에서 [게시자 도메인이](howto-configure-publisher-domain.md) 구성 되지 않은 경우에 발생 합니다.

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch  

애플리케이션의 게시자 도메인(<publisherDomain>)이 파트너 센터에서 이메일 확인을 수행하는 데 사용되는 도메인(<pcDomain>)과 일치하지 않습니다. 이러한 도메인이 일치하는지 확인하고 다시 시도하세요. 
    
앱의 [게시자 도메인](howto-configure-publisher-domain.md) 또는 Azure AD 테 넌 트에 추가 된 [사용자 지정 도메인](../fundamentals/add-custom-domain.md) 중 하나가 파트너 센터에서 전자 메일 확인을 수행 하는 데 사용 되는 도메인과 일치 하지 않는 경우에 발생 합니다.

### <a name="notauthorizedtoverifypublisher"></a>NotAuthorizedToVerifyPublisher   

애플리케이션(<AppId>)에서 확인된 게시자 속성을 설정할 수 있는 권한이 없습니다. 
  
로그인 [한 사용자](#common-issues) 가 Azure AD의 MPN 계정에 대 한 적절 한 역할의 멤버가 아닌 경우 가장 일반적으로 발생 하는 것입니다. 자세한 내용은 사용 가능한 역할 목록은 [요구 사항](publisher-verification-overview.md#requirements) 을 참조 하세요.

### <a name="mpnidwasnotprovided"></a>MPNIdWasNotProvided  

MPN ID가 요청 본문에 제공되지 않았거나 요청 콘텐츠 형식이 "application/json"이 아닙니다. 

### <a name="msanotsupported"></a>MSANotSupported  

이 기능은 Microsoft 소비자 계정에서 지원되지 않습니다. Azure AD 사용자가 Azure AD에 등록한 애플리케이션만 지원됩니다.

### <a name="interactionrequired"></a>InteractionRequired

앱에 확인 된 게시자를 추가 하기 전에 multi-factor authentication을 수행 하지 않은 경우에 발생 합니다. 자세한 내용은 [일반적인 문제](#common-issues) 를 참조 하세요. 참고: MFA는 확인 된 게시자를 추가 하려고 할 때 동일한 세션에서 수행 해야 합니다. MFA를 사용 하도록 설정 했지만 세션에서 수행할 필요가 없는 경우 요청이 실패 합니다.   

표시 되는 오류 메시지는 다음과 같습니다. "관리자의 구성 변경으로 인해 또는 새 위치로 이동 했으므로 계속 하려면 multi-factor authentication을 사용 해야 합니다."

## <a name="next-steps"></a>다음 단계

이전 정보를 모두 검토했지만 여전히 Microsoft Graph에서 오류를 수신하는 경우 실패한 요청과 관련하여 다음 정보를 최대한 많이 수집하고 [Microsoft 지원에 문의](developer-support-help-options.md#open-a-support-request)하세요.

- 타임스탬프 
- CorrelationId 
- 로그인한 사용자의 ObjectID 또는 UserPrincipalName 
- 대상 애플리케이션의 ObjectId
- 대상 애플리케이션의 AppId
- 앱이 등록된 TenantId
- MPN ID
- 만들고 있는 REST 요청 
- 반환되는 오류 코드 및 메시지
