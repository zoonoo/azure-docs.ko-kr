---
title: PowerShell을 사용하여 관리 ID를 애플리케이션 역할에 할당 - Azure AD
description: PowerShell을 사용하여 다른 애플리케이션의 역할에 관리 ID 액세스 권한을 할당하기 위한 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: johndowns
manager: ''
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: jodowns
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5150c26d67b77008c11764cc6e51ca7085ffcb31
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784914"
---
# <a name="assign-a-managed-identity-access-to-an-application-role-using-powershell"></a>PowerShell을 사용하여 애플리케이션 역할에 관리 ID 액세스 권한 할당

Azure 리소스에 대한 관리 ID는 Azure Active Directory에서 ID를 Azure 서비스에 제공합니다. 코드에 자격 증명이 없어도 작동합니다. Azure 서비스는 Azure AD 인증을 지원하는 서비스를 인증하는 데 이 ID를 사용할 수 있습니다. 애플리케이션 역할은 역할 기반 액세스 제어의 형식을 제공하며 서비스에서 권한 부여 규칙을 구현할 수 있도록 합니다.

이 문서에서는 Azure AD PowerShell을 사용하여 다른 애플리케이션에서 노출하는 애플리케이션 역할에 관리 ID를 할당하는 방법을 알아봅니다.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 구성 요소

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 관리 ID의 차이점](overview.md#managed-identity-types)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- 예제 스크립트를 실행하려면 다음 두 가지 옵션을 사용합니다.
    - 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보기** 단추를 사용하여 열 수 있는 [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용합니다.
    - 최신 버전의 [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)을 설치하여 스크립트를 로컬로 실행합니다.

## <a name="assign-a-managed-identity-access-to-another-applications-app-role"></a>다른 애플리케이션의 앱 역할에 관리 ID 액세스 권한 할당

1. [Azure VM과 같은](qs-configure-powershell-windows-vm.md) Azure 리소스에서 관리 ID를 사용하도록 설정합니다.

1. 관리 ID의 서비스 주체의 개체 ID를 찾습니다.

   **시스템 할당 관리 ID의 경우** 리소스 **ID** 페이지의 Azure Portal에서 개체 ID를 찾을 수 있습니다. 다음 PowerShell 스크립트를 사용하여 개체 ID를 찾을 수도 있습니다. 1단계에서 만든 리소스의 리소스 ID가 필요하며 이는 리소스 **속성** 페이지의 Azure Portal에서 사용할 수 있습니다.

    ```powershell
    $resourceIdWithManagedIdentity = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.Compute/virtualMachines/{my virtual machine name}'
    (Get-AzResource -ResourceId $resourceIdWithManagedIdentity).Identity.PrincipalId
    ```

    **사용자 할당 관리 ID의 경우** 리소스 **개요** 페이지의 Azure Portal에서 관리 ID의 개체 ID를 찾을 수 있습니다. 다음 PowerShell 스크립트를 사용하여 개체 ID를 찾을 수도 있습니다. 사용자 할당 관리 ID의 리소스 ID가 필요합니다.

    ```powershell
    $userManagedIdentityResourceId = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{my managed identity name}'
    (Get-AzResource -ResourceId $userManagedIdentityResourceId).Properties.PrincipalId
    ```

1. 관리 ID가 요청을 보낼 대상 서비스를 나타내는 새 애플리케이션 등록을 만듭니다. 관리 ID에 앱 역할 권한을 공개적으로 부여하는 API 또는 서비스에 Azure AD 테넌트의 서비스 주체가 이미 있는 경우 이 단계를 건너뜁니다. 예를 들어 Microsoft Graph API에 대한 관리 ID 액세스 권한을 부여하려는 경우 이 단계를 건너뛸 수 있습니다.

1. 서비스 애플리케이션의 서비스 주체 개체 ID를 찾습니다. Azure Portal을 사용하여 이러한 값을 확인할 수 있습니다. Azure Active Directory로 이동하여 **엔터프라이즈 애플리케이션** 페이지를 연 다음 애플리케이션을 찾고 **개체 ID** 를 찾습니다. 다음 PowerShell 스크립트를 사용하여 표시 이름으로 서비스 주체의 개체 ID를 찾을 수도 있습니다.

    ```powershell
    $serverServicePrincipalObjectId = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$applicationName'").ObjectId
    ```

    > [!NOTE]
    > 애플리케이션의 표시 이름은 고유하지 않으므로 올바른 애플리케이션의 서비스 주체를 찾았는지 확인해야 합니다.

1. 3단계에서 만든 애플리케이션에 [앱 역할](../develop/howto-add-app-roles-in-azure-ad-apps.md)을 추가합니다. 해당 역할은 Azure Portal 또는 Microsoft Graph를 통해 만들 수 있습니다. 예를 들어 다음과 같이 앱 역할을 추가할 수 있습니다.

    ```json
    {
        "allowedMemberTypes": [
            "Application"
        ],
        "displayName": "Read data from MyApi",
        "id": "0566419e-bb95-4d9d-a4f8-ed9a0f147fa6",
        "isEnabled": true,
        "description": "Allow the application to read data as itself.",
        "value": "MyApi.Read.All"
    }
    ```

1. 관리 ID에 앱 역할을 할당합니다. 앱 역할을 할당하려면 다음 정보가 필요합니다.
    * `managedIdentityObjectId`: 2단계에서 찾은 관리 ID의 서비스 주체의 개체 ID입니다.
    * `serverServicePrincipalObjectId`: 4단계에서 찾은 서버 애플리케이션의 서비스 주체의 개체 ID입니다.
    * `appRoleId`: 5단계에서 생성한 서버 앱에 의해 노출된 앱 역할의 ID입니다. 예에서 앱 역할 ID는 `0566419e-bb95-4d9d-a4f8-ed9a0f147fa6`입니다.
   
   다음 PowerShell 스크립트를 실행하여 역할 할당을 추가합니다.

    ```powershell
    New-AzureADServiceAppRoleAssignment -ObjectId $managedIdentityObjectId -Id $appRoleId -PrincipalId $managedIdentityObjectId -ResourceId $serverServicePrincipalObjectId
    ```

## <a name="complete-script"></a>전체 스크립트

이 예제 스크립트에서는 Azure 웹앱의 관리 ID를 앱 역할에 할당하는 방법을 보여 줍니다.

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$tenantID = '<tenant-id>'

# The name of your web app, which has a managed identity that should be assigned to the server app's app role.
$webAppName = '<web-app-name>'
$resourceGroupName = '<resource-group-name-containing-web-app>'

# The name of the server app that exposes the app role.
$serverApplicationName = '<server-application-name>' # For example, MyApi

# The name of the app role that the managed identity should be assigned to.
$appRoleName = '<app-role-name>' # For example, MyApi.Read.All

# Look up the web app's managed identity's object ID.
$managedIdentityObjectId = (Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $webAppName).identity.principalid

Connect-AzureAD -TenantId $tenantID

# Look up the details about the server app's service principal and app role.
$serverServicePrincipal = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$serverApplicationName'")
$serverServicePrincipalObjectId = $serverServicePrincipal.ObjectId
$appRoleId = ($serverServicePrincipal.AppRoles | Where-Object {$_.Value -eq $appRoleName }).Id

# Assign the managed identity access to the app role.
New-AzureADServiceAppRoleAssignment `
    -ObjectId $managedIdentityObjectId `
    -Id $appRoleId `
    -PrincipalId $managedIdentityObjectId `
    -ResourceId $serverServicePrincipalObjectId
```

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 관리 ID 개요](overview.md)
- Azure 가상 머신에서 관리 ID를 사용하려면 [PowerShell을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-powershell-windows-vm.md)을 참조하세요.
