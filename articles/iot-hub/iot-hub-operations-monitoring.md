---
title: "Azure IoT Hub 작업 모니터링 | Microsoft 문서"
description: "IoT Hub 작업 모니터링을 사용하여 실시간으로 IoT Hub에 대한 작업의 상태를 모니터링하는 방법입니다."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a299f3a5-b14d-4586-9c3b-44aea14ed013
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2016
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 796bf9b1219b7f0e2c68688c5f5b51163ef4b49b
ms.lasthandoff: 03/07/2017


---
# <a name="iot-hub-operations-monitoring"></a>IoT Hub 작업 모니터링
IoT Hub 작업 모니터링을 사용하면 실시간으로 IoT Hub에 대한 작업의 상태를 모니터링할 수 있습니다. IoT Hub는 몇 가지 작업 범주에 걸쳐 이벤트를 추적합니다. 하나 이상의 범주에서 IoT hub의 끝점으로 처리할 이벤트를 보내도록 선택할 수 있습니다. 데이터에 오류가 있는지 모니터링하거나 데이터 패턴을 기반으로 좀 더 복잡한 처리를 설정할 수 있습니다.

IoT Hub는 다음 여섯 가지 범주의 이벤트를 모니터링합니다.

* 장치 ID 작업
* 장치 원격 분석
* 클라우드-장치 메시지
* 연결
* 파일 업로드
* 메시지 라우팅

## <a name="how-to-enable-operations-monitoring"></a>작업 모니터링을 사용하는 방법
1. IoT Hub를 만듭니다. IoT Hub를 만드는 방법에 관한 지침은 [시작][lnk-get-started] 가이드에서 확인할 수 있습니다.
2. IoT Hub의 블레이드를 엽니다. 여기에서 **작업 모니터링**을 클릭합니다.
   
    ![][1]
3. 모니터링하고자 하는 모니터링 범주를 선택한 다음 **저장**을 클릭합니다. 이벤트는 **모니터링 설정**에 나열된 이벤트 허브 호환 끝점에서 읽어오는 데 사용할 수 있습니다. IoT Hub 끝점은 `messages/operationsmonitoringevents`라고 합니다.
   
    ![][2]

> [!NOTE]
> **연결** 범주에 대한 **자세한 정보 표시** 모니터링을 선택하면 IoT Hub가 추가 진단 메시지를 생성하게 됩니다. 다른 모든 범주의 경우 **자세한 정보 표시** 설정으로 인해 각 오류 메시지에서 IoT Hub가 포함하는 정보량이 달라집니다.

## <a name="event-categories-and-how-to-use-them"></a>이벤트 범주 및 사용 방법
각 작업 모니터링 범주는 IoT Hub와의 여러 상호 작용 유형을 추적하고 각 모니터링 범주에는 해당 범주의 이벤트가 어떻게 구성되는지를 정의하는 스키마가 있습니다.

### <a name="device-identity-operations"></a>장치 ID 작업
장치 ID 작업 범주는 IoT Hub ID 레지스트리의 항목을 만들거나, 업데이트하거나 삭제하려고 할 때 발생하는 오류를 추적합니다. 이 범주를 추적하는 것은 프로비전 시나리오에 유용합니다.

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": "userAgent",
         "sharedAccessPolicy": "accessPolicy"
    }

### <a name="device-telemetry"></a>장치 원격 분석
장치 원격 분석 범주는 IoT Hub에서 발생하고 원격 분석 파이프라인에 관련된 오류를 추적합니다. 이 범주에는 원격 분석 이벤트를 보내고(예: 제한) 원격 분석 이벤트를 수신할 때(예: 무단된 판독기) 발생하는 오류가 포함됩니다. 이 범주는 장치 자체에서 실행되는 코드에 의해 발생한 오류를 포착할 수 없습니다.

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "time": "UTC timestamp",
         "operationName": "ingress",
         "category": "DeviceTelemetry",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": "UTC timestamp"
    }

