---
title: Linux VM 시스템 할당 관리 ID를 사용하여 Azure AD Graph API에 액세스
description: Linux VM 시스템 할당 관리 ID를 사용하여 Azure AD Graph API에 액세스하는 프로세스를 안내하는 자습서입니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 481cb560daa26e59de2c78cc64bab9fb168eed58
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445408"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-ad-graph-api"></a>자습서: Linux VM 시스템 할당 관리 ID를 사용하여 Azure AD Graph API에 액세스

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice.md)]

이 자습서에서는 Linux VM(가상 머신)에 시스템 할당 관리 ID를 사용하여 Azure AD Graph API에 액세스하여 그룹 멤버 자격을 검색하는 방법을 보여 줍니다. Azure 리소스에 대한 관리 ID는 Azure에서 자동으로 관리되며 이를 사용하면 Azure AD 인증을 지원하는 서비스에 인증할 수 있으므로 코드에 자격 증명을 삽입할 필요가 없습니다.  

이 자습서에서는 Azure AD 그룹에서 VM ID의 멤버 자격을 쿼리합니다. 그룹 정보는 종종 권한 부여 결정에 사용됩니다. 내부적으로 VM의 관리 ID는 Azure AD의 **서비스 주체**로 표시됩니다. 

> [!div class="checklist"]
> * Azure에 연결
> * Azure AD에서 그룹에 VM ID 추가 
> * Azure AD Graph에 대한 VM ID 액세스 권한 부여 
> * VM ID를 사용하여 액세스 토큰을 가져온 다음, Azure AD Graph를 호출하는 데 사용

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Azure CLI 최신 버전 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)

- Azure AD Graph에 대한 VM ID 액세스 권한을 부여하려면 Azure AD에서 **글로벌 관리자** 역할을 계정에 할당해야 합니다.

## <a name="connect-to-azure-ad"></a>Azure에 연결

Azure AD에 연결하여 VM을 그룹에 할당하고 그룹 멤버 자격을 검색할 수 있는 권한을 VM에 부여해야 합니다.

```cli
az login
```

## <a name="add-your-vms-identity-to-a-group-in-azure-ad"></a>Azure AD에서 그룹에 VM의 ID 추가

Linux VM에서 시스템 할당 관리 ID를 사용하도록 설정하면 Azure AD에 서비스 주체가 만들어집니다.  그룹에 VM을 추가해야 합니다. Azure AD에서 그룹에 VM을 추가하는 방법에 대한 지침은 다음 문서를 참조하세요.

- [그룹 멤버 추가](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add)

## <a name="grant-your-vm-access-to-the-azure-ad-graph-api"></a>Azure AD Graph API에 대한 VM 액세스 권한 부여

Azure 리소스에 대한 관리 ID를 사용하면 코드에서 Azure AD 인증을 지원하는 리소스에 인증하기 위한 액세스 토큰을 가져올 수 있습니다. Microsoft Azure AD Graph API는 Azure AD 인증을 지원합니다. 이 단계에서는 Azure AD Graph에 대한 VM ID의 서비스 주체 액세스 권한을 부여하여 그룹 멤버 자격을 쿼리할 수 있습니다. 서비스 주체에게는 **애플리케이션 권한**을 통해 Microsoft 또는 Azure AD Graph에 대한 액세스 권한이 부여됩니다. 부여해야 하는 애플리케이션 권한의 유형은 MS 또는 Azure AD Graph에서 액세스하려는 엔터티에 따라 다릅니다.

이 자습서에서는 `Directory.Read.All` 애플리케이션 권한을 사용하여 그룹 멤버 자격을 쿼리할 수 있는 능력을 VM ID에 부여합니다. 이 권한을 부여하려면 Azure AD에서 글로벌 관리자 역할이 할당된 사용자 계정이 필요합니다. 일반적으로 Azure Portal에서 애플리케이션의 등록으로 이동하고, 여기서 권한을 추가하여 애플리케이션 권한을 부여합니다. 그러나 Azure 리소스에 대한 관리 ID는 Azure AD에서 애플리케이션 개체를 등록하지 않고 서비스 주체만 등록합니다. 애플리케이션 권한을 등록하려면 Azure AD PowerShell 명령줄 도구를 사용합니다. 

