---
title: Azure 키 자격 증명 모음 및 Azure CLI를 통해 저장소 계정 키 관리
description: 저장소 계정 키는 Azure 키 자격 증명 모음과 Azure 저장소 계정에 대한 키 기반 액세스 간에 원활한 통합을 제공합니다.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.openlocfilehash: 104f3423b07eaa3269ffccc054cd2f779bbdabf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199821"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>키 볼트 및 Azure CLI를 통해 저장소 계정 키 관리

Azure 저장소 계정은 계정 이름과 키로 구성된 자격 증명을 사용합니다. 키는 자동으로 생성되며 암호가 아닌 암호로 제공됩니다. 키 볼트는 [키 볼트 비밀로](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)저장하여 저장소 계정 키를 관리합니다. 

Key Vault 관리형 저장소 계정 키 기능을 사용하여 Azure 저장소 계정과 키를 나열(동기화)하고 주기적으로 키를 다시 생성(회전)할 수 있습니다. 저장소 계정과 클래식 저장소 계정 모두에 대한 키를 관리할 수 있습니다.

관리 저장소 계정 키 기능을 사용하는 경우 다음 사항을 고려하십시오.

- 키 값은 호출자에 대한 응답으로 반환되지 않습니다.
- 키 볼트만 저장소 계정 키를 관리해야 합니다. 키를 직접 관리하지 말고 Key Vault 프로세스를 방해하지 마십시오.
- 키 Vault 개체는 저장소 계정 키를 관리해야 합니다. 여러 개체에서 키 관리를 허용하지 마십시오.
- Key Vault에서 서비스 주체가 아닌 사용자 주체를 사용하여 저장소 계정을 관리하도록 요청할 수 있습니다.
- 키 볼트만 사용하여 키를 다시 생성합니다. 스토리지 계정 키를 수동으로 다시 생성하지 않습니다.

Microsoft의 클라우드 기반 ID 및 액세스 관리 서비스인 Azure Active Directory(Azure AD)와 Azure 저장소 통합을 사용하는 것이 좋습니다. Azure AD 통합은 [Azure Blob 및 큐에](../storage/common/storage-auth-aad.md)사용할 수 있으며 Azure 키 볼트와 마찬가지로 Azure 저장소에 대한 OAuth2 토큰 기반 액세스를 제공합니다.

Azure AD를 사용하면 저장소 계정 자격 증명 대신 응용 프로그램 또는 사용자 ID를 사용하여 클라이언트 응용 프로그램을 인증할 수 있습니다. Azure에서 실행할 때 [Azure AD 관리 ID를](/azure/active-directory/managed-identities-azure-resources/) 사용할 수 있습니다. 관리되는 ID는 클라이언트 인증및 응용 프로그램 또는 응용 프로그램과 함께 자격 증명을 저장할 필요가 없습니다.

Azure AD는 역할 기반 액세스 제어(RBAC)를 사용하여 키 볼트에서 지원하는 권한 부여를 관리합니다.

## <a name="service-principal-application-id"></a>서비스 주체 응용 프로그램 ID

