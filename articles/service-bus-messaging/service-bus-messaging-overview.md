---
title: Azure Service Bus 메시지 개요 | Microsoft Docs
description: 이 문서에서는 완전 관리형 엔터프라이즈 통합 메시지 브로커인 Azure Service Bus에 대한 간략한 개요를 제공합니다.
ms.topic: overview
ms.date: 11/20/2020
ms.openlocfilehash: 7453e8dd300ad754fb58489f059670af209314ab
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881604"
---
# <a name="what-is-azure-service-bus"></a>Azure Service Bus란?
Microsoft Azure Service Bus는 메시지 큐와 게시-구독 토픽이 있는 완전 관리형 엔터프라이즈 메시지 broker입니다. Service Bus는 애플리케이션과 서비스를 서로 분리하는 데 사용되며, 다음과 같은 이점을 제공합니다.

- 경쟁하는 작업자 간에 작업 부하 분산
- 서비스 및 애플리케이션 경계에서 데이터 및 제어를 안전하게 라우팅하고 전송
- 높은 수준의 안정성이 필요한 트랜잭션 작업 조정 

## <a name="overview"></a>개요
데이터는 *메시지* 를 사용하여 서로 다른 애플리케이션 및 서비스간에 전송됩니다. 메시지는 메타데이터로 데코레이팅된 컨테이너이며 데이터를 포함합니다. 다음과 같은 일반적인 형식으로 인코딩된 정형 데이터를 포함하여 모든 종류의 정보는 데이터가 될 수 있습니다: JSON, XML, Apache Avro, 일반 텍스트 등.

몇 가지 일반적인 메시징 시나리오는 다음과 같습니다.

* *메시징* 주문, 저널 판매 및 구입 또는 인벤토리 이동과 같은 비즈니스 데이터를 전송합니다.
* *애플리케이션 분리*. 애플리케이션 및 서비스의 안정성과 확장성을 개선합니다. 생산자와 소비자가 반드시 온라인 상태이거나 동시에 자리에 있지 않아도 됩니다. [부하가 평준화](/azure/architecture/patterns/queue-based-load-leveling)되므로 트래픽 급증으로 인해 서비스 요금이 과도하게 부과되는 일이 없습니다. 
* *부하 분산*. 여러 [경쟁하는 소비자](/azure/architecture/patterns/competing-consumers)가 한 큐에서 동시에 읽을 수 있으며, 각 소비자는 특정 메시지에 대한 배타적 소유권을 안전하게 획득합니다. 
* *토픽 및 구독*. 사용 1: [게시자와 구독자](/azure/architecture/patterns/publisher-subscriber) 간에 *n* 관계가 있으므로, 구독자는 게시된 메시지 스트림에서 특정 메시지를 선택할 수 있습니다.
* *트랜잭션*. 원자성 트랜잭션의 범위에서 여러 작업을 수행할 수 있습니다. 예를 들어 트랜잭션 범위에서 다음 작업을 수행할 수 있습니다.  

    1. 한 큐에서 메시지를 가져옵니다.
    2. 처리 결과를 하나 이상의 서로 다른 큐에 게시합니다.
    3. 원래 큐의 입력 메시지를 이동합니다. 
    
    입력 메시지가 성공적으로 확인되는 경우를 포함하여 성공할 때만 다운스트림 소비자에게 결과가 표시되므로, 일회성 처리 의미 체계를 허용합니다. 이 트랜잭션 모델은 더 큰 솔루션 컨텍스트에서 [보정 트랜잭션](/azure/architecture/patterns/compensating-transaction) 패턴을 구현하기 위한 강력한 기반입니다. 
* *메시지 세션*. 엄격한 메시지 정렬 또는 메시지 지연이 필요한 워크플로 및 멀티플렉싱 전송의 대규모 조정을 구현합니다.

