---
title: Azure IoT Hub 할당량 및 제한 이해 | Microsoft 문서
description: 개발자 가이드 - IoT Hub에 적용할 할당량 및 예상되는 제한 동작을 설명합니다.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: daeb09acd11d727b11ad8a7b98d97ff90fddc6d8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228258"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>참조 - IoT Hub 할당량 및 제한

This article explains the quotas for an IoT Hub, and provides information to help you understand how throttling works.

## <a name="quotas-and-throttling"></a>할당량 및 제한

각 Azure 구독은 IoT Hub 최대 50개와 무료 허브 최대 1개를 가질 수 있습니다.

각 IoT 허브는 특정 계층에서 특정한 단위 수로 프로비전됩니다. 계층과 단위 수는 보낼 수 있는 메시지의 최대 일일 할당량을 결정합니다. 일일 할당량을 계산하는 데 사용되는 메시지 크기는 무료 계층 허브의 경우 0.5KB이며 기타 모든 계층의 경우 4KB입니다. 자세한 내용은 [Azure IoT Hub 가격](https://azure.microsoft.com/pricing/details/iot-hub/)을 참조하세요.

또한 계층은 IoT Hub가 모든 작업에 강제로 적용하는 조정 제한을 결정합니다.

### <a name="iot-plug-and-play"></a>IoT 플러그 앤 플레이

During public preview, IoT Plug and Play devices will send separate messages per interface, which may increase the number of messages counted towards your message quota.

## <a name="operation-throttles"></a>작업 제한

Operation throttles are rate limitations that are applied in minute ranges and are intended to prevent abuse. They're also subject to [traffic shaping](#traffic-shaping).

다음 표에서는 적용된 제한을 보여 줍니다. 값은 개별 허브라고 합니다.

| 제한 | 무료, B1 및 S1 | B2 및 S2 | B3 및 S3 | 
| -------- | ------- | ------- | ------- |
| [Identity registry operations](#identity-registry-operations-throttle) (create, retrieve, list, update, delete) | 1.67/초/단위(100/분/단위) | 1.67/초/단위(100/분/단위) | 83.33/sec/unit (5,000/min/unit) |
| [New device connections](#device-connections-throttle) (this limit applies to the rate of _new connections_, not the total number of connections) | 100/초 또는 12/초/단위 이상 <br/> 예를 들어 두 개의 S1 단위는 2\*12=24 새 연결/초이지만 단위에는 100개 이상의 새 연결/초가 있습니다. S1 단위가 9개인 경우 단위 전체에 대한 값은 108 새 연결/초(9\*12)입니다. | 120 새 연결/초/단위 | 6,000 new connections/sec/unit |
| 디바이스-&gt;클라우드 보내기 | Higher of 100 send operations/sec or 12 send operations/sec/unit <br/> For example, two S1 units are 2\*12 = 24/sec, but you have at least 100 send operations/sec across your units. With nine S1 units, you have 108 send operations/sec (9\*12) across your units. | 120 send operations/sec/unit | 6,000 send operations/sec/unit |
| 클라우드-디바이스 보내기<sup>1</sup> | 1.67 send operations/sec/unit (100 messages/min/unit) | 1.67 send operations/sec/unit (100 send operations/min/unit) | 83.33 send operations/sec/unit (5,000 send operations/min/unit) |
| 클라우드-디바이스 받기<sup>1</sup> <br/> (디바이스에서 HTTPS를 사용하는 경우에만)| 16.67 receive operations/sec/unit (1,000 receive operations/min/unit) | 16.67 receive operations/sec/unit (1,000 receive operations/min/unit) | 833.33 receive operations/sec/unit (50,000 receive operations/min/unit) |
| 파일 업로드 | 1.67 file upload initiations/sec/unit (100/min/unit) | 1.67 file upload initiations/sec/unit (100/min/unit) | 83.33 file upload initiations/sec/unit (5,000/min/unit) |
| 직접 메서드<sup>1</sup> | 160KB/sec/unit<sup>2</sup> | 480KB/sec/unit<sup>2</sup> | 24MB/sec/unit<sup>2</sup> | 
| 쿼리 | 20/min/unit | 20/min/unit | 1,000/min/unit |
| 쌍(디바이스 및 모듈) 읽기<sup>1</sup> | 100/sec | Higher of 100/sec or 10/sec/unit | 500/sec/unit |
| 쌍 업데이트(디바이스 및 모듈)<sup>1</sup> | 50/sec | Higher of 50/sec or 5/sec/unit | 250/sec/unit |
| 작업(Job) 작업<sup>1</sup> <br/> (만들기, 업데이트, 나열, 삭제) | 1.67/초/단위(100/분/단위) | 1.67/초/단위(100/분/단위) | 83.33/sec/unit (5,000/min/unit) |
| 작업 디바이스 연산<sup>1</sup> <br/> (쌍 업데이트, 직접 메서드 호출) | 10/초 | 10/초 또는 1/초/단위 이상 | 50/초/단위 |
| 구성 및 에지 배포<sup>1</sup> <br/> (만들기, 업데이트, 나열, 삭제) | 0.33/초/단위(20/분/단위) | 0.33/초/단위(20/분/단위) | 0.33/초/단위(20/분/단위) |
| Device stream initiation rate<sup>1</sup> | 새 스트림 5개/초 | 새 스트림 5개/초 | 새 스트림 5개/초 |
| Maximum number of concurrently connected device streams<sup>1</sup> | 50 | 50 | 50 |
| Maximum device stream data transfer<sup>1</sup> (aggregate volume per day) | 300MB | 300MB | 300MB |

<sup>1</sup>이 기능은 IoT Hub의 기본 계층에서 사용할 수 없습니다. 자세한 내용은 [올바른 IoT Hub를 선택하는 방법](iot-hub-scaling.md)을 참조하세요. <br/><sup>2</sup>Throttling meter size is 4 KB.

### <a name="throttling-details"></a>Throttling Details

* The meter size determines at what increments your throttling limit is consumed. If your direct call's payload is between 0 and 4 KB, it is counted as 4 KB. You can make up to 40 calls per second per unit before hitting the limit of 160 KB/sec/unit.

   Similarly, if your payload is between 4 KB and 8 KB, each call accounts for 8 KB and you can make up to 20 calls per second per unit before hitting the max limit.

   Finally, if your payload size is between 156KB and 160 KB, you'll be able to make only 1 call per second per unit in your hub before hitting the limit of 160 KB/sec/unit.

*  For *Jobs device operations (update twin, invoke direct method)* for tier S2, 50/sec/unit only applies to when you invoke methods using jobs. If you invoke direct methods directly, the original throttling limit of 24 MB/sec/unit (for S2) applies.

*  **Quota** is the aggregate number of messages you can send in your hub *per day*. You can find your hub's quota limit under the column **Total number of messages /day** on the [IoT Hub pricing page](https://azure.microsoft.com/pricing/details/iot-hub/).

*  Your cloud-to-device and device-to-cloud throttles determine the maximum *rate* at which you can send messages -- number of messages irrespective of 4 KB chunks. Each message can be up to 256 KB which is the [maximum message size](iot-hub-devguide-quotas-throttling.md#other-limits).

*  It's a good practice to throttle your calls so that you don't hit/exceed the throttling limits. If you do hit the limit, IoT Hub responds with error code 429 and the client should back-off and retry. These limits are per hub (or in some cases per hub/unit). For more information, refer to [Manage connectivity and reliable messaging/Retry patterns](iot-hub-reliability-features-in-sdks.md#retry-patterns).

### <a name="traffic-shaping"></a>Traffic shaping

To accommodate burst traffic, IoT Hub accepts requests above the throttle for a limited time. The first few of these requests are processed immediately. However, if the number of requests continues violate the throttle, IoT Hub starts placing the requests in a queue and processed at the limit rate. This effect is called *traffic shaping*. Furthermore, the size of this queue is limited. If the throttle violation continues, eventually the queue fills up, and IoT Hub starts rejecting requests with `429 ThrottlingException`.

For example, you use a simulated device to send 200 device-to-cloud messages per second to your S1 IoT Hub (which has a limit of 100/sec D2C sends). For the first minute or two, the messages are processed immediately. However, since the device continues to send more messages than the throttle limit, IoT Hub begins to only process 100 messages per second and puts the rest in a queue. You start noticing increased latency. Eventually, you start getting `429 ThrottlingException` as the queue fills up, and the "number of throttle errors" in [IoT Hub's metrics](iot-hub-metrics.md) starts increasing.

### <a name="identity-registry-operations-throttle"></a>Identity registry operations throttle

Device identity registry operations are intended for run-time use in device management and provisioning scenarios. 많은 수의 디바이스 ID는 [가져오기 및 내보내기 작업](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)을 통해 읽거나 업데이트할 수 있습니다.

### <a name="device-connections-throttle"></a>Device connections throttle

*디바이스 연결* 제한은 IoT Hub에서 새 디바이스 연결을 설정할 수 있는 속도를 제어합니다. *디바이스 연결* 제한은 동시에 연결되는 디바이스의 최대 수를 제어하지 않습니다. *디바이스 연결* 속도 제한은 IoT Hub에 대해 프로비전되는 단위의 수에 따라 다릅니다.

예를 들어 S1 단위 하나를 구매하는 경우 초당 연결 제한은 100개입니다. Therefore, to connect 100,000 devices, it takes at least 1,000 seconds (approximately 16 minutes). 그러나 ID 레지스트리에 등록한 수만큼의 디바이스를 동시에 연결할 수 있습니다.

## <a name="other-limits"></a>기타 제한

IoT Hub에는 다른 작업 제한도 적용됩니다.

| 작업(Operation) | 제한 |
| --------- | ----- |
| 디바이스 | The total number of devices plus modules that can be registered to a single IoT hub is capped at 1,000,000. The only way to increase this limit is to contact [Microsoft Support](https://azure.microsoft.com/support/options/).|
| 파일 업로드 | 10 concurrent file uploads per device. |
| 작업<sup>1</sup> | Maximum concurrent jobs is 1 (for Free and S1), 5 (for S2), and 10 (for S3). However, the max concurrent [device import/export jobs](iot-hub-bulk-identity-mgmt.md) is 1 for all tiers. <br/>Job history is retained up to 30 days. |
| 추가 엔드포인트 | 유료 SKU 허브에는 10개, 무료 SKU 허브에는 하나의 추가 엔드포인트가 있을 수 있습니다. |
| Message routing queries | Paid SKU hubs may have 100 routing queries. Free SKU hubs may have five routing queries. |
| 메시지 보강 | Paid SKU hubs can have up to 10 message enrichments. Free SKU hubs can have up to 2 message enrichments.|
| 디바이스-클라우드 메시징 | 최대 메시지 크기 256KB |
| 클라우드-디바이스 메시징<sup>1</sup> | 최대 메시지 크기 64KB Maximum pending messages for delivery is 50 per device. |
| 직접 메서드<sup>1</sup> | 최대 직접 메서드 페이로드 크기는 128KB입니다. |
| 자동 디바이스 구성<sup>1</sup> | 유료 SKU 허브당 100개 구성입니다. 체험 SKU 허브당 20개 구성입니다. |
| IoT Edge automatic deployments<sup>1</sup> | 배포당 20개의 모듈 유료 SKU 허브당 100개 배포입니다. 10 deployments per free SKU hub. |
| 쌍<sup>1</sup> | Maximum size of desired properties and reported properties sections are 32 KB each. Maximum size of tags section is 8 KB. |

<sup>1</sup>이 기능은 IoT Hub의 기본 계층에서 사용할 수 없습니다. 자세한 내용은 [올바른 IoT Hub를 선택하는 방법](iot-hub-scaling.md)을 참조하세요.

## <a name="increasing-the-quota-or-throttle-limit"></a>Increasing the quota or throttle limit

At any given time, you can increase quotas or throttle limits by [increasing the number of provisioned units in an IoT hub](iot-hub-upgrade.md).

## <a name="latency"></a>대기 시간

IoT Hub는 모든 작업에 낮은 대기 시간을 제공하기 위해 노력합니다. However, due to network conditions and other unpredictable factors it cannot guarantee a certain latency. 솔루션을 설계할 때 다음을 수행해야 합니다.

* IoT Hub 작업의 최대 대기 시간을 가정하지 마세요.
* 디바이스에 가장 가까운 Azure 지역에서 IoT Hub를 프로비전합니다.
* Azure IoT Edge를 사용하여 디바이스 또는 디바이스에 가까운 게이트웨이에서 대기 시간에 민감한 작업을 수행하는 것이 좋습니다.

여러 IoT Hub 단위는 앞에서 설명한 대로 제한에 영향을 주지만 추가 대기 시간을 주거나 보장하지 않습니다.

작업 대기 시간에 예기치 않은 증가가 발생한 경우 [Microsoft 지원](https://azure.microsoft.com/support/options/)에 문의하세요.

## <a name="next-steps"></a>다음 단계

IoT Hub 제한 동작에 대한 자세한 내용은 [IoT Hub 제한](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/) 블로그 게시물을 참조하세요.

이 IoT Hub 개발자 가이드의 다른 참조 자료:

* [IoT Hub 엔드포인트](iot-hub-devguide-endpoints.md)
