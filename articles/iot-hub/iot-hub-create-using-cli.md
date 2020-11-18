---
title: Azure CLI를 사용하여 IoT Hub 만들기 | Microsoft 문서
description: Azure CLI 명령을 사용 하 여 리소스 그룹을 만든 다음 리소스 그룹에서 IoT hub를 만드는 방법에 대해 알아봅니다. 허브를 제거 하는 방법에 대해서도 알아봅니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: e9757b94330f889869080a0e961ab2728cee86f6
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659933"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Azure CLI를 사용하여 IoT Hub 만들기

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

이 문서는 Azure CLI를 사용하여 IoT Hub를 만드는 방법을 보여줍니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

Azure CLI를 사용하여 리소스 그룹을 만든 다음 IoT Hub를 추가합니다.

1. IoT Hub는 리소스 그룹에서 만들어야 합니다. 기존 리소스 그룹을 사용하거나 다음 [리소스 그룹을 만드는 명령](/cli/azure/resource)을 실행합니다.
    
   ```azurecli-interactive
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > 이전 예제에서는 미국 서부 위치에서 리소스 그룹을 만듭니다. 이 명령을 실행하여 사용할 수 있는 위치의 목록을 볼 수 있습니다. 
   >
   > ```azurecli-interactive
   > az account list-locations -o table
   > ```
   >

2. 리소스 그룹에서 IoT Hub에 대해 글로벌한 고유 이름을 사용하여 다음 [IoT Hub를 만드는 명령](/cli/azure/iot/hub#az-iot-hub-create)을 실행합니다.
    
   ```azurecli-interactive
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


이전 명령은 청구 대상인 S1 가격 책정 계층에 IoT Hub를 만듭니다. 자세한 내용은 [Azure IoT Hub 가격 책정](https://azure.microsoft.com/pricing/details/iot-hub/)을 참조하세요.

## <a name="remove-an-iot-hub"></a>IoT Hub 제거

Azure CLI를 사용하여 IoT Hub와 같은 [개별 리소스를 삭제](/cli/azure/resource)하거나 IoT Hub를 포함한 리소스 그룹 및 모든 해당 리소스를 삭제할 수 있습니다.

[IoT hub를 삭제](/cli/azure/iot/hub#az-iot-hub-delete)하려면 다음 명령을 실행 합니다.

```azurecli-interactive
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

[리소스 그룹](/cli/azure/group#az-group-delete) 및 모든 해당 리소스를 삭제 하려면 다음 명령을 실행 합니다.

```azurecli-interactive
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>다음 단계

IoT Hub 사용에 관한 자세한 내용은 다음 문서를 참조하세요.

* [IoT Hub 개발자 가이드](iot-hub-devguide.md)
* [Azure Portal을 사용하여 IoT Hub 관리](iot-hub-create-through-portal.md)