Apache ActiveMQ와 같은 다른 메시지 broker에 익숙한 분들에게는 Service Bus의 개념이 알고 있는 지식과 비슷할 것입니다. Service Bus는 PaaS(platform-as-a-service) 제품이므로 다음 작업에 대해 신경 쓸 필요가 없다는 점이 결정적인 차이점입니다. Azure가 이러한 작업을 알아서 처리합니다. 

- 로그 배치 및 디스크 공간 관리
- 백업 처리
- 운영 체제 또는 제품 패치 유지
- 하드웨어 오류 해결 
- 예약 머신으로 장애 조치(failover)

## <a name="compliance-with-standards-and-protocols"></a>표준 및 프로토콜 규정 준수

Service Bus의 기본 유선 프로토콜은 오픈 ISO/IEC 표준인 [AMQP(Advanced Messaging Queueing Protocol) 1.0](service-bus-amqp-overview.md)입니다. 따라서 고객은 Service Bus와 ActiveMQ 또는 RabbitMQ 같은 온-프레미스 broker에 대해 작동하는 애플리케이션을 작성할 수 있습니다. [AMQP 프로토콜 가이드](service-bus-amqp-protocol-guide.md)는 이러한 추상화를 구현하려 할 때 도움이 되는 자세한 정보를 제공합니다.

[Service Bus Premium](service-bus-premium-messaging.md)은 Java/Jakarta EE [JMS(Java Message Service) 2.0](how-to-use-java-message-service-20.md) API와 완벽하게 호환됩니다. 그리고 Service Bus Standard는 큐에 중점을 둔 JMS 1.1 하위 세트를 지원합니다. JMS는 메시지 broker에 사용되는 일반적인 추상화이며 인기 있는 Spring 프레임워크를 포함하여 여러 애플리케이션 및 프레임워크와 통합됩니다. 다른 broker에서 Azure Service Bus로 전환하려면 큐 및 토픽의 토폴로지를 다시 만들고 클라이언트 공급자 종속성 및 구성을 변경하기만 하면 됩니다. 예제는 [ActiveMQ 마이그레이션 가이드](migrate-jms-activemq-to-servicebus.md)를 참조하세요.

## <a name="concepts-and-terminology"></a>개념 및 용어 
이 섹션에서는 Service Bus의 개념과 용어를 설명합니다.

### <a name="namespaces"></a>네임스페이스
네임스페이스는 모든 메시징 구성 요소에 대한 컨테이너입니다. 여러 큐 및 토픽은 단일 네임스페이스에 있을 수 있으며 네임스페이스는 종종 애플리케이션 컨테이너로 사용됩니다. 

네임스페이스는 다른 broker의 용어로 "서버"와 비교할 수 있지만, 그 개념은 직접적으로 일치하지 않습니다. Service Bus 네임스페이스는 수십 개의 전체 활성 가상 머신으로 구성된 대용량 클러스터의 고유한 용량 조각입니다. 필요에 따라 세 개의 [Azure 가용성 영역](../availability-zones/az-overview.md)으로 확장할 수 있습니다. 따라서 메시지 broker를 대규모로 실행할 때 얻게 되는 가용성 및 견고함이라는 이점을 모두 누릴 수 있습니다. 그리고 기본 복잡성에 대해 걱정할 필요가 없습니다. Service Bus는 "서버리스" 메시지입니다.

### <a name="queues"></a>큐
메시지는 *큐* 간에서 전송 및 수신됩니다. 큐는 수신 애플리케이션이 수신 및 처리할 수 있을 때까지 메시지를 저장합니다.

![큐](./media/service-bus-messaging-overview/about-service-bus-queue.png)

큐의 메시지는 도착 시 순서가 지정되고 타임스탬프가 지정됩니다. broker의 승인을 받은 메시지는 항상 삼중 중복 스토리지에 안전하게 보관되며, 네임스페이스가 영역을 사용하도록 설정되면 가용성 영역으로 확장됩니다. Service Bus는 클라이언트에 수락된 것으로 보고된 메시지를 절대로 메모리 또는 휘발성 스토리지에 남겨 두지 않습니다.

