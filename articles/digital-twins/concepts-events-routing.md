---
title: Azure Digital Twins를 사용하여 이벤트 및 메시지 라우팅 | Microsoft Docs
description: Azure Digital Twins를 사용하여 서비스 엔드포인트에 이벤트 및 메시지를 라우팅하는 개요
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 9b861f0991b11637c7b27b645d4506e658ea53b3
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324032"
---
# <a name="routing-events-and-messages"></a>이벤트 라우팅 및 메시지

IoT 솔루션은 저장소, 분석 등을 비롯한 몇 가지 강력한 서비스를 통합하는 경우가 있습니다. 이 문서에서는 Azure Digital Twins 앱을 Azure 분석, AI 및 저장소 서비스에 연결하여 심층 인사이트 및 기능을 사용하여 강화하는 방법을 설명합니다.

## <a name="route-types"></a>경로 형식

Digital Twins는 IoT 이벤트를 다른 Azure 서비스 또는 비즈니스 응용 프로그램에 통합하는 두 가지 방법을 제공합니다.

* **Digital Twins 이벤트 라우팅**: 공간 그래프의 개체가 변경되거나, 원격 분석 데이터를 수신하거나, 사용자 정의 함수가 미리 정의된 조건에 따라 알림을 만들 때 Azure Digital Twins 이벤트를 트리거할 수 있습니다. 사용자는 추가로 처리하기 위해 이러한 이벤트를 [Event Hubs](https://azure.microsoft.com/services/event-hubs/), [Service Bus 토픽](https://azure.microsoft.com/services/service-bus/) 또는 [Event Grid](https://azure.microsoft.com/services/event-grid/)로 보낼 수 있습니다.

* **장치 원격 분석 라우팅**: 라우팅 이벤트 외에도 Azure Digital Twins는 추가 인사이트 및 분석을 위해 원시 장치 원격 분석 메시지를 Event Hubs로 라우팅할 수도 있습니다. 이러한 형식의 메시지는 Digital Twins에 의해 처리되지 않고 **Event Hub**로만 전달됩니다.

사용자는 이벤트를 보내거나 메시지를 전달하도록 하나 이상의 송신 엔드포인트를 지정할 수 있습니다. 이벤트 및 메시지는 이러한 미리 정의된 라우팅 기본 설정에 따라 엔드포이트로 전송됩니다. 즉, 사용자는 그래프 작업 이벤트를 수신하는 하나의 특정 엔드포인트, 장치 원격 분석 이벤트를 수신하는 다른 특정 엔드포인트 등을 지정할 수 있습니다.

![Digital Twins 이벤트 라우팅][1]

**Event Hubs**에 라우팅하면 원래 수신된 동일한 시퀀스로 엔드포인트에 도착하도록 원격 분석 메시지를 보내는 순서를 유지 관리합니다. **Event Grid** 및 **Service Bus**는 발생했던 것과 동일한 순서로 엔드포인트가 이벤트를 받도록 보장하지 않습니다. 그러나 이벤트 스키마는 이벤트가 엔드포인트에 도착한 후에 순서를 식별하는 데 사용할 수 있는 타임스탬프를 포함합니다.

## <a name="route-implementation"></a>경로 구현

Azure Digital Twins 서비스는 현재 다음 **EndpointTypes**를 지원합니다.

* **EventHub**: Event Hub 연결 문자열 엔드포인트입니다.
* **ServiceBus**: Service Bus 연결 문자열 엔드포인트입니다.
* **EventGrid**: Event Grid 연결 문자열 엔드포인트입니다.

Azure Digital Twins는 현재 선택한 엔드포인트로 전송될 다음 **EventTypes**을 지원합니다.

* **DeviceMessages**: 사용자의 장치에서 보내지고 시스템에 의해 전달된 원격 분석 메시지입니다.
* **TopologyOperation**: 그래프 또는 그래프의 메타데이터를 변경하는 작업입니다. 예를 들어, 공백과 같이 엔터티를 추가하거나 삭제합니다.
* **SpaceChange**:장치 원격 분석 메시지를 결과로 공간의 계산된 값이 변경됩니다.
* **SensorChange**:장치 원격 분석 메시지를 결과로 센서의 계산된 값이 변경됩니다.
* **UdfCustom**: 사용자 정의 함수에서 사용자 지정 알림입니다.

> [!IMPORTANT]
> 일부 **EndpointTypes**는 모든 **EventTypes**를 지원합니다.
> **EndpointType** 각각에 대해 허용되는 **EventTypes**은 아래 표를 참조하세요.

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| **EventHub**|     X          |         X         |     X       |      X       |   X       |
| **Service Bus**|              |         X         |     X       |      X       |   X       |
| **EventGrid**|               |         X         |     X       |      X       |   X       |

>[!NOTE]
>이벤트 스키마의 엔드포인트 및 예제를 만드는 방법에 대한 자세한 내용은 [엔드포인트 및 송신](how-to-egress-endpoints.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

공개 미리 보기 제한에 대한 자세한 내용은 [Azure Digital Twins 미리 보기 제한](concepts-service-limits.md)을 참고하세요.

Azure Digital Twins 샘플을 사용하려면 [사용 가능한 회의실을 찾는 빠른 시작](quickstart-view-occupancy-dotnet.md)을 참고하세요.

<!-- Images -->
[1]: media/concepts/digital-twins-events-routing.png