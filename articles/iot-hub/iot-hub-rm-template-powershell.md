<properties
	pageTitle="ARM 템플릿 및 PowerShell을 사용하여 IoT Hub 만들기 | Microsoft Azure"
	description="이 자습서에 따라 리소스 관리자 템플릿을 사용하여 PowerShell로 IoT Hub를 만드는 작업을 시작할 수 있습니다."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="07/19/2016"
     ms.author="dobett"/>

# Powershell을 사용하여 IoT Hub 만들기

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## 소개

ARM(Azure 리소스 관리자)을 사용하여 Azure IoT Hub를 프로그래밍 방식으로 만들고 관리합니다. 이 자습서에서는 리소스 관리자 템플릿을 사용하여 PowerShell로 IoT Hub를 만드는 방법을 보여 줍니다.

> [AZURE.NOTE] Azure에는 리소스를 만들고 작업하기 위한 두 가지 다양한 배포 모델이 있습니다. [리소스 관리자 및 클래식](../resource-manager-deployment-model.md) 이 문서에서는 리소스 관리자 배포 모델 사용에 대해 설명합니다.

이 자습서를 완료하려면 다음이 필요합니다.

- 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][lnk-free-trial]을 참조하세요.
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] 이상

> [AZURE.TIP] [Azure 리소스 관리자로 Azure PowerShell 사용][lnk-powershell-arm] 문서에 PowerShell 스크립트 및 ARM 템플릿을 사용하여 Azure 리소스를 만드는 방법에 대한 자세한 내용이 설명되어 있습니다.

## Azure 구독에 연결

PowerShell 명령 프롬프트에서 다음 명령을 입력하여 Azure 구독에 로그인합니다.

```
Login-AzureRmAccount
```

다음 명령을 사용하여 IoT Hub 및 현재 지원되는 API 버전을 배포할 수 있는 위치를 검색할 수 있습니다.

```
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

IoT Hub가 지원되는 위치 중 하나에서 다음 명령을 사용하여 IoT Hub를 포함하는 리소스 그룹을 만듭니다. 이 예에서는 **MyIoTRG1**이라는 리소스 그룹을 만듭니다.

```
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## IoT hub를 만들 템플릿 제출

JSON 템플릿을 사용하여 리소스 그룹에 새 IoT hub를 만듭니다. 템플릿을 사용하여 기존 IoT Hub를 변경할 수도 있습니다.

1. 텍스트 편집기에서 새로운 표준 IoT Hub를 만드는 다음 리소스 정의를 사용하여 **template.json**이라는 ARM 템플릿을 만듭니다. 이 예에서는 **미국 동부** 지역에 IoT Hub를 추가하고 이벤트 허브와 호환되는 끝점에 두 개의 소비자 그룹(**cg1** 및 **cg2**)을 만들고 **2016-02-03** API 버전을 사용합니다. 또한 이 템플릿에서 매개 변수로 IoT Hub 이름 **hubName**을 전달해야 합니다.

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg1')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg2')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

2. 로컬 컴퓨터에 템플릿 파일을 저장합니다. 이 예에서는 **c:\\templates** 폴더에 저장하는 것으로 가정합니다.

3. 다음 명령을 실행하여 새 IoT Hub를 배포하고, 매개 변수로 IoT Hub 이름을 전달합니다. 이 예에서, IoT Hub 이름은 **abcmyiothub**입니다. (이 이름은 전역적으로 고유해야 하므로 사용자의 이름 또는 이니셜을 포함해야 합니다.)

    ```
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```

4. 앞에서 만든 IoT Hub의 키가 출력에 표시됩니다.

5. [포털][lnk-azure-portal]을 방문하여 리소스 목록을 보거나 **Get AzureRmResource** PowerShell cmdlet을 사용하여 응용 프로그램이 새 IoT Hub를 추가했는지 확인할 수 있습니다.

> [AZURE.NOTE] 이 예제 응용 프로그램은 대금이 청구되는 S1 표준 IoT Hub를 추가합니다. 완료되면 [포털][lnk-azure-portal] 또는 **Remove-AzureRmResource** PowerShell cmdlet을 사용하여 IoT Hub를 삭제할 수 있습니다.

## 다음 단계

ARM 템플릿을 사용하여 PowerShell에서 IoT Hub를 배포했으니 구체적인 내용을 알아볼 차례입니다.

- [IoT Hub 리소스 공급자 REST API][lnk-rest-api]의 기능을 읽어보세요.
- Azure 리소스 관리자의 기능에 대한 자세한 내용은 [Azure 리소스 관리자 개요][lnk-azure-rm-overview]를 참조하세요.

IoT Hub를 개발하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [C SDK 소개][lnk-c-sdk]
- [IoT Hub SDK][lnk-sdks]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

- [솔루션 디자인][lnk-design]
- [샘플 UI를 사용하여 장치 관리 탐색][lnk-dmui]
- [Gateway SDK를 사용하는 장치 시뮬레이션][lnk-gateway]
- [Azure 포털을 사용하여 IoT Hub 관리][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-powershell-arm]: ../powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-sdks-summary.md

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0720_2016-->