---
title: 포함 파일
description: 포함 파일
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 12/13/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7add8c10fd3224b9c287ea4cc672191157f56a09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60861900"
---
다음 표에 Azure Service Bus 메시징 관련 할당량 정보가 있습니다. 가격 책정에 대 한 정보 및 Service Bus에 대 한 다른 할당량 [Service Bus 가격 책정](https://azure.microsoft.com/pricing/details/service-bus/)합니다.

| 할당량 이름 | 범위 | 메모 | 값 |
| --- | --- | --- | --- |
| Azure 구독 당 기본 또는 표준 네임 스페이스 최대 수 |네임스페이스 |Azure portal에서 기본 또는 표준 네임 스페이스를 추가 하기 위한 후속 요청이 거부 됩니다. |100|
| Azure 구독 당 프리미엄 네임 스페이스의 최대 수 |네임스페이스 |추가 프리미엄 네임 스페이스에 대 한 후속 요청은 포털에서 거부 됩니다. |25 |
| 큐 또는 토픽 크기 |엔터티 |큐 또는 토픽을 만들 때 정의 됩니다. <br/><br/> 이후에 들어오는 메시지는 거부 되 고 호출 코드에서 예외가 수신 됩니다. |1, 2, 3, 4GB 또는 5GB<br /><br />프리미엄 SKU에서 사용 하 여 표준 SKU [분할](/azure/service-bus-messaging/service-bus-partitioning) 활성화 최대 큐 또는 토픽 크기는 80GB입니다. |
| 네임스페이스에 대한 동시 연결 수 |네임스페이스 |추가 연결에 대 한 후속 요청은 거부 되 고 호출 코드에서 예외가 수신 됩니다. REST 작업은 동시 TCP 연결 방향으로 계산 되지 않습니다. |NetMessaging: 1,000.<br /><br />AMQP: 5,000. |
| 큐, 토픽 또는 구독 엔터티에서 요청을 수신 하는 동시에 실행 수 |엔터티 |후속 수신 요청은 거부 되 고 호출 코드에서 예외가 수신 됩니다. 이 할당량은 항목의 모든 구독 전반에 걸쳐 종합된 동시 수신 명령 수에 적용됩니다. |5,000 |
| 네임 스페이스 당 큐 또는 항목 수 |네임스페이스 |네임스페이스에 새 토픽 또는 큐를 만들기 위한 후속 요청이 거부됩니다. 따라서 [Azure Portal][Azure portal]을 통해 구성된 경우 오류 메시지가 생성됩니다. 관리 API에서 호출되는 경우 호출 코드에서 예외가 수신됩니다. |기본 또는 표준 계층의 1,000입니다. 토픽 및 네임 스페이스에 큐의 총 1,000 작아야 합니다. <br/><br/>프리미엄 계층의 경우 메시징 단위 (MU) 당 1,000 개. 최대 제한은 4,000입니다. |
| 수가 [분할 된 큐 또는 항목](/azure/service-bus-messaging/service-bus-partitioning) 네임 스페이스 당 |네임스페이스 |네임스페이스에 분할된 새 토픽 또는 큐를 만들기 위한 후속 요청이 거부됩니다. 따라서 [Azure Portal][Azure portal]을 통해 구성된 경우 오류 메시지가 생성됩니다. 관리 API, 예외에서에서 호출 되는 경우 **QuotaExceededException** 호출 코드에서 수신 됩니다. |기본 및 표준 계층: 100<br/><br/>지원 되지 않습니다 분할 된 엔터티를 [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) 계층입니다.<br/><br />각 분할 된 큐 또는 항목의 네임 스페이스 당 1,000 개의 엔터티를 할당량으로 계산합니다. |
| 모든 메시징 엔터티 경로의 최대 크기: 큐 또는 토픽 |엔터티 |- |260 자입니다. |
| 모든 메시징 엔터티 이름의 최대 크기: 네임스페이스, 구독 또는 구독 규칙 |엔터티 |- |50 자입니다. |
| [메시지 ID](/dotnet/api/microsoft.azure.servicebus.message.messageid)의 최대 크기 | 엔터티 |- | 128 |
| 메시지의 최대 크기 [세션 ID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | 엔터티 |- | 128 |
| 큐, 토픽 또는 구독 엔터티의 메시지 크기 |엔터티 |이러한 할당량을 초과 하는 들어오는 메시지는 거부 되 고 호출 코드에서 예외가 수신 됩니다. |최대 메시지 크기: 경우 256KB [표준 계층](../articles/service-bus-messaging/service-bus-premium-messaging.md)에 대 한 1MB [프리미엄 계층](../articles/service-bus-messaging/service-bus-premium-messaging.md)합니다. <br /><br />시스템 오버헤드로 인해, 이 제한이 이러한 값보다 작습니다.<br /><br />최대 헤더 크기: 64KB입니다.<br /><br />Propertybag에 헤더 속성의 최대 수: **바이트/없는 정수 MaxValue**합니다.<br /><br />속성 모음의 최대 속성 크기: 명시적 제한은 없습니다. 최대 헤더 크기로 제한됩니다. |
| 큐, 토픽 또는 구독 엔터티의 메시지 속성 크기 |엔터티 | 예외 **SerializationException** 생성 됩니다. |각 속성에 대 한 최대 메시지 속성 크기는 32,000 합니다. 모든 속성의 누적 크기는 64,000를 초과할 수 없습니다. 전체 헤더에이 제한이 적용 됩니다는 [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)에 있는 사용자 속성과 시스템 속성을 모두 같은 [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [레이블](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label), 및 [ MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid)합니다. |
| 토픽당 구독 수 |엔터티 |토픽에 추가 구독을 만들기 위한 후속 요청이 거부됩니다. 따라서 포털을 통해 구성된 경우 오류 메시지가 표시됩니다. 관리 API에서 호출되는 경우 호출 코드에서 예외가 수신됩니다. |표준 계층: 각 구독 할당량에 대해 계산을 1,000 엔터티, 큐, 토픽 및 네임 스페이스 당 구독 합니다. <br/> <br/> 프리미엄 계층: 2,000. |
| 토픽당 SQL 필터 수 |엔터티 |항목에서 추가 필터를 만들기 위한 후속 요청이 거부 되며 호출 코드에서 예외를 수신 하며 |2,000 |
| 토픽당 상관 관계 필터 수 |엔터티 |항목에서 추가 필터를 만들기 위한 후속 요청이 거부 되며 호출 코드에서 예외를 수신 하며 |100,000 |
| SQL 필터 또는 작업의 크기 |네임스페이스 |추가 필터를 만들기 위한 후속 요청이 거부 되며 호출 코드에서 예외를 수신 하며 |필터 조건 문자열의 최대 길이: 1,024 (1K)입니다.<br /><br />규칙 작업 문자열의 최대 길이: 1,024 (1K)입니다.<br /><br />규칙 작업당 식의 최대 수: 32. |
| 네임스페이스, 큐 또는 토픽당 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 규칙 수 |엔터티, 네임스페이스 |추가 규칙 만들기에 대 한 후속 요청은 거부 되 고 호출 코드에서 예외가 수신 됩니다. |최대 규칙 수: 12. <br /><br /> Service Bus 네임스페이스에 구성된 규칙이 해당 네임스페이스에 있는 모든 큐 및 토픽에 적용됩니다. |
| 트랜잭션당 메시지 수 | 트랜잭션 | 추가로 들어오는 메시지는 거부 되 고 호출 코드에서 받은 라는 예외 보낼 수 없음"100 개가 넘는 메시지 단일 트랜잭션에서"입니다. | 100 <br /><br /> **Send()** 및 **SendAsync()** 작업 모두에 해당합니다. |

[Azure portal]: https://portal.azure.com