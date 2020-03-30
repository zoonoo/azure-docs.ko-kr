---
title: IoT Hub에 대한 Event Grid, 라우팅 비교 | Microsoft Docs
description: IoT Hub은 자체 메시지 라우팅 서비스를 제공하지만, 또한 Event Grid와 통합하여 이벤트 게시를 합니다. 두 기능을 비교합니다.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 877467b65d346c871dd93f4b3f96b2c1664fa4b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73906796"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>IoT Hub에 대한 메시지 라우팅과 Event Grid 비교

Azure IoT Hub는 연결된 장치에서 데이터를 스트리밍하고 해당 데이터를 비즈니스 응용 프로그램에 통합하는 기능을 제공합니다. IoT Hub는 IoT 이벤트를 다른 Azure 서비스 또는 비즈니스 애플리케이션에 통합하기 위한 두 가지 방법을 제공합니다. 이 아티클에서는 이 기능을 제공하여 시나리오에 가장 적합한 옵션을 선택할 수 있도록 하는 두 가지 기능을 설명합니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[IoT Hub 메시지 라우팅](iot-hub-devguide-messages-d2c.md)**: 이 IoT Hub 기능을 사용하면 Azure Storage 컨테이너, 이벤트 허브, 서비스 버스 큐 및 서비스 버스 항목과 같은 서비스 끝점으로 장치-클라우드 메시지를 라우팅할 수 있습니다. 라우팅은 엔드포인트에 라우팅하기 전에 데이터를 필터링하는 쿼리 기능도 제공합니다. 디바이스 원격 분석 데이터 외에도 작업을 트리거하는 데 사용할 수 있는 [원격 분석 이외 이벤트](iot-hub-devguide-messages-d2c.md#non-telemetry-events)를 보낼 수도 있습니다. 

**Event Grid와 IoT Hub 통합**: Azure Event Grid는 게시-구독 모델을 사용하여 완전히 관리되는 이벤트 라우팅 서비스입니다. IoT Hub 및 Event Grid는 거의 실시간으로 [IoT Hub 이벤트를 Azure 및 외부 Azure 서비스에 통합](iot-hub-event-grid.md)하기 위해 함께 작동합니다. IoT Hub는 [장치 이벤트와](iot-hub-event-grid.md#event-types) 원격 분석 이벤트를 모두 게시합니다.

## <a name="differences"></a>차이점

메시지 라우팅 및 Event Grid가 경고 구성을 사용하도록 설정하는 한편 둘 사이에는 몇 가지 주요 차이점이 있습니다. 자세한 내용은 다음 표를 참조하세요.

| 기능 | IoT Hub 메시지 라우팅 | Event Grid와 IoT Hub 통합 |
| ------- | --------------- | ---------- |
| **장치 메시지 및 이벤트** | 예. 메시지 라우팅은 원격 분석 데이터, 보고서 장치 쌍 변경, 장치 수명 주기 이벤트 및 디지털 트윈 변경 [이벤트(IoT 플러그 앤 플레이 공개 미리 보기의](../iot-pnp/overview-iot-plug-and-play.md)일부)에 사용할 수 있습니다. | 예. 이벤트 그리드는 원격 분석 데이터에 사용할 수 있지만 IoT Hub에서 장치를 생성, 삭제, 연결 및 연결이 끊을 때 보고할 수도 있습니다. |
| **순서 지정** | 예, 이벤트 순서가 유지 관리됩니다.  | 아니요, 이벤트 순서가 보장되지 않습니다. | 
| **필터링** | 메시지 애플리케이션 속성, 메시지 시스템 속성, 메시지 본문, 디바이스 쌍 태그 및 디바이스 쌍 속성에 대한 다양한 필터링입니다. 필터링은 디지털 쌍 변경 이벤트에 적용되지 않습니다. 예를 보려면 [메시지 라우팅 쿼리 구문](iot-hub-devguide-routing-query-syntax.md)을 참조하세요. | 각 이벤트의 이벤트 유형, 제목 유형 및 특성을 기반으로 필터링합니다. 예: 이벤트 [그리드 구독에서 필터링 이벤트 이해하기 를 참조합니다.](../event-grid/event-filtering.md) 원격 분석 이벤트에 가입할 때 데이터에 추가 필터를 적용하여 IoT Hub에서 메시지 속성, 메시지 본문 및 장치 쌍에 대해 필터링한 다음 이벤트 그리드에 게시할 수 있습니다. [이벤트를 필터링하는 방법을](../iot-hub/iot-hub-event-grid.md#filter-events)참조하십시오. |
| **엔드포인트** | <ul><li>Event Hubs</li> <li>Azure Blob Storage</li> <li>Service Bus 큐</li> <li>Service Bus 토픽</li></ul><br>유료 IoT Hub SKU(S1, S2, S3)은 사용자 지정 엔드포인트 10개로 제한됩니다. IoT Hub당 100 경로를 만들 수 있습니다. | <ul><li>Azure Functions</li> <li>Azure Automation</li> <li>Event Hubs</li> <li>Logic Apps</li> <li>스토리지 Blob</li> <li>사용자 지정 토픽</li> <li>Queue Storage</li> <li>Microsoft Flow</li> <li>Webhook 통한 다른 공급 업체 서비스</li></ul><br>IoT Hub당 500개의 엔드포인트가 지원됩니다. 엔드포인트 최신 목록은 [Event Grid 이벤트 처리기](../event-grid/overview.md#event-handlers)를 참조하세요. |
| **비용** | 메시지 라우팅에 대한 별도의 요금은 없습니다. IoT Hub로의 원격 분석 수신만 청구됩니다. 예를 들어 세 가지 다른 엔드포인트로 라우팅되는 메시지가 있을 경우 하나의 메시지에 대해서만 요금이 청구됩니다. | IoT Hub는 무료입니다. Event Grid는 매월 처음 100,000개의 작업을 무료로 제공하며, 그 후 백만 개당 0.60 USD를 제공합니다. |

## <a name="similarities"></a>유사성

IoT Hub 메시지 라우팅 및 Event Grid는 또한 유사성을 가졌으며, 그 중 일부는 다음 테이블에 자세히 나와 있습니다.

| 기능 | IoT Hub 메시지 라우팅 | Event Grid와 IoT Hub 통합 |
| ------- | --------------- | ---------- |
| **최대 메시지 크기** | 256 KB, 디바이스-클라우드 | 256 KB, 디바이스-클라우드 |
| **안정성** | 높음: 각 경로에 대해 최소 한 번씩 엔드포인트에 각 메시지를 배달합니다. 한 시간 내에 배달되지 않는 모든 메시지에 만료됩니다. | 높음: 각 구독에 대해 각 메시지를 최소 한 번 웹후크에 배달합니다. 24시간 이내에 배달되지 않는 모든 이벤트 만료됩니다. | 
| **확장성** | 높음: 수백만 대의 동시에 연결된 디바이스가 수십억 개의 메시지를 보내는 것을 지원하도록 최적화됩니다. | 높음: 지역마다 초당 10,000,000 이벤트 라우팅이 가능합니다. |
| **대기 시간이** | 낮음: 거의 실시간. | 낮음: 거의 실시간. |
| **여러 엔드포인트로 보냄** | 예, 단일 메시지를 여러 엔드포인트에 보냅니다. | 예, 단일 메시지를 여러 엔드포인트에 보냅니다.  
| **보안** | IoT Hub는 디바이스 단위 ID 및 취소 가능한 액세스 제어를 제공합니다. 자세한 내용은 [IoT Hub 액세스 제어](iot-hub-devguide-security.md)를 참조하세요. | Event Grid는 이벤트 구독, 이벤트 게시 및 웹후크 이벤트 전달이라는 세 지점에 대한 유효성 검사를 제공합니다. 자세한 내용은 [Event Grid 보안 및 인증](../event-grid/security-authentication.md)을 참조하세요. |

## <a name="how-to-choose"></a>선택 방법

IoT Hub 메시지 라우팅 및 Event Grid와 IoT Hub 통합은 비슷한 결과를 달성하기 위해 다른 작업을 수행합니다. 둘 다 IoT Hub 솔루션에서 정보를 받아 전달하여 다른 서비스가 반응할 수 있게 합니다. 어떤 것을 사용할지 어떻게 결정합니까? 결정을 내리는 데 도움이 되는 다음 질문을 고려한다. 

* **엔드포인트에 어떤 종류의 데이터를 보냅니까?**

   원격 분석 데이터를 다른 서비스에 전송해야 할 경우 IoT Hub 메시지 라우팅을 사용합니다. 메시지 라우팅을 통해 메시지 애플리케이션 및 시스템 속성, 메시지 본문, 디바이스 쌍 태그 및 디바이스 쌍 속성을 쿼리할 수도 있습니다.

   Event Grid와 IoT Hub 통합은 IoT Hub 서비스에서 발생하는 이벤트와 함께 작동합니다. 이러한 IoT Hub 이벤트에는 원격 분석 데이터, 생성된 장치, 삭제됨, 연결됨 및 연결이 끊어진 장치가 포함됩니다. 원격 분석 이벤트에 가입할 때 데이터에 추가 필터를 적용하여 IoT Hub에서 메시지 속성, 메시지 본문 및 장치 쌍에 대해 필터링한 다음 이벤트 그리드에 게시할 수 있습니다. [이벤트를 필터링하는 방법을](../iot-hub/iot-hub-event-grid.md#filter-events)참조하십시오.

* **이 정보를 수신하려면 어떤 엔드포인트가 필요하나요?**

   IoT Hub 메시지 라우팅은 제한된 수의 고유 끝점 및 끝점 유형을 지원하지만 커넥터를 빌드하여 데이터 및 이벤트를 추가 끝점으로 다시 라우팅할 수 있습니다. 지원되는 엔드포인트의 전체 목록은, 이전 섹션의 테이블을 참조합니다. 

   Event Grid와의 IoT Hub 통합은 IoT Hub당 500개의 엔드포인트와 더 다양한 엔드포인트 유형을 지원합니다. 기본적으로 Azure Function, 논리 앱, 저장소 및 서비스 버스 큐와 통합되며 웹후크와 함께 작동하여 Azure 서비스 에코시스템 외부및 타사 비즈니스 응용 프로그램으로 전송 데이터를 확장합니다.

* **데이터가 순서대로 도착하는 것이 중요합니까?**

   IoT Hub 메시지 라우팅은 메시지를 보낸 순서를 유지 관리하여 같은 방식으로 도착하게 합니다.

   Event Grid는 발생했던 순서와 같은 순서로 엔드포인트가 이벤트를 받도록 보장하지 않습니다. 메시지의 절대 순서가 중요하거나 소비자가 메시지에 대해 신뢰할 수 있는 고유 식별자가 필요한 경우 메시지 라우팅을 사용하는 것이 좋습니다. 

## <a name="next-steps"></a>다음 단계

* [IoT Hub 메시지 라우팅](iot-hub-devguide-messages-d2c.md) 및 [IoT Hub 엔드포인트](iot-hub-devguide-endpoints.md)에 대해 자세히 알아봅니다.
* [Azure Event Grid](../event-grid/overview.md)에 대해 자세히 알아봅니다.
* 메시지 경로를 만드는 방법에 대한 자세한 내용은 [경로를 사용하여 IoT Hub 디바이스-클라우드 메시지 처리](../iot-hub/tutorial-routing.md) 자습서를 참조하세요.
* [논리 앱을 사용하여 Azure IoT Hub 이벤트에 대한 전자 메일 알림을 전송하여](../event-grid/publish-iot-hub-events-to-logic-apps.md)이벤트 그리드 통합을 사용해 보십시오.
