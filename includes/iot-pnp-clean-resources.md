---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: d88d166e001e71cabdabc3a3d344adc4da19aa8c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453678"
---
## <a name="clean-up-resources"></a>리소스 정리

IoT 플러그 앤 플레이 추가 문서를 계속 진행하려면 이 빠른 시작에서 사용한 리소스를 그대로 두고 재사용하세요. 그렇지 않으면 추가 요금이 발생하지 않도록 이 빠른 시작에서 만든 리소스를 삭제하세요.

Azure CLI에 대한 다음 명령을 사용하여 전체 리소스 그룹을 삭제하면 허브와 등록된 디바이스를 한 번에 삭제할 수 있습니다. (하지만 이러한 리소스가 다른 용도로 보유하고 있는 다른 리소스와 리소스 그룹을 공유하는 경우에는 이 방법을 사용하지 마세요.)

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```
IoT 허브만 삭제하려면 Azure CLI을 사용하여 다음 명령을 실행합니다.

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

IoT 허브에 등록한 디바이스 ID만 삭제하려면 Azure CLI를 사용하여 다음 명령을 실행합니다.

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

개발 머신에서 복제된 샘플 파일을 제거할 수도 있습니다.