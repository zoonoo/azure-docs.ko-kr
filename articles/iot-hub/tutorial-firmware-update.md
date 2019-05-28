---
title: Azure IoT Hub를 통해 디바이스 펌웨어 업데이트 | Microsoft Docs
description: 작업 및 디바이스 쌍을 사용하여 디바이스 펌웨어 업데이트 프로세스를 구현합니다.
services: iot-hub
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2019
ms.custom: mvc
ms.openlocfilehash: 57ec4990447070d1889f7476b89abb742296c056
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597528"
---
# <a name="tutorial-implement-a-device-firmware-update-process"></a>자습서: 디바이스 펌웨어 업데이트 프로세스 구현

IoT Hub에 연결된 디바이스에서 펌웨어를 업데이트해야 합니다. 예를 들어 펌웨어에 새로운 기능을 추가하거나 보안 패치를 적용하려고 합니다. 여러 IoT 시나리오에서는 물리적으로 방문한 다음, 디바이스에 펌웨어 업데이트를 수동으로 적용하는 것은 실용적이지 않습니다. 이 자습서에서는 허브에 연결된 백 엔드 애플리케이션을 통해 원격으로 펌웨어 업데이트 프로세스를 시작하고 모니터링할 수 있는 방법을 보여줍니다.

펌웨어 업데이트 프로세스를 만들고 모니터링하기 위해 이 자습서의 백 엔드 애플리케이션은 IoT Hub에서 _구성_을 만듭니다. IoT Hub [자동 디바이스 관리](iot-hub-auto-device-config.md)는 이 구성을 사용하여 모든 냉각기 디바이스에 대한 일련의 _디바이스 쌍 desired 속성_을 업데이트합니다. desired 속성은 필요한 펌웨어 업데이트의 세부 정보를 지정합니다. 냉각기 장치가 펌웨어 업데이트 프로세스를 실행하는 동안 _장치 쌍 reported 속성_을 사용하여 백 엔드 애플리케이션에 해당 상태를 보고합니다. 백 엔드 애플리케이션은 구성을 사용하여 장치에서 전송된 reported 속성을 모니터링하고 완료될 때까지 펌웨어 업데이트 프로세스를 추적할 수 있습니다.

![펌웨어 업데이트 프로세스](media/tutorial-firmware-update/Process.png)

이 자습서에서는 다음 작업을 완료합니다.

> [!div class="checklist"]
> * IoT Hub를 만들고 디바이스 ID 레지스트리에 테스트 디바이스를 추가합니다.
> * 구성을 만들어서 펌웨어 업데이트를 정의합니다.
> * 디바이스에서 펌웨어 업데이트 프로세스를 시뮬레이션합니다.
> * 펌웨어 업데이트 과정이 진행되면 디바이스의 상태 업데이트를 수신합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에서 실행하는 두 개의 샘플 애플리케이션은 Node.js를 사용하여 작성되었습니다. 개발 컴퓨터에 Node.js v10.x.x 이상이 필요합니다.

