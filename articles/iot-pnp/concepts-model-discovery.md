---
title: IoT 플러그 앤 플레이 미리 보기 모델 검색 구현 | Microsoft Docs
description: 솔루션 빌더는 솔루션에서 IoT 플러그 앤 플레이 모델 검색을 구현 하는 방법에 대해 알아봅니다.
author: prashmo
ms.author: prashmo
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 364b85a8ead09858b97d5d7e6ca8c130b9960b2c
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337384"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>Iot 솔루션에서 IoT 플러그 앤 플레이 미리 보기 모델 검색 구현

이 문서에서는 iot 솔루션에서 IoT 플러그 앤 플레이 Preview 모델 검색을 구현할 수 있는 방법을 설명 합니다. 모델 검색은 다음 방법을 설명 합니다.

- IoT 플러그 앤 플레이 장치는 해당 모델 ID를 등록 합니다.
- IoT 솔루션은 장치에서 구현 하는 인터페이스를 검색 합니다.

IoT 솔루션은 다음과 같은 두 가지 범주로 나뉩니다.

- *용도에 맞는 iot 솔루션* 은 알려진 iot 플러그 앤 플레이 장치 모델 집합에서 작동 합니다.

- *모델 기반 iot 솔루션* 은 모든 IoT 플러그 앤 플레이 장치에서 작동할 수 있습니다. 모델 기반 솔루션을 구축 하는 것은 더 복잡 하지만, 나중에 추가 된 장치를 사용 하 여 솔루션이 작동 한다는 이점도 있습니다.

    모델 기반 IoT 솔루션을 빌드하려면 IoT 플러그 앤 플레이 인터페이스 기본 형식: 원격 분석, 속성 및 명령에 대 한 논리를 구축 해야 합니다. 솔루션의 논리는 여러 원격 분석, 속성 및 명령 기능을 결합 하 여 장치를 나타냅니다.

이 문서에서는 두 가지 유형의 솔루션 모두에서 모델 검색을 구현 하는 방법을 설명 합니다.

## <a name="model-discovery"></a>모델 검색

장치가 구현 하는 모델을 검색 하기 위해 솔루션은 이벤트 기반 검색 또는 쌍 기반 검색을 사용 하 여 모델 ID를 가져올 수 있습니다.

### <a name="event-based-discovery"></a>이벤트 기반 검색

IoT 플러그 앤 플레이 장치가 IoT Hub에 연결 하는 경우 구현 하는 모델을 등록 합니다. 이 등록으로 인해 [디지털 쌍 변경 이벤트](concepts-digital-twin.md#digital-twin-change-events) 알림이 발생 합니다. 디지털 쌍 이벤트에 대해 라우팅을 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [IoT Hub 메시지 라우팅을 사용 하 여 여러 끝점으로 장치-클라우드 메시지 보내기](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)를 참조 하세요.

솔루션은 다음 코드 조각에 표시 된 이벤트를 사용 하 여 연결 된 IoT 플러그 앤 플레이 장치에 대해 알아보고 모델 ID를 가져올 수 있습니다.

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/22/2020 8:02:27 PM
iothub-message-source:digitalTwinChangeEvents
correlation-id:100f322dc2c5
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:example:TemperatureController;1"
  }
]
```

이 이벤트는 장치 모델 ID가 추가 되거나 업데이트 될 때 트리거됩니다.

### <a name="twin-based-discovery"></a>쌍 기반 검색

솔루션이 지정 된 장치의 기능을 알고 싶을 경우 [디지털 쌍 가져오기](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin/getdigitaltwin) API를 사용 하 여 정보를 검색할 수 있습니다.

다음 디지털 쌍 조각에는 `$metadata.$model` IoT 플러그 앤 플레이 장치의 모델 ID가 포함 되어 있습니다.

```json
{
    "$dtId": "sample-device",
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

솔루션은 다음 코드 조각과 같이 **Get** 쌍을 사용 하 여 장치 쌍에서 모델 ID를 검색할 수도 있습니다.

```json
{
    "deviceId": "sample-device",
    "etag": "AAAAAAAAAAc=",
    "deviceEtag": "NTk0ODUyODgx",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "2020-07-17T06:12:26.8402249Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {...}
    }
}
```

## <a name="model-resolution"></a>모델 확인

솔루션은 모델 확인을 사용 하 여 모델 ID에서 모델을 구성 하는 인터페이스에 대 한 액세스를 가져옵니다. 

- 솔루션은 이러한 인터페이스를 로컬 폴더의 파일로 저장 하도록 선택할 수 있습니다. 
- 솔루션은 [모델 리포지토리](concepts-model-repository.md)를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 모델 검색에 대해 알아보았습니다. IoT 솔루션에 대 한 자세한 내용은 솔루션에 다른 기능을 사용 하는 [Azure iot 플랫폼](overview-iot-plug-and-play.md) 에 대해 알아보세요.

- [솔루션에서 장치와 상호 작용](quickstart-service-node.md)
- [IoT 디지털 쌍 REST API](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin)
- [Azure IoT 탐색기](howto-use-iot-explorer.md)
