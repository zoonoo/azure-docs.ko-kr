<properties
 pageTitle="IoT Hub 작업 모니터링"
 description="Azure IoT Hub 작업 모니터링의 개요를 통해 실시간으로 IoT Hub에 대한 작업의 상태를 모니터링할 수 있습니다."
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/11/2016"
 ms.author="nberdy"/>

# 작업 모니터링 소개

IoT Hub 작업 모니터링을 사용하면 실시간으로 IoT Hub에 대한 작업의 상태를 모니터링할 수 있습니다. IoT Hub는 작업의 여러 범주에 걸쳐 이벤트를 추적하고 사용자는 하나 이상의 범주에서 IoT Hub 끝점까지 처리할 이벤트를 전송할 수 있습니다. 데이터에 오류가 있는지 모니터링하거나 데이터 패턴을 기반으로 좀 더 복잡한 처리를 설정할 수 있습니다.

IoT Hub는 다음 다섯 가지 범주의 이벤트를 모니터링합니다.

- 장치 ID 작업
- 장치 원격 분석
- 클라우드-장치 명령
- 연결
- 파일 업로드

## 작업 모니터링을 사용하는 방법

1. IoT Hub를 만듭니다. IoT Hub를 만드는 방법에 관한 지침은 [시작][lnk-get-started] 가이드에서 확인할 수 있습니다.

2. IoT Hub의 블레이드를 엽니다. 여기에서 **작업 모니터링**을 클릭합니다.

    ![][1]

3. 모니터링하고자 하는 모니터링 범주를 선택한 다음 **저장**을 클릭합니다. 이벤트는 **모니터링 설정**에 나열된 이벤트 허브 호환 끝점에서 읽어오는 데 사용할 수 있습니다. IoT Hub 끝점은 `messages/operationsmonitoringevents`라고 합니다.

    ![][2]

## 이벤트 범주 및 사용 방법

각 작업 모니터링 범주는 IoT Hub와의 여러 상호 작용 유형을 추적하고 각 모니터링 범주에는 해당 범주의 이벤트가 어떻게 구성되는지를 정의하는 스키마가 있습니다.

### 장치 ID 작업

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

### 장치 원격 분석

장치 원격 분석 범주는 IoT Hub에서 발생하고 원격 분석 파이프라인에 관련된 오류를 추적합니다. 이 범주에는 원격 분석 이벤트를 보내고(예: 제한) 원격 분석 이벤트를 수신할 때(예: 무단된 판독기) 발생하는 오류가 포함됩니다. 이 범주는 장치 자체에서 실행되는 코드에 의해 발생한 오류를 포착할 수 없습니다.

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{"scope":"device","type":"sas","issuer":"iothub"}",
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

### 클라우드-장치 명령

클라우드-장치 명령 범주는 IoT Hub에서 발생하고 장치 명령 파이프라인에 관련된 오류를 추적합니다. 이 범주에는 명령을 보낼 때(예: 권한이 없는 보낸 사람), 명령을 받을 때(예: 전달 수 초과), 그리고 명령 피드백을 받을 때(예: 피드백 만료) 발생하는 오류가 포함됩니다. 이 범주는 명령이 성공적으로 전달된 경우 명령이 부적절하게 처리하는 장치로부터 오류를 포착하지 않습니다.

    {
         "messageSizeInBytes": 1234,
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
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

### 연결

연결 범주는 장치가 IoT Hub에 연결되거나 연결이 해제될 때 발생하는 오류를 추적합니다. 이 범주를 추적하는 것은 무단 연결 시도를 식별하고 연결 상태가 좋지 않은 영역에서 장치의 연결이 끊어졌을 때 추적하는 데 유용합니다.

    {
         "durationMs": 1234,
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
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

### 파일 업로드

파일 업로드 범주는 IoT Hub에서 발생하고 파일 업로드 기능과 관련된 오류를 추적합니다. 이 범주에는 SAS URI에서 발생하는 오류(예: 장치가 허브에 업로드 완료 사실을 알리기 전에 만료되는 경우), 장치에서 보고한 실패한 업로드, IoT Hub 알림 메시지를 만드는 동안 저장소에서 파일을 찾을 수 없는 경우 등이 포함됩니다. 이 범주는 장치가 저장소로 파일을 업로드하는 동안 직접 발생하는 오류를 검색할 수 없습니다.

    {
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
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

## 다음 단계

이제 작업 모니터링의 개요를 살펴보았으므로 IoT Hub를 관리하는 방법에 대한 자세한 내용은 [IoT Hub에 대한 액세스 관리][lnk-itpro]를 참조하세요.

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

- [솔루션 디자인][lnk-design]
- [개발자 가이드][lnk-devguide]
- [샘플 UI를 사용하여 장치 관리 탐색][lnk-dmui]
- [Gateway SDK를 사용하는 장치 시뮬레이션][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-itpro]: iot-hub-itpro-info.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

<!---HONumber=AcomDC_0817_2016-->