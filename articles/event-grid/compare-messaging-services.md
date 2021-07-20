---
title: Azure 메시지 서비스 비교
description: 세 가지 Azure 메시징 서비스인 Azure Event Grid, Event Hubs 및 Service Bus를 설명합니다. 다양한 시나리오에 사용할 서비스를 권장합니다.
ms.topic: overview
ms.date: 07/01/2021
ms.openlocfilehash: f4980ca3c3741645af03874c5d75d9ef8da6084b
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113218712"
---
# <a name="choose-between-azure-messaging-services---event-grid-event-hubs-and-service-bus"></a>세 가지 Azure 메시징 서비스인 Event Grid, Event Hubs 및 Service Bus 중에서 선택합니다.

Azure는 솔루션 전체에서 이벤트 또는 메시지를 전달하는 데 도움이 되는 세 가지 서비스를 제공합니다. 이러한 서비스는 다음과 같습니다.

- Azure Event Grid
- Azure Event Hubs
- Azure Service Bus

약간의 유사성이 있지만 각 서비스는 특정 시나리오를 위해 설계되었습니다. 이 문서는 이러한 서비스 간의 차이점을 설명하고 애플리케이션에 대해 어떤 것을 선택하는지 이해하는 데 도움을 줍니다. 대부분의 경우에서 메시지 서비스는 상호 보완적이며 함께 사용할 수 있습니다.

## <a name="event-vs-message-services"></a>이벤트와 메시지 서비스 비교
이벤트를 전달하는 서비스와 메시지를 전달하는 서비스 간에는 중요한 차이점이 있습니다.

### <a name="event"></a>이벤트
이벤트는 조건 또는 상태 변경에 대한 간단한 알림입니다. 이벤트 게시자는 이벤트 처리 방법에 대한 기대가 없습니다. 이벤트 소비자는 알림을 어떻게 처리할지 결정합니다. 이벤트는 불연속 단위 또는 시리즈의 일부일 수 있습니다.

불연속 이벤트는 상태 변경을 보고하며 실행이 가능합니다. 다음 단계를 수행하기 위해 소비자는 무언가가 발생했다는 것만 알면 됩니다. 이벤트 데이터는 발생한 상황에 대한 정보를 포함하지만 이벤트를 트리거한 데이터가 없습니다. 예를 들어, 이벤트는 소비자에게 파일이 만들어진 것을 알립니다. 파일에 대한 일반 정보를 포함할 수 있지만 파일 자체는 포함하지 않습니다. 불연속 이벤트는 크기를 조정해야 하는 서버리스 솔루션에 이상적입니다.

시리즈 이벤트는 조건을 보고하며 분석이 가능합니다. 이벤트는 시간 순서가 지정되며 상호 연관됩니다. 소비자는 발생한 결과를 분석하기 위해 순차화된 일련의 이벤트가 필요합니다.

### <a name="message"></a>메시지
메시지는 사용되거나 다른 위치에 저장되는 서비스에서 생성된 원시 데이터입니다. 메시지는 메시지 파이프라인을 트리거한 데이터를 포함합니다. 메시지 게시자는 소비자가 메시지를 처리하는 방식에 대한 기대를 합니다. 양측 사이에 계약이 존재합니다. 예를 들어 게시자는 원시 데이터가 포함된 메시지를 보내고 소비자가 이 데이터로부터 파일을 만들고 작업이 완료되면 응답을 보낼 것으로 기대합니다.


## <a name="azure-event-grid"></a>Azure Event Grid
Event Grid는 이벤트 구동, 반응성 프로그래밍을 사용할 수 있는 이벤트 백플레인입니다. 게시-구독 모델을 사용합니다. 게시자는 이벤트를 내보내지만 이벤트에가 처리되는 방식에 대한 기대치가 없습니다. 구독자는 처리할 이벤트를 결정합니다.

Event Grid는 Azure 서비스와 긴밀히 통합하고 타사 서비스와 통합할 수 있습니다. 이벤트 소비를 단순화하고 상수 폴링에 대한 필요성을 제거하여 비용을 낮춥니다. Event Grid는 효율적이고 안정적으로 Azure 및 비 Azure 리소스에서 이벤트를 라우팅합니다. 등록된 구독자 엔드포인트에 이벤트를 분산합니다. 이벤트 메시지는 서비스 및 애플리케이션의 변화에 대응하는 데 필요한 정보를 포함합니다. Event Grid는 데이터 파이프라인이 아니며, 업데이트된 실제 개체를 배달하지 않습니다.

다음과 같은 특징이 있습니다.

- 동적으로 확장 가능
- 저렴한 비용
- 서버를 사용하지 않음
- 하나 이상의 이벤트 전달

자세한 내용은 [Event Grid 개요](overview.md)를 참조하세요.

