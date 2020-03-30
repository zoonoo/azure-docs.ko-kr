---
title: Azure CLI에서 IoT 중앙 관리 | 마이크로 소프트 문서
description: 이 문서에서는 CLI를 사용하여 IoT Central 응용 프로그램을 만들고 관리하는 방법에 대해 설명합니다. CLI를 사용하여 응용 프로그램을 보고 수정하고 제거할 수 있습니다.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: df24a2dc6e9bd058a2f8b1355b8760653ed3128a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365514"
---
# <a name="manage-iot-central-from-azure-cli"></a>Azure CLI에서 IoT 중앙 관리

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Azure IoT Central 응용 프로그램 [관리자](https://aka.ms/iotcentral) 웹 사이트에서 IoT Central 응용 프로그램을 만들고 관리하는 대신 [Azure CLI를](/cli/azure/) 사용하여 응용 프로그램을 관리할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

로컬 컴퓨터에서 Azure CLI를 실행하려면 [Azure CLI 설치를](/cli/azure/install-azure-cli)참조하십시오. Azure CLI를 로컬로 실행하는 경우 이 문서에서 명령을 시도하기 전에 **az 로그인** 명령을 사용하여 Azure에 로그인합니다.

> [!TIP]
> 다른 Azure 구독에서 CLI 명령을 실행해야 하는 경우 [활성 구독 변경을](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription)참조하십시오.

## <a name="install-the-extension"></a>확장 설치

이 문서의 명령은 **azure-iot** CLI 확장의 일부입니다. 다음 명령을 실행하여 확장을 설치합니다.

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="create-an-application"></a>애플리케이션 만들기

az [iotcentral 앱 create](/cli/azure/iotcentral/app#az-iotcentral-app-create) 명령을 사용하여 Azure 구독에서 IoT Central 응용 프로그램을 만듭니다. 예를 들어:

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
  --sku ST1 --template "iotc-pnp-preview@1.0.0" \
  --display-name "My Custom Display Name"
```

이러한 명령은 먼저 응용 프로그램에 대한 미국 동부 지역에 리소스 그룹을 만듭니다. 다음 표는 **az iotcentral 앱 create** 명령과 함께 사용되는 매개 변수에 대해 설명합니다.

| 매개 변수         | 설명 |
| ----------------- | ----------- |
| resource-group    | 애플리케이션을 포함하는 리소스 그룹입니다. 리소스 그룹이 구독에 이미 있어야 합니다. |
| 위치          | 기본적으로 이 명령은 리소스 그룹의 위치를 사용합니다. 현재 **오스트레일리아,** **아시아 태평양,** **유럽,** **미국,** **영국**및 **일본** 지역에서 IoT Central 응용 프로그램을 만들 수 있습니다. |
| name              | Azure Portal의 애플리케이션 이름입니다. |
| 하위         | 애플리케이션 URL의 하위 도메인입니다. 예제에서 애플리케이션 URL은 `https://mysubdomain.azureiotcentral.com`입니다. |
| sku               | 현재 **ST1** 또는 **ST2**를 사용할 수 있습니다. [Azure IoT Central 가격 책정](https://azure.microsoft.com/pricing/details/iot-central/)을 참조하세요. |
| template          | 사용할 애플리케이션 템플릿입니다. 자세한 내용은 다음 표를 참조하세요. |
| display-name      | UI에 표시되는 애플리케이션 이름입니다. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>애플리케이션 보기

az [iotcentral 앱 목록](/cli/azure/iotcentral/app#az-iotcentral-app-list) 명령을 사용하여 IoT Central 응용 프로그램을 나열하고 메타데이터를 봅니다.

## <a name="modify-an-application"></a>애플리케이션 수정

az [iotcentral 앱 업데이트](/cli/azure/iotcentral/app#az-iotcentral-app-update) 명령을 사용하여 IoT Central 응용 프로그램의 메타데이터를 업데이트합니다. 예를 들어 애플리케이션의 표시 이름을 변경합니다.

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>애플리케이션 제거

az [iotcentral 앱 삭제](/cli/azure/iotcentral/app#az-iotcentral-app-delete) 명령을 사용하여 IoT 중앙 응용 프로그램을 삭제합니다. 예를 들어:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>다음 단계

Azure CLI에서 Azure IoT Central 응용 프로그램을 관리하는 방법을 배웠으니 다음 단계가 제안되었습니다.

> [!div class="nextstepaction"]
> [애플리케이션 관리](howto-administer.md)
