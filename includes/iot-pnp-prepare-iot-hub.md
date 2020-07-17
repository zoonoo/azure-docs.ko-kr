---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: a3340eb9e53afa83c35109bad7d22f81413dd644
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80234144"
---
## <a name="prepare-an-iot-hub"></a>IoT Hub 준비

또한 이 빠른 시작을 완료하려면 Azure 구독의 Azure IoT Hub가 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. IoT Hub가 없는 경우 [다음 지침에 따라 새로 만듭니다](../articles/iot-hub/iot-hub-create-using-cli.md).

Azure CLI를 로컬로 사용하는 경우 먼저 `az login`을 사용하여 Azure 구독에 로그인합니다. Azure Cloud Shell에서 이러한 명령을 실행하는 경우 자동으로 로그인됩니다.

Azure CLI을 로컬로 사용하는 경우 `az` 버전은 **2.0.73** 이상이어야 합니다. Azure Cloud Shell은 최신 버전을 사용합니다. `az --version` 명령을 사용하여 컴퓨터에 설치된 버전을 확인합니다.

다음 명령을 실행하여 인스턴스에 Azure CLI용 Microsoft Azure IoT 확장을 추가합니다.

```azurecli-interactive
az extension add --name azure-iot
```

다음 명령을 실행하여 IoT Hub에 디바이스 ID를 만듭니다. **YourIoTHubName** 및 **YourDeviceID** 자리 표시자를 사용자 고유의 _IoT Hub 이름_과 선택한 _디바이스 ID_로 바꿉니다.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

방금 등록한 디바이스의 _디바이스 연결 문자열_을 가져오려면 다음 명령을 실행합니다(나중에 사용하기 위해 참고).

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```
