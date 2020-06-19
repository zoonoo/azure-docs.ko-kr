---
title: Azure Service Bus 메시지 개요 | Microsoft Docs
description: 이 문서에서는 완전 관리형 엔터프라이즈 통합 메시지 브로커인 Azure Service Bus에 대한 개괄적인 개요를 제공합니다.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: overview
ms.date: 11/04/2019
ms.custom: mvc
ms.author: aschhab
ms.openlocfilehash: 49a54491c36ef29209d1a53094cc5baf57057557
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84707893"
---
# <a name="what-is-azure-service-bus"></a>Azure Service Bus란?

Microsoft Azure Service Bus는 완전히 관리되는 엔터프라이즈 통합 메시지 broker입니다. Service Bus는 애플리케이션 및 서비스를 분리할 수 있습니다. Service Bus는 데이터 및 상태의 비동기 전송을 위한 안정적인 보안 플랫폼을 제공합니다.

데이터는 *메시지*를 사용하여 서로 다른 애플리케이션 및 서비스간에 전송됩니다. 메시지는 이진 형식이며, JSON, XML 또는 텍스트만 포함할 수 있습니다. 자세한 내용은 [Integration Services](https://azure.com/integration)를 참조하세요.

몇 가지 일반적인 메시징 시나리오는 다음과 같습니다.

* *메시징* 주문, 저널 판매 및 구입 또는 인벤토리 이동과 같은 비즈니스 데이터를 전송합니다.
* *애플리케이션 분리*. 애플리케이션 및 서비스의 안정성과 확장성을 개선합니다. 클라이언트와 서비스가 동시에 온라인 상태일 필요는 없습니다.
* *토픽 및 구독*. 게시자와 구독자 간의 1:*n* 관계를 활성화합니다.
* *메시지 세션*. 메시지 순서 지정 또는 메시지 지연이 필요한 워크플로를 구현합니다.

## <a name="namespaces"></a>네임스페이스

네임스페이스는 모든 메시징 구성 요소에 대한 컨테이너입니다. 여러 큐 및 토픽은 단일 네임스페이스에 있을 수 있으며 네임스페이스는 종종 애플리케이션 컨테이너로 사용됩니다.

## <a name="queues"></a>큐

메시지는 *큐* 간에서 전송 및 수신됩니다. 큐는 수신 애플리케이션이 수신 및 처리할 수 있을 때까지 메시지를 저장합니다.

![큐](./media/service-bus-messaging-overview/about-service-bus-queue.png)

큐의 메시지는 도착 시 순서가 지정되고 타임스탬프가 지정됩니다. 허용되면 메시지는 중복 스토리지에 안전하게 보관됩니다. 메시지는 요청 시에만 메시지를 전송하는 *끌어오기* 모드에서 전송됩니다.

## <a name="topics"></a>토픽

*토픽*을 사용하여 메시지를 보내고 받을 수도 있습니다. 큐는 종종 지점 간 통신에 사용되지만 토픽은 게시/구독 시나리오에서 유용합니다.

![항목](./media/service-bus-messaging-overview/about-service-bus-topic.png)

토픽은 여러 개의 독립 구독을 가질 수 있습니다. 토픽에 대한 구독자는 해당 토픽으로 전송된 각 메시지의 복사본을 수신할 수 있습니다. 구독은 명명된 엔터티입니다. 구독은 지속되지만 만료되거나 자동으로 삭제될 수 있습니다.

개별 구독에서 토픽에 전송하는 모든 메시지를 수신하지 않을 수도 있습니다. 이 경우 *규칙* 및 *필터*를 사용하여 선택적 *작업*을 트리거하는 조건을 정의할 수 있습니다. 지정된 메시지를 필터링하고 메시지 속성을 설정하거나 수정할 수 있습니다. 자세한 내용은 [토픽 필터 및 작업](topic-filters.md)을 참조하세요.

## <a name="advanced-features"></a>고급 기능

Service Bus에는 보다 복잡한 메시징 문제를 해결할 수 있는 고급 기능이 있습니다. 다음 섹션에서는 이러한 기능 일부를 설명합니다.

### <a name="message-sessions"></a>메시지 세션

Service Bus에서 FIFO(first-in, first-out) 보장을 만들려면 세션을 사용합니다. 메시지 세션을 사용하면 관련 메시지의 무제한 시퀀스를 공동으로 순서를 지정하여 처리할 수 있습니다. 자세한 내용은 [메시지 세션: FIFO(선입선출)](message-sessions.md)를 참조하세요.

### <a name="autoforwarding"></a>자동 전달

자동 전달 기능은 큐 또는 구독을 다른 큐 또는 토픽에 연결합니다. 이러한 항목은 네임스페이스의 일부여야 합니다. 자동 전달을 사용할 경우 Service Bus는 큐 또는 구독에서 메시지를 자동으로 제거하고 다른 큐 또는 토픽에 넣습니다. 자세한 내용은 [자동 전달을 사용한 Service Bus 엔터티 연결](service-bus-auto-forwarding.md)을 참조하세요.

### <a name="dead-letter-queue"></a>배달하지 못한 편지 큐

Service Bus은 DLQ(배달하지 못한 편지 큐)를 지원합니다. DLQ는 받는 사람에게 배달할 수 없는 메시지를 보관합니다. 처리할 수 없는 메시지를 보관합니다. Service Bus를 사용하여 DLQ에서 메시지를 제거하고 검사할 수 있습니다. 자세한 내용은 [Service Bus 배달 못한 편지 큐의 개요](service-bus-dead-letter-queues.md)를 참조하세요.

### <a name="scheduled-delivery"></a>예약 배달

지연된 처리를 위해 큐 또는 토픽에 메시지를 제출할 수 있습니다. 특정 시간에 시스템에서 처리될 수 있도록 작업을 예약할 수 있습니다. 자세한 내용은 [예약된 메시지](message-sequencing.md#scheduled-messages)를 참조하세요.

### <a name="message-deferral"></a>메시지 지연

큐 또는 구독 클라이언트는 나중으로 메시지 검색을 연기할 수 있습니다. 이러한 지연은 애플리케이션의 특수한 상황 때문에 발생할 수 있습니다. 메시지는 큐나 구독에 남아 있지만 따로 분리됩니다. 자세한 내용은 [메시지 지연](message-deferral.md)을 참조하세요.

### <a name="batching"></a>일괄 처리

클라이언트 쪽 일괄 처리를 통해 큐 또는 토픽 클라이언트가 일정 시간 동안 메시지 전송을 연기할 수 있습니다. 클라이언트가 이 기간 동안 추가 메시지를 보내면 메시지를 단일 배치로 전송합니다. 자세한 내용은 [클라이언트 쪽 일괄 처리](service-bus-performance-improvements.md#client-side-batching)를 참조하세요.

### <a name="transactions"></a>트랜잭션

트랜잭션 그룹은 두 개 이상의 작업을 *실행 범위*로 그룹화합니다. Service Bus는 단일 트랜잭션 범위 내에서 단일 메시징 엔터티에 대한 그룹화 작업을 지원합니다. 메시지 엔터티는 큐, 토픽 또는 구독일 수 있습니다. 자세한 내용은 [Service Bus 트랜잭션 처리의 개요](service-bus-transactions.md)를 참조하세요.

### <a name="filtering-and-actions"></a>필터링 및 작업

구독자는 토픽에서 수신할 메시지를 정의할 수 있습니다. 이러한 메시지는 하나 이상의 명명된 구독 규칙의 형태로 지정됩니다. 일치하는 각 규칙 조건에 대해 구독은 메시지의 복사본을 생성하며 일치하는 규칙마다 다르게 주석을 달 수 있습니다. 자세한 내용은 [토픽 필터 및 작업](topic-filters.md)을 참조하세요.

### <a name="autodelete-on-idle"></a>유휴 상태에서 자동 삭제

유휴 상태에서 자동 삭제를 통해 큐가 자동으로 삭제된 후 유휴 간격을 지정할 수 있습니다. 최소 기간은 5분입니다. 자세한 내용은 [QueueDescription.AutoDeleteOnIdle 속성](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle)을 참조하세요.

### <a name="duplicate-detection"></a>중복 검색

오류로 인해 클라이언트에서 전송 작업의 결과를 의심할 수 있습니다. 중복 검색을 사용하면 보낸 사람이 동일한 메시지를 다시 보낼 수 있습니다. 또 다른 옵션은 큐 또는 토픽에서 중복된 사본을 삭제하도록 하는 것입니다. 자세한 내용은 [중복 검색](duplicate-detection.md)을 참조하세요.

### <a name="security-protocols"></a>보안 프로토콜
<a name="sas-rbac-and-managed-identities-for-azure-resources"></a>

Service Bus는 [SAS(공유 액세스 서명)](service-bus-sas.md), [RBAC(역할 기반 액세스 제어)](authenticate-application.md) 및 [Azure 리소스에 대한 관리 ID](service-bus-managed-service-identity.md) 같은 보안 프로토콜을 지원합니다.

### <a name="geo-disaster-recovery"></a>지리적 재해 복구

Azure 지역 또는 데이터 센터에서 가동 중단이 발생하는 경우 지리적 재해 복구를 통해 데이터 처리는 다른 지역 또는 데이터 센터에서 작업을 계속할 수 있습니다. 자세한 내용은 [Azure Service Bus 지역 재해 복구](service-bus-geo-dr.md)를 참조하세요.

### <a name="security"></a>보안

Service Bus는 표준 [AMQP 1.0](service-bus-amqp-overview.md) 및 [HTTP/REST](/rest/api/servicebus/) 프로토콜을 지원합니다.

## <a name="client-libraries"></a>클라이언트 라이브러리

Service Bus는 [.NET](https://github.com/Azure/azure-service-bus-dotnet/tree/master), [Java](https://github.com/Azure/azure-service-bus-java/tree/master) 및 [JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client)에 대한 클라이언트 라이브러리를 지원합니다.

## <a name="integration"></a>통합

Service Bus는 다음 Azure 서비스와 완벽하게 통합됩니다.

* [Event Grid](https://azure.microsoft.com/services/event-grid/)
* [Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Dynamics 365](https://dynamics.microsoft.com)
* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)

## <a name="next-steps"></a>다음 단계

Service Bus 메시징 사용을 시작하려면 다음 문서를 참조하세요.

* Azure 메시징 서비스를 비교하려면 [서비스의 비교](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json)를 참조하세요.
* [.NET](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md) 또는 [JMS](service-bus-java-how-to-use-jms-api-amqp.md)에서 빠른 시작 시도
* Service Bus 리소스를 관리하려면 [Service Bus 탐색기](https://github.com/paolosalvatori/ServiceBusExplorer/releases)를 참조하세요.
* 표준 및 프리미엄 계층 및 해당 가격 책정에 대한 자세한 내용은 [Service Bus 가격 책정](https://azure.microsoft.com/pricing/details/service-bus/)을 참조하세요.
* 프리미엄 계층의 성능 및 대기 시간에 대한 자세한 내용은 [프리미엄 메시징](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722)을 참조하세요.
