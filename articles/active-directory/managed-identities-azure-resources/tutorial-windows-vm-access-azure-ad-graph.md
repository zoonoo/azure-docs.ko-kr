---
title: Windows VM 시스템 할당 관리 ID를 사용하여 Azure AD Graph API에 액세스
description: 자습서에서는 Windows VM 시스템 할당 관리 ID를 사용하여 Azure AD Graph API에 액세스하는 프로세스를 단계별로 안내합니다.
services: active-directory
documentationcenter: ''
author: daveba
manager: daveba
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2018
ms.author: daveba
ms.openlocfilehash: 4396ab093b20e4a7fb89ddbfe821be117bb8ebfb
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54438680"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-ad-graph-api"></a>자습서: Windows VM 시스템 할당 관리 ID를 사용하여 Azure AD Graph API에 액세스

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice.md)]

이 자습서에서는 그룹 구성원 자격을 검색하기 위해 Windows VM(가상 머신)에 대한 시스템 할당 관리 ID를 사용하여 Microsoft Graph API에 액세스하는 방법을 보여줍니다. Azure 리소스에 대한 관리 ID는 Azure에서 자동으로 관리되며 이를 사용하면 Azure AD 인증을 지원하는 서비스에 인증할 수 있으므로 코드에 자격 증명을 삽입할 필요가 없습니다.  이 자습서에서는 Azure AD 그룹의 VM ID 구성원 자격을 쿼리합니다. 예를 들어 그룹 정보는 권한 부여 결정에 종종 사용됩니다. 내부에서 VM의 관리 ID는 Azure AD의 **서비스 주체**로 표시됩니다. 그룹 쿼리를 수행하기 전에 Azure AD의 그룹에 VM ID를 나타내는 서비스 주체를 추가합니다. 이 작업은 Azure PowerShell, Azure AD PowerShell 또는 Azure CLI를 사용하여 수행할 수 있습니다.

> [!div class="checklist"]
> * Azure에 연결
> * Azure AD에서 그룹에 VM ID 추가 
> * Azure AD Graph에 대한 VM ID 액세스 권한 부여 
> * VM ID를 사용하여 액세스 토큰을 가져온 다음, Azure AD Graph를 호출하는 데 사용

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- Azure AD Graph에 대한 VM ID 액세스 권한을 부여하려면 Azure AD에서 **글로벌 관리자** 역할을 계정에 할당해야 합니다.

## <a name="connect-to-azure-ad"></a>Azure에 연결

Azure AD에 연결하여 VM을 그룹에 할당하고 그룹 멤버 자격을 검색할 수 있는 권한을 VM에 부여해야 합니다.

```powershell
Connect-AzureAD
```

## <a name="add-your-vm-identity-to-a-group-in-azure-ad"></a>Azure AD의 그룹에 VM ID를 추가

Windows VM에서 시스템 할당 관리 ID를 사용하도록 설정한 경우 Azure AD에서 서비스 주체를 만들었습니다.  이제 그룹에 VM을 추가해야 합니다.  다음 예제에서는 Azure AD에서 새 그룹을 만들고 해당 그룹에 VM의 서비스 주체를 추가합니다.

```powershell
New-AzureADGroup -DisplayName "myGroup" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$AzureADGroup = Get-AzureADGroup -Filter "displayName eq 'myGroup'"
$ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq 'myVM'"
Add-AzureADGroupMember -ObjectId $AzureADGroup.ObjectID -RefObjectId $ManagedIdentitiesServicePrincipal.ObjectId
```
## <a name="grant-your-vm-access-to-the-azure-ad-graph-api"></a>Azure AD Graph API에 대한 VM 액세스 권한 부여

Azure 리소스에 대한 관리 ID를 사용하면 코드에서 Azure AD 인증을 지원하는 리소스에 인증하기 위한 액세스 토큰을 가져올 수 있습니다. Microsoft Azure AD Graph API는 Azure AD 인증을 지원합니다. 이 단계에서는 Azure AD Graph에 대한 VM ID의 서비스 주체 액세스 권한을 부여하여 그룹 멤버 자격을 쿼리할 수 있습니다. 서비스 주체에게는 **애플리케이션 권한**을 통해 Microsoft 또는 Azure AD Graph에 대한 액세스 권한이 부여됩니다. 부여해야 하는 애플리케이션 권한의 유형은 MS 또는 Azure AD Graph에서 액세스하려는 엔터티에 따라 다릅니다.

이 자습서에서는 ```Directory.Read.All``` 애플리케이션 권한을 사용하여 그룹 멤버 자격을 쿼리할 수 있는 능력을 VM ID에 부여합니다. 이 권한을 부여하려면 Azure AD에서 글로벌 관리자 역할이 할당된 사용자 계정이 필요합니다. 일반적으로 Azure Portal에서 애플리케이션의 등록으로 이동하고, 여기서 권한을 추가하여 애플리케이션 권한을 부여합니다. 그러나 Azure 리소스에 대한 관리 ID는 Azure AD에서 애플리케이션 개체를 등록하지 않고 서비스 주체만 등록합니다. 애플리케이션 권한을 등록하려면 Azure AD PowerShell 명령줄 도구를 사용합니다. 

