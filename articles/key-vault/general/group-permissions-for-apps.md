---
title: 애플리케이션에 Azure Key Vault에 대한 액세스 권한 부여 - Azure Key Vault | Microsoft Docs
description: 액세스 제어 정책을 사용하여 서비스 주체를 수동으로 등록하고 Azure Key Vault에 대한 액세스 권한을 제공하는 방법에 대해 알아봅니다. 이 방법은 경우에 따라 필요할 수도 있습니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 09/27/2019
ms.author: mbaldwin
ms.openlocfilehash: d0607b1adb76fd32ce9f4dc9ebede92c7aa96862
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588758"
---
# <a name="provide-key-vault-authentication-with-an-access-control-policy"></a>액세스 제어 정책을 사용하여 Key Vault 인증 제공

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

클라우드 기반 애플리케이션을 Key Vault에 인증하는 가장 간단한 방법은 관리 ID를 사용하는 것입니다. 자세한 내용은 [App Service 관리 ID를 사용하여 Azure Key Vault에 액세스](managed-identity.md)를 참조하세요.  온-프레미스 애플리케이션을 만들거나 로컬 개발을 수행하거나 관리 ID를 사용할 수 없는 경우, 대신 서비스 주체를 수동으로 등록하고 액세스 제어 정책을 사용하여 키 자격 증명 모음에 액세스할 수 있습니다.  

키 자격 증명 모음은 각 항목에서 고유한 권한 세트를 "보안 주체"에 부여하는 최대 1,024개의 액세스 정책 항목을 지원합니다.   예를 들어 [.NET용 Azure Key Vault 클라이언트 라이브러리 빠른 시작](../secrets/quick-create-net.md)의 콘솔 앱에서 키 자격 증명 모음에 액세스하는 방법은 다음과 같습니다.