### <a name="cloud-to-device-commands"></a>클라우드-장치 명령
클라우드-장치 명령 범주는 IoT Hub에서 발생하고 클라우드-장치 메시지 파이프라인에 관련된 오류를 추적합니다. 이 범주에는 클라우드-장치 메시지를 보낼 때(예: 권한이 없는 보낸 사람), 클라우드-장치 메시지를 받을 때(예: 전달 수 초과), 그리고 클라우드-장치 메시지 피드백을 받을 때(예: 피드백 만료) 발생하는 오류가 포함됩니다. 이 범주는 클라우드-장치 메시지가 성공적으로 전달된 경우 클라우드-장치 메시지가 부적절하게 처리하는 장치로부터 오류를 포착하지 않습니다.

    {
         "messageSizeInBytes": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "deliveryAcknowledgement": 0,
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "C2DCommands",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": “UTC timestamp"
    }

### <a name="connections"></a>연결
연결 범주는 장치가 IoT Hub에 연결되거나 연결이 해제될 때 발생하는 오류를 추적합니다. 이 범주를 추적하는 것은 무단 연결 시도를 식별하고 연결 상태가 좋지 않은 영역에서 장치의 연결이 끊어졌을 때 추적하는 데 유용합니다.

    {
         "durationMs": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "deviceConnect",
         "category": "Connections",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID"
    }

### <a name="file-uploads"></a>파일 업로드

파일 업로드 범주는 IoT Hub에서 발생하고 파일 업로드 기능과 관련된 오류를 추적합니다. 이 범주에는 다음이 포함됩니다.

- SAS URI에서 발생하는 오류(예: 장치가 허브에 완료된 업로드를 알리기 전에 만료되는 경우).
- 장치에 의해 보고된 실패한 업로드.
- IoT Hub 알림 메시지 생성 중 저장소에서 파일을 찾을 수 없는 경우 발생하는 오류.

이 범주는 장치가 저장소로 파일을 업로드하는 동안 직접 발생하는 오류를 검색할 수 없습니다.


    {
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "HTTP",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "fileUpload",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "blobUri": "http//bloburi.com",
         "durationMs": 1234
    }

### <a name="message-routing"></a>메시지 라우팅
메시지 라우팅 범주는 IoT Hub에서 감지하는 대로 메시지 경로 평가 및 끝점 상태 중에 발생한 오류를 추적합니다. 이 범주에는 규칙이 "undefined"로 평가되는 경우, IoT Hub가 끝점을 데드로 표시한 경우 및 다른 오류가 끝점에서 수신되는 경우와 같은 이벤트가 포함됩니다. 이 범주는 메시지 자체에 대한 특정 오류(예: 장치 제한 오류)를 포함하지 않습니다. 해당 오류는 "장치 원격 분석" 범주 아래에서 보고됩니다.
        
    {
        "messageSizeInBytes": 1234,
        "time": "UTC timestamp",
        "operationName": "ingress",
        "category": "routes",
        "level": "Error",
        "deviceId": "device-ID",
        "messageId": "ID of message",
        "routeName": "myroute",
        "endpointName": "myendpoint",
        "details": "ExternalEndpointDisabled"
    }

## <a name="view-events"></a>이벤트 보기

*iothub-explorer* 도구를 사용하여 IoT Hub가 모니터링 이벤트를 생성하는지 빠르게 테스트할 수 있습니다. 도구를 설치하려면 [iothub-explorer][lnk-iothub-explorer] GitHub 리포지토리의 지침을 참조하세요.

1. 포털에서 **연결** 모니터링 범주가 **자세한 정보 표시**로 설정되어 있는지 확인합니다.

1. 명령 프롬프트에서 다음 명령을 실행하여 모니터링 끝점에서 읽습니다.

    ```
    iothub-explorer monitor-ops --login {your iothubowner connection string}
    ```

1. 또 다른 명령 프롬프트에서 다음 명령을 실행하여 장치-클라우드 메시지를 보내는 장치를 시뮬레이트합니다.

    ```
    iothub-explorer simulate-device {your device name} --send "My test message" --login {your iothubowner connection string}
    ```

1. 첫 번째 명령 프롬프트에는 시뮬레이트된 장치가 IoT Hub에 연결될 때 모니터링 이벤트가 표시됩니다.

## <a name="next-steps"></a>다음 단계
IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT Hub 개발자 가이드][lnk-devguide]
* [IoT Gateway SDK를 사용하는 장치 시뮬레이션][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer

