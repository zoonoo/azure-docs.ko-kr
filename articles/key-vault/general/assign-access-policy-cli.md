---
title: Azure Key Vault 액세스 정책(CLI) 할당
description: Azure CLI를 사용하여 보안 주체 또는 애플리케이션 ID에 Key Vault 액세스 정책을 할당하는 방법입니다.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 349d7453962a736c9f15bb7d31d5a44098f463a4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791952"
---
# <a name="assign-a-key-vault-access-policy"></a>Key Vault 액세스 정책 할당

Key Vault 액세스 정책은 사용자, 애플리케이션, 사용자 그룹 등의 특정 보안 주체가 Key Vault [비밀](../secrets/index.yml), [키](../keys/index.yml), [인증서](../certificates/index.yml)에 대해 서로 다른 작업을 수행할 수 있는지 여부를 결정합니다. [Azure Portal](assign-access-policy-portal.md), Azure CLI(이 문서에서 설명) 또는 [Azure PowerShell](assign-access-policy-powershell.md)을 사용하여 액세스 정책을 할당할 수 있습니다.

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Azure CLI를 사용하여 Azure Active Directory에 그룹을 만드는 방법에 대한 자세한 내용은 [az ad group create](/cli/azure/ad/group#az_ad_group_create) 및 [az ad group member add](/cli/azure/ad/group/member#az_ad_group_member_add)를 참조하세요.

## <a name="configure-the-azure-cli-and-sign-in"></a>Azure CLI 구성 및 로그인

1. Azure CLI 명령을 로컬로 실행하려면 [Azure CLI](/cli/azure/install-azure-cli)를 설치합니다.
 
    클라우드에서 직접 명령을 실행하려면 [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용합니다.

1. 로컬 CLI만 해당: `az login`을 사용하여 Azure에 로그인합니다.

    ```bash
    az login
    ```

    필요한 경우 `az login` 명령은 브라우저 창을 열어 자격 증명을 수집합니다.

## <a name="acquire-the-object-id"></a>개체 ID 가져오기

액세스 정책을 할당할 애플리케이션, 그룹, 사용자의 개체 ID를 결정합니다.

- 애플리케이션 및 기타 서비스 사용자: [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) 명령을 사용하여 서비스 사용자를 검색합니다. 명령의 출력을 검사하여 액세스 정책을 할당할 보안 주체의 개체 ID를 확인합니다.

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- 그룹: [az ad group list](/cli/azure/ad/group#az_ad_group_list) 명령을 사용하여 `--display-name` 매개 변수로 결과를 필터링합니다.

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- 사용자: [az ad user show](/cli/azure/ad/user#az_ad_user_show) 명령을 사용하여 `--id` 매개 변수에 사용자의 이메일 주소를 전달합니다.

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>액세스 정책 할당
    
[Az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) 명령을 사용하여 원하는 사용 권한을 할당합니다.

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

`<object-id>`를 보안 주체의 개체 ID로 바꿉니다.

이러한 특정 유형에 권한을 할당할 때는 `--secret-permissions`, `--key-permissions`, `--certificate-permissions`만 포함해야 합니다. `<secret-permissions>`, `<key-permissions>` 및 `<certificate-permissions>`에 허용되는 값은 [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) 설명서에 제공됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault 보안: ID 및 액세스 관리](security-overview.md#identity-management)
- [키 자격 증명 모음 보안](security-overview.md)
- [Azure Key Vault 개발자 가이드](developers-guide.md)