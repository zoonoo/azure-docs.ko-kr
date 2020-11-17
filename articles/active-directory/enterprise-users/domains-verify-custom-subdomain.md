---
title: PowerShell 및 그래프를 사용 하 여 하위 도메인 인증 유형 변경-Azure Active Directory | Microsoft Docs
description: Azure Active Directory의 루트 도메인 설정에서 상속 된 기본 하위 도메인 인증 설정을 변경 합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 330a34f38aaa73fca8290d1638219fa8f517e1cb
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647378"
---
# <a name="change-subdomain-authentication-type-in-azure-active-directory"></a>Azure Active Directory에서 하위 도메인 인증 유형 변경

루트 도메인이 Azure Active Directory (Azure AD)에 추가 된 후에 Azure AD 조직의 해당 루트에 추가 된 모든 후속 하위 도메인은 루트 도메인에서 인증 설정을 자동으로 상속 합니다. 그러나 루트 도메인 설정과 별개로 도메인 인증 설정을 관리 하려는 경우에는 Microsoft Graph API를 사용할 수 있습니다. 예를 들어 contoso.com와 같은 페더레이션된 루트 도메인이 있는 경우이 문서를 통해 페더레이션된 대신 관리 되는 child.contoso.com와 같은 하위 도메인을 확인 하는 데 도움이 될 수 있습니다.

Azure AD 포털에서 부모 도메인이 페더레이션 되 고 관리자가 **사용자 지정 도메인 이름** 페이지에서 관리 하위 도메인을 확인 하려고 하면 "하나 이상의 속성에 잘못 된 값이 있습니다." 라는 이유로 ' 도메인을 추가 하지 못했습니다. ' 오류가 표시 됩니다. Microsoft 365 관리 센터에서이 하위 도메인을 추가 하려고 하면 비슷한 오류가 표시 됩니다. 오류에 대 한 자세한 내용은 [자식 도메인이 Office 365, Azure 또는 Intune의 부모 도메인 변경 내용을 상속 하지 않음](/office365/troubleshoot/administration/child-domain-fails-inherit-parent-domain-changes)을 참조 하세요.

## <a name="how-to-verify-a-custom-subdomain"></a>사용자 지정 하위 도메인을 확인 하는 방법

하위 도메인은 기본적으로 루트 도메인의 인증 유형을 상속 하기 때문에 인증 유형을 원하는 유형으로 설정할 수 있도록 Microsoft Graph를 사용 하 여 하위 도메인을 Azure AD의 루트 도메인으로 승격 해야 합니다.

### <a name="add-the-subdomain-and-view-its-authentication-type"></a>하위 도메인을 추가 하 고 해당 인증 유형을 확인 합니다.

1. PowerShell을 사용 하 여 루트 도메인의 기본 인증 유형을 가진 새 하위 도메인을 추가 합니다. Azure AD 및 Microsoft 365 관리 센터는 아직이 작업을 지원 하지 않습니다.

   ```powershell
   New-MsolDomain -Name "child.mydomain.com" -Authentication Federated
   ```

1. [AZURE AD Graph Explorer](https://graphexplorer.azurewebsites.net) 를 사용 하 여 도메인을 가져옵니다. 도메인은 루트 도메인이 아니므로 루트 도메인 인증 유형을 상속 합니다. 사용자 고유의 테 넌 트 ID를 사용 하 여 명령 및 결과가 다음과 같이 표시 될 수 있습니다.

   ```http
   GET https://graph.windows.net/{tenant_id}/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Federated",
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": false,          <---------------- Not a root domain, so it inherits parent domain's authentication type (federated)
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null
     },
   ```

### <a name="use-azure-ad-graph-explorer-api-to-make-this-a-root-domain"></a>Azure AD Graph Explorer API를 사용 하 여 루트 도메인으로 설정

다음 명령을 사용 하 여 하위 도메인을 승격 합니다.

```http
POST https://graph.windows.net/{tenant_id}/domains/child.mydomain.com/promote?api-version=1.6
```

### <a name="change-the-subdomain-authentication-type"></a>하위 도메인 인증 유형 변경

1. 하위 도메인 인증 유형을 변경 하려면 다음 명령을 사용 합니다.

   ```powershell
   Set-MsolDomainAuthentication -DomainName child.mydomain.com -Authentication Managed
   ```

1. 이제 Azure AD Graph Explorer에서 GET을 통해 하위 도메인 인증 유형을 관리 하 고 있는지 확인 합니다.

   ```http
   GET https://graph.windows.net/{{tenant_id} }/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Managed",   <---------- Now this domain is successfully added as Managed and not inheriting Federated status
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": true,   <------------------------------ Also a root domain, so not inheriting from parent domain any longer
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [
             "Email",
             "OfficeCommunicationsOnline",
             "Intune"
         ],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null }
   ```

## <a name="next-steps"></a>다음 단계

- [사용자 지정 도메인 이름 추가](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
- [도메인 이름 관리](domains-manage.md)
- [Microsoft Graph API를 통해 사용자 지정 도메인 이름 ForceDelete](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)