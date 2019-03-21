---
title: Azure CLI 스크립트 샘플 - Azure App Configuration 저장소에서 내보내기 | Microsoft Docs
description: Azure App Configuration 저장소에서 내보내려면 정보 및 샘플 스크립트 제공
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 480e21994846f5b2adb85bcf846df2a7cdda4bda
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444473"
---
# <a name="export-from-an-azure-app-configuration-store"></a>Azure App Configuration 저장소에서 내보내기

이 샘플 스크립트는 Azure App Configuration 저장소에서 키 값을 내보냅니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

다음 명령을 실행하여 먼저 Azure App Configuration CLI 확장을 설치해야 합니다.

        az extension add -n appconfig

## <a name="sample-script"></a>샘플 스크립트

```azurecli-interactive
#!/bin/bash

# Export all key-values
az appconfig kv export --name myTestAppConfigStore --file ~/Export.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 앱 구성 저장소를 내보냅니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az appconfig export](/cli/azure/ext/appconfig/appconfig) | 앱 구성 저장소 리소스에서 내보냅니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가적인 App Configuration CLI 스크립트 샘플은 [Azure App Configuration 설명서](../cli-samples.md)에서 확인할 수 있습니다.
