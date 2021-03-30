---
title: 관리형 HSM 데이터 평면 역할 관리 - Azure Key Vault | Microsoft Docs
description: 이 문서를 사용하여 관리형 HSM에 대한 역할 할당을 관리합니다.
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 4d36b2c2178c7205246cd7c59aefedef3358e473
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951745"
---
# <a name="managed-hsm-role-management"></a>관리형 HSM 역할 관리

> [!NOTE]
> Key Vault는 자격 증명 모음과 관리형 HSM이라는 두 가지 유형의 리소스를 지원합니다. 이 문서는 **관리형 HSM** 에 대한 것입니다. 자격 증명 모음을 관리하는 방법을 알아보려면 [Azure CLI를 사용하여 Key Vault 관리](../general/manage-with-cli2.md)를 참조하세요.

관리형 HSM의 개요는 [관리형 HSM이란?](overview.md)을 참조하세요. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

이 문서에서는 관리형 HSM 데이터 평면의 역할을 관리하는 방법을 보여줍니다. 관리형 HSM 액세스 제어 모델에 대한 자세한 내용은 [관리형 HSM 액세스 제어](access-control.md)를 참조하세요.

보안 주체(예: 사용자, 서비스 주체, 그룹 또는 관리 ID)가 관리형 HSM 데이터 평면 작업을 수행할 수 있도록 하려면 해당 작업을 수행할 수 있는 역할이 할당되어야 합니다. 예를 들어 애플리케이션이 키를 사용하여 서명 작업을 수행할 수 있도록 하려면 "Microsoft.KeyVault/managedHSM/keys/sign/action"을 데이터 작업 중 하나로 포함하는 역할을 할당해야 합니다. 특정 범위에서 역할을 할당할 수 있습니다. 관리형 HSM 로컬 RBAC는 HSM 전체(`/` 또는 `/keys`) 및 키(`/keys/<keyname>`)당 두 개의 범위를 지원합니다.

