---
title: 솔루션에서 IoT 플러그 앤 플레이 모델 사용 | Microsoft Docs
description: 솔루션 빌더는 iot 솔루션에서 IoT 플러그 앤 플레이 모델을 사용 하는 방법에 대해 알아봅니다.
author: arunmannengal
ms.author: arunmann
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4cdd6f63c9e5e717a533b88702b2886387fe3e39
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475246"
---
# <a name="use-iot-plug-and-play-models-in-an-iot-solution"></a>Iot 솔루션에서 IoT 플러그 앤 플레이 모델 사용

이 문서에서는 IoT 솔루션에서 IoT 플러그 앤 플레이 장치의 모델 ID를 식별 한 다음 해당 모델 정의를 검색할 수 있는 방법을 설명 합니다.

IoT 솔루션은 다음과 같은 두 가지 범주로 나뉩니다.

- 목적에 맞게 *작성 된 솔루션* 은 솔루션에 연결 하는 IoT 플러그 앤 플레이 장치에 대해 알려진 모델 집합을 사용 하 여 작동 합니다. 솔루션을 개발할 때 이러한 모델을 사용 합니다.

- *모델 기반* 솔루션은 IoT 플러그 앤 플레이 장치의 모델에서 사용할 수 있습니다. 모델 기반 솔루션을 구축 하는 것은 더 복잡 하지만, 나중에 추가 될 수 있는 모든 장치에서 솔루션이 작동 하는 이점을 누릴 수 있습니다. 모델 기반 IoT 솔루션은 모델을 검색 하 고이를 사용 하 여 장치에서 구현 하는 원격 분석, 속성 및 명령을 결정 합니다.

Iot 플러그 앤 플레이 모델을 사용 하려면 IoT 솔루션:

1. 솔루션에 연결 된 IoT 플러그 앤 플레이 장치에 의해 구현 된 모델의 모델 ID를 식별 합니다.

1. 모델 ID를 사용 하 여 모델 리포지토리 또는 사용자 지정 저장소에서 연결 된 장치의 모델 정의를 검색 합니다.

## <a name="identify-model-id"></a>모델 ID 식별

IoT 플러그 앤 플레이 장치가 IoT Hub에 연결 하는 경우 IoT Hub를 사용 하 여 구현 하는 모델의 모델 ID를 등록 합니다.

IoT Hub 장치 연결 흐름의 일부로 장치 모델 ID를 사용 하 여 솔루션에 알립니다.

솔루션은 다음 세 가지 방법 중 하나를 사용 하 여 IoT 플러그 앤 플레이 장치의 모델 ID를 가져올 수 있습니다.

### <a name="get-device-twin-api"></a>장치 쌍 API 가져오기

솔루션은 [장치 쌍 가져오기](https://docs.microsoft.com/rest/api/iothub/service/twin/getdevicetwin) API를 사용 하 여 IoT 플러그 앤 플레이 장치의 모델 ID를 검색할 수 있습니다.

다음 장치 쌍 응답 코드 조각에는 `modelId` IoT 플러그 앤 플레이 장치의 모델 ID가 포함 되어 있습니다.

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

### <a name="get-digital-twin-api"></a>디지털 쌍 API 가져오기

이 솔루션은 [Get Digital](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin/getdigitaltwin) 쌍 API를 사용 하 여 IoT 플러그 앤 플레이 장치에 의해 구현 된 모델의 모델 ID를 검색할 수 있습니다.

다음 디지털 쌍 응답 코드 조각에는 `$metadata.$model` IoT 플러그 앤 플레이 장치의 모델 ID가 포함 되어 있습니다.

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

### <a name="digital-twin-change-event-notification"></a>디지털 쌍 변경 이벤트 알림

장치에 연결 되 면 [디지털 쌍 변경 이벤트](concepts-digital-twin.md#digital-twin-change-events) 알림이 발생 합니다. 솔루션은이 이벤트 알림을 구독 해야 합니다. 디지털 쌍 이벤트에 대해 라우팅을 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [IoT Hub 메시지 라우팅을 사용 하 여 여러 끝점으로 장치-클라우드 메시지 보내기](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)를 참조 하세요.

솔루션은 다음 코드 조각에 표시 된 이벤트를 사용 하 여 연결 되는 IoT 플러그 앤 플레이 장치에 대해 알아보고 모델 ID를 가져올 수 있습니다.

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

## <a name="retrieve-a-model-definition"></a>모델 정의 검색

솔루션은 위에서 식별 된 모델 ID를 사용 하 여 해당 모델 정의를 검색 합니다.

솔루션은 다음 옵션 중 하나를 사용 하 여 모델 정의를 가져올 수 있습니다.

### <a name="model-repository"></a>모델 리포지토리

솔루션은 [모델 리포지토리](concepts-model-repository.md) 를 사용 하 여 모델을 검색할 수 있습니다. 장치 빌더 또는 솔루션 빌더는 미리 리포지토리에 모델을 업로드 해야 하므로 솔루션에서 해당 모델을 검색할 수 있습니다.

새 장치 연결에 대 한 모델 ID를 확인 한 후에는 다음 단계를 수행 합니다.

1. 모델 리포지토리에서 모델 ID를 사용 하 여 모델 정의를 검색 합니다. 자세한 내용은 [모델 가져오기](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync)를 참조 하세요.

1. 연결 된 장치의 모델 정의를 사용 하 여 장치의 기능을 열거할 수 있습니다.

1. 장치의 열거 된 기능을 사용 하 여 사용자가 [장치와 상호 작용](quickstart-service-node.md)하도록 허용할 수 있습니다.

### <a name="custom-store"></a>사용자 지정 저장소

솔루션은 이러한 모델 정의를 로컬 파일 시스템에 저장 하거나 공용 파일 저장소에 저장 하거나 사용자 지정 구현을 사용할 수 있습니다.

새 장치 연결에 대 한 모델 ID를 확인 한 후에는 다음 단계를 수행 합니다.

1. 사용자 지정 저장소에서 모델 ID를 사용 하 여 모델 정의를 검색 합니다.

1. 연결 된 장치의 모델 정의를 사용 하 여 장치의 기능을 열거할 수 있습니다. 

1. 장치의 열거 된 기능을 사용 하 여 사용자가 [장치와 상호 작용](quickstart-service-node.md)하도록 허용할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

Iot 솔루션에서 IoT 플러그 앤 플레이 모델을 통합 하는 방법을 배웠으므로 이제 몇 가지 제안 된 다음 단계를 수행 합니다.

- [솔루션에서 장치와 상호 작용](quickstart-service-node.md)
- [IoT 디지털 쌍 REST API](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin)
- [Azure IoT 탐색기](howto-use-iot-explorer.md)
