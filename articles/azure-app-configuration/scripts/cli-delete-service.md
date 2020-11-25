---
title: Azure CLI 스크립트 샘플 - Azure App Configuration 저장소 삭제
titleSuffix: Azure App Configuration
description: 샘플 Azure CLI 스크립트를 사용하여 Azure App Configuration 저장소를 삭제합니다. 스크립트에 사용되는 명령에 대한 참조 문서 링크를 참조하세요.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.custom: devx-track-azurecli
ms.openlocfilehash: 70d3505ce14fcecece55391bc0a5361838b34111
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566897"
---
# <a name="delete-an-azure-app-configuration-store"></a>Azure App Configuration 저장소 삭제

이 샘플 스크립트는 Azure App Configuration 인스턴스를 삭제합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 이 자습서에는 Azure CLI 버전 2.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="sample-script"></a>샘플 스크립트

```azurecli-interactive
#/bin/bash

# Delete an App Configuration store named myTestAppConfigStore from the Resource Group myResourceGroup
az appconfig delete --name myTestAppConfigStore --resource-group myResourceGroup
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 App Configuration 저장소를 삭제합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az appconfig delete](/cli/azure/appconfig#az-appconfig-delete) | App Configuration 저장소 리소스를 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가적인 App Configuration CLI 스크립트 샘플은 [Azure App Configuration CLI 샘플](../cli-samples.md)에서 확인할 수 있습니다.