Key Vault 액세스 제어에 대한 자세한 내용은 [Azure Key Vault 보안: ID 및 액세스 관리](overview-security.md#identity-and-access-management)를 참조하세요. 액세스 제어에 대한 자세한 내용은 다음을 참조하세요. 

- [키](../keys/index.yml)
- [비밀 액세스 제어](../secrets/index.yml)
- [인증서 액세스 제어](../certificates/index.yml)

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>필수 구성 요소

- 키 자격 증명 모음. 다음 빠른 시작 중 하나의 단계에 따라 기존 키 자격 증명 모음을 사용하거나 새로 만들 수 있습니다.
   - [Azure CLI를 사용하여 키 자격 증명 모음 만들기](../secrets/quick-create-cli.md)
   - [Azure PowerShell을 사용하여 키 자격 증명 모음 만들기](../secrets/quick-create-powershell.md)
   - [Azure Portal을 사용하여 키 자격 증명 모음 만들기](../secrets/quick-create-portal.md)
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 또는 [Azure PowerShell](/powershell/azure/). 또는 [Azure Portal](https://portal.azure.com)을 사용할 수 있습니다.

## <a name="grant-access-to-your-key-vault"></a>키 자격 증명 모음에 대한 액세스 권한 부여

각각의 키 자격 증명 모음 액세스 정책 항목은 고유한 권한 세트를 보안 주체에 부여합니다.

- **애플리케이션** - 클라우드 기반 애플리케이션인 경우 [관리 ID를 대신 사용하여(가능한 경우) Azure Key Vault에 액세스](managed-identity.md)해야 합니다.
- **Azure AD 그룹** - 키 자격 증명 모음은 1,024개의 액세스 정책 항목만 지원하지만, 여러 애플리케이션과 사용자를 단일 Azure AD 그룹에 추가한 다음, 해당 그룹을 단일 항목으로 액세스 제어 정책에 추가할 수 있습니다.
- **사용자** - 키 자격 증명 모음에 대한 직접 액세스 권한을 사용자에게 **부여하지 않는 것이 좋습니다**. 사용자를 Azure AD 그룹에 추가한 다음, 키 자격 증명 모음에 대한 액세스 권한을 이 그룹에 부여하는 것이 가장 좋습니다. [Azure Key Vault 보안: ID 및 액세스 관리](overview-security.md#identity-and-access-management)를 참조하세요.


### <a name="get-the-objectid"></a>objectId 가져오기

키 자격 증명 모음에 대한 액세스 권한을 애플리케이션, Azure AD 그룹 또는 사용자에게 부여하려면 먼저 objectId를 가져와야 합니다.

#### <a name="applications"></a>애플리케이션

애플리케이션의 objectId는 연결된 서비스 주체와 일치합니다. 서비스 주체에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 및 서비스 주체 개체](../../active-directory/develop/app-objects-and-service-principals.md)를 참조하세요. 

애플리케이션의 objectId를 가져오는 방법에는 두 가지가 있습니다.  첫 번째 방법은 애플리케이션을 Azure Active Directory에 등록하는 것입니다. 이렇게 하려면 빠른 시작의 [Microsoft ID 플랫폼을 사용하여 애플리케이션 등록](../../active-directory/develop/quickstart-register-app.md)의 단계를 수행합니다. 등록이 완료되면 objectId가 "애플리케이션(클라이언트) ID"로 나열됩니다.

두 번째 방법은 터미널 창에서 서비스 주체를 만드는 것입니다. Azure CLI를 통해 [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 명령을 사용하고 "http://&lt;my-unique-service-principal-name&gt;" 형식으로 -n 플래그에 고유한 서비스 사용자 이름을 제공합니다.

```azurecli-interactive
az ad sp create-for-rbac -n "http://<my-unique-service-principal-name"
```

출력에서 objectId가 `clientID`로 나열됩니다.

Azure PowerShell을 사용하는 경우 [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal?view=azps-2.7.0) cmdlet을 사용합니다.


```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName <my-unique-service-principal-name>
```

출력에서 objectId가 `Id`(`ApplicationId`가 아님)로 나열됩니다.

#### <a name="azure-ad-groups"></a>Azure AD 그룹

여러 애플리케이션과 사용자를 Azure AD 그룹에 추가한 다음, 키 자격 증명 모음에 대한 액세스 권한을 해당 그룹에 부여할 수 있습니다.  자세한 내용은 아래의 [Azure AD 그룹에 멤버 만들기 및 추가](#creating-and-adding-members-to-an-azure-ad-group) 섹션을 참조하세요.

Azure CLI를 사용하여 Azure AD 그룹의 objectId를 찾으려면 [az ad group list](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) 명령을 사용합니다. 조직에 많은 그룹이 있을 수 있으므로 `--display-name` 매개 변수에 대한 검색 문자열도 제공해야 합니다.

```azurecli-interactive
az ad group list --display-name <search-string>
```
JSON에서 objectId가 반환됩니다.

```json
    "objectId": "48b21bfb-74d6-48d2-868f-ff9eeaf38a64",
    "objectType": "Group",
    "odata.type": "Microsoft.DirectoryServices.Group",
```

Azure PowerShell을 사용하여 Azure AD 그룹의 objectId를 찾으려면 [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) cmdlet을 사용합니다. 조직에 많은 그룹이 있을 수 있으므로 `-SearchString` 매개 변수에 대한 검색 문자열도 제공하는 것이 좋습니다.

```azurepowershell-interactive
Get-AzADGroup -SearchString <search-string>
```

출력에서 objectId가 `Id`로 나열됩니다.

```console
...
Id                    : 1cef38c4-388c-45a9-b5ae-3d88375e166a
...
```

#### <a name="users"></a>사용자

개별 사용자를 키 자격 증명 모음의 액세스 제어 정책에 추가할 수도 있습니다. **이 방법은 추천되지 않습니다.** 대신 사용자를 Azure AD 그룹에 추가하고, 해당 그룹을 정책에 추가하는 것이 좋습니다.

그럼에도 불구하고 Azure CLI를 사용하여 사용자를 찾으려면 [az ad user show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) 명령을 사용하여 사용자 이메일 주소를 `--id` 매개 변수에 전달합니다.


```azurecli-interactive
az ad user show --id <email-address-of-user>
```

출력에서 사용자의 objectId가 반환됩니다.

```console
  ...
  "objectId": "f76a2a6f-3b6d-4735-9abd-14dccbf70fd9",
  "objectType": "User",
  ...
```

Azure PowerShell을 사용하여 사용자를 찾으려면 [Get-AzADUser](/powershell/module/az.resources/get-azaduser?view=azps-2.7.0) cmdlet을 사용하여 사용자 이메일 주소를 `-UserPrincipalName` 매개 변수에 전달합니다.

```azurepowershell-interactive
 Get-AzAdUser -UserPrincipalName <email-address-of-user>
```

출력에서 사용자의 objectId가 `Id`로 반환됩니다.

```console
...
Id                : f76a2a6f-3b6d-4735-9abd-14dccbf70fd9
Type              :
```

### <a name="give-the-principal-access-to-your-key-vault"></a>키 자격 증명 모음에 대한 액세스 권한을 보안 주체에 부여

이제 보안 주체의 objectID가 있으므로 키와 비밀 모두에 대한 get(가져오기), list(나열), set(설정) 및 delete(삭제) 권한을 부여하는 키 자격 증명 모음 및 원하는 모든 추가 권한에 대한 액세스 정책을 만들 수 있습니다.

Azure CLI를 사용하는 경우 이 작업은 objectId를 [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 명령에 전달하여 수행됩니다.

```azurecli-interactive
az keyvault set-policy -n <your-unique-keyvault-name> --spn <ApplicationID-of-your-service-principal> --secret-permissions get list set delete --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

Azure PowerShell을 사용하는 경우 이 작업은 objectId를 [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) cmdlet에 전달하여 수행됩니다. 

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <your-key-vault-name> -PermissionsToKeys create,decrypt,delete,encrypt,get,list,unwrapKey,wrapKey -PermissionsToSecrets get,list,set,delete -ObjectId <Id>

```

## <a name="creating-and-adding-members-to-an-azure-ad-group"></a>Azure AD 그룹에 멤버 만들기 및 추가

Azure AD 그룹을 만들고, 애플리케이션과 사용자를 그룹에 추가하고, 키 자격 증명 모음에 대한 액세스 권한을 그룹에 부여할 수 있습니다.  이렇게 하면 여러 애플리케이션을 단일 액세스 정책 항목으로 키 자격 증명 모음에 추가할 수 있으며, 사용자가 키 자격 증명 모음에 직접 액세스(추천되지 않음)할 필요가 없습니다. 자세한 내용은 [Azure Active Directory 그룹을 사용하여 앱 및 리소스 액세스 관리](../../active-directory/fundamentals/active-directory-manage-groups.md)를 참조하세요.

### <a name="additional-prerequisites"></a>추가 필수 조건

[위의 필수 조건](#prerequisites) 외에도 Azure Active Directory 테넌트에서 그룹을 만들거나 편집할 수 있는 권한이 필요합니다. 권한이 없는 경우 Azure Active Directory 관리자에게 문의합니다.

PowerShell을 사용하려면 [Azure AD PowerShell 모듈](https://www.powershellgallery.com/packages/AzureAD/2.0.2.50)도 필요합니다.

### <a name="create-an-azure-active-directory-group"></a>Azure Active Directory 그룹 만들기

Azure CLI [az ad group create](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) 명령 또는 Azure PowerShell [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup?view=azureadps-2.0) cmdlet을 사용하여 새 Azure Active Directory 그룹을 만듭니다.


```azurecli-interactive
az ad group create --display-name <your-group-display-name> --mail-nickname <your-group-mail-nickname>
```

```powershell
New-AzADGroup -DisplayName <your-group-display-name> -MailNickName <your-group-mail-nickname>
```

두 경우 모두 아래 단계에서 필요하므로 새로 만든 그룹(GroupId)을 적어 둡니다.

### <a name="find-the-objectids-of-your-applications-and-users"></a>애플리케이션 및 사용자의 objectId 찾기

Azure CLI에서 애플리케이션의 objectId는 `--show-mine` 매개 변수가 있는 [az ad sp list](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) 명령을 사용하여 찾을 수 있습니다.

```azurecli-interactive
az ad sp list --show-mine
```

Azure PowerShell에서 애플리케이션의 objectId는 [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal?view=azps-2.7.0) cmdlet을 통해 검색 문자열을 `-SearchString` 매개 변수에 전달하여 찾습니다.

```azurepowershell-interactive
Get-AzADServicePrincipal -SearchString <search-string>
```

사용자의 objectId를 찾으려면 위의 [사용자](#users) 섹션에 나오는 단계를 따릅니다.

### <a name="add-your-applications-and-users-to-the-group"></a>애플리케이션 및 사용자를 그룹에 추가

이제 새로 만든 Azure AD 그룹에 objectId를 추가합니다.

Azure CLI를 사용하는 경우 [az ad group member add](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add)를 사용하여 objectId를 `--member-id` 매개 변수에 전달합니다.


```azurecli-interactive
az ad group member add -g <groupId> --member-id <objectId>
```

Azure PowerShell을 사용하는 경우 [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember?view=azps-2.7.0) cmdlet을 사용하여 objectId를 `-MemberObjectId` 매개 변수에 전달합니다.

```azurepowershell-interactive
Add-AzADGroupMember -TargetGroupObjectId <groupId> -MemberObjectId <objectId> 
```

### <a name="give-the-ad-group-access-to-your-key-vault"></a>키 자격 증명 모음에 대한 액세스 권한을 AD 그룹에 부여

마지막으로, Azure CLI [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 명령 또는 Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) cmdlet을 사용하여 키 자격 증명 모음에 대한 액세스 권한을 AD 그룹에 부여합니다. 예제는 위의 [키 자격 증명 모음에 대한 액세스 권한을 애플리케이션, Azure AD 그룹 또는 사용자에 부여](#give-the-principal-access-to-your-key-vault) 섹션을 참조하세요.

또한 애플리케이션에는 Key Vault에 할당된 ID 및 액세스 관리(IAM) 역할이 하나 이상 필요합니다. 그렇지 않으면 로그인 할 수 없고 구독에 액세스할 수 있는 권한이 없어 실패합니다.

> [!WARNING]
> 관리 ID가 있는 Azure AD 그룹은 토큰을 새로 고치고 적용하는 데 최대 8시간이 필요할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault 보안: ID 및 액세스 관리](overview-security.md#identity-and-access-management)
- [App Service 관리 ID를 사용하여 Key Vault 인증 제공](managed-identity.md)
- [키 자격 증명 모음 보안](secure-your-key-vault.md)).
- [Azure Key Vault 개발자 가이드](developers-guide.md)
- [Azure Key Vault 모범 사례](best-practices.md) 검토
