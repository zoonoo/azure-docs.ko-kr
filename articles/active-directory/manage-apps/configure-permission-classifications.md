---
title: Azure AD를 사용 하 여 권한 분류 구성
description: 위임 된 권한 분류를 관리 하는 방법을 알아봅니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: phsignor
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: 448106b1c514762c101991f5f944d521f7ea6f75
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368522"
---
# <a name="configure-permission-classifications"></a>권한 분류 구성

권한 분류를 사용하면 조직의 정책 및 위험 평가에 따라 서로 다른 권한에 적용되는 영향을 식별할 수 있습니다. 예를 들어 동의 정책에서 권한 분류를 사용하여 사용자가 동의할 수 있는 권한 세트를 식별할 수 있습니다.

## <a name="manage-permission-classifications"></a>권한 분류 관리

현재는 "낮은 영향" 권한 분류만 지원됩니다. 관리자 동의가 필요하지 않은 위임된 권한만 "낮은 영향"으로 분류할 수 있습니다.

> [!TIP]
> 기본 로그인을 수행 하는 데 필요한 최소 권한은 `openid` `profile` `email` `User.Read` `offline_access` Microsoft Graph에 대 한 모든 위임 된 권한으로,, 및입니다. 이러한 권한을 사용 하면 앱에서 로그인 한 사용자의 전체 프로필 정보를 읽고 사용자가 더 이상 앱을 사용 하지 않는 경우에도이 액세스를 유지할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal를 사용 하 여 사용 권한을 분류 하려면 다음 단계를 따르세요.

1. [전역 관리자](../roles/permissions-reference.md#global-administrator--company-administrator), [응용 프로그램 관리자](../roles/permissions-reference.md#application-administrator)또는 [클라우드 응용 프로그램 관리자 권한](../roles/permissions-reference.md#cloud-application-administrator) 으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.
1. **Azure Active Directory** > **엔터프라이즈 애플리케이션** > **동의 및 권한** > **권한 분류**를 선택합니다.
1. 다른 권한을 "낮은 영향"으로 분류하려면 **권한 추가**를 선택합니다.
1. API를 선택한 후 위임된 권한을 선택합니다.

이 예에서는 Single Sign-On에 필요한 최소 권한 세트를 분류했습니다.

:::image type="content" source="media/configure-permission-classifications/permission-classifications.png" alt-text="권한 분류":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

최신 Azure AD PowerShell 미리 보기 모듈 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true)를 사용하여 권한을 분류할 수 있습니다. 권한 분류는 권한을 게시하는 API의 **ServicePrincipal** 개체에 구성됩니다.

#### <a name="list-the-current-permission-classifications-for-an-api"></a>API에 대 한 현재 사용 권한 분류를 나열 합니다.

1. API의 **ServicePrincipal** 개체를 검색합니다. 여기서는 Microsoft Graph API의 ServicePrincipal 개체를 검색합니다.

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. API의 위임된 권한 분류를 읽습니다.

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="classify-a-permission-as-low-impact"></a>사용 권한을 "낮은 영향"으로 분류

1. API의 **ServicePrincipal** 개체를 검색합니다. 여기서는 Microsoft Graph API의 ServicePrincipal 개체를 검색합니다.

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. 분류하려는 위임된 권한을 찾습니다.

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. 권한 이름 및 ID를 사용하여 권한 분류를 설정합니다.

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="remove-a-delegated-permission-classification"></a>위임 된 권한 분류 제거

1. API의 **ServicePrincipal** 개체를 검색합니다. 여기서는 Microsoft Graph API의 ServicePrincipal 개체를 검색합니다.

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. 제거하려는 위임된 권한 분류를 찾습니다.

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. 권한 분류를 삭제합니다.

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

---

## <a name="next-steps"></a>다음 단계

자세히 알아보려면 다음을 수행합니다.

* [사용자 승인 설정 구성](configure-user-consent.md)
* [관리자 동의 워크플로 구성](configure-admin-consent-workflow.md)
* [애플리케이션에 대한 동의를 관리하고 동의 요청을 평가하는 방법 알아보기](manage-consent-requests.md)
* [애플리케이션에 대한 테넌트 전체 관리자 동의 부여](grant-admin-consent.md)
* [Microsoft ID 플랫폼의 권한 및 동의](../develop/active-directory-v2-scopes.md)

도움말을 얻거나 질문에 대한 답변을 찾으려면 다음을 수행합니다.
* [StackOverflow의 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
