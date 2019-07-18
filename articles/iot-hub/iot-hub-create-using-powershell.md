---
title: PowerShell cmdlet을 사용하여 Azure IoT Hub 만들기 | Microsoft Docs
description: PowerShell cmdlet을 사용하여 IoT Hub를 만드는 방법입니다.
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: fd376728a1ebdf769c7f2ae76d61a60703e13711
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66146594"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>새로 만들기-AzIotHub cmdlet을 사용 하 여 IoT hub 만들기

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>소개

Azure PowerShell cmdlet을 사용하여 Azure IoT Hub를 만들고 관리할 수 있습니다. 이 자습서에서는 PowerShell로 IoT Hub를 만드는 방법을 보여 줍니다.

이 방법 문서를 완료하려면 Azure 구독이 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Azure 구독에 연결

Cloud Shell을 사용하는 경우 이미 구독에 로그인되어 있습니다. 대신 PowerShell을 로컬로 실행하는 경우 다음 명령을 입력하여 Azure 구독에 로그인합니다.

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

IoT Hub를 배포할 리소스 그룹이 필요합니다. 기존 리소스 그룹을 사용하거나 리소스 그룹을 새로 만들 수 있습니다.

IoT hub에 대 한 리소스 그룹을 만들려면 사용 합니다 [새로 만들기-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/New-azResourceGroup) 명령입니다. 이 예에서는 **미국 동부** 지역에 **MyIoTRG1**이라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

이전 단계에서 만든 리소스 그룹에 IoT hub를 만들려면 사용 합니다 [새로 만들기-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/New-azIotHub) 명령 합니다. 이 예제에서는 **미국 동부** 지역에 **MyTestIoTHub**라는 **S1** 허브를 만듭니다.

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

IoT 허브의 이름은 전역적으로 고유해야 합니다.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

사용 하 여 구독에 있는 모든 IoT hub를 나열할 수 있습니다 합니다 [Get AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/Get-azIotHub) 명령:

```azurepowershell-interactive
Get-AzIotHub
```

이 예제에서는 이전 단계에서 만든 S1 표준 IoT Hub를 보여 줍니다.

사용 하 여 IoT hub를 삭제할 수 있습니다 합니다 [제거 AzIotHub](https://docs.microsoft.com/powershell/module/az.iothub/remove-aziothub) 명령:

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

또는 리소스 그룹을 제거할 수 있습니다 하 고 리소스를 모두 포함 된 사용 하는 [제거 AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/Remove-azResourceGroup) 명령:

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>다음 단계

이제 PowerShell cmdlet을 사용하여 IoT 허브를 배포했습니다. 더 자세히 알아보려면 다음 문서를 참조하세요.

* [IoT 허브 작업을 다른 PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.iothub/)

* [IoT Hub 리소스 공급자 REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource)

IoT Hub를 개발하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [C SDK 소개](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [Azure IoT Edge를 사용하여 에지 디바이스에 AI 배포](../iot-edge/tutorial-simulate-device-linux.md)