모든 관리형 HSM 기본 제공 역할 및 허용되는 작업 목록은 [관리형 HSM 기본 제공 역할](built-in-roles.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서 Azure CLI 명령을 사용하려면 다음 항목이 있어야 합니다.

* Microsoft Azure에 대한 구독. 아직 구독하지 않은 경우 [평가판](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.
* Azure CLI 버전 2.21.0 이상 `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.
* 구독의 관리형 HSM. 관리형 HSM을 프로비저닝하고 활성화하려면 [빠른 시작: Azure CLI를 사용하여 관리형 HSM을 프로비저닝 및 활성화](quick-create-cli.md)를 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Azure에 로그인

CLI를 사용하여 Azure에 로그인하려면 다음을 입력합니다.

```azurecli
az login
```

CLI를 통한 로그인 옵션에 대한 자세한 내용은 [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli)을 참조하세요.

## <a name="create-a-new-role-assignment"></a>새 역할 할당 만들기

### <a name="assign-roles-for-all-keys"></a>모든 키에 역할 할당

`az keyvault role assignment create` 명령을 사용하여 ContosoHSM의 모든 **키**(범위 `/keys`)에 대해 사용자 주체 이름 **user2\@contoso.com** 으로 식별되는 사용자에게 **관리형 HSM Crypto 책임자** 역할을 할당합니다.

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys
```

### <a name="assign-role-for-a-specific-key"></a>특정 키에 대한 역할 할당

`az keyvault role assignment create` 명령을 사용하여 **myrsakey** 라는 특정 키에 대해 사용자 주체 이름 **user2\@contoso.com** 으로 식별되는 사용자에게 **관리형 HSM Crypto 책임자** 역할을 할당합니다.

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey
```

## <a name="list-existing-role-assignments"></a>기존 역할 할당 나열

`az keyvault role assignment list`를 사용하여 역할 할당을 나열합니다.

모든 사용자에 대한 범위 / (범위를 지정하지 않은 경우 기본값)의 모든 역할 할당(--담당자가 지정되지 않은 경우 기본값)

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM
```

특정 사용자 **user1@contoso.com** 에 대한 HSM 수준의 모든 역할 할당.

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user@contoso.com
```

> [!NOTE]
> 범위가 /(또는 /keys)인 경우 list 명령은 최상위 수준의 모든 역할 할당만 나열하고 개별 키 수준의 역할 할당은 표시하지 않습니다.

특정 키 **myrsakey** 에 대한 특정 사용자 **user2@contoso.com** 에 대한 모든 역할 할당.

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey
```

특정 키 **myrsakey** 에 대한 특정 사용자 **user2@contoso.com** 의 역할 **관리형 HSM Crypto 책임자** 에 대한 특정 역할 할당


```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey --role "Managed HSM Crypto Officer"
```

## <a name="delete-a-role-assignment"></a>역할 할당 삭제

`az keyvault role assignment delete` 명령을 사용하여 **myrsakey2** 키에 대해 사용자 **user2\@contoso.com** 에 할당된 **관리형 HSM Crypto 책임자** 역할을 삭제합니다.

```azurecli-interactive
az keyvault role assignment delete --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey2
```

## <a name="list-all-available-role-definitions"></a>사용 가능한 모든 역할 정의 나열

`az keyvault role definition list` 명령을 사용하여 모든 역할 정의를 나열합니다.

```azurecli-interactive
az keyvault role definition list --hsm-name ContosoMHSM
```

## <a name="create-a-new-role-definition"></a>새 역할 정의 만들기

Managed HSM에는 대부분의 일반적인 사용 시나리오에 유용한 몇 가지 기본 제공(미리 정의된) 역할이 있습니다. 역할이 수행할 수 있는 특정 작업 목록을 사용하여 고유한 역할을 정의할 수 있습니다. 그런 다음, 이 역할을 보안 주체에 할당하여 지정된 작업에 대한 권한을 부여할 수 있습니다. 

JSON 문자열을 사용하여 **내 사용자 지정 역할** 이라는 역할에 `az keyvault role definition create` 명령을 사용합니다.
```azurecli-interactive
az keyvault role definition create --hsm-name ContosoMHSM --role-definition '{
    "roleName": "My Custom Role",
    "description": "The description of the custom rule.",
    "actions": [],
    "notActions": [],
    "dataActions": [
        "Microsoft.KeyVault/managedHsm/keys/read/action"
    ],
    "notDataActions": []
}'
```

역할 정의에 대한 JSON 문자열을 포함하는 **my-custom-role-definition.json** 이라는 파일의 역할에 `az keyvault role definition create` 명령을 사용합니다. 위 예제를 참조하세요.
```azurecli-interactive
az keyvault role definition create --hsm-name ContosoMHSM --role-definition @my-custom-role-definition.json
```

## <a name="show-details-of-a-role-definition"></a>역할 정의에 대한 세부 정보 표시

`az keyvault role definition show` 명령을 통해 이름(GUID)을 사용하여 특정 역할 정의에 대한 세부 정보를 확인합니다.

```azurecli-interactive
az keyvault role definition show --hsm-name ContosoMHSM --name xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

## <a name="update-a-custom-role-definition"></a>사용자 지정 역할 정의 업데이트

`az keyvault role definition update` 명령을 통해 JSON 문자열을 사용하여 **내 사용자 지정 역할** 이라는 역할을 업데이트합니다.
```azurecli-interactive
az keyvault role definition create --hsm-name ContosoMHSM --role-definition '{
            "roleName": "My Custom Role",
            "name": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "id": "Microsoft.KeyVault/providers/Microsoft.Authorization/roleDefinitions/xxxxxxxx-
        xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "description": "The description of the custom rule.",
            "actions": [],
            "notActions": [],
            "dataActions": [
                "Microsoft.KeyVault/managedHsm/keys/read/action",
                "Microsoft.KeyVault/managedHsm/keys/write/action",
                "Microsoft.KeyVault/managedHsm/keys/backup/action",
                "Microsoft.KeyVault/managedHsm/keys/create"
            ],
            "notDataActions": []
        }'
```

## <a name="delete-custom-role-definition"></a>사용자 지정 역할 정의 삭제

`az keyvault role definition delete` 명령을 통해 이름(GUID)을 사용하여 특정 역할 정의에 대한 세부 정보를 확인합니다. 
```azurecli-interactive
az keyvault role definition delete --hsm-name ContosoMHSM --name xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

> [!NOTE]
> 기본 제공 역할은 삭제할 수 없습니다. 사용자 지정 역할을 삭제하면 해당 사용자 지정 역할을 사용하는 모든 역할 할당이 사라지게 됩니다.


## <a name="next-steps"></a>다음 단계

- [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/overview.md) 개요를 참조하세요.
- [관리형 HSM 역할 관리](role-management.md)에 대한 자습서를 참조하세요.
- [관리형 HSM 액세스 제어 모델](access-control.md)에 대한 자세한 정보
- 모든 [관리형 HSM 로컬 RBAC의 기본 제공 역할](built-in-roles.md)을 참조하세요.
