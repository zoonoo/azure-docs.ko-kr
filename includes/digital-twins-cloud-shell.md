---
author: baanders
description: Azure Digital Twins용 파일 포함 - Cloud Shell 및 IoT 확장 설정
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 8be070826de0334483f4150925c05cb4dfb73f2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85805802"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Cloud Shell 세션 설정

Cloud Shell 창을 연 후 가장 먼저 해야 할 일은 로그인하고 셸 컨텍스트를 이 세션의 구독으로 설정하는 것입니다. Cloud Shell에서 다음 명령을 실행합니다.

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

Azure Digital Twins에서 이 구독을 처음 사용하는 경우 이 명령을 실행하여 Azure Digital Twins 네임스페이스에 등록합니다. (확실하지 않으면 이전에 수행한 적이 있더라도 다시 실행해도 됩니다.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

다음으로, [**Azure CLI용 Microsoft Azure IoT 확장**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest)을 Cloud Shell에 추가하여 Azure Digital Twins 및 기타 IoT 서비스와 상호 작용하는 명령을 사용하도록 설정합니다. 

먼저이 명령을 실행 하 여 이미 설치한 모든 확장의 목록을 확인 합니다.

```azurecli-interactive
az extension list
```

출력에서 `"name"` 각 목록 항목에 대 한 필드를 찾아 확장의 이름을 확인 합니다.

출력을 사용 하 여 확장 설정에 대해 실행할 다음 명령을 결정 합니다 (둘 이상 실행 될 수 있음).
* 목록에 다음이 포함 된 경우 `azure-iot` : 확장이 이미 있습니다. 다음 명령을 실행 하 여 최신 업데이트가 있는지 확인 합니다.

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* 목록에 다음이 포함 되어 **있지** 않으면 `azure-iot` 확장을 설치 해야 합니다. 이 명령 사용:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* 목록에 포함 된 경우 `azure-iot-cli-ext` : 확장의 레거시 버전입니다. 한 번에 한 버전의 확장만 설치 해야 하므로 레거시 확장을 제거 해야 합니다. 이 명령 사용:

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```

이제 Cloud Shell에서 Azure Digital Twins를 사용할 준비가 되었습니다.

사용 가능한 최상위 Azure Digital Twins 명령 목록을 보려면 언제든지 `az dt -h`를 실행하여 이를 확인할 수 있습니다.