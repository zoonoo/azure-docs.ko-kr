---
title: 기본 제공 edgeAgent 직접 메서드 - Azure IoT Edge
description: IoT Edge 에이전트 런타임 모듈의 기본 제공 직접 메서드를 사용하여 IoT Edge 배포 모니터링 및 관리
author: kgremban
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 35f226962e1c293a3d39e5e4667394a157d3e8a0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528094"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>기본 제공 직접 메서드를 사용하여 여 edgeAgent와 통신

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge 에이전트 모듈에 포함된 직접 메서드를 사용하여 IoT Edge 배포를 모니터링하고 관리합니다. 직접 메서드는 디바이스에서 구현된 다음 클라우드에서 호출할 수 있습니다. IoT Edge 에이전트에는 IoT Edge 디바이스를 원격으로 모니터링하고 관리하는 데 도움이 되는 직접 메서드가 포함되어 있습니다.

직접 메서드, 그 사용 방법 및 사용자 고유의 모듈에서 이를 구현하는 방법에 대한 자세한 내용은 [IoT Hub에서의 직접 메서드 인식 및 호출](../iot-hub/iot-hub-devguide-direct-methods.md)을 참조하세요.

직접 메서드의 이름은 대/소문자를 구분하지 않고 처리됩니다.

## <a name="ping"></a>Ping

**Ping** 메서드는 IoT Edge가 디바이스에서 실행되고 있는지 여부 또는 디바이스에 IoT Hub에 대한 열린 연결이 있는지 여부를 확인하는 데 유용합니다. 이 직접 메서드를 사용하여 IoT Edge 에이전트를 ping하고 해당 상태를 가져옵니다. 성공적인 ping은 빈 페이로드 및 **“status”: 200** 을 반환합니다.

예를 들면 다음과 같습니다.

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

Azure Portal에서 메서드 이름이 `ping`이며 빈 JSON 페이로드(`{}`)가 있는 메서드를 호출합니다.

![Azure Portal에서 ‘ping’ 직접 메서드 호출](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>모듈 다시 시작

**RestartModule** 메서드는 IoT Edge 디바이스에서 실행되는 모듈의 원격 관리를 허용합니다. 모듈이 실패 상태 또는 기타 비정상 동작을 보고하는 경우 IoT 모듈을 다시 시작하도록 IoT Edge 에이전트를 트리거할 수 있습니다. 성공적으로 다시 시작하는 명령은 빈 페이로드 및 **“status”: 200** 을 반환합니다.

RestartModule 메서드는 IoT Edge 버전 1.0.9 이상에서 사용할 수 있습니다.

>[!TIP]
>Azure Portal의 IoT Edge 문제 해결 페이지에서는 모듈을 다시 시작하기 위한 간소화된 환경을 제공합니다. 자세한 내용은 [Azure Portal에서 IoT Edge 디바이스 모니터링 및 문제 해결](troubleshoot-in-portal.md)을 참조하세요.

edgeAgent 모듈 자체를 포함하여 IoT Edge 디바이스에서 실행되는 모든 모듈에서 RestartModule 직접 메서드를 사용할 수 있습니다. 단, 이 직접 메서드를 사용하여 edgeAgent를 종료하는 경우 모듈을 다시 시작하는 동안 연결이 중단되므로 성공 결과를 수신할 수 없습니다.

예를 들면 다음과 같습니다.

```azurecli
az iot hub invoke-module-method --method-name 'RestartModule' -n <hub name> -d <device name> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "id": "<module name>"
    }
'
```

Azure Portal에서 메서드 이름이 `RestartModule`이며 다음과 같은 JSON 페이로드가 있는 메서드를 호출합니다.

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Azure Portal에서 ‘RestartModule’ 직접 메서드 호출](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="diagnostic-direct-methods"></a>진단 직접 메서드

* [GetModuleLogs](how-to-retrieve-iot-edge-logs.md#retrieve-module-logs): 직접 메서드의 응답에서 모듈 로그를 인라인으로 검색합니다.
* [UploadModuleLogs](how-to-retrieve-iot-edge-logs.md#upload-module-logs): 모듈 로그를 검색하고 Azure Blob Storage에 업로드합니다.
* [UploadSupportBundle](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics): 지원 번들을 사용하여 모듈 로그를 검색하고 Azure Blob Storage에 zip 파일을 업로드합니다.
* [GetTaskStatus](how-to-retrieve-iot-edge-logs.md#get-upload-request-status): 업로드 로그 또는 지원 번들 요청의 상태를 확인합니다.

1\.0.10 릴리스부터 진단 직접 메서드를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[IoT Edge 에이전트 및 IoT Edge 허브 모듈 쌍의 속성](module-edgeagent-edgehub.md)
