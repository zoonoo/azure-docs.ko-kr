---
title: 런타임에서 디바이스를 관리하는 방법 알아보기 - Azure IoT Edge | Microsoft Docs
description: Learn how the IoT Edge runtime manages modules, security, communication, and reporting on your devices
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6382159c2a9d73b9db21dd0467be0e68cf4b4c2d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456602"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Azure IoT Edge 런타임 및 해당 아키텍처 이해

The IoT Edge runtime is a collection of programs that turn a device into an IoT Edge device. Collectively, the IoT Edge runtime components enable IoT Edge devices to receive code to run at the edge and communicate the results. 

The IoT Edge runtime is responsible for the following functions on IoT Edge devices:

* 디바이스에 워크로드를 설치하고 업데이트합니다.
* 디바이스에서 Azure IoT Edge 보안 표준을 유지합니다.
* Ensure that [IoT Edge modules](iot-edge-modules.md) are always running.
* 원격 모니터링을 위해 모듈 상태를 클라우드에 보고합니다.
* Manage communication between downstream devices and IoT Edge devices.
* Manage communication between modules on the IoT Edge device.
* Manage communication between the IoT Edge device and the cloud.

![런타임은 인사이트 및 모듈 상태를 IoT Hub에 전달합니다.](./media/iot-edge-runtime/Pipeline.png)

IoT Edge 런타임의 책임은 모듈 관리와 통신이라는 두 가지 범주로 나뉩니다. These two roles are performed by two components that are part of the IoT Edge runtime. The *IoT Edge hub* is responsible for communication, while the *IoT Edge agent* deploys and monitors the modules. 

IoT Edge 허브와 IoT Edge 에이전트는 모두 IoT Edge 디바이스에서 실행되는 다른 모듈과 마찬가지로 모듈입니다. They're sometimes referred to as the *runtime modules*. 

## <a name="iot-edge-hub"></a>IoT Edge 허브

IoT Edge 허브는 Azure IoT Edge 런타임을 구성하는 두 가지 모듈 중 하나입니다. IoT Hub와 동일한 프로토콜 엔드포인트를 노출하여 IoT Hub에 대한 로컬 프록시 역할을 합니다. 이 일관성은 클라이언트(디바이스 또는 모듈)에서 IoT Hub와 마찬가지로 IoT Edge 런타임에 연결할 수 있음을 의미합니다. 

>[!NOTE]
> IoT Edge hub supports clients that connect using MQTT or AMQP. HTTP를 사용하는 클라이언트는 지원하지 않습니다. 

IoT Edge 허브는 로컬로 실행되는 IoT Hub의 전체 버전이 아닙니다. IoT Edge 허브에서 자동으로 IoT Hub에 위임하는 몇 가지 사항이 있습니다. 예를 들어 디바이스에서 처음으로 연결하려고 할 때 IoT Edge 허브는 IoT Hub에 인증 요청을 전달합니다. 첫 번째 연결이 설정되면 IoT Edge 허브에서 보안 정보를 로컬로 캐시합니다. 클라우드에서 인증하지 않아도 해당 디바이스로부터의 후속 연결이 허용됩니다. 

IoT Edge 허브는 IoT Edge 솔루션에서 사용하는 대역폭을 줄이기 위해 클라우드에 실제로 연결되는 수를 최적화합니다. IoT Edge hub takes logical connections from clients like modules or downstream devices and combines them for a single physical connection to the cloud. 이 프로세스의 세부 정보는 솔루션의 나머지 부분에 투명합니다. 클라이언트는 모두 동일한 연결을 통해 전송되는 경우에도 클라우드에 대한 자체 연결이 있다고 생각하면 됩니다. 

![IoT Edge 허브는 물리적 디바이스와 IoT Hub 간의 게이트웨이입니다.](./media/iot-edge-runtime/Gateway.png)

IoT Edge 허브는 IoT Hub에 연결되어 있는지 여부를 결정할 수 있습니다. 연결이 끊어진 경우 IoT Edge 허브는 메시지 또는 쌍 업데이트를 로컬로 저장합니다. 연결이 다시 설정되면 모든 데이터가 동기화됩니다. 이 임시 캐시에 사용되는 위치는 IoT Edge 허브 모듈 쌍의 속성으로 결정됩니다. 캐시의 크기는 제한되지 않으며, 디바이스에 스토리지 용량이 있는 동안에는 증가합니다. For more information, see [Offline capabilities](offline-capabilities.md).

### <a name="module-communication"></a>모듈 통신

IoT Edge 허브는 모듈 간 통신을 용이하게 합니다. IoT Edge 허브를 메시지 broker로 사용하면 모듈이 서로 독립적으로 유지됩니다. 모듈은 메시지를 수락하는 입력과 메시지를 쓰는 출력을 지정하기만 하면 됩니다. A solution developer can stitch these inputs and outputs together so that the modules process data in the order specific to that solution. 

![IoT Edge 허브는 모듈 간 통신을 용이하게 합니다](./media/iot-edge-runtime/module-endpoints.png)

