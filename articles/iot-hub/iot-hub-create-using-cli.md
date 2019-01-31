---
title: Azure CLI를 사용하여 IoT Hub 만들기 | Microsoft 문서
description: Azure CLI를 사용하여 Azure IoT Hub를 만드는 방법입니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: 78ea9071f220b2a78c6d9260d47145f22284d760
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250268"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Azure CLI를 사용하여 IoT Hub 만들기

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

이 문서는 Azure CLI를 사용하여 IoT Hub를 만드는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 조건

이 방법 문서를 완료하려면 Azure 구독이 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Azure 계정 로그인 및 설정

Cloud Shell을 사용하는 대신 로컬로 Azure CLI를 실행하는 경우 Azure 계정에 로그인해야 합니다.

명령 프롬프트에서 [login 명령](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)을 실행합니다.

   ```azurecli
   az login
   ```

지침에 따라 코드를 사용하여 인증하고 웹 브라우저를 통해 Azure 계정에 로그인합니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

Azure CLI를 사용하여 리소스 그룹을 만든 다음 IoT Hub를 추가합니다.

1. IoT Hub는 리소스 그룹에서 만들어야 합니다. 기존 리소스 그룹을 사용하거나 다음 [리소스 그룹을 만드는 명령](https://docs.microsoft.com/cli/azure/resource)을 실행합니다.
    
   ```azurecli
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > 이전 예제에서는 미국 서부 위치에서 리소스 그룹을 만듭니다. 이 명령을 실행하여 사용할 수 있는 위치의 목록을 볼 수 있습니다. 
   >
   >``` bash
   >az account list-locations -o table
   >```
   >

2. 리소스 그룹에서 IoT Hub에 대해 글로벌한 고유 이름을 사용하여 다음 [IoT Hub를 만드는 명령](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create)을 실행합니다.
    
   ```azurecli
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


이전 명령은 청구 대상인 S1 가격 책정 계층에 IoT Hub를 만듭니다. 자세한 내용은 [Azure IoT Hub 가격 책정](https://azure.microsoft.com/pricing/details/iot-hub/)을 참조하세요.

## <a name="remove-an-iot-hub"></a>IoT Hub 제거

Azure CLI를 사용하여 IoT Hub와 같은 [개별 리소스를 삭제](https://docs.microsoft.com/cli/azure/resource)하거나 IoT Hub를 포함한 리소스 그룹 및 모든 해당 리소스를 삭제할 수 있습니다.

[IoT Hub를 삭제](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete)하려면 다음 명령을 실행합니다.

```azurecli
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

모든 해당 리소스 및 [리소스 그룹을 삭제](https://docs.microsoft.com/cli/azure/group#az-group-delete)하려면 다음 명령을 실행합니다.

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>다음 단계

IoT Hub 사용에 관한 자세한 내용은 다음 문서를 참조하세요.

* [IoT Hub 개발자 가이드](iot-hub-devguide.md)
* [Azure Portal을 사용하여 IoT Hub 관리](iot-hub-create-through-portal.md)
