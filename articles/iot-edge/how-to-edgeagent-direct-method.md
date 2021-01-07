---
title: 기본 제공 edgeAgent 직접 메서드-Azure IoT Edge
description: IoT Edge agent runtime 모듈의 기본 제공 직접 메서드를 사용 하 여 IoT Edge 배포 모니터링 및 관리
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 42c79526288fb7e05959ac60cddc6f468656ffd4
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972546"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>기본 제공 직접 메서드를 사용 하 여 edgeAgent와 통신

IoT Edge agent 모듈에 포함 된 직접 메서드를 사용 하 여 IoT Edge 배포를 모니터링 하 고 관리 합니다. 직접 메서드는 장치에서 구현 된 다음 클라우드에서 호출할 수 있습니다. IoT Edge 에이전트에는 IoT Edge 장치를 원격으로 모니터링 하 고 관리 하는 데 도움이 되는 직접 방법이 포함 되어 있습니다.

직접 메서드에 대 한 자세한 내용, 사용 방법 및 사용자 고유의 모듈에서 구현 하는 방법에 대 한 자세한 내용은 [IoT Hub에서 직접 메서드 이해 및 호출](../iot-hub/iot-hub-devguide-direct-methods.md)을 참조 하세요.

이러한 직접 메서드의 이름은 대/소문자를 구분 하지 않고 처리 됩니다.

## <a name="ping"></a>Ping

**Ping** 방법은 IoT Edge 장치에서 실행 되 고 있는지 여부 또는 장치에 IoT Hub에 대 한 열린 연결이 있는지 여부를 확인 하는 데 유용 합니다. 이 직접 메서드를 사용 하 여 IoT Edge 에이전트를 ping 하 고 해당 상태를 가져옵니다. 성공적인 ping은 빈 페이로드 및 **"status": 200**을 반환 합니다.

예를 들면 다음과 같습니다.

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

Azure Portal에서 메서드 이름과 빈 JSON 페이로드를 사용 하 여 메서드를 호출 합니다 `ping` `{}` .

![Azure Portal에서 직접 메서드 ' ping ' 호출](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>모듈 다시 시작

**RestartModule** 메서드는 IoT Edge 장치에서 실행 되는 모듈의 원격 관리를 허용 합니다. 모듈이 실패 상태 또는 기타 비정상 동작을 보고 하는 경우 IoT Edge 에이전트를 트리거하여 다시 시작할 수 있습니다. 성공적으로 다시 시작 하는 명령은 빈 페이로드 및 **"status": 200**을 반환 합니다.

RestartModule 메서드는 IoT Edge 버전 1.0.9 이상에서 사용할 수 있습니다. 

EdgeAgent 모듈 자체를 포함 하 여 IoT Edge 장치에서 실행 되는 모든 모듈에서 RestartModule direct 메서드를 사용할 수 있습니다. 그러나이 직접 메서드를 사용 하 여 edgeAgent를 종료 하는 경우 모듈을 다시 시작 하는 동안 연결이 중단 되므로 성공 결과가 수신 되지 않습니다.

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

Azure Portal에서 메서드 이름 `RestartModule` 및 다음 JSON 페이로드를 사용 하 여 메서드를 호출 합니다.

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Azure Portal에서 직접 메서드 ' RestartModule '를 호출 합니다.](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="diagnostic-direct-methods"></a>진단 직접 메서드

* [GetModuleLogs](how-to-retrieve-iot-edge-logs.md#retrieve-module-logs): 직접 메서드의 응답에서 모듈 로그를 인라인으로 검색 합니다.
* [UploadModuleLogs](how-to-retrieve-iot-edge-logs.md#upload-module-logs): 모듈 로그를 검색 하 고 Azure Blob Storage에 업로드 합니다.
* [UploadSupportBundle](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics): 지원 번들을 사용 하 여 모듈 로그를 검색 하 고 Azure Blob Storage에 zip 파일을 업로드 합니다.
* [Gettaskstatus](how-to-retrieve-iot-edge-logs.md#get-upload-request-status): 업로드 로그 또는 지원 번들 요청의 상태를 확인 합니다.

1.0.10 릴리스에서는 이러한 진단 직접 메서드를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[IoT Edge 에이전트 및 IoT Edge 허브 모듈 쌍의 속성](module-edgeagent-edgehub.md)
