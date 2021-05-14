---
title: Azure CLI에서 IoT Central 관리 | Microsoft Docs
description: 이 문서에서는 CLI를 사용하여 IoT Central 애플리케이션을 만들고 관리하는 방법을 설명합니다. CLI를 사용하여 애플리케이션을 보고, 수정하고, 제거할 수 있습니다.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: philmea
ms.openlocfilehash: 3a5d37c546e57725b15c9ad42c7b422c8c34eed5
ms.sourcegitcommit: b35c7f3e7f0e30d337db382abb7c11a69723997e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109685138"
---
# <a name="manage-iot-central-from-azure-cli"></a>Azure CLI에서 IoT Central 관리

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

[Azure IoT Central 애플리케이션 관리자](https://aka.ms/iotcentral) 웹 사이트에서 IoT Central 애플리케이션을 만들고 관리하는 대신 [Azure CLI](/cli/azure/)를 사용하여 애플리케이션을 관리할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 다른 Azure 구독에서 CLI 명령을 실행해야 하는 경우 [활성 구독 변경](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription)을 참조하세요.

## <a name="create-an-application"></a>애플리케이션 만들기

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

[az iot central app create](/cli/azure/iot/central/app#az_iot_central_app_create) 명령을 사용하여 Azure 구독에서 IoT Central 애플리케이션을 만듭니다. 예를 들어:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iot central app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview" \
  --display-name "My Custom Display Name"
```

이 명령은 먼저 애플리케이션의 미국 동부 지역에 리소스 그룹을 만듭니다. 다음 표에서는 **az iot central app create** 명령에 사용되는 매개 변수를 설명합니다.

| 매개 변수         | Description |
| ----------------- | ----------- |
| resource-group    | 애플리케이션을 포함하는 리소스 그룹입니다. 리소스 그룹이 구독에 이미 있어야 합니다. |
| 위치          | 기본적으로 이 명령은 리소스 그룹의 위치를 사용합니다. 현재 **오스트레일리아**, **아시아 태평양**, **유럽**, **미국**, **영국**, **일본** 지역에서 IoT Central 애플리케이션을 만들 수 있습니다. |
| name              | Azure Portal의 애플리케이션 이름입니다. |
| subdomain         | 애플리케이션 URL의 하위 도메인입니다. 예제에서 애플리케이션 URL은 `https://mysubdomain.azureiotcentral.com`입니다. |
| sku               | 현재 **ST1** 또는 **ST2** 를 사용할 수 있습니다. [Azure IoT Central 가격 책정](https://azure.microsoft.com/pricing/details/iot-central/)을 참조하세요. |
| template          | 사용할 애플리케이션 템플릿입니다. 자세한 내용은 다음 표를 참조하세요. |
| display-name      | UI에 표시되는 애플리케이션 이름입니다. |

### <a name="application-templates"></a>애플리케이션 템플릿

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

사용자 고유의 애플리케이션 템플릿을 만든 경우 이 템플릿을 사용하여 새 애플리케이션을 만들 수 있습니다. 애플리케이션 템플릿을 요청하면 앱의 [애플리케이션 템플릿 내보내기](howto-use-app-templates.md#create-an-application-template) 섹션 아래에 있는 내보낸 앱의 URL 공유 가능 링크에 표시된 앱 ID를 입력합니다.

## <a name="view-your-applications"></a>애플리케이션 보기

[az iot central app list](/cli/azure/iot/central/app#az_iot_central_app_list) 명령을 사용하여 IoT Central 애플리케이션을 나열하고 메타데이터를 봅니다.

## <a name="modify-an-application"></a>애플리케이션 수정

[az iot central app update](/cli/azure/iot/central/app#az_iot_central_app_update) 명령을 사용하여 IoT Central 애플리케이션의 메타데이터를 업데이트합니다. 예를 들어 애플리케이션의 표시 이름을 변경합니다.

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>애플리케이션 제거

[az iot central app delete](/cli/azure/iot/central/app#az_iot_central_app_delete) 명령을 사용하여 IoT Central 애플리케이션을 삭제합니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>다음 단계

이제 Azure CLI에서 Azure IoT Central 애플리케이션을 관리하는 방법을 알아보았으므로 다음 단계를 진행하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [애플리케이션 관리](howto-administer.md)
