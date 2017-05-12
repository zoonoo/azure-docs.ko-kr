---
title: "PowerShell cmdlet을 사용하여 Azure IoT Hub 만들기 | Microsoft Docs"
description: "PowerShell cmdlet을 사용하여 IoT Hub를 만드는 방법입니다."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: 7914b9f1f747d246f58c19b7faf56357bd39a201
ms.contentlocale: ko-kr
ms.lasthandoff: 05/04/2017


---
# <a name="create-an-iot-hub-using-the-new-azurermiothub-cmdlet"></a>New-AzureRmIotHub cmdlet을 사용하여 IoT Hub 만들기

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>소개

Azure PowerShell cmdlet을 사용하여 Azure IoT Hub를 만들고 관리할 수 있습니다. 이 자습서에서는 PowerShell로 IoT Hub를 만드는 방법을 보여 줍니다.

> [!NOTE]
> Azure에는 리소스를 만들고 작업하는 [Azure Resource Manager와 클래식](../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다.  이 문서에서는 Azure Resource Manager 배포 모델 사용에 대해 설명합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 내에 [계정][lnk-free-trial]을 만들 수 있습니다.
* [Azure PowerShell cmdlet][lnk-powershell-install]

## <a name="connect-to-your-azure-subscription"></a>Azure 구독에 연결
PowerShell 명령 프롬프트에서 다음 명령을 입력하여 Azure 구독에 로그인합니다.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>리소스 그룹 만들기

IoT Hub를 배포할 리소스 그룹이 필요합니다. 기존 리소스 그룹을 사용하거나 리소스 그룹을 새로 만들 수 있습니다.

다음 명령을 사용하여 IoT Hub를 배포할 수 있는 위치를 검색할 수 있습니다.

```powershell
((Get-AzureRmResourceProvider `
  -ProviderNamespace Microsoft.Devices).ResourceTypes `
  | Where-Object ResourceTypeName -eq IoTHubs).Locations
```

IoT Hub가 지원되는 위치 중 하나에서 IoT Hub에 대한 리소스 그룹을 만들려면 다음 명령을 사용합니다. 이 예에서는 **미국 동부** 지역에 **MyIoTRG1**이라는 리소스 그룹을 만듭니다.

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

이전 단계에서 만든 리소스 그룹에 IoT Hub를 만들려면 다음 명령을 사용합니다. 이 예제에서는 **미국 동부** 지역에 **MyTestIoTHub**라는 **S1** 허브를 만듭니다.

```powershell
New-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

> [!NOTE]
> IoT Hub의 이름은 고유해야 합니다.

다음 명령을 사용하여 구독에 있는 모든 IoT Hub를 나열할 수 있습니다.

```powershell
Get-AzureRmIotHub
```

이전 예제에서는 대금이 청구되는 S1 표준 IoT Hub를 추가합니다. 다음 명령을 사용하여 IoT Hub를 삭제할 수 있습니다.

```powershell
Remove-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

또는 다음 명령을 사용하여 리소스 그룹과 리소스 그룹이 포함된 모든 리소스를 제거할 수 있습니다.

```powershell
Remove-AzureRmResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>다음 단계

이제 PowerShell cmdlet을 사용하여 IoT Hub를 배포했으면 구체적인 내용을 알아볼 차례입니다.

* [IoT Hub로 작업하기 위한 다른 PowerShell cmdlet][lnk-iothub-cmdlets]을 검색합니다.
* [IoT Hub 리소스 공급자 REST API][lnk-rest-api]의 기능을 읽어보세요.

IoT Hub를 개발하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [C SDK 소개][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT Gateway SDK를 사용하는 장치 시뮬레이션][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-iothub-cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.iothub/
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

