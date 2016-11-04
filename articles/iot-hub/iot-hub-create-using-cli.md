---
title: CLI를 사용하여 IoT Hub 만들기 | Microsoft Docs
description: 이 문서에서는 Azure 명령줄 인터페이스를 사용하여 IoT Hub를 만듭니다.
services: iot-hub
documentationcenter: .net
author: BeatriceOltean
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2016
ms.author: boltean

---
# <a name="create-an-iot-hub-using-cli"></a>CLI를 사용하여 IoT Hub 만들기
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>소개
Azure 명령줄 인터페이스를 사용하여 Azure IoT Hub를 프로그래밍 방식으로 만들고 관리할 수 있습니다. 이 문서는 Azure CLI를 사용하여 IoT Hub를 만드는 방법을 보여줍니다.

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. [Azure 무료 평가판][lnk-free-trial] 계정을 몇 분 안에 만들 수 있습니다.
* [Azure CLI 0.10.4][lnk-CLI-install] 이상. Azure CLI가 이미 있다면 다음 명령을 사용하여 명령 프롬프트에서 현재 버전을 확인할 수 있습니다.
  ```
    azure --version
  ```

> [!NOTE]
> Azure에는 리소스를 만들고 작업하는 [Resource Manager와 클래식](../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. Azure CLI는 Azure Resource Manager 모드여야 합니다.
> 
> ```
> azure config mode arm
> ```
> 
> 

## <a name="set-your-azure-account-and-subscription"></a>Azure 계정 및 구독 설정
1. 명령 프롬프트에서 다음 명령을 입력하여 로그인합니다.
   
   ```
    azure login
   ```
   제안된 웹 브라우저와 코드를 사용하여 인증합니다.
2. Azure 구독이 여러 개 있는 경우 Azure에 연결하면 자격 증명과 연결된 모든 구독에 액세스할 수 있습니다. 다음 명령을 사용하여 구독 및 기본적으로 사용되는 구독을 볼 수 있습니다.
   
   ```
    azure account list 
   ```

나머지 명령을 실행할 구독 컨텍스트를 설정하려면 다음을 사용합니다.

```
    azure account set <subscription name>
```

1. 리소스 그룹이 없으면 **exampleResourceGroup**라는 이름의 리소스 그룹을 만듭니다. 
   ```
    azure group create -n exampleResourceGroup -l westus
   ```

> [!TIP]
> [Azure 리소스 및 리소스 그룹 관리를 위해 Azure CLI 사용][lnk-CLI-arm] 문서는 Azure CLI를 사용하여 Azure 리소스를 관리하는 방법에 대해 자세한 정보를 제공합니다. 
> 
> 

## <a name="create-and-iot-hub"></a>IoT Hub 만들기
필수 매개 변수:

```
 azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>  
    - <resourceGroup> The resource group name (case insensitive alphanumeric, underscore and hyphen, 1-64 length)
    - <name> (The name of the IoT hub to be created. The format is case insensitive alphanumeric, underscore and hyphen, 3-50 length )
    - <location> (The location (azure region/datacenter) where the IoT hub will be provisioned.
    - <sku-name> (The name of the sku, one of: [F1, S1, S2, S3] etc. For the latest full list refer to the pricing page for IoT Hub.
    - <units> (The number of provisioned units. Range : F1 [1-1] : S1, S2 [1-200] : S3 [1-10]. IoT Hub units are based on your total message count and the number of devices you want to connect.)
```
만들기에 사용할 수 있는 모든 매개 변수를 보려면 명령 프롬프트에서 help 명령을 사용합니다.

```
    azure iothub create -h 
```
간단한 예제:

 **exampleResourceGroup**이라는 리소스 그룹에 **exampleIoTHubName**이라는 IoT Hub를 만들려면 간단히 다음 명령을 실행합니다.

```
    azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [!NOTE]
> 이 CLI 명령은 대금이 청구되는 S1 표준 IoT Hub를 만듭니다. 다음 명령을 사용하여 IoT hub **exampleIoTHubName**을 삭제할 수 있습니다. 
> 
> ```
> azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
> ```
> 
> 
> 

## <a name="next-steps"></a>다음 단계
IoT Hub를 개발하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [IoT Hub SDK][lnk-sdks]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [Azure Portal을 사용하여 IoT Hub 관리][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]: ../xplat-cli-install.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-CLI-arm]: ../xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 



<!--HONumber=Oct16_HO2-->


