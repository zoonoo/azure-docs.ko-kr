---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/17/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 3ac72360db5fb3d075b4caa3208f372f22441c7b
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755667"
---
## <a name="prepare-an-iot-hub"></a>IoT Hub 준비

이 문서의 단계를 완료하려면 Azure 구독의 Azure IoT 허브가 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

Azure CLI를 로컬로 사용하는 경우 먼저 `az login`을 사용하여 Azure 구독에 로그인합니다. Azure Cloud Shell에서 이러한 명령을 실행하는 경우 자동으로 로그인됩니다.

Azure CLI를 로컬로 사용하는 경우 `az` 버전은 **2.8.0** 이상이어야 합니다. Azure Cloud Shell은 최신 버전을 사용합니다. `az --version` 명령을 사용하여 컴퓨터에 설치된 버전을 확인합니다.

다음 명령을 실행하여 인스턴스에 Azure CLI용 Microsoft Azure IoT 확장을 추가합니다.

```azurecli-interactive
az extension add --name azure-iot
```

사용할 IoT 허브가 아직 없는 경우 다음 명령을 실행하여 구독에 리소스 그룹 및 체험 계층 IoT 허브를 만듭니다. `<YourIoTHubName>`을 선택한 허브 이름으로 바꿉니다.

```azurecli-interactive
az group create --name my-pnp-resourcegroup \
    --location centralus
az iot hub create --name <YourIoTHubName> \
    --resource-group my-pnp-resourcegroup --sku F1
```

> [!NOTE]
> IoT 플러그 앤 플레이는 현재 미국 중부, 북유럽 및 일본 동부에서 생성된 IoT 허브에서 사용할 수 있습니다. IoT 플러그 앤 플레이 지원은 기본 계층 IoT 허브에 포함되지 않습니다.

다음 명령을 실행하여 IoT Hub에 디바이스 ID를 만듭니다. `<YourIoTHubName>` 및 `<YourDeviceID>` 자리 표시자를 사용자 고유의 _IoT Hub 이름_ 과 선택한 _디바이스 ID_ 로 바꿉니다.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```
