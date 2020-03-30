---
title: Azure AD 스키마 및 사용자 지정 식 이해
description: 이 문서에서는 Azure AD 스키마, 프로비저닝 에이전트가 흐르는 특성 및 사용자 지정 식에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ac09fb3faf55be6c07a1e0a88b6e2032c9ab8ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299332"
---
# <a name="understand-the-azure-ad-schema"></a>Azure AD 스키마 이해
Azure Active Directory(Azure AD)의 개체는 모든 디렉터리와 마찬가지로 사용자, 그룹 및 연락처와 같은 것을 나타내는 프로그래밍 방식의 상위 수준 데이터 구문입니다. Azure AD에서 새 사용자 또는 연락처를 만들 때 해당 개체의 새 인스턴스를 만듭니다. 이러한 인스턴스는 속성에 따라 구분할 수 있습니다.

Azure AD의 속성은 Azure AD에서 개체 인스턴스에 대한 정보를 저장하는 요소입니다.

Azure AD 스키마는 항목에 사용할 수 있는 속성, 해당 속성이 가질 수 있는 값의 종류 및 사용자가 해당 값과 상호 작용하는 방법을 정의합니다. 

Azure AD에는 두 가지 유형의 속성이 있습니다.
- **기본 제공 속성**: Azure AD 스키마에서 미리 정의된 속성입니다. 이러한 속성은 서로 다른 용도를 제공하며 액세스할 수 있거나 액세스할 수 없을 수도 있습니다.
- **디렉터리 확장**: 사용자 고유의 사용을 위해 Azure AD를 사용자 지정할 수 있도록 제공되는 속성입니다. 예를 들어 온-프레미스 Active Directory를 특정 특성으로 확장하고 해당 특성을 흐름하려는 경우 제공된 사용자 지정 속성 중 하나를 사용할 수 있습니다. 

## <a name="attributes-and-expressions"></a>특성 및 식
사용자와 같은 개체가 Azure AD에 프로비전되면 사용자 개체의 새 인스턴스가 만들어집니다. 이 생성에는 특성이라고도 하는 해당 개체의 속성이 포함됩니다. 처음에 새로 생성된 개체에는 동기화 규칙에 의해 결정되는 값으로 설정된 특성이 있습니다. 그런 다음 이러한 특성은 클라우드 프로비저닝 에이전트를 통해 최신 상태로 유지됩니다.

![개체 프로비저닝](media/concept-attributes/attribute1.png)

예를 들어 사용자가 마케팅 부서의 일부일 수 있습니다. Azure AD 부서 특성은 프로비전될 때 처음에 만들어지며 값은 마케팅으로 설정됩니다. 6개월 후 Sales로 변경하면 온-프레미스 Active Directory 부서 특성이 Sales로 변경됩니다. 이 변경 내용은 Azure AD와 동기화되며 Azure AD 사용자 개체에 반영됩니다.

특성 동기화는 Azure AD의 값이 온-프레미스 특성의 값으로 직접 설정된 직접적일 수 있습니다. 또는 프로그래밍 방식식에서 동기화를 처리할 수 있습니다. 값을 채우기 위해 일부 논리 또는 결정을 수행해야 하는 경우 프로그래밍 방식의 식이 필요합니다.

예를 들어 mail 속성 "을john.smith@contoso.com사용하고 ""@contoso.com부분을 제거하고 "john.smith" 값만 흐르면 다음과 같은 것을 사용합니다.

`Replace([mail], "@contoso.com", , ,"", ,)`  

**샘플 입/출력:** <br>

* **입력**(메일): “john.smith@contoso.com”
* **출력**: "john.smith"

사용자 지정 식 및 구문을 작성하는 방법에 대한 자세한 내용은 [Azure Active Directory의 특성 매핑에 대한 식 작성을](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)참조하십시오.

다음 표에는 일반적인 특성과 공통 특성이 Azure AD와 동기화되는 방식이 나열되어 있습니다.


|온-프레미스 Active Directory|매핑 유형|Azure AD|
|-----|-----|-----|
|cn|직접|공통 이름
|countryCode|직접|countryCode|
|displayName|직접|displayName|
|givenName|식|givenName|
|objectGUID|직접|소스앵커리너이너|  
|userprincipalName|직접|userPrincipalName|
|프록시드레스|직접|ProxyAddress|

## <a name="view-the-schema"></a>스키마 보기
> [!WARNING]
> 클라우드 프로비저닝 구성은 서비스 주체를 만듭니다. 서비스 주체는 Azure 포털에 표시됩니다. Azure 포털의 서비스 주체 환경을 사용하여 특성 매핑을 수정해서는 안 됩니다.  이는 지원되지 않습니다.

스키마를 보고 확인하려면 다음 단계를 따르십시오.

