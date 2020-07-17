---
title: 모듈 쌍 모니터링-Azure IoT Edge
description: 장치 쌍 및 모듈 쌍을 해석 하 여 연결 및 상태를 확인 하는 방법입니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/29/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c24cef2cf9e4c54d16ebc75eb1a56273d8826355
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84221134"
---
# <a name="monitor-module-twins"></a>모듈 쌍 모니터링

Azure IoT Hub의 모듈 쌍은 IoT Edge 배포의 연결 및 상태를 모니터링할 수 있도록 합니다. 모듈 쌍은 IoT hub에 실행 중인 모듈의 성능에 대 한 유용한 정보를 저장 합니다. [IoT Edge 에이전트](iot-edge-runtime.md#iot-edge-agent) 및 [IoT Edge 허브](iot-edge-runtime.md#iot-edge-hub) 런타임 모듈은 각각 해당 모듈 쌍을 유지 관리 합니다 `$edgeAgent` `$edgeHub` .

* `$edgeAgent`IoT Edge 에이전트 및 IoT Edge 허브 런타임 모듈과 사용자 지정 모듈에 대 한 상태 및 연결 데이터를 포함 합니다. IoT Edge 에이전트는 모듈을 배포 하 고, 모니터링 하 고, Azure IoT hub에 연결 상태를 보고 하는 일을 담당 합니다.
* `$edgeHub`장치에서 실행 되는 IoT Edge 허브와 Azure IoT hub 간의 통신에 대 한 데이터를 포함 합니다. 여기에는 다운스트림 장치에서 들어오는 메시지를 처리 하는 작업이 포함 됩니다. IoT Edge 허브는 Azure IoT Hub와 IoT Edge 장치 및 모듈 간의 통신 처리를 담당 합니다.

데이터는 모듈 쌍의 JSON 구조에서 desired 및 보고 된 속성 집합과 함께 메타 데이터 및 태그로 구성 됩니다. deployment.js파일에 지정 된 원하는 속성이 모듈 쌍에 복사 됩니다. IoT Edge 에이전트 및 IoT Edge 허브는 각 모듈에 대해 보고 된 속성을 업데이트 합니다.

마찬가지로 deployment.js파일의 사용자 지정 모듈에 대해 지정 된 desired 속성은 해당 모듈 쌍으로 복사 되지만 솔루션은 보고 된 속성 값을 제공 해야 합니다.

이 문서에서는 Azure Portal, Azure CLI 및 Visual Studio Code에서 모듈 쌍을 검토 하는 방법을 설명 합니다. 장치에서 배포를 수신 하는 방법을 모니터링 하는 방법에 대 한 자세한 내용은 [IoT Edge 배포 모니터링](how-to-monitor-iot-edge-deployments.md)을 참조 하세요. 모듈 쌍의 개념에 대 한 개요는 [IoT Hub에서 모듈 쌍 이해 및 사용](../iot-hub/iot-hub-devguide-module-twins.md)을 참조 하세요.

> [!TIP]
> IoT Edge 장치를 IoT hub에서 분리 한 경우 런타임 모듈의 보고 된 속성이 부실 할 수 있습니다. 모듈을 [ping](how-to-edgeagent-direct-method.md#ping) `$edgeAgent` 하 여 연결이 끊어진 경우를 확인할 수 있습니다.

## <a name="monitor-runtime-module-twins"></a>런타임 모듈 쌍 모니터링

배포 연결 문제를 해결 하려면 IoT Edge 에이전트 및 IoT Edge 허브 런타임 모듈 쌍을 검토 한 후 다른 모듈로 드릴 다운 합니다.

### <a name="monitor-iot-edge-agent-module-twin"></a>IoT Edge 에이전트 모듈 쌍 모니터링

다음 JSON에서는 대부분의 `$edgeAgent` json 섹션이 축소 된 Visual Studio Code의 모듈 쌍을 보여 줍니다.

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

JSON은 위쪽에서 시작 하 여 다음 섹션에서 설명할 수 있습니다.

* 메타 데이터-연결 데이터를 포함 합니다. 흥미롭게도 IoT Edge 에이전트의 연결 상태는 항상 연결이 끊어진 상태 `"connectionState": "Disconnected"` 입니다. 연결 상태가 D2C (장치-클라우드) 메시지와 관련 된 이유 이며 IoT Edge 에이전트는 D2C 메시지를 보내지 않습니다.
* 속성-및 하위 `desired` 섹션을 포함 `reported` 합니다.
* 속성 (축소 표시 됨)은 deployment.js파일의 연산자에 의해 설정 된 속성 값이 필요 합니다.
* 속성. 보고 됨-IoT Edge 에이전트에서 보고 한 최신 속성 값입니다.

`properties.desired`및 섹션은 모두 `properties.reported` 비슷한 구조를 가지 며 스키마, 버전 및 런타임 정보에 대 한 추가 메타 데이터를 포함 합니다. 또한 `modules` 사용자 지정 모듈 (예:)에 대 한 섹션과 `SimulatedTemperatureSensor` `systemModules` 및 런타임 모듈에 대 한 섹션이 포함 되어 `$edgeAgent` `$edgeHub` 있습니다.

보고 된 속성 값을 원하는 값과 비교 하 여 불일치를 확인 하 고 문제 해결에 도움이 될 수 있는 연결이 있는지 확인할 수 있습니다. 이러한 비교를 수행 하는 `$lastUpdated` `metadata` 동안 조사 중인 속성에 대 한 섹션에서 보고 된 값을 확인 합니다.

문제 해결을 위해 다음 속성을 확인 해야 합니다.

* **exitcode** -0이 아닌 값은 모듈이 오류로 인해 중지 되었음을 나타냅니다. 그러나 모듈이 의도적으로 중지 됨 상태로 설정 된 경우에는 오류 코드 137 또는 143이 사용 됩니다.

* **Laststarttimeutc** -컨테이너가 마지막으로 시작 된 **날짜/시간** 을 표시 합니다. 컨테이너를 시작 하지 않은 경우이 값은 0001-01-01T00:00:00Z입니다.

* **Lastexittimeutc** -컨테이너가 마지막으로 완료 된 **날짜/시간** 을 표시 합니다. 컨테이너가 실행 중이 고 중지 되지 않은 경우이 값은 0001-01-01T00:00:00Z입니다.

* **Runtimestatus** -다음 값 중 하나일 수 있습니다.

    | 값 | 설명 |
    | --- | --- |
    | 알 수 없음 | 배포를 만들 때까지 기본 상태입니다. |
    | 백오프 | 모듈이 시작 되도록 예약 되었지만 현재 실행 되 고 있지 않습니다. 이 값은 다시 시작 시 상태를 변경 하는 모듈에 유용 합니다. 실패 모듈이 휴지 기간 동안 다시 시작을 대기 하는 경우 모듈은 백오프 상태가 됩니다. |
    | 실행 중 | 모듈이 현재 실행 중임을 나타냅니다. |
    | 상태가 | 상태 프로브 검사에 실패 했거나 시간이 초과 되었음을 나타냅니다. |
    | 중지됨 | 모듈이 성공적으로 종료 되었음을 나타냅니다. 종료 코드는 0입니다. |
    | 실패 | 모듈이 실패 종료 코드 (0이 아닌)로 종료 되었음을 나타냅니다. 이 모듈은 적용 되는 다시 시작 정책에 따라이 상태에서 백오프로 다시 전환할 수 있습니다. 이 상태는 모듈에서 복구할 수 없는 오류가 발생 했음을 나타낼 수 있습니다. Microsoft Monitoring Agent (MMA)가 더 이상 모듈을 resuscitate 수 없어 새 배포가 필요 하면 오류가 발생 합니다. |

자세한 내용은 [EdgeAgent 보고 된 속성](module-edgeagent-edgehub.md#edgeagent-reported-properties) 을 참조 하세요.

### <a name="monitor-iot-edge-hub-module-twin"></a>IoT Edge 허브 모듈 쌍 모니터링

다음 JSON에서는 대부분의 `$edgeHub` json 섹션이 축소 된 Visual Studio Code의 모듈 쌍을 보여 줍니다.

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

JSON은 위쪽에서 시작 하 여 다음 섹션에서 설명할 수 있습니다.

* 메타 데이터-연결 데이터를 포함 합니다.

* 속성-및 하위 `desired` 섹션을 포함 `reported` 합니다.
* 속성 (축소 표시 됨)은 deployment.js파일의 연산자에 의해 설정 된 속성 값이 필요 합니다.
* 속성. 보고 됨-IoT Edge 허브가 보고 한 최신 속성 값입니다.

다운스트림 장치에 문제가 발생 하는 경우이 데이터를 검토 하는 것이 좋습니다.

## <a name="monitor-custom-module-twins"></a>사용자 지정 모듈 쌍 모니터링

사용자 지정 모듈의 연결에 대 한 정보는 IoT Edge 에이전트 모듈 쌍에서 유지 관리 됩니다. 사용자 지정 모듈에 대 한 모듈 쌍은 주로 솔루션에 대 한 데이터를 유지 관리 하는 데 사용 됩니다. 파일의 deployment.js에서 정의한 desired 속성은 모듈 쌍에 반영 되 고 모듈은 필요한 경우 보고 된 속성 값을 업데이트할 수 있습니다.

[Azure IoT Hub 장치 sdk](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-hub-device-sdks) 에서 선호 하는 프로그래밍 언어를 사용 하 여 모듈의 응용 프로그램 코드에 따라 모듈 쌍의 보고 된 속성 값을 업데이트할 수 있습니다. 다음 절차에서는 [SimulatedTemperatureSensor](https://github.com/Azure/iotedge/blob/dd5be125df165783e4e1800f393be18e6a8275a3/edge-modules/SimulatedTemperatureSensor/src/Program.cs) 모듈의 코드를 사용 하 여 .Net 용 Azure SDK를 사용 합니다.

1. [CreateFromEnvironmentAysnc](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.createfromenvironmentasync) 메서드를 사용 하 여 [ModuleClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient) 의 인스턴스를 만듭니다.

1. [GetTwinAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.gettwinasync?view=azure-dotnet) 메서드를 사용 하 여 모듈 쌍의 속성 컬렉션을 가져옵니다.

1. [SetDesiredPropertyUpdateCallbackAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.setdesiredpropertyupdatecallbackasync?view=azure-dotnet) 메서드를 사용 하 여 desired 속성의 변경 내용을 catch 하기 위해 수신기 (콜백 전달)를 만듭니다.

1. 콜백 메서드에서 [UpdateReportedPropertiesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient) 메서드를 사용 하 여 모듈 쌍의 보고 된 속성을 업데이트 하 고 설정 하려는 속성 값의 [TwinCollection](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.shared.twincollection) 을 전달 합니다.

## <a name="access-the-module-twins"></a>모듈 쌍 액세스

Azure IoT Hub, Visual Studio Code 및 Azure CLI에서 모듈 쌍에 대 한 JSON을 검토할 수 있습니다.

### <a name="monitor-in-azure-iot-hub"></a>Azure IoT Hub 모니터

모듈 쌍에 대 한 JSON을 보려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.
1. 왼쪽 창 메뉴에서 **IoT Edge**를 선택합니다.
1. **IoT Edge 장치** 탭에서 모니터링 하려는 모듈이 있는 장치의 **장치 ID** 를 선택 합니다.
1. **모듈 탭에서** 모듈 이름을 선택한 다음 위쪽 메뉴 모음에서 **모듈 id** 쌍을 선택 합니다.

  ![Azure Portal에서 볼 모듈 쌍을 선택 합니다.](./media/how-to-monitor-module-twins/select-module-twin.png)

"이 모듈에 대 한 모듈 id가 없습니다." 라는 메시지가 표시 되는 경우이 오류는 원래 id를 만든 백 엔드 솔루션을 더 이상 사용할 수 없음을 나타냅니다.

### <a name="monitor-module-twins-in-visual-studio-code"></a>Visual Studio Code에서 모듈 쌍 모니터링

모듈 쌍을 검토 하 고 편집 하려면 다음을 수행 합니다.

1. 아직 설치 하지 않은 경우 Visual Studio Code 용 [Azure IoT 도구 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 을 설치 합니다.
1. **탐색기**에서 **Azure IoT Hub**을 확장 한 다음 모니터링 하려는 모듈이 포함 된 장치를 확장 합니다.
1. 모듈을 마우스 오른쪽 단추로 클릭 하 고 **모듈 쌍 편집**을 선택 합니다. 모듈 쌍의 임시 파일은 컴퓨터에 다운로드 되 고 Visual Studio Code 표시 됩니다.

  ![Visual Studio Code에서 편집할 모듈 쌍 가져오기](./media/how-to-monitor-module-twins/edit-module-twin-vscode.png)

변경을 수행 하는 경우 편집기에서 코드 위의 **모듈 쌍 업데이트** 를 선택 하 여 IoT hub에 대 한 변경 내용을 저장 합니다.

  ![Visual Studio Code에서 모듈 쌍 업데이트](./media/how-to-monitor-module-twins/update-module-twin-vscode.png)

### <a name="monitor-module-twins-in-azure-cli"></a>Azure CLI에서 모듈 쌍 모니터링

IoT Edge 실행 중인지 확인 하려면 [az IoT hub invoke-module 메서드](how-to-edgeagent-direct-method.md#ping) 를 사용 하 여 IoT Edge 에이전트를 ping 합니다.

[Az iot hub 모듈](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/module-twin) 쌍 구조는 다음 명령을 제공 합니다.

* **az iot hub module-쌍 표시** -모듈 쌍 정의를 표시 합니다.
* **az iot hub module-쌍 업데이트** -모듈 쌍 정의를 업데이트 합니다.
* **az iot hub module-쌍 replace** -모듈 쌍 정의를 대상 JSON으로 바꿉니다.

## <a name="next-steps"></a>다음 단계

[기본 제공 직접 메서드를 사용하여 EdgeAgent와 통신](how-to-edgeagent-direct-method.md)하는 방법에 대해 알아봅니다.
