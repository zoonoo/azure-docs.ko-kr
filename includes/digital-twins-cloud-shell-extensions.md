---
author: baanders
description: Azure Digital Twins에 대한 포함 파일 - 최신 IoT 확장 설정
ms.service: digital-twins
ms.topic: include
ms.date: 7/31/2020
ms.author: baanders
ms.openlocfilehash: 984739a728f6ac5e28eeb561e0d7b6ec0485ca13
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496868"
---
먼저 다음 명령을 실행하여 이미 설치한 모든 확장에 대한 목록을 확인합니다.

```azurecli-interactive
az extension list
```

출력은 현재 보유하고 있는 모든 확장의 배열입니다. 각 목록 항목에 대해 `"name"` 필드를 찾아서 확장의 이름을 확인합니다.

출력을 사용하여 다음 명령 중에서 확장 설정에 대해 실행할 명령을 결정합니다(둘 이상 실행할 수 있음).
* 목록에 `azure-iot`가 포함된 경우: 확장이 이미 있습니다. 다음 명령을 실행하여 최신 업데이트가 있고 사용 가능한 업데이트가 더 이상 없는지 확인합니다.

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* 목록에 `azure-iot`가 포함되지 **않은** 경우: 확장을 설치해야 합니다. 다음 명령을 사용합니다.

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* 목록에 `azure-iot-cli-ext`가 포함된 경우: 이는 확장의 레거시 버전입니다. 한 번에 하나의 확장 버전만 설치해야 하므로 레거시 확장을 제거해야 합니다. 다음 명령을 사용합니다.

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```