Azure AD 테넌트는 등록된 각 응용 프로그램에 [서비스 주체를](/azure/active-directory/develop/developer-glossary#service-principal-object)제공합니다. 서비스 주체는 RBAC를 통해 다른 Azure 리소스에 액세스하기 위해 권한 부여 설정 중에 사용되는 응용 프로그램 ID역할을 합니다.

키 볼트는 모든 Azure AD 테넌트의 사전 등록된 Microsoft 응용 프로그램입니다. 키 볼트는 각 Azure 클라우드에서 동일한 응용 프로그램 ID로 등록됩니다.

| 테넌트 | 클라우드 | 애플리케이션 UI |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure 공용 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| 기타  | 모두 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>사전 요구 사항

이 가이드를 완료하려면 먼저 다음을 수행해야 합니다.

- [Azure CLI를 설치합니다.](/cli/azure/install-azure-cli)
- [키 자격 증명 모음 만들기](quick-create-cli.md)
- [Azure 저장소 계정을 만듭니다.](../storage/common/storage-account-create.md?tabs=azure-cli) 저장소 계정 이름은 소문자와 숫자만 사용해야 합니다. 이름의 길이는 3자에서 24자 사이여야 합니다.
      
## <a name="manage-storage-account-keys"></a>저장소 계정 키 관리

### <a name="connect-to-your-azure-account"></a>Azure 계정에 연결

[az 로그인](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 명령을 사용하여 Azure CLI 세션을 인증합니다.

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>저장소 계정에 키 볼트 액세스 권한 부여

Azure CLI [az 역할 할당 만들기](/cli/azure/role/assignment?view=azure-cli-latest) 명령을 사용하여 Key Vault가 저장소 계정에 액세스할 수 있도록 합니다. 명령에 다음 매개 변수 값을 제공합니다.

- `--role`: "저장소 계정 키 운영자 서비스 역할" RBAC 역할을 전달합니다. 이 역할은 저장소 계정에 대한 액세스 범위를 제한합니다. 클래식 저장소 계정의 경우 대신 "클래식 저장소 계정 키 운영자 서비스 역할"을 전달합니다.
- `--assignee-object-id`: Azure 공용 클라우드의 키 볼트에 대한 개체 ID인 "93c27d83-f79b-4cb2-8dd4-4aa716542e74"값을 전달합니다. Azure 정부 클라우드에서 키 자격 증명 모음에 대한 개체 ID를 얻으려면 [서비스 주체 응용 프로그램 ID를](#service-principal-application-id)참조하십시오.
- `--scope`: 양식에 `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`있는 저장소 계정 리소스 ID를 전달합니다. 구독 ID를 찾으려면 Azure CLI [az 계정 목록](/cli/azure/account?view=azure-cli-latest#az-account-list) 명령을 사용합니다. 저장소 계정 이름 및 저장소 계정 리소스 그룹을 찾으려면 Azure CLI [az 저장소 계정 목록](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) 명령을 사용합니다.

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

### <a name="create-a-key-vault-managed-storage-account"></a>키 볼트 관리 저장소 계정 만들기

 Azure CLI [az 키볼트](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add) 저장소 명령을 사용하여 키 볼트 관리 저장소 계정을 만듭니다. 재생 기간을 90일로 설정합니다. 90일이 지나면 키 볼트가 활성 키를 `key1` `key2` 에서 `key1`로 다시 생성하고 교환합니다. `key1`그러면 활성 키로 표시됩니다. 명령에 다음 매개 변수 값을 제공합니다.

- `--vault-name`: 키 볼트의 이름을 전달합니다. 키 자격 증명 모음의 이름을 찾으려면 Azure CLI [az 키 볼트 목록](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list) 명령을 사용합니다.
- `-n`: 저장소 계정의 이름을 전달합니다. 저장소 계정의 이름을 찾으려면 Azure CLI [az 저장소 계정 목록](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) 명령을 사용합니다.
- `--resource-id`: 양식에 `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`있는 저장소 계정 리소스 ID를 전달합니다. 구독 ID를 찾으려면 Azure CLI [az 계정 목록](/cli/azure/account?view=azure-cli-latest#az-account-list) 명령을 사용합니다. 저장소 계정 이름 및 저장소 계정 리소스 그룹을 찾으려면 Azure CLI [az 저장소 계정 목록](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) 명령을 사용합니다.
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>공유 액세스 서명 토큰

Key Vault에 공유 액세스 서명 토큰을 생성하도록 요청할 수도 있습니다. 공유 액세스 서명은 스토리지 계정의 리소스에 대한 위임된 권한을 제공합니다. 계정 키를 공유하지 않고 클라이언트에게 저장소 계정의 리소스에 대한 액세스 권한을 부여할 수 있습니다. 공유 액세스 서명은 계정 키를 손상시키지 않고 저장소 리소스를 안전하게 공유할 수 있는 방법을 제공합니다.

이 섹션의 명령은 다음 작업을 완료합니다.

- 계정 공유 액세스 서명 `<YourSASDefinitionName>`정의를 설정합니다. 정의는 키 자격 증명 모음의 `<YourStorageAccountName>` `<YourKeyVaultName>`키 자격 증명 모음관리 저장소 계정에 설정됩니다.
- Blob, 파일, 테이블 및 큐 서비스에 대한 계정 공유 액세스 서명 토큰을 만듭니다. 토큰은 리소스 유형 서비스, 컨테이너 및 개체에 대해 만들어집니다. 토큰은 모든 권한, https 를 초과하며 지정된 시작 및 종료 날짜로 만들어집니다.
- 볼트에서 키 볼트 관리 스토리지 공유 액세스 서명 정의를 설정합니다. 정의에는 생성된 공유 액세스 서명 토큰의 템플릿 URI가 있습니다. 정의에는 공유 액세스 서명 `account` 유형이 있으며 N 일 동안 유효합니다.
- 공유 액세스 서명이 키 자격 증명 모음에 비밀로 저장되었는지 확인합니다.

### <a name="create-a-shared-access-signature-token"></a>공유 액세스 서명 토큰 만들기

Azure CLI az 저장소 계정 [생성 sas](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) 명령을 사용하여 공유 액세스 서명 정의를 만듭니다. 이 작업에는 `storage` `setsas` 및 사용 권한이 필요합니다.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
작업이 성공적으로 실행되면 출력을 복사합니다.

```console
"se=2020-01-01&sp=***"
```

이 출력은 다음 단계에서 `--template-id` 매개 변수에 전달됩니다.

### <a name="generate-a-shared-access-signature-definition"></a>공유 액세스 서명 정의 생성

Azure CLI [az 키볼트 저장소 sas-정의 만들기](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create) 명령을 사용하여 이전 단계의 출력을 `--template-id` 매개 변수로 전달하여 공유 액세스 서명 정의를 만듭니다.  선택한 이름을 매개 변수에 `-n` 제공할 수 있습니다.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>공유 액세스 서명 정의 확인

Azure CLI [az 키볼트 비밀 목록](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) 및 az [keyvault 비밀 표시](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 명령을 사용하여 공유 액세스 서명 정의가 키 자격 증명 모음에 저장되었는지 확인할 수 있습니다.

먼저 [az keyvault 비밀 목록](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) 명령을 사용하여 키 자격 증명 모음에서 공유 액세스 서명 정의를 찾습니다.

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

SAS 정의에 해당하는 비밀에는 다음과 같은 속성이 있습니다.

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

이제 [az keyvault 비밀 표시](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 명령과 `id` 속성을 사용하여 해당 비밀의 내용을 볼 수 있습니다.

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

이 명령의 출력에는 SAS 정의`value`문자열이 로 표시됩니다.


## <a name="next-steps"></a>다음 단계

- [키, 암호 및 인증서에](https://docs.microsoft.com/rest/api/keyvault/)대해 자세히 알아봅니다.
- Azure 키 [볼트 팀 블로그의](https://blogs.technet.microsoft.com/kv/)문서를 검토합니다.
- az [키볼트 스토리지](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) 참조 문서를 참조하십시오.