Azure AD Graph:
- 서비스 주체 appId(앱 권한을 부여할 때 사용됨): 00000002-0000-0000-c000-000000000000
- 리소스 ID(Azure 리소스에 대한 관리 ID에서 액세스 토큰을 요청할 때 사용됨): https://graph.windows.net
- 사용 권한 범위 참조: [Azure AD Graph 사용 권한 참조](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

### <a name="grant-application-permissions-using-azure-ad-powershell"></a>Azure AD PowerShell을 사용하여 애플리케이션 사용 권한 부여

이 옵션을 사용하려면 Azure AD PowerShell이 필요합니다. 설치하지 않은 경우 [최신 버전을 다운로드](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2)한 후 계속합니다.

1. PowerShell 창을 열고 Azure AD에 연결합니다.

   ```powershell
   Connect-AzureAD
   ```

2. 다음 PowerShell 명령을 실행하여 VM ID를 나타내는 서비스 주체에 ``Directory.Read.All`` 애플리케이션 사용 권한을 할당합니다.

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq 'myVM'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}
   New-AzureAdServiceAppRoleAssignment -ObjectId $ManagedIdentitiesServicePrincipal.ObjectId -PrincipalId $ManagedIdentitiesServicePrincipal.ObjectId -ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
   ``` 

   마지막 명령에서 출력은 이와 같이 할당의 ID를 반환해야 합니다.
        
   `ObjectId`:`gzR5KyLAiUOTiqFhNeWZWBtK7ZKqNJxAiWYXYVHlgMs`

   `ResourceDisplayName`:`Windows Azure Active Directory`

   `PrincipalDisplayName`:`myVM` 

   오류 `bad request, one or more properties are invalid`로 `New-AzureAdServiceAppRoleAssignment`에 대한 호출이 실패하는 경우 앱 사용 권한이 이미 VM ID의 서비스 주체에 할당될 수 있습니다. 다음 PowerShell 명령을 사용하여 VM ID와 Azure AD Graph 간에 애플리케이션 사용 권한이 이미 있는지 확인할 수 있습니다.

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq '<VM-NAME>'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}
   Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId | Where-Object {$_.Id -eq $AppRole.Id -and $_.PrincipalId -eq $ManagedIdentitiesServicePrincipal.ObjectId}
   ```

   다음 PowerShell 명령을 사용하여 Azure AD Graph에 부여된 모든 앱 사용 권한을 나열할 수 있습니다.

   ```powershell
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId
   ``` 

   다음 PowerShell 명령을 사용하여 Azure AD Graph용 VM ID에 부여된 앱 사용 권한을 제거할 수 있습니다.

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq '<VM-NAME>'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}   
   $ServiceAppRoleAssignment = Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId | Where-Object {$_.Id -eq $AppRole.Id -and $_.PrincipalId -eq $ManagedIdentitiesServicePrincipal.ObjectId}
   Remove-AzureADServiceAppRoleAssignment -AppRoleAssignmentId $ServiceAppRoleAssignment.ObjectId -ObjectId $ManagedIdentitiesServicePrincipal.ObjectId
   ```
 
## <a name="get-an-access-token-using-the-vms-identity-to-call-azure-ad-graph"></a>VM ID를 사용하여 액세스 토큰을 가져와서 Azure AD Graph를 호출 

Azure AD Graph의 인증을 위해 VM의 시스템 할당 관리 ID를 사용하려면 VM에서 요청해야 합니다.

1. 포털에서 **Virtual Machines**로 이동한 다음, Windows VM으로 이동하여 **개요** 블레이드에서 **연결**을 클릭합니다.  
2. Windows VM을 만들 때 사용한 **사용자 이름**과 **암호**를 입력합니다.
3. 이제 가상 머신에서 원격 데스크톱 연결을 만들었으므로 원격 세션에서 PowerShell을 엽니다.  
4. PowerShell의 Invoke-WebRequest를 사용하여 Azure AD Graph에 대한 액세스 토큰을 가져오도록 Azure 리소스 엔드포인트의 로컬 관리 ID에 요청합니다.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://graph.windows.net' -Method GET -Headers @{Metadata="true"}
   ```

   JSON 개체에서 PowerShell 개체로 응답을 변환합니다.

   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   응답에서 액세스 토큰을 추출합니다.

   ```powershell
   $AccessToken = $content.access_token
   ```

5. VM ID 서비스 주체의 개체 ID를 사용하여(이전 단계에서 선언된 변수에서 이 값을 검색할 수 있습니다: ``$ManagedIdentitiesServicePrincipal.ObjectId``), 해당 그룹 구성원 자격을 검색하려면 Azure AD Graph API를 쿼리할 수 있습니다. 이전 단계의 개체 ID로 <OBJECT ID>를 바꾸고 이전에 획득한 액세스 토큰으로 <ACCESS-TOKEN>을 바꿉니다.

   ```powershell
   Invoke-WebRequest 'https://graph.windows.net/<Tenant ID>/servicePrincipals/<VM Object ID>/getMemberGroups?api-version=1.6' -Method POST -Body '{"securityEnabledOnly":"false"}' -Headers @{Authorization="Bearer $AccessToken"} -ContentType "application/json"
   ```
   
   응답에는 이전 단계에서 VM의 서비스 주체를 추가한 그룹의 `Object ID`가 포함됩니다.

   응답:

   ```powershell   
   Content : {"odata.metadata":"https://graph.windows.net/<Tenant ID>/$metadata#Collection(Edm.String)","value":["<ObjectID of VM's group membership>"]}
   ```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Windows VM 시스템 할당 관리 ID를 사용하여 Azure AD Graph에 액세스하는 방법을 알아보았습니다.  Azure AD Graph에 대해 자세히 알려면 다음을 참조합니다.

>[!div class="nextstepaction"]
>[Azure AD Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)