[nodejs.org](https://nodejs.org)에서 여러 플랫폼에 대한 Node.js를 다운로드할 수 있습니다.

다음 명령을 사용하여 개발 컴퓨터에서 Node.js의 현재 버전을 확인할 수 있습니다.

```cmd/sh
node --version
```

[https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip)에서 샘플 Node.js 프로젝트를 다운로드하고 ZIP 보관 파일을 추출합니다.

## <a name="set-up-azure-resources"></a>Azure 리소스 설정

이 자습서를 완료하려면 Azure 구독에 디바이스 ID 레지스트리에 디바이스가 추가된 IoT Hub가 있어야 합니다. 디바이스 ID 레지스트리의 항목을 사용하면 이 자습서에서 실행하는 시뮬레이션된 디바이스를 허브에 연결할 수 있습니다.

구독에 아직 IoT 허브를 설정하지 않은 경우 다음 CLI 스크립트를 사용하여 하나의 IoT 허브를 설정할 수 있습니다. 다음 스크립트는 IoT 허브에 대해 **tutorial-iot-hub**라는 이름을 사용하므로 실행할 때는 이 이름을 사용자의 고유 이름으로 바꿔야 합니다. 이 스크립트의 경우 **미국 중부** 지역에 리소스 그룹과 허브를 만들지만, 가장 가까운 지역으로 변경할 수 있습니다. 그리고 백 엔드 애플리케이션 예제에서 IoT Hub에 연결하는 데 사용하는 IoT Hub 서비스 연결 문자열을 검색합니다.

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Create a resource group
az group create --name tutorial-iot-hub-rg --location $location

# Create your free-tier IoT Hub. You can only have one free IoT Hub per subscription
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --sku F1

# Make a note of the service connection string, you need it later
az iot hub show-connection-string --name $hubname -o table

```

이 자습서에서는 **MyFirmwareUpdateDevice**라는 시뮬레이션된 디바이스를 사용합니다. 다음 스크립트는 이 디바이스를 디바이스 ID 레지스트리에 추가하고, 태그 값을 설정하고, 해당 연결 문자열을 검색합니다.

```azurecli-interactive
# Set the name of your IoT hub
hubname=tutorial-iot-hub

# Create the device in the identity registry
az iot hub device-identity create --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Add a device type tag
az iot hub device-twin update --device-id MyFirmwareUpdateDevice --hub-name $hubname --set tags='{"devicetype":"chiller"}'

# Retrieve the device connection string, you need this later
az iot hub device-identity show-connection-string --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

> [!TIP]
> Windows 명령 프롬프트 또는 Powershell 프롬프트에서 이러한 명령을 실행할 때 JSON 문자열을 인용하는 방법에 대한 정보는 [azure-iot-cli-extension 팁](https://github.com/Azure/azure-iot-cli-extension/wiki/Tips
)을 참조하세요.

## <a name="start-the-firmware-update"></a>펌웨어 업데이트 시작

백 엔드 애플리케이션에서 [자동 장치 관리 구성](iot-hub-automatic-device-management.md#create-a-configuration)을 만들어서 냉각기의 **devicetype**으로 태그로 지정된 모든 장치에서 펌웨어 업데이트 프로세스를 시작합니다. 이 섹션에서 수행하는 방법은 다음과 같습니다.

* 백 엔드 애플리케이션의 구성을 만듭니다.
* 완료할 작업을 모니터링합니다.

### <a name="use-desired-properties-to-start-the-firmware-upgrade-from-the-back-end-application"></a>desired 속성을 사용하여 백 엔드 애플리케이션의 펌웨어 업그레이드 시작

구성을 만드는 백 엔드 애플리케이션 코드를 보려면 다운로드한 샘플 Node.js 프로젝트의 **iot-허브/자습서/FirmwareUpdate** 폴더로 이동합니다. 그런 다음, 텍스트 편집기에서 ServiceClient.js 파일을 엽니다.

백 엔드 애플리케이션에서는 다음 구성을 만듭니다.

[!code-javascript[Automatic device management configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=configuration "Automatic device management configuration")]

다음 구성 파일에는 다음 섹션이 포함됩니다.

* `content`는 선택된 디바이스에 전송된 펌웨어 desired 속성을 지정합니다.
* `metrics`는 펌웨어 업데이트의 상태를 보고하는 실행할 쿼리를 지정합니다.
* `targetCondition`은 펌웨어 업데이트를 받을 디바이스를 선택합니다.
* `priorty`는 다른 구성에 대한 이 구성의 상대적 우선 순위를 설정합니다.

백 엔드 애플리케이션은 다음 코드를 사용하여 desired 속성을 설정하는 구성을 만듭니다.

[!code-javascript[Create configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=createConfiguration "Create configuration")]

구성을 만든 후에 애플리케이션은 펌웨어 업데이트를 모니터링합니다.

[!code-javascript[Monitor firmware update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=monitorConfiguration "Monitor firmware update")]

구성은 다음과 같은 두 가지 형식의 메트릭을 보고합니다.

* 대상으로 지정된 디바이스 수 및 업데이트가 적용된 디바이스 수를 보고하는 시스템 메트릭입니다.
* 구성에 추가한 쿼리에서 생성된 사용자 지정 메트릭입니다. 이 자습서에서 쿼리는 업데이트 프로세스의 각 단계에 있는 디바이스 수를 보고합니다.

### <a name="respond-to-the-firmware-upgrade-request-on-the-device"></a>디바이스에 대한 펌웨어 업그레이드 요청에 응답

백 엔드 애플리케이션에서 전송된 펌웨어 desired 속성을 처리하는 시뮬레이션된 장치 코드를 보려면 다운로드한 샘플 Node.js 프로젝트의 **iot-hub/Tutorials/FirmwareUpdate** 폴더로 이동합니다. 그런 다음, 텍스트 편집기에서 SimulatedDevice.js 파일을 엽니다.

시뮬레이션된 장치 애플리케이션은 장치 쌍에서 **properties.desired.firmware** desired 속성에 대한 업데이트의 처리기를 만듭니다. 처리기에서 업데이트 프로세스를 시작하기 전에 desired 속성에 대해 몇 가지 기본 검사를 수행합니다.

[!code-javascript[Handle desired property update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=initiateUpdate "Handle desired property update")]

## <a name="update-the-firmware"></a>펌웨어 업데이트

**initiateFirmwareUpdateFlow** 함수는 업데이트를 실행합니다. 이 함수는 **waterfall** 함수를 사용하여 시퀀스에서 업데이트 프로세스의 단계를 실행합니다. 이 예제에서 펌웨어 업데이트에는 네 가지 단계가 있습니다. 첫 번째 단계는 이미지를 다운로드하고, 두 번째 단계는 체크섬을 사용하여 이미지를 확인하고, 세 번째 단계는 이미지를 적용하고, 마지막 단계는 디바이스를 다시 부팅합니다.

[!code-javascript[Firmware update flow](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=firmwareupdateflow "Firmware update flow")]

시뮬레이션된 디바이스는 업데이트 프로세스 중에 reported 속성을 사용하여 진행률을 보고합니다.

[!code-javascript[Reported properties](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=reportedProperties "Reported properties")]

다음 코드 조각에서는 다운로드 단계의 구현을 보여줍니다. 시뮬레이션된 장치는 다운로드 단계 중에 reported 속성을 사용하여 백 엔드 애플리케이션에 상태 정보를 전송합니다.

[!code-javascript[Download image phase](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=downloadimagephase "Download image phase")]

## <a name="run-the-sample"></a>샘플 실행

이 섹션에서는 두 개의 애플리케이션 예제를 실행하여 백 엔드 애플리케이션이 시뮬레이션된 장치에서 펌웨어 업데이트 프로세스를 관리하도록 구성을 만드는 과정을 관찰합니다.

시뮬레이션된 장치 및 백 엔드 애플리케이션을 실행하려면 장치 및 서비스 연결 문자열이 필요합니다. 이 자습서의 시작에서 리소스를 만들 때 연결 문자열을 적어두었습니다.

시뮬레이션된 장치 애플리케이션을 실행하려면 셸 또는 명령 프롬프트 창을 열고 다운로드한 Node.js 프로젝트의 **iot-hub/Tutorials/FirmwareUpdate** 폴더로 이동합니다. 그런 다음, 다음 명령을 실행합니다.

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

백 엔드 애플리케이션을 실행하려면 다른 셸 또는 명령 프롬프트 창을 엽니다. 그런 다음, 다운로드한 Node.js 프로젝트의 **iot-hub/Tutorials/FirmwareUpdate** 폴더로 이동합니다. 그런 다음, 다음 명령을 실행합니다.

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

다음 스크린샷에서는 시뮬레이션된 장치 애플리케이션의 출력을 보여주고, 백 엔드 애플리케이션의 펌웨어 desired 속성 업데이트에 응답하는 방법을 보여줍니다.

![시뮬레이션된 디바이스](./media/tutorial-firmware-update/SimulatedDevice.png)

다음 스크린샷에서는 백 엔드 애플리케이션의 출력을 보여주고, 펌웨어 desired 속성을 업데이트하도록 구성을 만드는 방법을 강조 표시합니다.

![백 엔드 애플리케이션](./media/tutorial-firmware-update/BackEnd1.png)

다음 스크린샷에서는 백 엔드 애플리케이션의 출력을 보여주고, 시뮬레이션된 장치의 펌웨어 업데이트 메트릭을 모니터링하는 방법을 강조 표시합니다.

![백 엔드 애플리케이션](./media/tutorial-firmware-update/BackEnd2.png)

IoT Hub 장치 ID 레지스트리의 대기 시간 때문에 백 엔드 애플리케이션에 전송된 일부 상태 업데이트가 표시되지 않을 수 있습니다. IoT Hub의 **자동 디바이스 관리 -&gt; IoT 디바이스 구성** 섹션에 있는 포털에서 메트릭이 표시되지 않을 수도 있습니다.

![포털에서 구성 보기](./media/tutorial-firmware-update/portalview.png)

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서를 완료하려면 리소스 그룹과 IoT 허브를 그대로 두고 나중에 다시 사용합니다.

더 이상 IoT Hub가 필요하지 않으면 포털에서 IoT Hub와 리소스 그룹을 삭제합니다. 이렇게 하려면 IoT 허브가 포함된 **tutorial-iot-hub-rg** 리소스 그룹을 선택하고 **삭제**를 클릭합니다.

또는 CLI를 사용합니다.

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 연결된 디바이스에서 펌웨어 업데이트 프로세스를 구현하는 방법을 알아보았습니다. 다음 자습서로 넘어가서 Azure IoT Hub 포털 도구와 Azure CLI 명령을 사용하여 디바이스 연결을 테스트하는 방법에 대해 알아보세요.

> [!div class="nextstepaction"]
> [시뮬레이션된 디바이스를 사용하여 IoT Hub와 연결 테스트](tutorial-connectivity.md)
