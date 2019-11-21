---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 34de38e91d47457d215c7ebf65d04ed2dbae5324
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224534"
---
The following table lists the limits associated with the different service tiers S1, S2, S3, and F1. For information about the cost of each *unit* in each tier, see [Azure IoT Hub pricing](https://azure.microsoft.com/pricing/details/iot-hub/).

| 리소스 | S1 표준 | S2 표준 | S3 표준 | F1 무료 |
| --- | --- | --- | --- | --- |
| 메시지 수/1일 |400,000 |6,000,000 |300,000,000 |8,000 |
| 최대 단위 |200 |200 |10 |1 |

> [!NOTE]
> If you anticipate using more than 200 units with an S1 or S2 tier hub or 10 units with an S3 tier hub, contact Microsoft Support.
> 
> 

The following table lists the limits that apply to IoT Hub resources.

| 리소스 | 제한 |
| --- | --- |
| Azure 구독당 최대 유료 IoT Hub |100 |
| Azure 구독당 최대 무료 IoT Hub |1 |
| Maximum number of characters in a device ID | 128 |
| 단일 호출에서 반환되는 최대 디바이스 ID<br/> 수 |1,000 |
| 디바이스-&gt;클라우드 메시지의 IoT Hub 메시지 최대 보존 |7일 |
| 디바이스-&gt;클라우드 메시지의 최대 크기 |256KB |
| 디바이스-&gt;클라우드 배치의 최대 크기 |AMQP 및 HTTP: 전체 일괄 처리에 256KB <br/>MQTT: 각 메시지에 256KB |
| 디바이스-&gt;클라우드 배치의 최대 메시지 |500 |
| 클라우드-&gt;디바이스 메시지의 최대 크기 |64KB |
| 클라우드-&gt;디바이스 메시지의 최대 TTL |2일 |
| 클라우드-&gt;디바이스 <br/> 메시지의 최대 전달 수 |100 |
| Maximum cloud-to-device queue depth per device |50 |
| 클라우드->장치 메시지에 대한 응답에서 피드백 <br/> 메시지의 최대 전달 수 |100 |
| 클라우드->장치 메시지에 대한 응답에서 피드백 <br/> 메시지의 최대 TTL |2일 |
| [Maximum size of device twin](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) | 8 KB for tags section, and 32 KB for desired and reported properties sections each |
| Maximum length of device twin string key | 1KB |
| Maximum length of device twin string value | 4KB |
| [Maximum depth of object in device twin](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 10 |
| 직접 메서드 페이로드의 최대 크기 | 128KB |
| 작업 내역의 최대 보존 | 30일 |
| 최대 동시 작업 | 10(S3의 경우), 5(S2의 경우), 1(S1의 경우) |
| 최대 추가 엔드포인트 | 10 (for S1, S2, and S3) |
| 최대 메시지 라우팅 규칙 | 100 (for S1, S2, and S3) |
| 동시에 연결되는 최대 디바이스 스트림 수 | 50(S1, S2, S3 및 F1만 해당) |
| 최대 디바이스 스트림 데이터 전송 | 하루 300MB(S1, S2, S3 및 F1만 해당) |

> [!NOTE]
> If you need more than 100 paid IoT hubs in an Azure subscription, contact Microsoft Support.

> [!NOTE]
> Currently, the total number of devices plus modules that can be registered to a single IoT hub is capped at 1,000,000. 이 제한을 높이려면 [Microsoft 지원](https://azure.microsoft.com/support/options/)에 문의하세요.

IoT Hub throttles requests when the following quotas are exceeded.

| 제한 | 허브 단위 값 |
| --- | --- |
| ID 레지스트리 작업 <br/> (create, retrieve, list, update, and delete), <br/> 개별 또는 대량 가져오기/내보내기 |83.33/sec/unit (5,000/min/unit) (for S3). <br/> 1.67/초/단위(100/분/단위)(S1 및 S2의 경우) |
| 디바이스 연결 |6,000/sec/unit (for S3), 120/sec/unit (for S2), 12/sec/unit (for S1). <br/>100/초의 최솟값. |
| 디바이스-&gt;클라우드 보내기 |6,000/sec/unit (for S3), 120/sec/unit (for S2), 12/sec/unit (for S1). <br/>100/초의 최솟값. |
| 클라우드-디바이스 보내기 | 83.33/sec/unit (5,000/min/unit) (for S3), 1.67/sec/unit (100/min/unit) (for S1 and S2). |
| 클라우드-디바이스 받기 |833.33/sec/unit (50,000/min/unit) (for S3), 16.67/sec/unit (1,000/min/unit) (for S1 and S2). |
| 파일 업로드 작업 |83.33 file upload initiations/sec/unit (5,000/min/unit) (for S3), 1.67 file upload initiations/sec/unit (100/min/unit) (for S1 and S2). <br/> 10,000 SAS URIs can be out for an Azure Storage account at one time.<br/> 10 SAS URI/디바이스는 한 번에 나올 수 있습니다. |
| 직접 메서드 | 24 MB/sec/unit (for S3), 480 KB/sec/unit (for S2), 160 KB/sec/unit (for S1).<br/> Based on 8-KB throttling meter size. |
| 디바이스 쌍 읽기 | 500/sec/unit (for S3), Maximum of 100/sec or 10/sec/unit (for S2), 100/sec (for S1) |
| 디바이스 쌍 업데이트 | 250/sec/unit (for S3), Maximum of 50/sec or 5/sec/unit (for S2), 50/sec (for S1) |
| 작업 연산 <br/> (create, update, list, and delete) | 83.33/sec/unit (5,000/min/unit) (for S3), 1.67/sec/unit (100/min/unit) (for S2), 1.67/sec/unit (100/min/unit) (for S1). |
| 디바이스 단위 작업 연산 처리량 | 50/sec/unit (for S3), maximum of 10/sec or 1/sec/unit (for S2), 10/sec (for S1). |
| 디바이스 스트림 시작 속도 | 5 new streams/sec (for S1, S2, S3, and F1 only). |
