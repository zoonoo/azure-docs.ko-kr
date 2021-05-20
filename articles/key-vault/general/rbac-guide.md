---
title: Azure RBAC를 사용하여 애플리케이션에 Azure 키 자격 증명 모음에 대한 액세스 권한 부여 | Microsoft Docs
description: Azure 역할 기반 액세스 제어를 사용하여 키, 비밀 및 인증서에 대한 액세스를 제공하는 방법을 알아봅니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 216df0d128e0557345db8f82f6010e1ef681593c
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968784"
---
# <a name="provide-access-to-key-vault-keys-certificates-and-secrets-with-an-azure-role-based-access-control"></a>Azure 역할 기반 액세스 제어를 사용하여 Key Vault 키, 인증서 및 비밀에 대한 액세스 제공

> [!NOTE]
> Key Vault 리소스 공급자는 **자격 증명 모음** 과 **관리되는 HSM** 이라는 두 가지 리소스 유형을 지원합니다. 이 문서에서 설명하는 액세스 제어는 **자격 증명 모음** 에만 적용됩니다. 관리되는 HSM에 대한 액세스 제어에 대해 자세히 알아보려면 [관리되는 HSM 액세스 제어](../managed-hsm/access-control.md)를 참조하세요.

Azure RBAC(Azure 역할 기반 액세스 제어)는 Azure 리소스의 세밀한 액세스를 관리하는 [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 기반의 권한 부여 시스템입니다.

Azure RBAC를 통해 사용자는 키, 비밀 및 인증서 권한을 관리할 수 있습니다. 모든 키 자격 증명 모음에 대한 모든 사용 권한을 관리하는 한 곳을 제공합니다. 

Azure RBAC 모델은 관리 그룹, 구독, 리소스 그룹 또는 개별 리소스의 다양한 범위 수준에서 사용 권한을 설정하는 기능을 제공합니다.  키 자격 증명 모음에 대한 Azure RBAC는 개별 키, 비밀 및 인증서에 대한 별도의 권한을 제공하는 기능도 제공합니다.

자세한 내용은 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 참조하세요.

## <a name="best-practices-for-individual-keys-secrets-and-certificates"></a>개별 키, 비밀 및 인증서에 대한 모범 사례

환경별로 애플리케이션당 자격 증명 모음을 사용하는 것이 좋습니다(개발, 사전 프로덕션 및 프로덕션).

개별 키, 비밀 및 인증서 권한은 특정 시나리오에만 사용해야 합니다.

-   계층 간에 액세스 제어를 분리해야 하는 다중 계층 애플리케이션

-   여러 애플리케이션 간에 개별 비밀 공유

Azure Key Vault 관리 지침에 대한 자세한 내용은 다음을 참조하세요.

- [Azure Key Vault 보안 개요](security-overview.md)
- [Azure Key Vault 서비스 제한 사항](service-limits.md)

## <a name="azure-built-in-roles-for-key-vault-data-plane-operations"></a>Key Vault 데이터 평면 작업을 위한 Azure 기본 제공 역할
> [!NOTE]
> `Key Vault Contributor` 역할은 관리 평면 작업에서 키 자격 증명 모음을 관리하는 것입니다. 키, 비밀 및 인증서에 대한 액세스를 허용하지 않습니다.

| 기본 제공 역할 | Description | ID |
| --- | --- | --- |
| Key Vault 관리자| 인증서, 키, 비밀을 포함하여 키 자격 증명 모음 및 해당 키 자격 증명 모음에 있는 모든 개체에 대한 모든 데이터 평면 작업을 수행합니다. 키 자격 증명 모음 리소스를 관리하거나 역할 할당을 관리할 수 없습니다. 'Azure 역할 기반 액세스 제어' 권한 모델을 사용하는 키 자격 증명 모음에만 적용됩니다. | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
| Key Vault 인증서 책임자 | 권한 관리를 제외한 키 자격 증명 모음의 인증서에 대한 작업을 수행합니다. 'Azure 역할 기반 액세스 제어' 권한 모델을 사용하는 키 자격 증명 모음에만 적용됩니다. | a4417e6f-fecd-4de8-b567-7b0420556985 |
| Key Vault 암호화 책임자 | 권한 관리를 제외한 키 자격 증명 모음 키에 대한 작업을 수행합니다. 'Azure 역할 기반 액세스 제어' 권한 모델을 사용하는 키 자격 증명 모음에만 적용됩니다. | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
| Key Vault 암호화 서비스 암호화 사용자 | 키의 메타데이터를 읽고 래핑/래핑 해제 작업을 수행합니다. 'Azure 역할 기반 액세스 제어' 권한 모델을 사용하는 키 자격 증명 모음에만 적용됩니다. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
| Key Vault 암호화 사용자  | 키를 사용하여 암호화 작업을 수행합니다. 'Azure 역할 기반 액세스 제어' 권한 모델을 사용하는 키 자격 증명 모음에만 적용됩니다. | 12338af0-0e69-4776-bea7-57ae8d297424 |
| Key Vault 읽기 권한자 | 키 자격 증명 모음 및 해당 인증서, 키, 비밀의 메타데이터를 읽습니다. 비밀 콘텐츠 또는 키 자료와 같은 중요한 값을 읽을 수 없습니다. 'Azure 역할 기반 액세스 제어' 권한 모델을 사용하는 키 자격 증명 모음에만 적용됩니다. | 21090545-7ca7-4776-b22c-e363652d74d2 |
| Key Vault 비밀 책임자| 권한 관리를 제외한 키 자격 증명 모음의 비밀에 대한 작업을 수행합니다. 'Azure 역할 기반 액세스 제어' 권한 모델을 사용하는 키 자격 증명 모음에만 적용됩니다. | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
| Key Vault 비밀 사용자 | 비밀 콘텐츠를 읽습니다. 'Azure 역할 기반 액세스 제어' 권한 모델을 사용하는 키 자격 증명 모음에만 적용됩니다. | 4633458b-17de-408a-b874-0445c86b69e6 |

Azure 기본 제공 역할 정의에 대한 자세한 정보는 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요.

## <a name="using-azure-rbac-secret-key-and-certificate-permissions-with-key-vault"></a>Key Vault에서 Azure RBAC 비밀, 키 및 인증서 사용 권한 사용

키 자격 증명 모음에 대한 새 Azure RBAC 권한 모델은 자격 증명 모음 액세스 정책 권한 모델에 대한 대안을 제공합니다. 

### <a name="prerequisites"></a>필수 구성 요소

역할 할당을 추가하려면 다음이 필요합니다.

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- `Microsoft.Authorization/roleAssignments/write` 및 `Microsoft.Authorization/roleAssignments/delete` 사용 권한(예: [사용자 액세스 관리자](../../role-based-access-control/built-in-roles.md#user-access-administrator) 또는 [소유자](../../role-based-access-control/built-in-roles.md#owner))

### <a name="enable-azure-rbac-permissions-on-key-vault"></a>키 자격 증명 모음에 대한 Azure RBAC 사용 권한 설정

> [!NOTE]
> 권한 모델을 변경하려면 [소유자](../../role-based-access-control/built-in-roles.md#owner) 및 [사용자 액세스 관리자](../../role-based-access-control/built-in-roles.md#user-access-administrator) 역할의 일부인 'Microsoft.Authorization/roleAssignments/write' 권한이 있어야 합니다. '서비스 관리자' 및 '공동 관리자'와 같은 클래식 구독 관리자 역할은 지원되지 않습니다.

1.  새 키 자격 증명 모음에 대한 Azure RBAC 사용 권한 설정:

    ![Azure RBAC 사용 권한 설정 - 새 자격 증명 모음](../media/rbac/image-1.png)

2.  기존 키 자격 증명 모음에 대한 Azure RBAC 사용 권한 설정:

    ![Azure RBAC 사용 권한 설정 - 기존 자격 증명 모음](../media/rbac/image-2.png)

> [!IMPORTANT]
> Azure RBAC 권한 모델을 설정하면 모든 액세스 정책 권한이 무효화됩니다. 동일한 Azure 역할이 할당되지 않은 경우 중단될 수 있습니다.

### <a name="assign-role"></a>역할 할당

> [!Note]
> 스크립트에서 역할 이름 대신 고유한 역할 ID를 사용하는 것이 좋습니다. 따라서 역할의 이름을 바꾸면 스크립트는 계속 작동합니다. 이 문서에서 역할 이름은 가독성을 위해서만 사용됩니다.

역할 할당을 만들려면 다음 명령을 실행합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope <scope>
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName <role_name> -SignInName <assignee_upn> -Scope <scope>

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName Reader -ApplicationId <applicationId> -Scope <scope>
```
---

Azure Portal에서 Azure 역할 할당 화면은 액세스 제어(IAM) 탭의 모든 리소스에 대해 사용할 수 있습니다.

![역할 할당 - (IAM) 탭](../media/rbac/image-3.png)

### <a name="resource-group-scope-role-assignment"></a>리소스 그룹 범위 역할 할당

1.  키 자격 증명 모음 리소스 그룹으로 이동합니다.
    ![역할 할당 - 리소스 그룹](../media/rbac/image-4.png)

2.  액세스 제어(IAM) \> 역할 할당 추가\>추가 클릭

3.  현재 사용자에 대해 Key Vault 읽기 권한자 역할 "Key Vault 읽기 권한자" 만들기

    ![역할 추가 - 리소스 그룹](../media/rbac/image-5.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Reader" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Reader' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Reader' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```
---

위의 역할 할당은 키 자격 증명 모음에 키 자격 증명 모음 개체를 나열하는 기능을 제공합니다.

### <a name="key-vault-scope-role-assignment"></a>Key Vault 범위 역할 할당

1. Key Vault \> Access control(IAM) 탭으로 이동

2. 역할 할당 추가\>추가 클릭

3. 현재 사용자에 대한 키 비밀 책임자 역할 "Key Vault 비밀 책임자"를 만듭니다.

    ![역할 할당 - 키 자격 증명 모음](../media/rbac/image-6.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e jalichwa@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```
---

위의 역할 할당을 만든 후에는 비밀을 생성/업데이트/삭제할 수 있습니다.

4. 비밀 수준 역할 할당 테스트를 위한 새 비밀(비밀 \> +생성/가져오기)을 만듭니다.

    ![역할 추가 - 키 자격 증명 모음](../media/rbac/image-7.png)

### <a name="secret-scope-role-assignment"></a>비밀 범위 역할 할당

1. 이전에 만든 비밀 중 하나를 엽니다. 개요 및 액세스 제어(IAM) 

2. 액세스 제어(IAM) 탭 클릭

    ![역할 할당 - 비밀](../media/rbac/image-8.png)

3. 현재 사용자에 대한 키 비밀 책임자 역할 "Key Vault 비밀 책임자"를 만듭니다. 위에서 Key Vault에 대해 수행한 것과 동일합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```
---

### <a name="test-and-verify"></a>테스트 및 확인

> [!NOTE]
> 브라우저는 캐싱을 사용하며 역할 할당을 제거한 후 페이지를 새로 고쳐야 합니다.<br>
> 역할 할당을 새로 고칠 때까지 몇 분 정도 허용

1. 키 자격 증명 모음 수준에서 "Key Vault 비밀 담당자" 역할 없이 새 비밀을 추가하는 유효성을 검사합니다.

키 자격 증명 모음 액세스 제어(IAM) 탭으로 이동하여 이 리소스에 대한 "Key Vault 비밀 담당자" 역할 할당을 제거합니다.

![할당 제거 - 키 자격 증명 모음](../media/rbac/image-9.png)

이전에 만든 비밀로 이동합니다. 모든 비밀 속성을 볼 수 있습니다.

![액세스 권한이 있는 비밀 보기](../media/rbac/image-10.png)

새 비밀 만들기(비밀 \> + 생성/가져오기)는 아래 오류를 표시해야 합니다.

   ![새 비밀 만들기](../media/rbac/image-11.png)

2.  비밀 수준에서 "Key Vault 비밀 책임자" 역할 없이 비밀 편집의 유효성을 검사합니다.

-   이전에 만든 비밀 Access Control(IAM) 탭으로 이동하여 이 리소스에 대한 "Key Vault 비밀 담당자" 역할 할당을 제거합니다.

-   이전에 만든 비밀로 이동합니다. 비밀 속성을 볼 수 있습니다.

   ![액세스 권한 없이 비밀 보기](../media/rbac/image-12.png)

3. 키 자격 증명 모음 수준에서 읽기 권한자 역할 없이 읽은 비밀의 유효성을 검사합니다.

-   키 자격 증명 모음 리소스 그룹 액세스 제어(IAM) 탭으로 이동하여 "Key Vault 읽기 권한자" 역할 할당을 제거합니다.

-   키 자격 증명 모음의 비밀 탭으로 이동하면 다음과 같은 오류가 표시됩니다.

   ![비밀 탭 - 오류](../media/rbac/image-13.png)

### <a name="creating-custom-roles"></a>사용자 지정 역할 만들기 

[az role definition create command](/cli/azure/role/definition#az-role-definition-create)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role definition create --role-definition '{ \
   "Name": "Backup Keys Operator", \
   "Description": "Perform key backup/restore operations", \
    "Actions": [ 
    ], \
    "DataActions": [ \
        "Microsoft.KeyVault/vaults/keys/read ", \
        "Microsoft.KeyVault/vaults/keys/backup/action", \
         "Microsoft.KeyVault/vaults/keys/restore/action" \
    ], \
    "NotDataActions": [ 
   ], \
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] \
}'
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$roleDefinition = @"
{ 
   "Name": "Backup Keys Operator", 
   "Description": "Perform key backup/restore operations", 
    "Actions": [ 
    ], 
    "DataActions": [ 
        "Microsoft.KeyVault/vaults/keys/read ", 
        "Microsoft.KeyVault/vaults/keys/backup/action", 
         "Microsoft.KeyVault/vaults/keys/restore/action" 
    ], 
    "NotDataActions": [ 
   ], 
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] 
}
"@

$roleDefinition | Out-File role.json

New-AzRoleDefinition -InputFile role.json
```
---

사용자 지정 역할을 만드는 방법에 대한 자세한 내용은 다음을 참조하세요.

[Azure 사용자 지정 역할](../../role-based-access-control/custom-roles.md)

## <a name="known-limits-and-performance"></a>알려진 제한 및 성능

-   구독당 2000개의 Azure 역할 할당

-   역할 할당 대기 시간: 현재 예상 성능에서 역할이 적용되도록 역할 할당이 변경된 후 최대 10분(600초)이 소요됩니다.

## <a name="learn-more"></a>자세한 정보

- [Azure RBAC 개요](../../role-based-access-control/overview.md)
- [사용자 지정 역할 자습서](../../role-based-access-control/tutorial-custom-role-cli.md)