메시지는 요청 시에만 메시지를 전송하는 *끌어오기* 모드에서 전송됩니다. 다른 클라우드 큐의 사용 중-폴링 모델과 달리, 끌어오기 작업은 오래 걸릴 수 있으며 메시지를 사용할 수 있게 되면 완료됩니다. 

### <a name="topics"></a>토픽

*토픽* 을 사용하여 메시지를 보내고 받을 수도 있습니다. 큐는 종종 지점 간 통신에 사용되지만 토픽은 게시/구독 시나리오에서 유용합니다.

![항목](./media/service-bus-messaging-overview/about-service-bus-topic.png)

토픽에는 여러 개의 독립 구독이 있을 수 있습니다. 이러한 독립 구독은 토픽에 연결되며, 그렇지 않으면 받는 사람 쪽의 큐와 정확히 같은 방식으로 작동합니다. 토픽에 대한 구독자는 해당 토픽으로 전송된 각 메시지의 복사본을 수신할 수 있습니다. 구독은 명명된 엔터티입니다. 구독은 기본적으로 지속되지만, 만료되면 자동으로 삭제되도록 구성할 수 있습니다. JMS API를 통해 Service Bus Premium을 사용하면 연결 기간 동안 존재하는 휘발성 구독을 만들 수 있습니다.

구독에 대한 규칙을 정의할 수 있습니다. 구독 규칙에는 구독으로 복사되는 메시지의 조건을 정의하는 *필터* 와 메시지 메타데이터를 수정할 수 있는 선택적 *작업* 이 포함되어 있습니다. 자세한 내용은 [토픽 필터 및 작업](topic-filters.md)을 참조하세요. 이 기능은 다음과 같은 시나리오에서 유용합니다.

- 토픽에 전송되는 메시지 중 일부는 구독에서 수신하지 않으려 합니다.
- 메시지가 구독을 통과할 때 추가 메타데이터로 메시지를 표시하려 합니다.

## <a name="advanced-features"></a>고급 기능

Service Bus에는 보다 복잡한 메시징 문제를 해결할 수 있는 고급 기능이 있습니다. 다음 섹션에서는 이러한 기능 일부를 설명합니다.

### <a name="message-sessions"></a>메시지 세션

Service Bus에서 FIFO(first-in, first-out) 보장을 만들려면 세션을 사용합니다. 메시지 세션을 사용하면 관련 메시지의 무제한 시퀀스를 독점적으로, 순서를 지정하여 처리할 수 있습니다. 또한 대규모 고가용성 시스템에서 세션을 처리할 수 있도록 세션 기능은 세션 상태를 저장할 수 있으며, 따라서 처리기 간에 세션을 안전하게 이동할 수 있습니다. 자세한 내용은 [메시지 세션: FIFO(선입선출)](message-sessions.md)를 참조하세요.

### <a name="autoforwarding"></a>자동 전달

자동 전달 기능은 큐 또는 구독을 같은 네임스페이스 내부의 다른 큐 또는 토픽에 연결합니다. 이 기능을 사용하면 Service Bus는 자동으로 큐 또는 구독의 메시지를 대상 큐 또는 토픽으로 이동합니다. 이 모든 이동은 트랜잭션 방식으로 수행됩니다. 자세한 내용은 [자동 전달을 사용한 Service Bus 엔터티 연결](service-bus-auto-forwarding.md)을 참조하세요.

### <a name="dead-letter-queue"></a>배달하지 못한 편지 큐

모든 Service Bus 큐 및 토픽 구독에는 DLQ(배달하지 못한 편지 큐)가 연결됩니다. DLQ는 다음 기준을 충족하는 메시지를 보관합니다. 

