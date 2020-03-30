---
title: Azure IoT Hub 기본 제공 엔드포인트 이해 | Microsoft Docs
description: 개발자 가이드에서는 기본 제공 Event Hub 호환 엔드포인트를 사용하여 디바이스-클라우드 메시지를 읽는 방법을 설명합니다.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: e7b8f8a33b741a8dcf2d1a68ae3cf86d6e3687eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284604"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>기본 제공 엔드포인트에서 디바이스-클라우드 메시지 읽기

기본적으로 메시지는 [Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/)와 호환되는 기본 제공 서비스 연결 엔드포인트(**messages/events**)로 라우팅됩니다. 이 엔드포인트는 현재 [AMQP](https://www.amqp.org/) 프로토콜을 통해서만 포트 5671에 노출됩니다. IoT Hub는 다음 속성을 노출하여 기본 제공 Event Hub와 호환되는 메시징 엔드포인트 **messages/events**를 제어할 수 있게 합니다.

| 속성            | 설명 |
| ------------------- | ----------- |
| **파티션 수** | 이 속성을 만들 때 설정하여 장치 간 이벤트 생성을 위한 [파티션](../event-hubs/event-hubs-features.md#partitions) 수를 정의합니다. |
| **보존 시간**  | 이 속성은 IoT Hub에서 메시지를 보존할 일 수를 지정합니다. 기본값은 1일이지만 7일로 늘릴 수 있습니다. |

IoT Hub를 사용하면 기본 제공 이벤트 허브에서 최대 7일 동안 데이터를 보존할 수 있습니다. IoT Hub를 만드는 동안 보존 시간을 설정할 수 있습니다. IoT Hub의 데이터 보존 시간은 IoT 허브 계층 및 단위 유형에 따라 다릅니다. 크기 면에서 기본 제공 이벤트 허브는 최대 메시지 크기의 메시지를 최소 24시간 의 할당량까지 유지할 수 있습니다. 예를 들어, 1S1 단위의 IoT Hub는 각각 4k 크기의 400K 메시지를 유지하기에 충분한 스토리지를 제공합니다. 기기에서 더 작은 메시지를 보내는 경우 사용 되는 저장소의 양에 따라 더 이상(최대 7일) 동안 보관될 수 있습니다. 지정된 보존 시간 동안 데이터를 최소한으로 유지합니다.

또한 IoT Hub를 사용하면 기본 제공 디바이스-클라우드 수신 엔드포인트에서 소비자 그룹을 관리할 수 있습니다. 각 IoT Hub에 대해 최대 20개의 소비자 그룹을 가질 수 있습니다.

[메시지 라우팅을](iot-hub-devguide-messages-d2c.md) 사용하고 있고 [대체 경로가](iot-hub-devguide-messages-d2c.md#fallback-route) 활성화된 경우 모든 경로에서 쿼리와 일치하지 않는 모든 메시지는 기본 제공 끝점으로 이동합니다. 이 대체 경로를 사용하지 않도록 설정하면 쿼리와 일치하지 않는 메시지가 삭제됩니다.

[IoT Hub 리소스 공급자 REST API](/rest/api/iothub/iothubresource)를 사용하여 또는 [Azure Portal](https://portal.azure.com)을 사용하여 프로그래밍 방식으로 보존 시간을 수정할 수 있습니다.

IoT Hub는 허브에서 수신한 디바이스-클라우드 메시지를 읽도록 백 엔드 서비스의 기본 제공 엔드포인트인 **messages/events**를 공개합니다. 이 엔드포인트는 Event Hub와 호환되기 때문에 Event Hubs 서비스가 메시지 읽기에 대해 지원하는 모든 메커니즘을 사용할 수 있습니다.

## <a name="read-from-the-built-in-endpoint"></a>기본 제공 엔드포인트에서 읽기

일부 제품 통합 및 이벤트 허브 SDK는 IoT Hub를 인식하고 IoT 허브 서비스 연결 문자열을 사용하여 기본 제공 엔드포인트에 연결할 수 있습니다.

IoT Hub를 인식하지 못하는 이벤트 허브 SDK 또는 제품 통합을 사용하는 경우 이벤트 허브 호환 끝점 및 이벤트 허브 호환 이름이 필요합니다. 다음과 같이 포털에서 이러한 값을 검색할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.

2. **기본 제공 엔드포인트**를 클릭합니다.

3. **이벤트** 섹션에는 **파티션,** 이벤트 **허브 호환 이름,** **이벤트 허브 호환 끝점,** **보존 시간**및 **소비자 그룹이**포함됩니다.

    ![디바이스-클라우드 설정](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

포털에서 이벤트 허브 호환 끝점 필드에는 다음과 같은 전체 이벤트 허브 연결 문자열이 포함되어 있습니다. ** 공유 액세스키이름=iothubowner; 공유 액세스키=키키키키=; EntityPath=iothub-ehub-abcd-1234-123456**. 사용 중SDK에 다른 값이 필요한 경우 다음과 같은 값이 표시됩니다.

| 이름 | 값 |
| ---- | ----- |
| 엔드포인트 | sb://abcd1234namespace.servicebus.windows.net/ |
| Hostname | abcd1234namespace.servicebus.windows.net |
| 네임스페이스 | abcd1234네임스페이스 |

지정된 Event Hubs에 연결할 수 있는 **ServiceConnect** 권한이 있는 공유 액세스 정책을 사용할 수 있습니다.

IoT Hub가 노출하는 기본 제공 이벤트 허브 호환 엔드포인트에 연결하는 데 사용할 수 있는 SDK는 다음과 같습니다.

| 언어 | SDK) | 예제 | 메모 |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [빠른 시작](quickstart-send-telemetry-dotnet.md) | 이벤트 허브 호환 정보 사용 |
 Java | https://github.com/Azure/azure-event-hubs-java | [빠른 시작](quickstart-send-telemetry-java.md) | 이벤트 허브 호환 정보 사용 |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [빠른 시작](quickstart-send-telemetry-node.md) | IoT 허브 연결 문자열 사용 |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | IoT 허브 연결 문자열 사용 |

IoT Hub가 노출하는 기본 제공 이벤트 허브 호환 엔드포인트와 함께 사용할 수 있는 제품 통합에는 다음이 포함됩니다.

* [Azure 함수](https://docs.microsoft.com/azure/azure-functions/). [Azure 함수를 사용하여 IoT Hub의 데이터 처리를](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/)참조하십시오.
* [Azure 스트림 분석](https://docs.microsoft.com/azure/stream-analytics/). [스트림 분석에 입력으로 데이터 스트림을](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)참조하십시오.
* [타임 시리즈 인사이트](https://docs.microsoft.com/azure/time-series-insights/). [IT 허브 이벤트 소스를 타임시리즈 인사이트 환경에 추가를](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md)참조하십시오.
* [Apache Storm spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). GitHub의 [spout 원본](https://github.com/apache/storm/tree/master/external/storm-eventhubs) 을 볼 수 있습니다.
* [아파치 스파크 통합](../hdinsight/spark/apache-spark-eventhub-streaming.md).
* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

## <a name="next-steps"></a>다음 단계

* IoT Hub 엔드포인트에 대한 자세한 내용은 [IoT Hub 엔드포인트](iot-hub-devguide-endpoints.md)를 참조하세요.

* [빠른 시작](quickstart-send-telemetry-node.md)에서는 시뮬레이트된 디바이스에서 디바이스-클라우드 메시지를 보내고 기본 제공 엔드포인트에서 메시지를 읽는 방법을 보여 줍니다. 

자세한 내용은 [경로를 사용하여 IoT Hub 디바이스-클라우드 메시지 처리](tutorial-routing.md) 자습서를 참조하세요.

* 디바이스-클라우드 메시지를 사용자 지정 엔드포인트로 라우팅하려면 [디바이스-클라우드 메시지에 대해 메시지 라우팅 및 사용자 지정 엔드포인트 사용](iot-hub-devguide-messages-read-custom.md)을 참조하세요.
