---
title: Azure Service Bus 메시징 - 고급 기능
description: 이 문서에서는 Azure Service Bus의 고급 기능에 대해 간략하게 설명합니다.
ms.topic: overview
ms.date: 06/11/2021
ms.openlocfilehash: 80fab956c182b6f934a767f2dffc09ca2d0c0214
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112034851"
---
# <a name="azure-service-bus---advanced-features"></a>Azure Service Bus - 고급 기능
Service Bus에는 보다 복잡한 메시징 문제를 해결할 수 있는 고급 기능이 있습니다. 이 문서에서는 이러한 기능 일부를 설명합니다.

## <a name="message-sessions"></a>메시지 세션
Service Bus에서 FIFO(first-in, first-out) 보장을 만들려면 세션을 사용합니다. 메시지 세션을 사용하면 관련 메시지의 무제한 시퀀스를 독점적으로, 순서를 지정하여 처리할 수 있습니다. 또한 대규모 고가용성 시스템에서 세션을 처리할 수 있도록 세션 기능은 세션 상태를 저장할 수 있으며, 따라서 처리기 간에 세션을 안전하게 이동할 수 있습니다. 자세한 내용은 [메시지 세션: FIFO(선입선출)](message-sessions.md)를 참조하세요.

## <a name="autoforwarding"></a>자동 전달
자동 전달 기능은 큐 또는 구독을 같은 네임스페이스 내부의 다른 큐 또는 토픽에 연결합니다. 이 기능을 사용하면 Service Bus는 자동으로 큐 또는 구독의 메시지를 대상 큐 또는 토픽으로 이동합니다. 이 모든 이동은 트랜잭션 방식으로 수행됩니다. 자세한 내용은 [자동 전달을 사용한 Service Bus 엔터티 연결](service-bus-auto-forwarding.md)을 참조하세요.

## <a name="dead-letter-queue"></a>배달하지 못한 편지 큐
모든 Service Bus 큐 및 토픽의 구독에는 연결된 DLQ(배달하지 못한 편지 큐)가 있습니다. DLQ는 다음 기준을 충족하는 메시지를 보관합니다. 

- 받는 사람에게 성공적으로 배달할 수 없습니다.
- 시간이 초과되었습니다.
- 수신 애플리케이션에서 명시적으로 열외로 취급됩니다. 

배달하지 못한 편지 큐의 메시지는 이 큐에 배치된 이유가 주석으로 추가됩니다. 배달하지 못한 편지 큐에는 특수 엔드포인트가 있습니다. 그렇지 않으면 일반 큐처럼 작동합니다. 애플리케이션 또는 도구는 DLQ를 찾아보거나 DLQ에서 큐를 제거할 수 있습니다. 배달하지 못한 편지 큐에서 자동으로 전달할 수도 있습니다. 자세한 내용은 [Service Bus 배달 못한 편지 큐의 개요](service-bus-dead-letter-queues.md)를 참조하세요.

## <a name="scheduled-delivery"></a>예약 배달
지연된 처리를 위해 큐 또는 토픽에 메시지를 제출하고, 메시지를 사용할 수 있게 되는 시간을 설정할 수 있습니다. 예약된 메시지를 취소할 수도 있습니다. 자세한 내용은 [예약된 메시지](message-sequencing.md#scheduled-messages)를 참조하세요.

## <a name="message-deferral"></a>메시지 지연
큐 또는 구독 클라이언트는 받은 메시지 검색을 나중으로 연기할 수 있습니다. 메시지가 예상 순서대로 게시되지 않았기 때문에 클라이언트가 다른 메시지를 받을 때까지 대기하려는 것일 수 있습니다. 지연된 메시지는 큐 또는 구독에 남아 있으며 서비스에서 할당한 시퀀스 번호를 사용하여 명시적으로 다시 활성화해야 합니다. 자세한 내용은 [메시지 지연](message-deferral.md)을 참조하세요.

## <a name="batching"></a>일괄 처리
클라이언트 쪽 일괄 처리를 사용하면 큐 또는 토픽 클라이언트가 메시지 세트를 누적하고 함께 전송할 수 있습니다. 이 방법은 대역폭을 절약하거나 처리량을 늘리기 위해 자주 사용됩니다. 자세한 내용은 [클라이언트 쪽 일괄 처리](service-bus-performance-improvements.md#client-side-batching)를 참조하세요.

## <a name="transactions"></a>트랜잭션
트랜잭션 그룹은 두 개 이상의 작업을 실행 범위로 그룹화합니다. Service Bus는 단일 트랜잭션 범위 내에서 여러 메시지 엔터티에 대한 작업을 그룹화할 수 있습니다. 메시지 엔터티는 큐, 토픽 또는 구독일 수 있습니다. 자세한 내용은 [Service Bus 트랜잭션 처리의 개요](service-bus-transactions.md)를 참조하세요.

## <a name="autodelete-on-idle"></a>유휴 상태에서 자동 삭제
유휴 상태에서 자동 삭제를 사용하여 유휴 간격을 지정할 수 있습니다. 그러면 이 시간이 경과한 후 큐 또는 토픽 구독이 자동으로 삭제됩니다. 최소 기간은 5분입니다. 

## <a name="duplicate-detection"></a>중복 검색
중복 검색 기능을 사용하면 보낸 사람은 동일한 메시지를 다시 전송할 수 있고 broker는 잠재적 중복을 삭제할 수 있습니다. 자세한 내용은 [중복 검색](duplicate-detection.md)을 참조하세요.

## <a name="geo-disaster-recovery"></a>지리적 재해 복구
Azure 지역에서 가동 중지 시간이 발생하는 경우 재해 복구 기능은 다른 지역 또는 데이터 센터에서 메시지 처리가 계속 진행되도록 합니다. 이 기능은 보조 지역에 사용 가능한 네임스페이스의 구조적 미러를 유지하며 네임스페이스 ID를 보조 네임스페이스로 전환하는 것을 허용합니다. 이미 게시된 메시지는 가용성 문제가 진정되면 복구할 수 있도록 이전 기본 네임스페이스에 유지됩니다. 자세한 내용은 [Azure Service Bus 지역 재해 복구](service-bus-geo-dr.md)를 참조하세요.

## <a name="security"></a>보안
Service Bus는 표준 [AMQP 1.0](service-bus-amqp-overview.md) 및 [HTTP 또는 REST](/rest/api/servicebus/) 프로토콜과 TLS(전송 수준 보안)를 비롯한 해당 보안 기능을 지원합니다. 클라이언트는 [공유 액세스 서명](service-bus-sas.md) 또는 [Azure Active Directory](service-bus-authentication-and-authorization.md) 역할 기반 보안을 사용하여 액세스 권한을 부여할 수 있습니다. 

원치 않는 트래픽으로부터 보호할 수 있도록 Service Bus는 IP 방화벽이나 가상 네트워크와의 통합 같은 [보안 기능](network-security.md)을 제공합니다. 

## <a name="next-steps"></a>다음 단계
이러한 Service Bus 기능을 사용하는 방법을 보여 주는 [Service Bus 메시징 샘플](service-bus-samples.md)을 참조하세요.
