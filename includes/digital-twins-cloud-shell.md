---
author: baanders
description: Azure Digital Twins용 파일 포함 - Cloud Shell 및 IoT 확장 설정
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 76480959d94b9e2a2e04857c85b7359105e73e4f
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84611476"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Cloud Shell 세션 설정

Cloud Shell 창을 연 후 가장 먼저 해야 할 일은 로그인하고 셸 컨텍스트를 이 세션의 구독으로 설정하는 것입니다. 

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

Azure Digital Twins에서 이 구독을 처음 사용하는 경우 이 명령을 실행하여 Azure Digital Twins 네임스페이스에 등록합니다. (확실하지 않으면 이전에 수행한 적이 있더라도 다시 실행해도 됩니다.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

그런 다음, Cloud Shell 인스턴스에서 다음 명령을 실행하여 Azure CLI용 Microsoft Azure IoT 확장을 추가합니다.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

> [!NOTE]
> 이 문서에서는 `azure-iot`라는 최신 버전의 Azure IoT 확장이 사용됩니다. 레거시 버전은 `azure-iot-cli-ext`라고 합니다. 한 번에 하나의 버전만 설치해야 합니다. `az extension list` 명령을 사용하여 현재 설치된 확장의 유효성을 검사할 수 있습니다.
> `az extension remove --name azure-cli-iot-ext`를 사용하여 레거시 버전의 확장을 제거합니다.
> `az extension add --name azure-iot`를 사용하여 새 버전의 확장을 추가합니다. 설치한 확장을 확인하려면 `az extension list`를 사용합니다.

> [!TIP]
> `az dt -h`를 실행하여 최상위 수준의 Azure Digital Twins 명령을 볼 수 있습니다.