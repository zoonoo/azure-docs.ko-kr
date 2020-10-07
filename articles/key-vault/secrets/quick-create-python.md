---
title: 빠른 시작 – Azure Key Vault Python 클라이언트 라이브러리 - 비밀 관리
description: Python 클라이언트 라이브러리를 사용하여 Azure Key Vault에서 암호를 생성, 검색 및 삭제하는 방법을 알아봅니다.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: cd8a5751c018b9b3b3b2ef96765545f2edab685b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "89489207"
---
# <a name="quickstart-azure-key-vault-secrets-client-library-for-python"></a>빠른 시작: Python용 Azure Key Vault 비밀 클라이언트 라이브러리

Python용 Azure Key Vault 클라이언트 라이브러리를 시작합니다. 아래 단계에 따라 패키지를 설치하고 기본 작업에 대한 예제 코드를 사용해 봅니다. Key Vault를 사용하여 비밀을 저장하면 코드에 비밀을 저장하지 않아도 되므로 앱의 보안이 강화됩니다.

[API 참조 설명서](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets) | [패키지(Python 패키지 인덱스)](https://pypi.org/project/azure-keyvault-secrets/)

## <a name="set-up-your-local-environment"></a>로컬 환경 설정

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Key Vault 비밀 라이브러리를 설치합니다.

    ```terminal
    pip install azure-keyvault-secrets
    ```

## <a name="create-a-resource-group-and-key-vault"></a>리소스 그룹 및 키 자격 증명 모음 만들기

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>키 자격 증명 모음에 대한 액세스 권한을 서비스 주체에 부여

다음 [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 명령을 실행하여 비밀에 대한 가져오기, 나열 및 설정 작업에 대한 서비스 주체에 권한을 부여합니다. 이 명령은 이전 단계에서 만든 `KEY_VAULT_NAME` 및 `AZURE_CLIENT_ID` 환경 변수를 사용합니다.

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --secret-permissions delete get list set 
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --secret-permissions delete get list set 
```

---

이 명령은 이전 단계에서 만든 `KEY_VAULT_NAME` 및 `AZURE_CLIENT_ID` 환경 변수를 사용합니다.

자세한 내용은 [액세스 정책 할당 - CLI](../general/assign-access-policy-cli.md)를 참조하세요.

## <a name="create-the-sample-code"></a>샘플 코드 만들기

Python용 Azure Key Vault 클라이언트 라이브러리를 사용하면 비밀 및 관련 자산(예: 인증서 및 암호화 키)을 관리할 수 있습니다. 다음 코드 샘플에서는 클라이언트를 만들고, 비밀을 설정하고, 비밀을 검색하고 비밀을 삭제하는 방법을 보여줍니다.

이 코드가 포함된 *kv_secrets.py*라는 파일을 만듭니다.

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = f"https://{keyVaultName}.vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = input("Input a name for your secret > ")
secretValue = input("Input a value for your secret > ")

print(f"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...")

client.set_secret(secretName, secretValue)

print(" done.")

print(f"Retrieving your secret from {keyVaultName}.")

retrieved_secret = client.get_secret(secretName)

print(f"Your secret is '{retrieved_secret.value}'.")
print(f"Deleting your secret from {keyVaultName} ...")

poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>코드 실행

이전 섹션의 코드가 *kv_secrets.py*라는 파일에 있는지 확인합니다. 그런 후, 다음 명령을 사용하여 코드를 실행합니다.

```terminal
python kv_secrets.py
```

- 권한 오류가 발생한 경우 [`az keyvault set-policy` 명령](#give-the-service-principal-access-to-your-key-vault)을 실행했는지 확인합니다.
- 동일한 비밀 이름으로 코드를 다시 실행하면 "(충돌) 비밀 <name>이 현재 삭제되었지만 복구 가능한 상태에 있습니다."라는 오류가 발생할 수 있습니다. 다른 비밀 이름을 사용합니다.

## <a name="code-details"></a>코드 세부 정보

### <a name="authenticate-and-create-a-client"></a>클라이언트 인증 및 만들기

앞의 코드에서 [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) 개체는 서비스 주체에 대해 만든 환경 변수를 사용합니다. 해당 클라이언트를 통해 작업하려는 리소스의 URI와 함께 Azure 라이브러리(예: [`SecretClient`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python))에서 클라이언트 개체를 만들 때마다 이 자격 증명을 제공합니다.

```python
credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>비밀 저장

키 자격 증명 모음에 대한 클라이언트 개체를 가져온 후에는 [set_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#set-secret-name--value----kwargs-) 메서드를 사용하여 비밀을 저장할 수 있습니다. 

```python
client.set_secret(secretName, secretValue)
```

`set_secret`를 호출하면 키 자격 증명 모음용 Azure REST API에 대한 호출이 생성됩니다.

요청을 처리할 때 Azure는 클라이언트에 제공한 자격 증명 개체를 사용하여 호출자의 ID(서비스 주체)를 인증합니다.

또한 호출자에게 요청된 작업을 수행할 권한이 있는지 확인합니다. 이전에 [`az keyvault set-policy` 명령](#give-the-service-principal-access-to-your-key-vault)을 사용하여 서비스 주체에게 이 권한을 부여했습니다.

### <a name="retrieve-a-secret"></a>비밀 검색

Key Vault에서 비밀을 읽으려면 [get_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#get-secret-name--version-none----kwargs-) 메서드를 사용합니다.

```python
retrieved_secret = client.get_secret(secretName)
 ```

비밀 값은 `retrieved_secret.value`에 포함되어 있습니다.

Azure CLI 명령 [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)를 사용하여 비밀을 검색할 수도 있습니다.

### <a name="delete-a-secret"></a>비밀 삭제

비밀을 삭제하려면 [begin_delete_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#begin-delete-secret-name----kwargs-) 메서드를 사용합니다.

```python
poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()
```

`begin_delete_secret` 메서드는 비동기식이며 폴러 개체를 반환합니다. 폴러의 `result` 메서드를 호출하면 작업이 완료될 때까지 대기합니다.

Azure CLI 명령 [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)를 사용하여 비밀이 제거되었는지 확인할 수 있습니다.

비밀이 삭제되었지만 당분간 복구 가능한 상태로 유지됩니다. 코드를 다시 실행하는 경우 다른 비밀 이름을 사용합니다.

## <a name="clean-up-resources"></a>리소스 정리

[인증서](../certificates/quick-create-python.md) 및 [키](../keys/quick-create-python.md)로도 실험하려면 이 문서에서 만든 Key Vault를 다시 사용할 수 있습니다.

그렇지 않으면 이 문서에서 만든 리소스를 완료한 후 다음 명령을 사용하여 리소스 그룹과 포함된 모든 리소스를 삭제합니다.

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault 개요](../general/overview.md)
- [Azure Key Vault 개발자 가이드](../general/developers-guide.md)
- [Azure Key Vault 모범 사례](../general/best-practices.md)
- [Key Vault로 인증](../general/authentication.md)