- 받는 사람에게 성공적으로 배달할 수 없습니다.
- 시간이 초과되었습니다.
- 수신 애플리케이션에서 명시적으로 열외로 취급됩니다. 

배달하지 못한 편지 큐의 메시지는 이 큐에 배치된 이유가 주석으로 추가됩니다. 배달하지 못한 편지 큐에는 특수 엔드포인트가 있습니다. 그렇지 않으면 일반 큐처럼 작동합니다. 애플리케이션 또는 도구는 DLQ를 찾아보거나 DLQ에서 큐를 제거할 수 있습니다. 배달하지 못한 편지 큐에서 자동으로 전달할 수도 있습니다. 자세한 내용은 [Service Bus 배달 못한 편지 큐의 개요](service-bus-dead-letter-queues.md)를 참조하세요.

### <a name="scheduled-delivery"></a>예약 배달

지연된 처리의 큐 또는 토픽에 메시지를 제출하고, 메시지를 사용할 수 있게 되는 시간을 설정할 수 있습니다. 예약된 메시지를 취소할 수도 있습니다. 자세한 내용은 [예약된 메시지](message-sequencing.md#scheduled-messages)를 참조하세요.

### <a name="message-deferral"></a>메시지 지연

큐 또는 구독 클라이언트는 받은 메시지 검색을 나중으로 연기할 수 있습니다. 메시지가 예상 순서대로 게시되지 않았기 때문에 클라이언트가 다른 메시지를 받을 때까지 대기하려는 것일 수 있습니다. 지연된 메시지는 큐 또는 구독에 남아 있으며 서비스에서 할당한 시퀀스 번호를 사용하여 명시적으로 다시 활성화해야 합니다. 자세한 내용은 [메시지 지연](message-deferral.md)을 참조하세요.

### <a name="batching"></a>일괄 처리

클라이언트 쪽 일괄 처리를 사용하면 큐 또는 토픽 클라이언트가 메시지 세트를 누적하고 함께 전송할 수 있습니다. 이 방법은 대역폭을 절약하거나 처리량을 늘리기 위해 자주 사용됩니다. 자세한 내용은 [클라이언트 쪽 일괄 처리](service-bus-performance-improvements.md#client-side-batching)를 참조하세요.

### <a name="transactions"></a>트랜잭션

트랜잭션 그룹은 두 개 이상의 작업을 *실행 범위* 로 그룹화합니다. Service Bus는 단일 트랜잭션 범위 내에서 여러 메시지 엔터티에 대한 작업을 그룹화할 수 있습니다. 메시지 엔터티는 큐, 토픽 또는 구독일 수 있습니다. 자세한 내용은 [Service Bus 트랜잭션 처리의 개요](service-bus-transactions.md)를 참조하세요.

### <a name="autodelete-on-idle"></a>유휴 상태에서 자동 삭제

유휴 상태에서 자동 삭제를 사용하여 유휴 간격을 지정할 수 있습니다. 그러면 이 시간이 경과한 후 큐 또는 토픽 구독이 자동으로 삭제됩니다. 최소 기간은 5분입니다. 자세한 내용은 [QueueDescription.AutoDeleteOnIdle 속성](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle)을 참조하세요.

### <a name="duplicate-detection"></a>중복 검색
중복 검색 기능을 사용하면 보낸 사람은 동일한 메시지를 다시 전송할 수 있고 broker는 잠재적 중복을 삭제할 수 있습니다. 중복 검색의 기반은 메시지의 `message-id` 속성 추적입니다. 즉, 애플리케이션에서 메시지를 다시 보낼 때 동일한 값을 사용하도록 주의해야 하며, 애플리케이션 관련 컨텍스트에서 직접 파생될 수 있습니다. 자세한 내용은 [중복 검색](duplicate-detection.md)을 참조하세요.

### <a name="geo-disaster-recovery"></a>지리적 재해 복구

Azure 지역에서 가동 중단이 발생하는 경우 재해 복구 기능은 다른 지역 또는 데이터 센터에서 데이터 처리가 계속 진행되도록 합니다. 이 기능은 보조 지역에 사용 가능한 네임스페이스의 구조적 미러를 유지하며 네임스페이스 ID를 보조 네임스페이스로 전환하는 것을 허용합니다. 이미 게시된 메시지는 가용성 문제가 진정되면 복구할 수 있도록 이전 기본 네임스페이스에 유지됩니다. 자세한 내용은 [Azure Service Bus 지역 재해 복구](service-bus-geo-dr.md)를 참조하세요.

### <a name="security"></a>보안

Service Bus는 표준 [AMQP 1.0](service-bus-amqp-overview.md) 및 [HTTP 또는 REST](/rest/api/servicebus/) 프로토콜과 TLS(전송 수준 보안)를 비롯한 해당 보안 기능을 지원합니다. 클라이언트는 [공유 액세스 서명](service-bus-sas.md) 또는 [Azure Active Directory](service-bus-authentication-and-authorization.md) 역할 기반 보안을 사용하여 액세스 권한을 부여할 수 있습니다. 

원치 않는 트래픽으로부터 보호할 수 있도록 Service Bus는 IP 방화벽이나 가상 네트워크와의 통합 같은 [보안 기능](network-security.md)을 제공합니다. 

## <a name="client-libraries"></a>클라이언트 라이브러리

완전히 지원되는 Service Bus 클라이언트 라이브러리는 Azure SDK를 통해 사용할 수 있습니다.

- [.NET용 Azure Service Bus](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [Java용 Azure Service Bus 라이브러리](/java/api/overview/azure/servicebus?preserve-view=true)
- [Java JMS 2.0용 Azure Service Bus 공급자](how-to-use-java-message-service-20.md)
- [JavaScript 및 TypeScript용 Azure Service Bus 모듈](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [Python용 Azure Service Bus 라이브러리](/python/api/overview/azure/servicebus?preserve-view=true)

[Azure Service Bus의 기본 프로토콜은 AMQP 1.0](service-bus-amqp-overview.md)이며 모든 AMQP 1.0 규격 프로토콜 클라이언트에서 사용할 수 있습니다. 여러 오픈 소스 AMQP 클라이언트에서 Service Bus 상호 운용성을 명시적으로 보여주는 샘플을 제공합니다. [AMQP 1.0 프로토콜 가이드](service-bus-amqp-protocol-guide.md)를 검토하여 AMQP 1.0 클라이언트에서 직접 Service Bus 기능을 사용하는 방법을 알아보세요.

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="integration"></a>통합

Service Bus는 다음과 같은 여러 Microsoft 및 Azure 서비스와 완벽하게 통합됩니다.

* [Event Grid](https://azure.microsoft.com/services/event-grid/)
* [Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Power Platform](https://powerplatform.microsoft.com/)
* [Dynamics 365](https://dynamics.microsoft.com)
* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)

## <a name="next-steps"></a>다음 단계

Service Bus 메시징 사용을 시작하려면 다음 문서를 참조하세요.

* Azure 메시징 서비스를 비교하려면 [서비스의 비교](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json)를 참조하세요.
* [.NET](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md) 또는 [JMS](service-bus-java-how-to-use-jms-api-amqp.md)에서 빠른 시작 시도
* Service Bus 리소스를 관리하려면 [Service Bus 탐색기](https://github.com/paolosalvatori/ServiceBusExplorer/releases)를 참조하세요.
* 표준 및 프리미엄 계층 및 해당 가격 책정에 대한 자세한 내용은 [Service Bus 가격 책정](https://azure.microsoft.com/pricing/details/service-bus/)을 참조하세요.
* 프리미엄 계층의 성능 및 대기 시간에 대한 자세한 내용은 [프리미엄 메시징](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722)을 참조하세요.