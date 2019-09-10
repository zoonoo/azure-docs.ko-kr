---
title: Azure CLI에서 IoT Central 관리 | Microsoft Docs
description: Azure CLI에서 IoT Central를 관리 합니다.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 1ec83541c62d93eee91348531797ecdeb8c9fc6e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873484"
---
# <a name="manage-iot-central-from-azure-cli"></a>Azure CLI에서 IoT Central 관리

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

[Azure IoT Central application manager](https://aka.ms/iotcentral) 웹 사이트에서 IoT Central 응용 프로그램을 만들고 관리 하는 대신 [Azure CLI](/cli/azure/) 를 사용 하 여 응용 프로그램을 관리할 수 있습니다.

## <a name="prerequisites"></a>전제 조건

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

로컬 컴퓨터에서 Azure CLI를 실행 하려면 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조 하세요. Azure CLI를 로컬로 실행 하는 경우 **az login** 명령을 사용 하 여 Azure에 로그인 한 후이 문서의 명령을 시도 합니다.

## <a name="create-an-application"></a>애플리케이션 만들기

[Az iotcentral app create](/cli/azure/iotcentral/app#az-iotcentral-app-create) 명령을 사용 하 여 Azure 구독에 IoT Central 응용 프로그램을 만듭니다. 예:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iotcentral app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku S1 --template "iotc-demo@1.0.0" \
  --display-name "My Custom Display Name"
```

이러한 명령은 먼저 응용 프로그램에 대 한 미국 동부 지역에 리소스 그룹을 만듭니다. 다음 표에서는 **az iotcentral app create** 명령에 사용 되는 매개 변수에 대해 설명 합니다.

| 매개 변수         | Description |
| ----------------- | ----------- |
| resource-group    | 애플리케이션을 포함하는 리소스 그룹입니다. 리소스 그룹이 구독에 이미 있어야 합니다. |
| 위치          | 기본적으로이 명령은 리소스 그룹의 위치를 사용 합니다. 현재 **미국 동부**, **미국 서 부**, **유럽**서 부 또는 **유럽 서부** 지역 또는 **오스트레일리아** 지리에 IoT Central 응용 프로그램을 만들 수 있습니다. |
| name              | Azure Portal의 애플리케이션 이름입니다. |
| 도메인이         | 애플리케이션 URL의 하위 도메인입니다. 예제에서 애플리케이션 URL은 https://mysubdomain.azureiotcentral.com 입니다. |
| sku               | 현재 유일한 값은 **S1**(표준 계층)입니다. [Azure IoT Central 가격 책정](https://azure.microsoft.com/pricing/details/iot-central/)을 참조하세요. |
| 템플릿          | 사용할 애플리케이션 템플릿입니다. 자세한 내용은 다음 표를 참조하세요. |
| display-name      | UI에 표시되는 애플리케이션 이름입니다. |

**애플리케이션 템플릿**

| 템플릿 이름            | Description |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | 사용자 고유의 디바이스 템플릿 및 디바이스로 채울 빈 애플리케이션을 만듭니다. |
| iotc-demo@1.0.0          | Refrigerated Vending Machine에 대해 이미 만든 디바이스 템플릿을 포함하는 애플리케이션을 만듭니다. 이 템플릿을 사용하여 Azure IoT Central 탐색을 시작하세요. |
| iotc-devkit-sample@1.0.0 | MXChip 또는 Raspberry Pi 디바이스를 연결할 수 있는 디바이스 템플릿을 사용하여 애플리케이션을 만듭니다. 다음 디바이스 중 하나를 실험하는 디바이스 개발자인 경우 이 템플릿을 사용합니다. |

> [!NOTE]
> **Preview 응용 프로그램** 템플릿은 현재 **북부** 및 **미국 중부** 지역 에서만 사용할 수 있습니다.

## <a name="view-your-applications"></a>애플리케이션 보기

[Az iotcentral app list](/cli/azure/iotcentral/app#az-iotcentral-app-list) 명령을 사용 하 여 IoT Central 응용 프로그램을 나열 하 고 메타 데이터를 볼 수 있습니다.

## <a name="modify-an-application"></a>애플리케이션 수정

[Az iotcentral app update](/cli/azure/iotcentral/app#az-iotcentral-app-update) 명령을 사용 하 여 IoT Central 응용 프로그램의 메타 데이터를 업데이트 합니다. 예를 들어 애플리케이션의 표시 이름을 변경합니다.

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>애플리케이션 제거

[Az iotcentral app delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) 명령을 사용 하 여 IoT Central 응용 프로그램을 삭제 합니다. 예:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>다음 단계

이제 Azure CLI에서 Azure IoT Central 응용 프로그램을 관리 하는 방법을 배웠으므로 제안 된 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [애플리케이션 관리](howto-administer.md)
