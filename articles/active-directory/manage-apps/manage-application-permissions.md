---
title: 사용자 및 관리자 권한 관리 - Azure Active Directory | Microsoft Docs
description: Azure AD에서 애플리케이션에 대한 사용 권한을 검토하고 관리하는 방법을 알아봅니다. 예를 들어, 애플리케이션에 부여된 모든 사용 권한을 해지할 수 있습니다.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: iangithinji
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e8cf0459ecdf93251d1d59a9396b6ee11b7701c
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108160820"
---
# <a name="take-action-on-overprivileged-or-suspicious-applications-in-azure-active-directory"></a>Azure Active Directory에서 권한 초과 또는 의심스러운 애플리케이션에 대한 조치 수행

애플리케이션 사용 권한을 검토하고 관리하는 방법을 알아봅니다. 이 문서에서는 시나리오에 따라 애플리케이션을 보호하기 위해 수행할 수 있는 다양한 작업을 제시합니다. 이러한 작업은 사용자 또는 관리자 동의를 통해 Azure AD(Azure Active Directory) 테넌트에 추가된 모든 애플리케이션에 적용됩니다.

애플리케이션에 동의하는 방법에 대한 자세한 내용은 [Azure Active Directory 동의 프레임워크](../develop/consent-framework.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

다음 작업을 수행하려면 전역 관리자, 애플리케이션 관리자 또는 클라우드 애플리케이션 관리자 권한으로 로그인해야 합니다.

애플리케이션에 대한 액세스를 제한하려면 사용자 할당이 필요하며, 애플리케이션에 사용자 또는 그룹을 할당해야 합니다.  자세한 내용은 [사용자 및 그룹을 할당하는 메서드](./assign-user-or-group-access-portal.md)를 참조하세요.

Azure AD 포털에 액세스하여 작업을 수행할 상황별 PowerShell 스크립트를 가져올 수 있습니다.

1. 전역 관리자, 애플리케이션 관리자 또는 클라우드 애플리케이션 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **Enterprise 애플리케이션** 을 선택합니다.
3. 액세스를 제한할 애플리케이션을 선택합니다.
4. **권한** 을 선택합니다. 명령 모음에서 **사용 권한 검토** 를 선택합니다.

![사용 권한 검토 창의 스크린샷.](./media/manage-application-permissions/review-permissions.png)

## <a name="control-access-to-an-application"></a>애플리케이션에 대한 액세스 제어

**사용자 할당** 설정을 켜서 애플리케이션에 대한 액세스를 제한하는 것이 좋습니다.

1. 전역 관리자, 애플리케이션 관리자 또는 클라우드 애플리케이션 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **Enterprise 애플리케이션** 을 선택합니다.
3. 액세스를 제한할 애플리케이션을 선택합니다.
4. **속성** 을 선택하고 **필요한 사용자 요구 사항** 을 **예** 로 설정합니다.
5. **사용자 및 그룹** 을 선택하고 애플리케이션에 할당된 원치 않는 사용자를 제거합니다.
6. 애플리케이션에 사용자 또는 그룹을 할당합니다.

필요에 따라 PowerShell을 사용하여 애플리케이션에 할당된 모든 사용자를 제거할 수 있습니다.

## <a name="revoke-all-permissions-for-an-application"></a>애플리케이션에 대한 모든 사용 권한 취소

PowerShell 스크립트를 사용하여 이 애플리케이션에 부여된 모든 사용 권한을 취소합니다.

> [!NOTE]
> 현재 부여된 사용 권한을 취소하더라도 사용자는 애플리케이션에 다시 동의해야 합니다. 사용자 동의를 차단하려면 [사용자가 애플리케이션에 동의하는 방법 구성](configure-user-consent.md)을 참조하세요.

또는 애플리케이션을 사용하지 않도록 설정하여 사용자가 앱에 액세스하지 못하게 하고 애플리케이션에서 데이터에 액세스하지 못하게 할 수 있습니다.

1. 전역 관리자, 애플리케이션 관리자 또는 클라우드 애플리케이션 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **Enterprise 애플리케이션** 을 선택합니다.
3. 액세스를 제한할 애플리케이션을 선택합니다.
4. **속성** 을 선택한 다음 **사용자가 로그인 할 수 있도록 설정합니까?** 를 **아니요** 로 설정합니다.

## <a name="investigate-a-suspicious-application"></a>의심스러운 애플리케이션 조사

**사용자 할당** 설정을 켜서 애플리케이션에 대한 액세스를 제한하는 것이 좋습니다. 그런 다음 사용자 및 관리자가 애플리케이션에 부여한 사용 권한을 검토합니다.

1. 전역 관리자, 애플리케이션 관리자 또는 클라우드 애플리케이션 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
3. **Azure Active Directory** > **Enterprise 애플리케이션** 을 선택합니다.
5. 액세스를 제한할 애플리케이션을 선택합니다.
6. **속성** 을 선택하고 **필요한 사용자 요구 사항** 을 **예** 로 설정합니다.
7. **사용 권한** 을 선택하고 관리자와 사용자가 동의한 권한을 검토합니다.

필요에 따라 PowerShell을 사용하여 다음을 수행할 수 있습니다.

- 할당된 모든 사용자를 제거하여 애플리케이션에 로그인하지 못하게 합니다.
- 애플리케이션에 대한 액세스 권한이 있는 사용자의 새로 고침 토큰을 무효화합니다.
- 애플리케이션에 대한 모든 사용 권한을 취소합니다.

또는 애플리케이션을 사용하지 않도록 설정하여 사용자의 액세스를 차단하고 애플리케이션이 데이터에 액세스하는 것을 방지합니다.

## <a name="disable-a-malicious-application"></a>악성 애플리케이션 사용 안 함

애플리케이션을 사용하지 않도록 설정하여 사용자의 액세스를 차단하고 애플리케이션이 데이터에 액세스하는 것을 방지하는 것이 좋습니다. 애플리케이션을 삭제하면 이후 사용자가 애플리케이션에 다시 동의하고 데이터에 대한 액세스 권한을 부여할 수 있습니다.

1. 전역 관리자, 애플리케이션 관리자 또는 클라우드 애플리케이션 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **Enterprise 애플리케이션** 을 선택합니다.
3. 액세스를 제한할 애플리케이션을 선택합니다.
4. **속성** 을 선택하고 개체 ID를 복사합니다.

### <a name="powershell-commands"></a>PowerShell 명령

서비스 주체 개체 ID를 가져옵니다.

1. 전역 관리자, 애플리케이션 관리자 또는 클라우드 애플리케이션 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **Enterprise 애플리케이션** 을 선택합니다.
3. 액세스를 제한할 애플리케이션을 선택합니다.
4. **속성** 을 선택하고 개체 ID를 복사합니다.

   ```powershell
   $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
   $sp.ObjectId
   ```

애플리케이션에 할당된 모든 사용자를 제거합니다.

```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get Azure AD App role assignments using objectId of the Service Principal
$assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true

# Remove all users and groups assigned to the application
$assignments | ForEach-Object {
    if ($_.PrincipalType -eq "User") {
        Remove-AzureADUserAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
    } elseif ($_.PrincipalType -eq "Group") {
        Remove-AzureADGroupAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
    }
}
```

애플리케이션에 부여된 모든 권한을 철회합니다.

```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get all delegated permissions for the service principal
$spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

# Remove all delegated permissions
$spOAuth2PermissionsGrants | ForEach-Object {
    Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
}

# Get all application permissions for the service principal
$spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

# Remove all delegated permissions
$spApplicationPermissions | ForEach-Object {
    Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
}
```

새로 고침 토큰을 무효화합니다.

```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get Azure AD App role assignments using objectID of the Service Principal
$assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

# Revoke refresh token for all users assigned to the application
$assignments | ForEach-Object {
    Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
}
```

## <a name="next-steps"></a>다음 단계

- [애플리케이션에 대한 동의를 관리하고 동의 요청 평가](manage-consent-requests.md)
- [사용자 동의 구성](configure-user-consent.md)
- [관리자 동의 워크플로 구성](configure-admin-consent-workflow.md)
