---
title: CLI (Azure Key Vault 액세스 정책) 할당
description: Azure CLI를 사용 하 여 서비스 주체 또는 응용 프로그램 id에 Key Vault 액세스 정책을 할당 하는 방법입니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 59ba81944ecdf4f2b6322f4298e61df33f5b1da8
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289182"
---
# <a name="assign-a-key-vault-access-policy"></a>Key Vault 액세스 정책 할당

Key Vault 액세스 정책은 지정 된 서비스 주체 (즉, 응용 프로그램 또는 사용자 그룹)가 Key Vault [비밀](../secrets/index.yml), [키](../keys/index.yml)및 [인증서](../certificates/index.yml)에 대해 다른 작업을 수행할 수 있는지 여부를 결정 합니다. [Azure Portal](assign-access-policy-portal.md), Azure CLI (이 문서) 또는 [Azure PowerShell](assign-access-policy-powershell.md)를 사용 하 여 액세스 정책을 할당할 수 있습니다.

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Azure CLI를 사용 하 여 Azure Active Directory에서 그룹을 만드는 방법에 대 한 자세한 내용은 [az ad group create](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) 및 [az ad group member add](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add)를 참조 하세요.

## <a name="configure-the-azure-cli-and-sign-in"></a>Azure CLI 구성 및 로그인

1. Azure CLI 명령을 로컬로 실행 하려면 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)를 설치 합니다.
 
    클라우드에서 직접 명령을 실행 하려면 [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용 합니다.

1. 로컬 CLI만 해당: 다음을 사용 하 여 Azure에 로그인 합니다 `az login` .

    ```bash
    az login
    ```

    `az login`필요한 경우이 명령은 브라우저 창을 열어 자격 증명을 수집 합니다.

## <a name="acquire-the-object-id"></a>개체 ID 가져오기

액세스 정책을 할당 하려는 응용 프로그램, 그룹 또는 사용자의 개체 ID를 확인 합니다.

- 응용 프로그램 및 기타 서비스 사용자: [az ad sp list](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) 명령을 사용 하 여 서비스 사용자를 검색 합니다. 명령의 출력을 검토 하 여 액세스 정책을 할당 하려는 보안 주체의 개체 ID를 확인 합니다.

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- 그룹: [az ad group list](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) 명령을 사용 하 여 매개 변수를 사용 하 여 결과를 필터링 합니다 `--display-name` .

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- 사용자: [az ad user show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) 명령을 사용 하 여 사용자의 전자 메일 주소를 `--id` 매개 변수로 전달 합니다.

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>액세스 정책 할당
    
[Az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 명령을 사용 하 여 원하는 사용 권한을 할당 합니다.

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

`<object-id>`서비스 사용자의 개체 ID로 대체 합니다.

`--secret-permissions` `--key-permissions` 특정 형식에 대 한 사용 권한을 할당 하는 경우, 및만 포함 해야 `--certificate-permissions` 합니다. , 및에 허용 되는 값은 `<secret-permissions>` `<key-permissions>` `<certificate-permissions>` [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 설명서에 제공 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault 보안: ID 및 액세스 관리](overview-security.md#identity-and-access-management)
- [키 자격 증명 모음을 보호](secure-your-key-vault.md)합니다.
- [Azure Key Vault 개발자 가이드](developers-guide.md)
- [Azure Key Vault 모범 사례](best-practices.md)