1.  그래프 [탐색기로](https://developer.microsoft.com/graph/graph-explorer)이동합니다.
1.  글로벌 관리자 계정으로 로그인합니다.
1.  왼쪽에서 사용 **권한을 수정하고** **Directory.ReadWrite.All이** *동의되었는지*확인합니다.
1.  쿼리를 `https://graph.microsoft.com/beta/serviceprincipals/?$filter=startswith(Displayname,'Active')`실행합니다. 이 쿼리는 필터링된 서비스 주체 목록을 반환합니다.
1.  의 `"appDisplayName": "Active Directory to Azure Active Directory Provisioning"` 값을 찾아 `"id"`기록합니다.
    ```
    "value": [
            {
                "id": "00d41b14-7958-45ad-9d75-d52fa29e02a1",
                "deletedDateTime": null,
                "accountEnabled": true,
                "appDisplayName": "Active Directory to Azure Active Directory Provisioning",
                "appId": "1a4721b3-e57f-4451-ae87-ef078703ec94",
                "applicationTemplateId": null,
                "appOwnerOrganizationId": "47df5bb7-e6bc-4256-afb0-dd8c8e3c1ce8",
                "appRoleAssignmentRequired": false,
                "displayName": "Active Directory to Azure Active Directory Provisioning",
                "errorUrl": null,
                "homepage": "https://account.activedirectory.windowsazure.com:444/applications/default.aspx?metadata=AD2AADProvisioning|ISV9.1|primary|z",
                "loginUrl": null,
                "logoutUrl": null,
                "notificationEmailAddresses": [],
                "preferredSingleSignOnMode": null,
                "preferredTokenSigningKeyEndDateTime": null,
                "preferredTokenSigningKeyThumbprint": null,
                "publisherName": "Active Directory Application Registry",
                "replyUrls": [],
                "samlMetadataUrl": null,
                "samlSingleSignOnSettings": null,
                "servicePrincipalNames": [
                    "http://adapplicationregistry.onmicrosoft.com/adprovisioningtoaad/primary",
                    "1a4721b3-e57f-4451-ae87-ef078703ec94"
                ],
                "signInAudience": "AzureADMultipleOrgs",
                "tags": [
                    "WindowsAzureActiveDirectoryIntegratedApp"
                ],
                "addIns": [],
                "api": {
                    "resourceSpecificApplicationPermissions": []
                },
                "appRoles": [
                    {
                        "allowedMemberTypes": [
                            "User"
                        ],
                        "description": "msiam_access",
                        "displayName": "msiam_access",
                        "id": "a0326856-1f51-4311-8ae7-a034d168eedf",
                        "isEnabled": true,
                        "origin": "Application",
                        "value": null
                    }
                ],
                "info": {
                    "termsOfServiceUrl": null,
                    "supportUrl": null,
                    "privacyStatementUrl": null,
                    "marketingUrl": null,
                    "logoUrl": null
                },
                "keyCredentials": [],
                "publishedPermissionScopes": [
                    {
                        "adminConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on behalf of the signed-in user.",
                        "adminConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "id": "d40ed463-646c-4efe-bb3e-3fa7d0006688",
                        "isEnabled": true,
                        "type": "User",
                        "userConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on your behalf.",
                        "userConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "value": "user_impersonation"
                    }
                ],
                "passwordCredentials": []
            },
    ```
1. 값으로 바꾸고 `{Service Principal id}` 쿼리를 `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`실행합니다.
1. 의 `"id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976"` 값을 찾아 `"id"`기록합니다.
    ```
    {
                "id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976",
                "templateId": "AD2AADProvisioning",
                "schedule": {
                    "expiration": null,
                    "interval": "PT2M",
                    "state": "Active"
                },
                "status": {
                    "countSuccessiveCompleteFailures": 0,
                    "escrowsPruned": false,
                    "code": "Active",
                    "lastSuccessfulExecutionWithExports": null,
                    "quarantine": null,
                    "steadyStateFirstAchievedTime": "2019-11-08T15:48:05.7360238Z",
                    "steadyStateLastAchievedTime": "2019-11-20T16:17:24.7957721Z",
                    "troubleshootingUrl": "",
                    "lastExecution": {
                        "activityIdentifier": "2dea06a7-2960-420d-931e-f6c807ebda24",
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 15,
                        "countEscrowedRaw": 15,
                        "countExported": 0,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "2019-11-20T16:15:21.116098Z",
                        "timeEnded": "2019-11-20T16:17:24.7488681Z"
                    },
                    "lastSuccessfulExecution": {
                        "activityIdentifier": null,
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 0,
                        "countEscrowedRaw": 0,
                        "countExported": 5,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "0001-01-01T00:00:00Z",
                        "timeEnded": "2019-11-20T14:09:46.8855027Z"
                    },
                    "progress": [],
                    "synchronizedEntryCountByType": [
                        {
                            "key": "group to Group",
                            "value": 33
                        },
                        {
                            "key": "user to User",
                            "value": 3
                        }
                    ]
                },
                "synchronizationJobSettings": [
                    {
                        "name": "Domain",
                        "value": "{\"DomainFQDN\":\"contoso.com\",\"DomainNetBios\":\"CONTOSO\",\"ForestFQDN\":\"contoso.com\",\"ForestNetBios\":\"CONTOSO\"}"
                    },
                    {
                        "name": "DomainFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "DomainNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "ForestFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "ForestNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "QuarantineTooManyDeletesThreshold",
                        "value": "500"
                    }
                ]
            }
    ```
1. 이제 쿼리를 `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema`실행합니다.
 
    예: https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

   값을 `{Service Principal Id}` `{AD2ADD Provisioning Id}` 바꾸고 바꿉을 바꿉을 가지고 있습니다.

1. 이 쿼리는 스키마를 반환합니다.

   ![반환된 스키마](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>다음 단계

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 프로비저닝이란?](what-is-cloud-provisioning.md)
