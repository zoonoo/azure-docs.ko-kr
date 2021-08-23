---
title: 모듈 쌍 모니터 - Azure IoT Edge
description: 연결 및 상태 확인을 위해 디바이스 쌍 및 모듈 쌍을 해석하는 방법을 설명합니다.
author: kgremban
ms.author: kgremban
ms.date: 05/29/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1bb022737c3c6f228e741681561867414c118592
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567013"
---
# <a name="monitor-module-twins"></a>모듈 쌍 모니터링

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Hub의 모듈 쌍은 IoT Edge 배포의 연결 및 상태를 모니터할 수 있게 해줍니다. 모듈 쌍은 실행 중인 모듈의 성능에 대한 유용한 정보를 IoT 허브에 저장합니다. [IoT Edge 에이전트](iot-edge-runtime.md#iot-edge-agent) 및 [IoT Edge 허브](iot-edge-runtime.md#iot-edge-hub) 런타임 모듈은 각각 모듈 쌍 `$edgeAgent` 및 `$edgeHub`를 유지 관리합니다.

* `$edgeAgent`에는 IoT Edge 에이전트 및 IoT Edge 허브 런타임 모듈과 사용자 지정 모듈에 대한 상태 및 연결 데이터가 포함됩니다. IoT Edge 에이전트는 모듈 배포, 모니터링, Azure IoT Hub에 대한 연결 상태 보고를 처리합니다.
* `$edgeHub`에는 디바이스에서 실행되는 IoT Edge 허브와 Azure IoT Hub 사이의 통신에 대한 데이터가 포함됩니다. 여기에는 다운스트림 디바이스에서 들어오는 메시지의 처리가 포함됩니다. IoT Edge 허브는 Azure IoT Hub와 IoT Edge 디바이스 및 모듈 사이의 통신을 처리합니다.

데이터는 모듈 쌍의 JSON 구조에 있는 원하는 속성 집합 및 보고된 속성 집합과 함께 메타데이터, 태그로 구성됩니다. deployment.json 파일에 지정한 원하는 속성은 모듈 쌍에 복사됩니다. IoT Edge 에이전트 및 IoT Edge 허브는 각각 해당 모듈에 대해 보고된 속성을 업데이트합니다.

마찬가지로 deployment.json 파일에 있는 사용자 지정 모듈에 대해 지정된 원하는 속성이 모듈 쌍에 복사되지만, 해당 솔루션이 보고된 속성 값을 제공합니다.

이 문서에서는 Azure portal, Azure CLI 및 Visual Studio Code에서 모듈 쌍을 검토하는 방법을 설명합니다. 디바이스가 배포를 수신하는 방법을 모니터하려면 [IoT Edge 배포 모니터](how-to-monitor-iot-edge-deployments.md)를 참조하세요. 모듈 쌍에 대한 개념은 [IoT Hub에서 모듈 쌍 이해 및 사용](../iot-hub/iot-hub-devguide-module-twins.md)을 참조하세요.

> [!TIP]
> IoT Edge 디바이스가 해당 IoT 허브에서 분리되면 런타임 모듈의 보고된 속성이 오래될 수 있습니다. `$edgeAgent` 모듈에 [ping](how-to-edgeagent-direct-method.md#ping)을 수행하여 연결이 끊어졌는지 확인할 수 있습니다.

## <a name="monitor-runtime-module-twins"></a>런타임 모듈 쌍 모니터

배포 연결 문제를 해결하려면 IoT Edge 에이전트 및 IoT Edge 허브 런타임 모듈 쌍을 검토한 후 다른 모듈로 드릴다운합니다.

### <a name="monitor-iot-edge-agent-module-twin"></a>IoT Edge 에이전트 모듈 쌍 모니터

다음 JSON은 Visual Studio Code에서 대부분의 JSON 섹션이 축소된 상태로 `$edgeAgent` 모듈 쌍을 보여줍니다.

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeAgent",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDUz",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 53,
  "properties": {
    "desired": { "···" },
    "reported": {
      "schemaVersion": "1.0",
      "version": { "···" },
      "lastDesiredStatus": { "···" },
      "runtime": { "···" },
      "systemModules": {
        "edgeAgent": { "···" },
        "edgeHub": { "···" }
      },
      "lastDesiredVersion": 5,
      "modules": {
        "SimulatedTemperatureSensor": { "···" }
      },
      "$metadata": { "···" },
      "$version": 48
    }
  }
}
```

JSON에 대해서는 위쪽부터 시작하여 다음 섹션에서 설명합니다.

* 메타데이터 - 연결 데이터를 포함합니다. 흥미롭게도 IoT Edge 에이전트의 연결 상태는 항상 연결이 끊어진 상태 `"connectionState": "Disconnected"`입니다. 연결 상태 이유는 D2C(디바이스-클라우드) 메시지와 관련이 있고 IoT Edge 에이전트가 D2C 메시지를 보내지 않습니다.
* 속성 - `desired` 및 `reported` 하위 섹션을 포함합니다.
* Properties.desired - (축소된 상태로 표시) deployment.json 파일에서 연산자로 설정된 예상된 속성 값입니다.
* Properties.reported - IoT Edge 에이전트에서 보고된 최신 속성 값입니다.

`properties.desired` 및 `properties.reported` 섹션 모두 비슷한 구조를 가지며, 스키마, 버전, 런타임 정보에 대한 추가 메타데이터를 포함합니다. 또한 사용자 지정 모듈에 대한 `modules` 섹션(예: `SimulatedTemperatureSensor`), `$edgeAgent` 및 `$edgeHub` 런타임 모듈에 대한 `systemModules` 섹션이 포함됩니다.

보고된 속성 값을 원하는 값과 비교하여 차이를 확인하고 문제 해결에 도움이 되는 연결 해제를 식별할 수 있습니다. 이러한 비교를 수행할 때는 조사 중인 속성에 대해 `metadata` 섹션에서 `$lastUpdated` 보고된 값을 확인합니다.

문제 해결을 위해서는 다음 속성을 조사하는 것이 중요합니다.

* **exitcode** - 값이 0이 아니면 모듈이 오류로 인해 중지된 것입니다. 하지만 오류 코드 137 또는 143은 모듈이 의도적으로 중지된 상태로 설정된 경우에 사용됩니다.

* **lastStartTimeUtc** - 컨테이너가 마지막으로 시작된 **DateTime** 을 표시합니다. 컨테이너가 시작되지 않았으면 이 값이 0001-01-01T00:00:00Z입니다.

* **lastExitTimeUtc** - 컨테이너가 마지막으로 완료된 **DateTime** 을 표시합니다. 컨테이너가 실행 중이고 이전에 중지되지 않았으면 이 값이 0001-01-01T00:00:00Z입니다.

* **runtimeStatus** - 다음 값 중 하나일 수 있습니다.

    | 값 | Description |
    | --- | --- |
    | 알 수 없음 | 배포가 생성될 때까지의 기본 상태입니다. |
    | 백오프 | 모듈 시작이 예약되었지만 현재 실행 중이 아닙니다. 이 값은 다시 시작할 때 상태가 변경되는 모듈에 유용합니다. 실패한 모듈이 휴지 기간 중 다시 시작되도록 기다리는 동안 이 모듈은 백오프 상태가 됩니다. |
    | 실행 중 | 모듈이 현재 실행 중임을 나타냅니다. |
    | 비정상 | 상태 프로브 검사가 실패했거나 시간이 초과되었음을 나타냅니다. |
    | 중지됨 | 모듈이 성공적으로 종료되었음을 나타냅니다(종료 코드 0). |
    | 실패 | 0이 아닌 오류 종료 코드와 함께 모듈이 종료되었음을 나타냅니다. 이 모듈은 실제 다시 시작 정책에 따라 이 상태에서 백오프로 다시 전환될 수 있습니다. 이 상태는 모듈에 복구할 수 없는 오류가 발생했음을 나타낼 수 있습니다. MMA(Microsoft Monitoring Agent)가 모듈을 더 이상 되살릴 수 없어서 새 배포가 필요한 경우 오류가 발생합니다. |

자세한 내용은 [EdgeAgent 보고된 속성](module-edgeagent-edgehub.md#edgeagent-reported-properties)을 참조하세요.

### <a name="monitor-iot-edge-hub-module-twin"></a>IoT Edge 허브 모듈 쌍 모니터

다음 JSON은 Visual Studio Code에서 대부분의 JSON 섹션이 축소된 상태로 `$edgeHub` 모듈 쌍을 보여줍니다.

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeHub",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDU2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Connected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 102,
    "properties": {
      "desired": { "···" },
      "reported": {
        "schemaVersion": "1.0",
        "version": { "···" },
      "lastDesiredVersion": 5,
      "lastDesiredStatus": { "···" },
      "clients": {
        "Windows109/SimulatedTemperatureSensor": {
          "status": "Disconnected",
          "lastConnectedTimeUtc": "2020-04-08T21:42:42.1743956Z",
          "lastDisconnectedTimeUtc": "2020-04-09T07:02:42.1398325Z"
        }
      },
      "$metadata": { "···" },
      "$version": 97
    }
  }
}

```

JSON에 대해서는 위쪽부터 시작하여 다음 섹션에서 설명합니다.

* 메타데이터 - 연결 데이터를 포함합니다.

* 속성 - `desired` 및 `reported` 하위 섹션을 포함합니다.
* Properties.desired - (축소된 상태로 표시) deployment.json 파일에서 연산자로 설정된 예상된 속성 값입니다.
* Properties.reported - IoT Edge 허브에서 보고된 최신 속성 값입니다.

다운스트림 디바이스에 문제가 있는 경우 이 데이터를 검토하는 것으로 시작하는 것이 좋습니다.

## <a name="monitor-custom-module-twins"></a>사용자 지정 모듈 쌍 모니터

사용자 지정 모듈의 연결에 대한 정보는 IoT Edge 에이전트 모듈 쌍에서 유지 관리됩니다. 사용자 지정 모듈의 모듈 쌍은 주로 솔루션 데이터를 유지 관리하기 위해 사용됩니다. deployment.json 파일에 정의한 원하는 속성이 모듈 쌍에 반영되고, 모듈이 필요에 따라 보고된 속성 값을 업데이트할 수 있습니다.

모듈의 애플리케이션 코드에 따라 [Azure IoT Hub 디바이스 SDK](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks)와 원하는 프로그래밍 언어를 사용하여 모듈 쌍에서 보고된 속성 값을 업데이트할 수 있습니다. 다음 절차에서는 이를 수행하기 위해 Azure SDK for .NET을 사용하여 [SimulatedTemperatureSensor](https://github.com/Azure/iotedge/blob/dd5be125df165783e4e1800f393be18e6a8275a3/edge-modules/SimulatedTemperatureSensor/src/Program.cs) 모듈의 코드가 사용됩니다.

1. [CreateFromEnvironmentAysnc](/dotnet/api/microsoft.azure.devices.client.moduleclient.createfromenvironmentasync) 메서드를 사용하여 [ModuleClient](/dotnet/api/microsoft.azure.devices.client.moduleclient)의 인스턴스를 만듭니다.

1. [GetTwinAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.gettwinasync) 메서드를 사용하여 모듈 쌍의 속성 컬렉션을 가져옵니다.

1. [SetDesiredPropertyUpdateCallbackAsync](/dotnet/api/microsoft.azure.devices.client.deviceclient.setdesiredpropertyupdatecallbackasync) 메서드를 사용하여 원하는 속성에 대해 변경 사항을 catch하기 위해 리스너를 만듭니다(콜백 전달).

1. 콜백 메서드에서 [UpdateReportedPropertiesAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient) 메서드를 사용하여 모듈 쌍에서 보고된 속성을 업데이트하고 설정하려는 속성 값의 [TwinCollection](/dotnet/api/microsoft.azure.devices.shared.twincollection)을 전달합니다.

## <a name="access-the-module-twins"></a>모듈 쌍 액세스

Azure IoT Hub, Visual Studio Code 및 Azure CLI를 사용하여 모듈 쌍에 대해 JSON을 검토할 수 있습니다.

### <a name="monitor-in-azure-iot-hub"></a>Azure IoT Hub에서 모니터

모듈 쌍에 대해 JSON을 보려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.
1. 왼쪽 창 메뉴에서 **IoT Edge** 를 선택합니다.
1. **IoT Edge 디바이스** 탭에서 모니터하려는 모듈이 있는 디바이스의 **디바이스 ID** 를 선택합니다.
1. **모듈** 탭에서 모듈 이름을 선택한 후 위쪽 메뉴 모음에서 **모듈 ID 쌍** 을 선택합니다.

  ![Azure Portal에서 확인할 모듈 쌍 선택](./media/how-to-monitor-module-twins/select-module-twin.png)

“이 모듈에 대한 모듈 ID가 존재하지 않습니다” 메시지가 표시되는 경우, 이 오류는 원래 ID를 만든 백엔드 솔루션을 더 이상 사용할 수 없음을 나타냅니다.

### <a name="monitor-module-twins-in-visual-studio-code"></a>Visual Studio Code에서 모듈 쌍 모니터

모듈 쌍을 검토하고 편집하려면 다음을 수행합니다.

1. 아직 설치되지 않았으면 Visual Studio Code용 [Azure IoT Tools 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)을 설치합니다.
1. **탐색기** 에서 **Azure IoT Hub** 를 확장한 후 모니터하려는 모듈로 디바이스를 확장합니다.
1. 모듈을 마우스 오른쪽 단추로 클릭하고 **모듈 쌍 편집** 을 선택합니다. 모듈 쌍의 임시 파일이 컴퓨터에 다운로드되고 Visual Studio Code에 표시됩니다.

  ![Visual Studio Code에서 편집할 모듈 쌍 가져오기](./media/how-to-monitor-module-twins/edit-module-twin-vscode.png)

항목을 변경한 편집기의 코드 위에서 **모듈 쌍 업데이트** 를 선택하여 변경 사항을 IoT 허브에 저장합니다.

  ![Visual Studio Code에서 모듈 쌍 업데이트](./media/how-to-monitor-module-twins/update-module-twin-vscode.png)

### <a name="monitor-module-twins-in-azure-cli"></a>Azure CLI에서 모듈 쌍 모니터

IoT Edge가 실행 중인지 확인하려면 [az iot hub invoke-module-method](how-to-edgeagent-direct-method.md#ping)를 사용하여 IoT Edge 에이전트에 ping을 수행합니다.

[az iot hub module-twin](/cli/azure/iot/hub/module-twin) 구조는 다음 명령을 제공합니다.

* **az iot hub module-twin show** - 모듈 쌍 정의를 표시합니다.
* **az iot hub module-twin update** - 모듈 쌍 정의를 업데이트합니다.
* **az iot hub module-twin replace** - 모듈 쌍 정의를 대상 JSON으로 바꿉니다.

>[!TIP]
>CLI 명령을 사용하여 런타임 모듈을 대상으로 지정하려면 모듈 ID의 `$` 문자를 이스케이프해야 할 수 있습니다. 예를 들어:
>
>```azurecli
>az iot hub module-twin show -m '$edgeAgent' -n <hub name> -d <device name>
>```
>
>또는
>
>```azurecli
>az iot hub module-twin show -m \$edgeAgent -n <hub name> -d <device name>
>```

## <a name="next-steps"></a>다음 단계

[기본 제공 직접 메서드를 사용하여 EdgeAgent와 통신](how-to-edgeagent-direct-method.md)하는 방법에 대해 알아봅니다.
