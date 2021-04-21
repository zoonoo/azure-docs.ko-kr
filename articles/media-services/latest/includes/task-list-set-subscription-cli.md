---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: ffd053b98a54d3e23eec62427f5c3d82df58954d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799857"
---
<!-- List and set subscriptions -->

1. [az account list](/cli/azure/account#az_account_list) 명령을 사용하여 구독 리스트를 가져옵니다.

    ```
    az account list --output table
    ```

2. 전환하려고 하는 구독 ID 또는 이름으로 `az account set`을 사용합니다.

    ```
    az account set --subscription "My Demos"
    ```
