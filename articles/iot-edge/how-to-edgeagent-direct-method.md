---
title: 기본 제공 에지에이전트 직접 메서드 - Azure IoT Edge
description: IoT Edge 에이전트 런타임 모듈에 내장된 직접 방법을 사용하여 IoT Edge 배포 모니터링 및 관리
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57b9d46918414cef9e8cbcffb941b98c98f985ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240356"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>기본 제공 직접 메서드를 사용하여 edgeAgent와 통신

IoT Edge 에이전트 모듈에 포함된 직접 적인 방법을 사용하여 IoT Edge 배포를 모니터링하고 관리합니다. 직접 메서드는 장치에서 구현된 다음 클라우드에서 호출할 수 있습니다. IoT Edge 에이전트에는 IoT Edge 장치를 원격으로 모니터링하고 관리하는 데 도움이 되는 직접 적인 방법이 포함되어 있습니다.

직접 메서드, 사용 방법 및 이를 자체 모듈에서 구현하는 방법에 대한 자세한 내용은 [IoT Hub에서 직접 메서드 이해 및 호출을](../iot-hub/iot-hub-devguide-direct-methods.md)참조하십시오.

이러한 직접 메서드의 이름은 대/소문자를 구분하지 않는 처리됩니다.

## <a name="ping"></a>Ping

**ping** 메서드는 IoT Edge가 장치에서 실행되고 있는지 또는 장치가 IoT Hub에 열려 있는지 여부를 확인하는 데 유용합니다. 이 직접 메서드를 사용하여 IoT Edge 에이전트를 ping하고 상태를 가져옵니다. ping이 성공적일 수록 빈 페이로드와 **"상태": 200을**반환합니다.

예를 들어:

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

Azure 포털에서 메서드 이름과 `ping` 빈 JSON 페이로드를 `{}`사용하여 메서드를 호출합니다.

![Azure 포털에서 직접 메서드 'ping' 호출](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>모듈 다시 시작

**RestartModule** 방법을 사용하면 IoT Edge 장치에서 실행되는 모듈을 원격으로 관리 할 수 있습니다. 모듈이 실패한 상태 또는 기타 비정상 동작을 보고하는 경우 IoT Edge 에이전트를 트리거하여 다시 시작할 수 있습니다. 성공적인 다시 시작 명령은 빈 페이로드 및 **"상태"를 반환합니다: 200**.

다시 시작Module 방법은 IoT Edge 버전 1.0.9 이상에서 사용할 수 있습니다. 

에지에이전트 모듈 자체를 포함하여 IoT Edge 장치에서 실행되는 모든 모듈에서 RestartModule 직접 메서드를 사용할 수 있습니다. 그러나 이 직접 메서드를 사용하여 edgeAgent를 종료하는 경우 모듈이 다시 시작되는 동안 연결이 중단되므로 성공 결과가 발생하지 않습니다.

예를 들어:

```azurecli
az iot hub invoke-module-method --method-name 'RestartModule' -n <hub name> -d <device name> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "id": "<module name>"
    }
'
```

Azure 포털에서 메서드 이름 `RestartModule` 및 다음 JSON 페이로드를 사용하여 메서드를 호출합니다.

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Azure 포털에서 직접 메서드 '다시 시작 모듈' 호출](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="experimental-methods"></a>실험 방법

새로운 직접 메서드 옵션은 다음을 포함하여 테스트할 실험 기능으로 사용할 수 있습니다.

* [UploadLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md): 모듈 로그를 검색하여 Azure Blob 저장소에 업로드합니다.
* [GetTaskStatus](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#gettaskstatus): 업로드 로그 요청의 상태를 확인합니다.
* [GetLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#getlogs): 직접 메서드의 응답에서 모듈 로그를 인라인으로 검색합니다.

## <a name="next-steps"></a>다음 단계

[IoT Edge 에이전트 및 IoT Edge 허브 모듈 쌍의 속성](module-edgeagent-edgehub.md)
