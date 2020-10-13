---
title: 다른 서비스와 통합
titleSuffix: Azure Digital Twins
description: Azure Digital Twins를 배포할 때의 수신 및 송신 요구 사항을 이해 합니다.
author: baanders
ms.author: baanders
ms.date: 3/16/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 7e360c158c7887109684d13f774cbbda1813373e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91729137"
---
# <a name="integrate-azure-digital-twins-with-other-services"></a>Azure Digital Twins를 다른 서비스와 통합

Azure Digital Twins는 일반적으로 다른 서비스와 함께 사용 됩니다. Azure Digital Twins는 [**이벤트 경로**](concepts-route-events.md)를 사용 하 여 원격 분석 및 알림을 전달 하는 데 사용 되는 [IoT Hub](../iot-hub/about-iot-hub.md) 또는 [Logic Apps](../logic-apps/logic-apps-overview.md)같은 업스트림 서비스의 데이터를 수신 합니다. 

Azure Digital Twins는 저장소, 워크플로 통합, 분석 등을 위해 데이터를 [Azure Maps](../azure-maps/about-azure-maps.md) 및 [Time Series Insights](../time-series-insights/time-series-insights-update-overview.md)같은 다운스트림 서비스로 라우팅할 수도 있습니다. 

## <a name="data-ingress"></a>데이터 수신

Azure Digital Twins는[IoT Hub](../iot-hub/about-iot-hub.md), [Logic Apps](../logic-apps/logic-apps-overview.md), 사용자 지정 서비스 등 모든 서비스의 데이터 및 이벤트를 기반으로 할 수 있습니다. 이렇게 하면 사용자 환경의 물리적 장치에서 원격 분석을 수집 하 고 클라우드의 Azure Digital Twins 그래프를 사용 하 여이 데이터를 처리할 수 있습니다.

Azure Digital Twins에는 기본 제공 되는 IoT Hub 없습니다. 현재 프로덕션에 있는 기존 IoT Hub를 사용 하거나 새 기존을 배포할 수 있습니다. 이는 IoT Hub의 모든 장치 관리 기능에 대 한 모든 권한을 제공 합니다.

모든 원본에서 Azure Digital Twins로 데이터를 수집 하려면 [**azure 함수**](../azure-functions/functions-overview.md)를 사용 합니다. 이 패턴에 대 한 자세한 내용은 [*방법: IoT Hub에서 원격 분석 수집*](how-to-ingest-iot-hub-data.md)또는 Azure Digital Twins [*자습서: 종단 간 솔루션 연결*](tutorial-end-to-end.md)에서 직접 사용해 보세요. 

또한 [*방법: Logic Apps와 통합*](how-to-integrate-logic-apps.md)에서 Azure Digital twins를 Logic Apps 트리거에 연결 하는 방법에 대해 알아볼 수 있습니다.

## <a name="data-egress-services"></a>데이터 송신 서비스

Azure Digital Twins는 연결 된 **끝점**에 데이터를 보낼 수 있습니다. 지원 되는 끝점은 다음과 같을 수 있습니다.
* [이벤트 허브](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

끝점은 관리 Api 또는 Azure Portal를 사용 하 여 Azure Digital Twins에 연결 됩니다. 끝점을 Azure Digital Twins에 연결 하는 방법에 대 한 자세한 내용은 [*방법: 끝점과 경로 관리*](how-to-manage-routes-apis-cli.md)를 확인 하세요.

[Azure Storage](../storage/common/storage-introduction.md), [Azure Maps](../azure-maps/about-azure-maps.md)또는 [Time Series Insights](../time-series-insights/time-series-insights-update-overview.md)와 같이 궁극적으로 데이터를 전달할 수 있는 다른 많은 서비스가 있습니다. 이러한 데이터를 이러한 서비스로 전송 하려면 대상 서비스를 끝점에 연결 합니다.

예를 들어 Azure Maps를 사용 하 고 위치와 Azure Digital Twins 쌍의 상관 [관계를 설정](concepts-twins-graph.md)하려는 경우 Event Grid에서 Azure Functions를 사용 하 여 배포의 모든 서비스 간 통신을 설정할 수 있습니다. [ *방법: Azure Digital Twins를 사용 하 여 Azure Maps 실내 맵 업데이트* 에서이에 대해 자세히 알아보세요.](how-to-integrate-maps.md)

또한 [*방법: Time Series Insights와 통합*](how-to-integrate-time-series-insights.md)에서 Time Series Insights 하는 것과 비슷한 방식으로 데이터를 라우팅하는 방법을 배울 수 있습니다.

## <a name="next-steps"></a>다음 단계

외부 서비스에 대 한 끝점 및 라우팅 이벤트에 대해 자세히 알아보세요.
* [*개념: Azure Digital Twins 이벤트 라우팅*](concepts-route-events.md)

IoT Hub에서 데이터를 수집 하도록 Azure Digital Twins를 설정 하는 방법을 참조 하세요.
* [*방법: IoT Hub에서 원격 분석 수집*](how-to-ingest-iot-hub-data.md)
