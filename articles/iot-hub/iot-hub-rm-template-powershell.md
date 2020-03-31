---
title: 템플릿을 사용하여 Azure IoT Hub 만들기(PowerShell) | Microsoft Docs
description: Azure 리소스 관리자 템플릿을 사용하여 Azure PowerShell을 사용하여 IoT 허브를 만드는 방법
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: a1f878361a96c5584f43b31135d79ff799f66efa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75976615"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Azure Resource Manager 템플릿을 사용하여 IoT Hub 만들기(PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Azure 리소스 관리자 템플릿을 사용하여 IoT Hub 및 소비자 그룹을 만드는 방법을 알아봅니다. Resource Manager 템플릿은 솔루션에 배포해야 하는 리소스를 정의하는 JSON 파일입니다. 리소스 관리자 템플릿 개발에 대한 자세한 내용은 [Azure 리소스 관리자 설명서를](https://docs.microsoft.com/azure/azure-resource-manager/)참조하십시오.

Azure 구독이 없는 경우 시작하기 전에 [무료 계정을 만드세요.](https://azure.microsoft.com/free/)

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

이 빠른 시작에 사용되는 리소스 관리자 템플릿은 [Azure 빠른 시작 템플릿에서 온 것입니다.](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/) 다음은 템플릿의 복사본입니다.

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

템플릿은 세 가지 끝점(eventhub, 클라우드-장치 및 메시징)과 소비자 그룹이 있는 Azure Iot 허브를 만듭니다. 자세한 템플릿 샘플은 [Azure 빠른 시작 템플릿 을](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular)참조하십시오. Iot Hub 템플릿 스키마는 [여기에서](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions)찾을 수 있습니다.

템플릿을 배포하는 방법에는 여러 가지가 있습니다.  이 자습서에서는 Azure PowerShell을 사용합니다.

PowerShell 스크립트를 실행하려면 **시도하여** Azure 클라우드 셸을 엽니다. 스크립트를 붙여넣은 다음 셸을 마우스 오른쪽 단추로 클릭한 다음 붙여넣기를 선택합니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$iotHubName = Read-Host -Prompt "Enter the IoT Hub name"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-with-consumergroup-create/azuredeploy.json" `
    -iotHubName $iotHubName
```

PowerShell 스크립트에서 볼 수 있듯이 사용되는 템플릿은 Azure 빠른 시작 템플릿에서 사용됩니다. 직접 사용하려면 먼저 템플릿 파일을 Cloud 셸에 업로드한 다음 스위치를 `-TemplateFile` 사용하여 파일 이름을 지정해야 합니다.  예를 들어 [템플릿 배포를](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template)참조하십시오.

## <a name="next-steps"></a>다음 단계

이제 Azure 리소스 관리자 템플릿을 사용하여 IoT 허브를 배포한 다음 을 자세히 살펴볼 수 있습니다.

* [IoT Hub 리소스 공급자 REST API][lnk-rest-api]의 기능을 읽어보세요.
* Azure Resource Manager의 기능에 대해 자세히 알아보려면 [Azure Resource Manager 개요][lnk-azure-rm-overview]를 참조하세요.
* 템플릿에서 사용할 JSON 구문 및 속성은 [Microsoft.Devices 리소스 종류](/azure/templates/microsoft.devices/iothub-allversions)를 참조하세요.

IoT Hub를 개발하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [C SDK 소개][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [Azure IoT Edge를 사용하여 에지 디바이스에 AI 배포][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-powershell-arm]: ../azure-resource-manager/management/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
