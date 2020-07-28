---
author: baanders
description: Azure Digital Twins용 파일 포함 - Cloud Shell 및 IoT 확장 설정
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: b7c91d648c06970d53799c6ff505919dea17b3c0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87032218"
---
열려 있는 [Azure Cloud Shell](https://shell.azure.com) 창에서 Azure Digital Twins 작업을 시작하려면 먼저 로그인하여 셸 컨텍스트를 이 세션에 대한 구독으로 설정합니다. Cloud Shell에서 다음 명령을 실행합니다.

```azurecli
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> 위의 명령에서 ID 대신 제품 이름을 사용할 수도 있습니다. 

Azure Digital Twins에서 이 구독을 처음 사용하는 경우 이 명령을 실행하여 Azure Digital Twins 네임스페이스에 등록합니다. (확실하지 않으면 이전에 수행한 적이 있더라도 다시 실행해도 됩니다.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

다음으로, [**Azure CLI용 Microsoft Azure IoT 확장**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest)을 Cloud Shell에 추가하여 Azure Digital Twins 및 기타 IoT 서비스와 상호 작용하는 명령을 사용하도록 설정합니다. 

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

이제 Cloud Shell에서 Azure Digital Twins를 사용할 준비가 되었습니다.

사용 가능한 최상위 Azure Digital Twins 명령 목록을 보려면 언제든지 `az dt -h`를 실행하여 이를 확인할 수 있습니다.