---
title: 다른 서비스와 통합
titleSuffix: Azure Digital Twins
description: Azure Digital Twins를 배포할 때의 수신 및 송신 요구 사항을 이해합니다.
author: baanders
ms.author: baanders
ms.date: 3/16/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 118b02ab694d27dbe4e13cbfa1a617a56b052772
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "92043071"
---
# <a name="integrate-azure-digital-twins-with-other-services"></a>Azure Digital Twins를 다른 서비스와 통합

Azure Digital Twins는 일반적으로 다른 서비스와 함께 사용되어 다양한 방법으로 데이터를 사용하는 유연하고 연결된 솔루션을 만듭니다.

Azure Digital Twins는 [**이벤트 경로**](concepts-route-events.md)를 사용하여 원격 분석 및 알림을 전달하는 데 사용되는 [IoT Hub](../iot-hub/about-iot-hub.md) 또는 [Logic Apps](../logic-apps/logic-apps-overview.md) 같은 업스트림 서비스에서 데이터를 받을 수 있습니다. 

Azure Digital Twins는 스토리지, 워크플로 통합, 분석 등을 위해 데이터를 [Azure Maps](../azure-maps/about-azure-maps.md) 및 [Time Series Insights](../time-series-insights/overview-what-is-tsi.md) 같은 다운스트림 서비스로 라우팅할 수도 있습니다. 

## <a name="data-ingress"></a>데이터 수신

Azure Digital Twins는 [IoT Hub](../iot-hub/about-iot-hub.md), [Logic Apps](../logic-apps/logic-apps-overview.md), 사용자 지정 서비스 등 모든 서비스의 데이터 및 이벤트를 기반으로 할 수 있습니다. 따라서 사용자 환경의 물리적 디바이스에서 원격 분석을 수집하고 클라우드의 Azure Digital Twins 그래프를 사용하여 이 데이터를 처리할 수 있습니다.

기본 제공 IoT Hub를 백그라운드에서 사용하는 대신 Azure Digital Twins를 사용하여 서비스와 함께 사용할 수 있는 "사용자 고유의" IoT Hub를 제공할 수 있습니다. 현재 프로덕션에 있는 기존 IoT Hub를 사용하거나 이 목적으로 사용할 새 IoT Hub를 배포할 수 있습니다. 이를 통해 IoT Hub의 모든 디바이스 관리 기능에 대한 모든 권한을 제공합니다.

모든 원본에서 Azure Digital Twins로 데이터를 수집하려면 [**Azure 함수**](../azure-functions/functions-overview.md)를 사용합니다. 이 패턴에 대한 자세한 내용은 [*방법: IoT Hub에서 원격 분석 수집*](how-to-ingest-iot-hub-data.md)을 참조하거나 Azure Digital Twins [*자습서: 엔드투엔드 솔루션 연결*](tutorial-end-to-end.md)에서 직접 확인해 보세요. 

또한 [*방법: Logic Apps와 통합*](how-to-integrate-logic-apps.md)에서 Azure Digital Twins를 Logic Apps 트리거에 연결하는 방법에 대해 알아볼 수 있습니다.

## <a name="data-egress-services"></a>데이터 송신 서비스

Azure Digital Twins는 연결된 **엔드포인트** 에 데이터를 보낼 수 있습니다. 지원되는 엔드포인트는 다음과 같을 수 있습니다.
* [이벤트 허브](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

엔드포인트는 관리 API 또는 Azure Portal을 사용하여 Azure Digital Twins에 연결됩니다. 엔드포인트를 Azure Digital Twins에 연결하는 방법에 대한 자세한 내용은 [*방법: 엔드포인트 및 경로 관리*](how-to-manage-routes-apis-cli.md)를 참조하세요.

데이터를 궁극적으로 전달할 수 있는 [Azure Storage](../storage/common/storage-introduction.md), [Azure Maps](../azure-maps/about-azure-maps.md) 또는 [Time Series Insights](../time-series-insights/overview-what-is-tsi.md) 같은 다른 다양한 서비스가 있습니다. 이러한 서비스로 데이터를 전송하려면 대상 서비스를 엔드포인트에 연결합니다.

예를 들어 Azure Maps를 사용하고 위치와 Azure Digital Twins [트윈 그래프](concepts-twins-graph.md)의 상관 관계를 설정하려는 경우 Event Grid에서 Azure Functions를 사용하여 배포의 모든 서비스 간 통신을 설정할 수 있습니다. 자세한 내용은 [*방법: Azure Digital Twins를 사용하여 Azure Maps 실내 맵 업데이트*](how-to-integrate-maps.md)를 참조하세요.

[*방법: Time Series Insights와 통합*](how-to-integrate-time-series-insights.md)에서도 Time Series Insights와 비슷한 방식으로 데이터를 라우팅하는 방법을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

외부 서비스에 대한 엔드포인트 및 이벤트 라우팅에 대해 자세히 알아봅니다.
* [*개념: Azure Digital Twins 이벤트 라우팅*](concepts-route-events.md)

IoT Hub에서 데이터를 수집하도록 Azure Digital Twins를 설정하는 방법을 참조하세요.
* [*방법: IoT Hub에서 원격 분석 수집*](how-to-ingest-iot-hub-data.md)