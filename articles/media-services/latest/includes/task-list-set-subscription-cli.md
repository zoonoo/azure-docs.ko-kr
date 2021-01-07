---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 94df457cc93a059de3f9bb294bd1c568b095781c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94885444"
---
<!-- List and set subscriptions -->

1. [az account list](/cli/azure/account#az-account-list) 명령을 사용하여 구독 리스트를 가져옵니다.

    ```
    az account list --output table
    ```

2. 전환하려고 하는 구독 ID 또는 이름으로 `az account set`을 사용합니다.

    ```
    az account set --subscription "My Demos"
    ```