Azure AD Graph:
- 서비스 주체 appId(앱 권한을 부여할 때 사용됨): 00000002-0000-0000-c000-000000000000
- 리소스 ID(Azure 리소스에 대한 관리 ID에서 액세스 토큰을 요청할 때 사용됨): https://graph.windows.net
- 사용 권한 범위 참조: [Azure AD Graph 사용 권한 참조](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

### <a name="grant-application-permissions-using-curl"></a>CURL을 사용하여 애플리케이션 권한 부여

1. CURL을 요청하는 토큰을 검색합니다.

   ```cli
   az account get-access-token --resource "https://graph.windows.net/"
   ```

2. VM의 `objectId`를 검색하여 적어 두어야 합니다. 이후 단계에서 그룹 멤버 자격을 읽을 수 있는 권한을 VM에 부여하는 데 사용됩니다. `<ACCESS TOKEN>`을 앞의 단계에서 검색한 액세스 토큰으로 바꿉니다.

   ```bash
   curl 'https://graph.windows.net/myorganization/servicePrincipals?$filter=startswith%28displayName%2C%27myVM%27%29&api-version=1.6' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

3. Azure AD Graph appID인 00000002-0000-0000-c000-000000000000을 사용하여 `odata.type: Microsoft.DirectoryServices.ServicePrincipal`에 대한 `objectId` 및 `Directory.Read.All` 앱 역할 권한에 대한 `id`를 검색하여 적어 둡니다.  `<ACCESS TOKEN>`을 앞에서 검색한 액세스 토큰으로 바꿉니다.

   ```bash
   curl "https://graph.windows.net/myorganization/servicePrincipals?api-version=1.6&%24filter=appId%20eq%20'00000002-0000-0000-c000-000000000000'" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   응답:

   ```json
   "odata.metadata":"https://graph.windows.net/myorganization/$metadata#directoryObjects",
   "value":[
      {
         "odata.type":"Microsoft.DirectoryServices.ServicePrincipal",
         "objectType":"ServicePrincipal",
         "objectId":"81789304-ff96-402b-ae73-07ec0db26721",
         "deletionTimestamp":null,
         "accountEnabled":true,
         "addIns":[
         ],
         "alternativeNames":[
         ],
         "appDisplayName":"Windows Azure Active Directory",
         "appId":"00000002-0000-0000-c000-000000000000",
         "appOwnerTenantId":"f8cdef31-a31e-4b4a-93e4-5f571e91255a",
         "appRoleAssignmentRequired":false,
         "appRoles":[
            {
               "allowedMemberTypes":[
                  "Application"
               ],
               "description":"Allows the app to read data in your company or school directory, such as users, groups, and apps.",
               "displayName":"Read directory data",
               "id":"5778995a-e1bf-45b8-affa-663a9f3f4d04",
               "isEnabled":true,
               "value":"Directory.Read.All"
            },
            {
               //other appRoles values
            }
   ``` 

4. 이제 Azure AD Graph API를 사용하여 Azure AD 디렉터리 개체에 VM의 서비스 주체 읽기 액세스 권한을 부여합니다.  `id` 값은 `Directory.Read.All` 앱 역할 권한에 대한 값이고, `resourceId`는 서비스 주체인 `odata.type:Microsoft.DirectoryServices.ServicePrincipal`에 대한 `objectId`입니다(이전 단계에서 적어 둔 값).

   ```bash
   curl "https://graph.windows.net/myorganization/servicePrincipals/<VM Object ID>/appRoleAssignments?api-version=1.6" -X POST -d '{"id":"5778995a-e1bf-45b8-affa-663a9f3f4d04","principalId":"<VM Object ID>","resourceId":"81789304-ff96-402b-ae73-07ec0db26721"}'-H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ``` 
 
## <a name="get-an-access-token-using-the-vms-identity-to-call-azure-ad-graph"></a>VM ID를 사용하여 액세스 토큰을 가져와서 Azure AD Graph를 호출 

아래의 단계를 완료하려면 SSH 클라이언트가 필요합니다. Windows를 사용 중인 경우 [Linux용 Windows 하위 시스템](https://msdn.microsoft.com/commandline/wsl/about)에서 SSH 클라이언트를 사용할 수 있습니다. SSH 클라이언트의 키 구성에 대한 도움이 필요하면 [Azure에서 Windows를 통해 SSH 키를 사용하는 방법](../../virtual-machines/linux/ssh-from-windows.md) 또는 [Azure에서 Linux VM용 SSH 공개 및 프라이빗 키 쌍을 만들고 사용하는 방법](../../virtual-machines/linux/mac-create-ssh-keys.md)을 참조하세요.

1. Portal에서 Linux VM으로 이동한 다음 **개요**에서 **연결**을 클릭합니다.  
2. 선택한 SSH 클라이언트를 사용하여 VM에 **연결**합니다. 
3. 터미널 창에서 CURL을 사용하여 Azure 리소스 엔드포인트에 대한 로컬 관리 ID를 요청하여 Azure AD Graph에 대한 액세스 토큰을 가져옵니다.  
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://graph.windows.net' -H Metadata:true
   ```    
   응답에는 Azure AD Graph에 액세스하는 데 필요한 액세스 토큰이 포함되어 있습니다.

   응답:

   ```bash
   {
       "access_token":"eyJ0eXAiOiJKV...",
       "expires_in":"3599",
       "expires_on":"1519622535",
       "not_before":"1519618635",
       "resource":"https://graph.windows.net",
       "token_type":"Bearer"
   }
   ```

4. VM의 서비스 주체의 개체 ID(이전 단계에서 검색한 값)를 사용하여 Azure AD Graph API를 쿼리함으로써 해당 그룹 멤버 자격을 검색할 수 있습니다. `<OBJECT-ID>`를 VM의 서비스 주체에 대한 개체 ID로 바꾸고, `<ACCESS-TOKEN>`을 이전에 얻은 액세스 토큰으로 바꿉니다.

   ```bash
   curl 'https://graph.windows.net/myorganization/servicePrincipals/<OBJECT-ID>/getMemberGroups?api-version=1.6' -X POST -d "{\"securityEnabledOnly\": false}" -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS-TOKEN>"
   ```

   응답:

   ```bash   
   Content : {"odata.metadata":"https://graph.windows.net/myorganization/$metadata#Collection(Edm.String)","value":["<ObjectID of VM's group membership>"]}
   ```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Linux VM 시스템 할당 관리 ID를 사용하여 Azure AD Graph에 액세스하는 방법을 알아보았습니다.  Azure AD Graph에 대한 자세한 내용은 다음을 참조하세요.

>[!div class="nextstepaction"]
>[Azure AD Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)
