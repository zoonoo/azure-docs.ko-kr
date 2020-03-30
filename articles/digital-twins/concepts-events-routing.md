---
title: 라우팅 이벤트 및 메시지 - Azure 디지털 트윈 | 마이크로 소프트 문서
description: Azure Digital Twins를 사용하여 서비스 엔드포인트에 이벤트 및 메시지를 라우팅하는 개요
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 65b760eaf28d907fab3654ed92f960be7556b0d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862359"
---
# <a name="routing-iot-events-and-messages"></a>IoT 이벤트 및 메시지 라우팅

사물 인터넷 솔루션은 종종 스토리지, 분석 등을 포함하는 몇 가지 강력한 서비스를 통합합니다. 이 문서에서는 Azure Digital Twins 앱을 Azure 분석, AI 및 스토리지 서비스에 연결하여 심층 인사이트 및 기능을 제공하는 방법을 설명합니다.

## <a name="route-types"></a>경로 형식  

Azure Digital Twins는 IoT 이벤트를 다른 Azure 서비스 또는 비즈니스 응용 프로그램과 연결하는 두 가지 방법을 제공합니다.

* **Azure Digital Twins 이벤트 라우팅**: 변경된 공간 그래프의 개체, 수신된 원격 분석 데이터, 미리 정의된 조건에 따라 알림을 만드는 사용자 정의 함수는 Azure Digital Twins 이벤트를 트리거할 수 있습니다. 사용자는 이러한 이벤트를 추가로 처리하기 위해 [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [Azure Service Bus 토픽](https://azure.microsoft.com/services/service-bus/) 또는 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)로 보낼 수 있습니다.

* **디바이스 원격 분석 라우팅**: 라우팅 이벤트 외에도 Azure Digital Twins는 추가 인사이트 및 분석을 위해 원시 디바이스 원격 분석 메시지를 Event Hubs로 라우팅할 수도 있습니다. 이러한 유형의 메시지는 Azure Digital Twins에서 처리되지 않습니다. 이벤트 허브로 전달되기만 합니다.

사용자는 이벤트를 보내거나 메시지를 전달하도록 하나 이상의 송신 엔드포인트를 지정할 수 있습니다. 이벤트 및 메시지는 이러한 미리 정의된 라우팅 기본 설정에 따라 엔드포이트로 전송됩니다. 즉, 사용자는 그래프 작업 이벤트를 수신하는 하나의 특정 엔드포인트, 디바이스 원격 분석 이벤트를 수신하는 또 하나의 특정 엔드포인트 등을 지정할 수 있습니다.

[![Azure Digital Twins 이벤트 라우팅](media/concepts/digital-twins-events-routing.png)](media/concepts/digital-twins-events-routing.png#lightbox)

Event Hubs로의 라우팅에서는 원격 분석 메시지가 전송되는 순서를 유지 관리합니다. 따라서 원래 수신된 순서와 동일하게 엔드포인트에 도달합니다. 

Event Grid 및 Service Bus는 발생했던 것과 동일한 순서로 엔드포인트가 이벤트를 받도록 보장하지 않습니다. 그러나 이벤트 스키마는 이벤트가 엔드포인트에 도착한 후에 순서를 식별하는 데 사용할 수 있는 타임스탬프를 포함합니다.

## <a name="route-implementation"></a>경로 구현

Azure Digital Twins 서비스는 현재 다음 **EndpointTypes**를 지원합니다.

* **EventHub**: Event Hub 연결 문자열 엔드포인트입니다.
* **ServiceBus**: Service Bus 연결 문자열 엔드포인트입니다.
* **EventGrid는** 이벤트 그리드 연결 문자열 끝점입니다.

Azure Digital Twins는 현재 선택한 엔드포인트로 전송될 다음 **EventTypes**을 지원합니다.

* **DeviceMessages는** 사용자의 장치에서 전송되고 시스템에서 전달되는 원격 분석 메시지입니다.
* **TopologyOperation**: 그래프 또는 그래프의 메타데이터를 변경하는 작업입니다. 예를 들어, 공백과 같이 엔터티를 추가하거나 삭제합니다.
* **SpaceChange**: 디바이스 원격 분석 메시지에서 발생하는 공간의 계산된 값 변경입니다.
* **SensorChange**: 디바이스 원격 분석 메시지에서 발생하는 센서의 계산된 값 변경입니다.
* **UdfCustom**: 사용자 정의 함수의 사용자 지정 알림입니다.

> [!IMPORTANT]  
> 일부 **EndpointTypes**는 모든 **EventTypes**를 지원합니다.
> 각 **끝점**유형에 대해 허용되는 **이벤트 유형에** 대한 다음 표를 검토합니다.

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| EventHub|     X          |         X         |     X       |      X       |   X       |
| ServiceBus|              |         X         |     X       |      X       |   X       |
| EventGrid|               |         X         |     X       |      X       |   X       |

>[!NOTE]  
>이벤트 스키마의 끝점 및 예제를 만드는 방법에 대한 자세한 내용은 [Egress 및 끝점을](how-to-egress-endpoints.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

- Azure 디지털 Twins 미리 보기 제한에 대해 자세히 알아보려면 [공개 미리 보기 서비스 제한을](concepts-service-limits.md)읽어보십시오.

- Azure 디지털 Twins 샘플을 사용해 [quickstart to find available rooms](quickstart-view-occupancy-dotnet.md)보십시오.