## <a name="azure-event-hubs"></a>Azure Event Hubs
Azure Event Hubs는 빅 데이터 스트리밍 플랫폼 및 이벤트 수집 서비스입니다. 초당 수백만 개의 이벤트를 수신하고 처리할 수 있습니다. 원격 분석 및 이벤트 스트림 데이터의 캡처, 보존 및 재생을 용이하게 합니다. 여러 동시 원본에서 데이터를 가져올 수 있습니다. Event Hubs를 통해 다양한 스트림 처리 인프라 및 분석 서비스에서 원격 분석 및 이벤트 데이터를 사용할 수 있습니다. 데이터 스트림 또는 번들 이벤트 일괄 처리로 사용 가능합니다. 이 서비스는 실시간 처리를 위한 신속한 데이터 검색과 저장된 원시 데이터의 반복 재생을 활성화하는 단일 솔루션을 제공합니다. 처리 및 분석을 위해 스트리밍 데이터를 파일에 캡처할 수 있습니다.

다음과 같은 특징이 있습니다.

- 짧은 대기 시간
- 초당 수백만 개의 이벤트를 수신하고 처리할 수 있습니다.
- 하나 이상의 이벤트 전달

자세한 내용은 [Event Hubs 개요](../event-hubs/event-hubs-about.md)를 참조하세요.

## <a name="azure-service-bus"></a>Azure Service Bus
Service Bus는 메시지 큐와 게시-구독 토픽이 있는 완전 관리형 엔터프라이즈 메시지 브로커입니다. 이 서비스는 트랜잭션, 순서 지정, 중복 검색 및 즉시 일관성을 필요로 하는 엔터프라이즈 애플리케이션을 대상으로 합니다. Service Bus는 클라우드 네이티브 애플리케이션을 활성화하여 비즈니스 프로세스에 대한 신뢰할 수 있는 상태 전환 관리를 제공합니다. 손실되거나 중복될 수 없는 높은 가치의 메시지를 처리할 때 Azure Service Bus를 사용합니다. 또한 이 서비스는 하이브리드 클라우드 솔루션 간 매우 안전한 통신을 용이하게 하고 클라우드 솔루션에 기존 온-프레미스 시스템을 연결할 수 있습니다.

Service Bus는 조정된 메시징 시스템입니다. 소비하는 주체에서 메시지를 받을 준비가 될 때까지 메시지를 "broker"(예: 큐)에 저장합니다. 다음과 같은 특징이 있습니다.

- 폴링을 필요로 하는 신뢰할 수 있는 비동기 메시지 전달(서비스로서의 엔터프라이즈 메시징)
- FIFO(선입 선출), 일괄 처리/세션, 트랜잭션, 배달 못 한 편지 처리, 임시 컨트롤, 라우팅 및 필터링 및 중복 검색 등의 고급 메시징 기능
- 하나 이상의 메시지 전달
- 메시지의 순차적 전달(선택 사항)

자세한 내용은 [Service Bus 개요](../service-bus-messaging/service-bus-messaging-overview.md)를 참조하세요.

## <a name="comparison-of-services"></a>서비스의 비교

| 서비스 | 용도 | Type | 사용 시기 |
| ------- | ------- | ---- | ----------- |
| Event Grid | 사후 프로그래밍 | 이벤트 배포(불연속) | 상태 변경에 대응 |
| Event Hubs | 빅 데이터 파이프라인 | 이벤트 스트리밍(시리즈) | 원격 분석 및 분산 데이터 스트리밍 |
| Service Bus | 높은 가치의 엔터프라이즈 메시지 | 메시지 | 주문 처리 및 금융 거래 |

## <a name="use-the-services-together"></a>서비스를 함께 사용
경우에 따라 고유한 역할을 수행하기 위해 서비스를 함께 사용합니다. 예를 들어, 전자 상거래 사이트는 주문을 처리하는 Service Bus, 사이트 원격 분석을 캡처하는 Event Hubs 및 발송된 항목과 같은 이벤트에 응답하는 Event Grid를 사용할 수 있습니다.

다른 경우에서는 함께 연결하여 이벤트 및 데이터 파이프라인을 형성합니다. Event Grid를 사용하여 다른 서비스에서 이벤트에 응답합니다. 데이터를 데이터 웨어하우스로 마이그레이션하기 위해 Event Hubs와 함께 Event Grid를 사용하는 예는 [데이터 웨어하우스로 빅 데이터 스트림](event-grid-event-hubs-integration.md)을 참조하세요. 다음 이미지는 데이터 스트리밍에 대한 워크플로를 보여 줍니다.

:::image type="content" source="./media/compare-messaging-services/overview.png" alt-text="Event Hubs, Service Bus 및 Event Grid를 함께 연결할 수 있는 방법을 보여주는 이미지.":::

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요. 
- [Azure의 비동기 메시징 옵션](/azure/architecture/guide/technology-choices/messaging)
- [이벤트, 데이터 요소 및 메시지 - 데이터에 적합한 Azure 메시지 서비스 선택](https://azure.microsoft.com/blog/events-data-points-and-messages-choosing-the-right-azure-messaging-service-for-your-data/)

