---
title: 게시자 확인 문제 해결 - Microsoft ID 플랫폼 | Azure
description: Microsoft Graph API를 호출하여 Microsoft ID 플랫폼에 대한 게시자 확인(미리 보기) 문제를 해결하는 방법을 설명합니다.
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
ms.openlocfilehash: dcdce6ee6683c5770f97f5f3dc20e1c9b409ead0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85477042"
---
# <a name="troubleshoot-publisher-verification-preview"></a>게시자 확인 문제 해결(미리 보기)
[게시자 확인(미리 보기)](publisher-verification-overview.md)을 사용하여 프로세스를 완료할 수 없거나 예기치 않은 동작이 발생하는 경우 오류를 수신하거나 예기치 않은 동작이 표시되면 먼저 다음을 수행해야 합니다. 

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
    1. MPN ID 및 기본 계정 연락처가 나열되는 [파트너 프로필 페이지](https://partner.microsoft.com/en-us/pcv/accountsettings/connectedpartnerprofile)로 이동합니다.

- **Azure AD 글로벌 관리자(회사 관리자 또는 테넌트 관리자라고도 함)가 누구인지 알 수 없습니다. 어떻게 찾을 수 있나요? 앱 관리자 또는 다른 관리자 역할은 어떻나요?**
    1. 조직의 기본 테넌트에서 사용자 계정을 사용하여 [Azure AD 포털](https://aad.portal.azure.com)에 로그인합니다.
    1. [역할 관리](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)로 이동합니다.
    1. "전역 관리자" 또는 원하는 관리자 역할을 클릭합니다.
    1. 해당 역할에 할당된 사용자의 목록이 표시됩니다.

- **내 MPN 계정에 대한 관리자가 누구인지 알 수 없습니다.** [MPN 사용자 관리 페이지](https://partner.microsoft.com/en-us/pcv/users)로 이동하고, 사용자 목록을 필터링하여 다양한 관리 역할에 속한 사용자를 확인합니다.

- **MPN ID가 잘못되었거나 액세스할 수 없다는 오류가 발생합니다.**
    1. [파트너 프로필](https://partner.microsoft.com/en-us/pcv/accountsettings/connectedpartnerprofile)로 이동하여 다음을 확인합니다. 
        - MPN ID가 올바릅니다. 
        - 오류 또는 "보류 중인 작업"이 표시되지 않으며, 법적 비즈니스 프로필 및 파트너 정보 아래에서 확인 상태가 모두 "권한 있음" 또는 "성공"으로 표시됩니다.
    1. [MPN 테넌트 관리 페이지](https://partner.microsoft.com/en-us/dashboard/account/v3/tenantmanagement)로 이동하여 앱이 등록되어 있고 사용자 계정으로 서명하는 테넌트가 연결된 테넌트 목록에 있는지 확인합니다.
    1. [MPN 사용자 관리 페이지](https://partner.microsoft.com/en-us/pcv/users)로 이동하여 로그인하는 사용자가 글로벌 관리자, MPN 관리자 또는 계정 관리자인지 확인합니다.

- **Azure AD 포털에 로그인하면 등록된 앱이 표시되지 않습니다. 그 이유는 무엇일까요?** 
    앱 등록이 다른 사용자 계정을 사용하여 만들어졌거나 다른 테넌트에 만들어졌을 수 있습니다. 앱 등록을 만든 테넌트에서 올바른 계정으로 로그인했는지 확인합니다.

- **Azure AD에서 앱 등록 소유자가 누구인지 어떻게 알 수 있나요?** 
    앱이 등록된 테넌트에 로그인하면 앱 등록 블레이드로 이동하여 앱을 클릭한 다음, [소유자]를 클릭합니다.

## <a name="making-microsoft-graph-api-calls"></a>Microsoft Graph API 호출 

문제가 있지만 UI에 표시되는 내용에 따라 이유를 이해할 수 없는 경우 Microsoft Graph 호출을 통해 앱 등록 포털에서 수행할 수 있는 것과 동일한 작업을 수행하여 문제 해결을 추가로 수행하는 것이 유용할 수 있습니다. 미리 보기 단계 중에는 이러한 API를 Microsoft Graph의 /beta 엔드포인트에서만 사용할 수 있습니다.  

이러한 요청을 수행하는 가장 쉬운 방법은 [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)를 사용하는 것입니다. [Postman](https://www.postman.com/) 또는 PowerShell을 사용하여 [웹 요청을 호출](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7)하는 것과 같은 다른 옵션을 고려할 수도 있습니다.  

Microsoft Graph를 사용하여 앱의 확인된 게시자를 설정하거나 해제하고 이러한 작업 중 하나를 수행한 후 결과를 확인할 수 있습니다. 결과는 앱 등록에 해당하는 [애플리케이션](/graph/api/resources/application?view=graph-rest-beta) 개체와 해당 앱에서 인스턴스화된 [서비스 주체](/graph/api/resources/serviceprincipal?view=graph-rest-beta) 모두에서 볼 수 있습니다. 이러한 개체 간의 관계에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조하세요.  

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
GET https://graph.microsoft.com/beta/applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec 

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
GET https://graph.microsoft.com/beta/servicePrincipals/010422a7-4d77-4f40-9335-b81ef5c23dd4 

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

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound     

제공한 MPN ID(<MPNID>)가 잘못되었습니다. 유효한 MPN ID를 제공하고 다시 시도하세요. 

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid    

제공한 MPN ID(<MPNID>)가 잘못되었습니다. 유효한 MPN ID를 제공하고 다시 시도하세요. 

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted  

제공한 MPN ID(<MPNID>)에서 심사 프로세스를 완료하지 않았습니다. 파트너 센터에서 이 프로세스를 완료하고 다시 시도하세요. 

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount  

제공한 MPN ID(<MPNID>)가 잘못되었습니다. 유효한 MPN ID를 제공하고 다시 시도하세요. 

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount    

제공한 MPN ID(<MPNID>)가 잘못되었습니다. 유효한 MPN ID를 제공하고 다시 시도하세요. 

### <a name="applicationnotfound"></a>ApplicationNotFound  

대상 애플리케이션(<AppId>)을 찾을 수 없습니다. 유효한 애플리케이션 ID를 제공하고 다시 시도하세요. 

### <a name="b2ctenantnotallowed"></a>B2CTenantNotAllowed  

이 기능은 Azure AD B2C 테넌트에서 지원되지 않습니다. 

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed    

이 기능은 이메일로 확인된 테넌트에서 지원되지 않습니다. 

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication   

게시자 도메인이 대상 애플리케이션(<AppId>)에 설정되어 있어야 합니다. 게시자 도메인을 설정하고 다시 시도하세요. 

### <a name="publisherdomainisnotdnsverified"></a>PublisherDomainIsNotDNSVerified  

대상 애플리케이션의 게시자 도메인(<publisherDomain>)이 이 테넌트에서 확인된 도메인이 아닙니다. DNS 확인을 사용하여 테넌트 도메인을 확인하고 다시 시도하세요. 

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch  

애플리케이션의 게시자 도메인(<publisherDomain>)이 파트너 센터에서 이메일 확인을 수행하는 데 사용되는 도메인(<pcDomain>)과 일치하지 않습니다. 이러한 도메인이 일치하는지 확인하고 다시 시도하세요. 

### <a name="notauthorizedtoverifypublisher"></a>NotAuthorizedToVerifyPublisher   

애플리케이션(<AppId>)에서 확인된 게시자 속성을 설정할 수 있는 권한이 없습니다. 

### <a name="mpnidwasnotprovided"></a>MPNIdWasNotProvided  

MPN ID가 요청 본문에 제공되지 않았거나 요청 콘텐츠 형식이 "application/json"이 아닙니다. 

### <a name="msanotsupported"></a>MSANotSupported  

이 기능은 Microsoft 소비자 계정에서 지원되지 않습니다. Azure AD 사용자가 Azure AD에 등록한 애플리케이션만 지원됩니다. 

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