IoT Edge 허브에 데이터를 보내려면 모듈에서 SendEventAsync 메서드를 호출합니다. 첫 번째 인수는 메시지를 보낼 출력을 지정합니다. The following pseudocode sends a message on **output1**:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync("output1", message); 
   ```

메시지를 수신하려면 특정 입력에 들어오는 메시지를 처리하는 콜백을 등록합니다. The following pseudocode registers the function messageProcessor to be used for processing all messages received on **input1**:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

For more information about the ModuleClient class and its communication methods, see the API reference for your preferred SDK language: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable), or [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

솔루션 개발자는 IoT Edge 허브에서 모듈 간에 메시지를 전달하는 방식을 결정하는 규칙을 지정합니다. Routing rules are defined in the cloud and pushed down to IoT Edge hub in its module twin. IoT Hub 경로에 대한 동일한 구문이 Azure IoT Edge의 모듈 간 경로를 정의하는 데 사용됩니다. For more information, see [Learn how to deploy modules and establish routes in IoT Edge](module-composition.md).   

![모듈 간 경로는 IoT Edge 허브를 통과합니다.](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>IoT Edge 에이전트

IoT Edge 에이전트는 Azure IoT Edge 런타임을 구성하는 다른 모듈입니다. 모듈을 인스턴스화하고, 모듈을 계속 실행하며, 모듈의 상태를 IoT Hub에 다시 보고합니다. This configuration data is written as a property of the IoT Edge agent module twin. 

[IoT Edge 보안 디먼](iot-edge-security-manager.md)은 디바이스 시작 시 IoT Edge 에이전트를 시작합니다. 에이전트는 IoT Hub에서 모듈 쌍을 검색하고 배포 매니페스트를 검사합니다. 배포 매니페스트는 시작되어야 하는 모듈을 선언하는 JSON 파일입니다. 

배포 매니페스트의 각 항목에는 모듈에 대한 특정 정보가 포함되며, 모듈의 수명 주기를 제어하기 위해 IoT Edge 에이전트에서 사용합니다. 더 유용한 속성 중 일부는 다음과 같습니다. 

* **settings.image** – IoT Edge 에이전트에서 모듈을 시작하는 데 사용하는 컨테이너 이미지입니다. 이미지가 암호로 보호되는 경우 IoT Edge 에이전트는 컨테이너 레지스트리에 대한 자격 증명으로 구성되어야 합니다. 컨테이너 레지스트리에 대한 자격 증명은 배포 매니페스트를 사용하여 또는 IoT Edge 프로그램 폴더에서 `config.yaml` 파일을 업데이트하여 IoT Edge 디바이스 자체에서 원격으로 구성될 수 있습니다.
* **settings.createOptions** – A string that is passed directly to the Moby container daemon when starting a module’s container. Adding options in this property allows for advanced configurations like port forwarding or mounting volumes into a module’s container.  
* **status** – IoT Edge 에이전트에서 모듈을 배치하는 상태입니다. Usually, this value is set to *running* as most people want the IoT Edge agent to immediately start all modules on the device. 그러나 중지할 모듈의 초기 상태를 지정하고, 나중에 IoT Edge 에이전트에 모듈 시작을 알릴 때까지 기다릴 수 있습니다. The IoT Edge agent reports the status of each module back to the cloud in the reported properties. desired 속성과 reported 속성의 차이는 표시기 또는 오작동 디바이스입니다. 지원되는 상태는 다음과 같습니다.
   * 다운로드 중
   * 실행 중
   * 비정상
   * 실패
   * 중지됨
* **restartPolicy** – IoT Edge 에이전트에서 모듈을 다시 시작하는 방법입니다. 가능한 값은 다음과 같습니다.
   * `never` – The IoT Edge agent never restarts the module.
   * `on-failure` - If the module crashes, the IoT Edge agent restarts it. 모듈이 완전히 종료되면 IoT Edge 에이전트에서 해당 모듈을 다시 시작하지 않습니다.
   * `on-unhealthy` - If the module crashes or is considered unhealthy, the IoT Edge agent restarts it.
   * `always` - If the module crashes, is considered unhealthy, or shuts down in any way, the IoT Edge agent restarts it. 
* **imagePullPolicy** - Whether the IoT Edge agent attempts to pull the latest image for a module automatically or not. If you don't specify a value, the default is *onCreate*. 가능한 값은 다음과 같습니다. 
   * `on-create` - When starting a module or updating a module based on a new deployment manifest, the IoT Edge agent will attempt to pull the module image from the container registry.
   * `never` - The IoT Edge agent will never attempt to pull the module image from the container registry. The expectation is that the module image is cached on the device, and any module image updates are made manually or managed by a third-party solution. 

IoT Edge 에이전트는 IoT Hub에 런타임 응답을 보냅니다. 가능한 응답 목록은 다음과 같습니다.
  * 200 - 확인
  * 400 - 배포 구성이 잘못되었거나 유효하지 않습니다.
  * 417 - The device doesn't have a deployment configuration set.
  * 412 - 배포 구성의 스키마 버전이 잘못되었습니다.
  * 406 - IoT Edge 디바이스가 오프라인 상태이거나 상태 보고서를 전송하지 않습니다.
  * 500 - IoT Edge 런타임에서 오류가 발생했습니다.

For more information, see [Learn how to deploy modules and establish routes in IoT Edge](module-composition.md).   

### <a name="security"></a>보안

IoT Edge 에이전트는 IoT Edge 디바이스의 보안에서 중요한 역할을 합니다. 예를 들어 모듈의 이미지를 시작하기 전에 해당 이미지의 확인과 같은 작업을 수행합니다. 

For more information about the Azure IoT Edge security framework, read about the [IoT Edge security manager](iot-edge-security-manager.md).

## <a name="next-steps"></a>다음 단계

[Azure IoT Edge 모듈 이해](iot-edge-modules.md)
