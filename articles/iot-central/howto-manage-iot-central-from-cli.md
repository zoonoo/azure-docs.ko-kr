---
title: Azure CLI의 IoT Central을 관리 | Microsoft Docs
description: Azure CLI의 IoT Central을 관리 합니다.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/07/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 9e5d842cece316bc9c53e1e8583f40a0f222b91d
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2019
ms.locfileid: "56809402"
---
# <a name="manage-iot-central-from-azure-cli"></a>Azure CLI의 IoT Central을 관리 합니다.

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

만들기 및 IoT Central에서 IoT Central 응용 프로그램을 관리 하는 대신 [응용 프로그램 관리자](https://aka.ms/iotcentral) 페이지에서 사용할 수 [Azure CLI](/cli/azure/) 응용 프로그램을 관리할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure CLI를 로컬 컴퓨터에서 실행 하려는 경우 참조 [Azure CLI 설치](/cli/azure/install-azure-cli)합니다. Azure CLI를 로컬로 실행 하는 경우 사용 합니다 **az 로그인** 이 문서의 명령을 시도 하기 전에 Azure에 로그인 하는 명령.

## <a name="create-an-application"></a>애플리케이션 만들기

사용 된 [az iotcentral 앱 만들기](/cli/azure/iotcentral/app#az-iotcentral-app-create) Azure 구독에서 IoT Central 응용 프로그램을 만들려면 명령입니다. 예를 들면 다음과 같습니다.

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

이 명령은 응용 프로그램에 대 한 지역 미국 동부에서 리소스 그룹을 먼저 만듭니다. 다음 표에서 사용 된 매개 변수를 **az iotcentral 앱 만들기** 명령:

| 매개 변수         | 설명 |
| ----------------- | ----------- |
| resource-group    | 애플리케이션을 포함하는 리소스 그룹입니다. 리소스 그룹이 구독에 이미 있어야 합니다. |
| location          | 기본적으로이 명령은 리소스 그룹 위치를 사용합니다. 현재 **미국 동부**, **미국 서부**, **북유럽** 또는 **유럽 서부** 지역에서 IoT Central 애플리케이션을 만들 수 있습니다. |
| 이름              | Azure Portal의 애플리케이션 이름입니다. |
| 하위 도메인         | 애플리케이션 URL의 하위 도메인입니다. 예제에서 애플리케이션 URL은 https://mysubdomain.azureiotcentral.com입니다. |
| sku               | 현재 유일한 값은 **S1**(표준 계층)입니다. [Azure IoT Central 가격 책정](https://azure.microsoft.com/pricing/details/iot-central/)을 참조하세요. |
| template          | 사용할 애플리케이션 템플릿입니다. 자세한 내용은 다음 표를 참조하세요. |
| display-name      | UI에 표시되는 애플리케이션 이름입니다. |

**애플리케이션 템플릿**

| 템플릿 이름            | 설명 |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | 사용자 고유의 장치 템플릿 및 장치로 채울 빈 애플리케이션을 만듭니다. |
| iotc-demo@1.0.0          | Refrigerated Vending Machine에 대해 이미 만든 장치 템플릿을 포함하는 애플리케이션을 만듭니다. 이 템플릿을 사용하여 Azure IoT Central 탐색을 시작하세요. |
| iotc-devkit-sample@1.0.0 | MXChip 또는 Raspberry Pi 장치를 연결할 수 있는 장치 템플릿을 사용하여 애플리케이션을 만듭니다. 다음 디바이스 중 하나를 실험하는 디바이스 개발자인 경우 이 템플릿을 사용합니다. |

## <a name="view-your-applications"></a>애플리케이션 보기

사용 된 [az iotcentral 앱 목록](/cli/azure/iotcentral/app#az-iotcentral-app-list) IoT Central 응용 프로그램을 나열 하 고 메타 데이터를 보려면 명령입니다.

## <a name="modify-an-application"></a>애플리케이션 수정

사용 된 [az iotcentral 앱 업데이트](/cli/azure/iotcentral/app#az-iotcentral-app-update) IoT Central 응용 프로그램의 메타 데이터를 업데이트 하는 명령입니다. 예를 들어 애플리케이션의 표시 이름을 변경합니다.

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>애플리케이션 제거

사용 된 [az iotcentral 앱 삭제](/cli/azure/iotcentral/app#az-iotcentral-app-delete) IoT Central 응용 프로그램을 삭제 하는 명령입니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>다음 단계

이제 Azure CLI에서 Azure IoT Central 응용 프로그램을 관리 하는 방법을 배웠으므로 제안 된 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [애플리케이션 관리](howto-administer.md)
