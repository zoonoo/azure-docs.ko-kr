---
title: Azure CLI 스크립트 샘플 - App Configuration 저장소에서 키 값 사용
titleSuffix: Azure App Configuration
description: Azure CLI 스크립트를 사용하여 App Configuration 저장소에서 키 값을 만들고, 보고, 업데이트하고, 삭제합니다.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: bc329feffbf22d63774ab0f3779d62d3ccf53adb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774130"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Azure App Configuration 저장소에서 키 값 사용

이 샘플 스크립트는 다음 작업을 수행하는 방법을 보여 줍니다.
* 새 키-값 쌍 만들기
* 기존의 모든 키-값 쌍 나열
* 새로 만든 키 값 업데이트
* 새 키-값 쌍 삭제

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 이 자습서에는 Azure CLI 버전 2.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.
## <a name="sample-script"></a>샘플 스크립트

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"
refKey="KeyVaultReferenceTestKey"
uri="[URL to value stored in Key Vault]"
uri2="[URL to another value stored in Key Vault]"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --key $newKey --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Create a new key-value referencing a value stored in Azure Key Vault
az appconfig kv set --name $appConfigName --key $refKey --content-type "application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8" --value "{\"uri\":\"$uri\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Update Key Vault reference
az appconfig kv set --name $appConfigName --key $refKey --value "{\"uri\":\"$uri2\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# Delete Key Vault reference
az appconfig kv delete --name $appConfigName --key $refKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

다음 표에는 샘플 스크립트에 사용되는 명령이 나와 있습니다. 

| 명령 | 메모 |
|---|---|
| [az appconfig kv set](/cli/azure/appconfig/kv#az_appconfig_kv_set) | 키-값 쌍을 만들거나 업데이트합니다. |
| [az appconfig kv list](/cli/azure/appconfig/kv#az_appconfig_kv_list) | 키 값을 App Configuration 저장소에 나열합니다. |
| [az appconfig kv delete](/cli/azure/appconfig/kv#az_appconfig_kv_delete) | 키-값 쌍을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가적인 App Configuration CLI 스크립트 샘플은 [Azure App Configuration CLI 샘플](../cli-samples.md)에서 확인할 수 있습니다.
