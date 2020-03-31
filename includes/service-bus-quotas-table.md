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
ms.openlocfilehash: b19dc7a85fafa1a4d875c84db9bbefabb3cd5a7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651527"
---
다음 표에는 Azure Service Bus 메시징과 관련된 할당량 정보가 나열되어 있습니다. 서비스 버스의 가격 및 기타 할당량에 대한 자세한 내용은 [서비스 버스 가격을](https://azure.microsoft.com/pricing/details/service-bus/)참조하십시오.

| 할당량 이름 | Scope | 메모 | 값 |
| --- | --- | --- | --- |
| Azure 구독당 기본 또는 표준 네임스페이스의 최대 수 |네임스페이스 |추가 기본 또는 표준 네임스페이스에 대한 후속 요청은 Azure 포털에서 거부됩니다. |100|
| Azure 구독당 최대 프리미엄 네임스페이스 수 |네임스페이스 |추가 Premium 네임스페이스에 대한 후속 요청은 포털에서 거부됩니다. |100 |
| 큐 또는 토픽 크기 |엔터티 |큐 또는 토픽을 만들 때 정의됩니다. <br/><br/> 후속 들어오는 메시지가 거부되고 호출 코드에서 예외가 수신됩니다. |1, 2, 3, 4GB 또는 5GB<br /><br />프리미엄 SKU 및 [분할이 활성화된](/azure/service-bus-messaging/service-bus-partitioning) 표준 SKU에서 최대 큐 또는 토픽 크기는 80GB입니다. |
| 네임스페이스에 대한 동시 연결 수 |네임스페이스 |추가 연결에 대한 후속 요청이 거부되고 호출 코드에서 예외가 수신됩니다. REST 작업은 동시 TCP 연결에 포함되지 않습니다. |넷 메시징: 1,000.<br /><br />AMQP: 5,000. |
| 큐, 토픽 또는 구독 엔터티에 대한 동시 수신 요청 수 |엔터티 |후속 수신 요청이 거부되고 호출 코드에서 예외가 수신됩니다. 이 할당량은 항목의 모든 구독 전반에 걸쳐 종합된 동시 수신 명령 수에 적용됩니다. |5,000 |
| 네임스페이스당 토픽 또는 큐 수 |네임스페이스 |네임스페이스에 새 토픽 또는 큐를 만들기 위한 후속 요청이 거부됩니다. 따라서 [Azure Portal][Azure portal]을 통해 구성된 경우 오류 메시지가 생성됩니다. 관리 API에서 호출되는 경우 호출 코드에서 예외가 수신됩니다. |기본 또는 표준 계층의 경우 10,000. 네임스페이스에서 토픽 및 큐의 총수는 10,000 이하이어야 합니다. <br/><br/>프리미엄 계층의 경우 메시징 단위당 1,000(MU)입니다. 최대 한도는 4,000입니다. |
| 네임스페이스당 [분할된 토픽 또는 큐](/azure/service-bus-messaging/service-bus-partitioning) 수 |네임스페이스 |네임스페이스에 분할된 새 토픽 또는 큐를 만들기 위한 후속 요청이 거부됩니다. 따라서 [Azure Portal][Azure portal]을 통해 구성된 경우 오류 메시지가 생성됩니다. 관리 API에서 호출된 경우 **예외 QuotaExceededException** 호출 코드에서 수신 됩니다. |기본 및 표준 등급: 100.<br/><br/>분할된 엔터티는 [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) 계층에서 지원되지 않습니다.<br/><br />분할된 각 큐 또는 토픽은 네임스페이스당 1,000개의 엔터티 할당량에 계산됩니다. |
| 모든 메시징 엔터티 경로의 최대 크기: 큐 또는 토픽 |엔터티 |- |260자. |
| 모든 메시징 엔터티 이름의 최대 크기: 네임스페이스, 구독 또는 구독 규칙 |엔터티 |- |50자. |
| [메시지 ID](/dotnet/api/microsoft.azure.servicebus.message.messageid)의 최대 크기 | 엔터티 |- | 128 |
| 메시지 [세션 ID의](/dotnet/api/microsoft.azure.servicebus.message.sessionid) 최대 크기 | 엔터티 |- | 128 |
| 큐, 토픽 또는 구독 엔터티에 대한 메시지 크기 |엔터티 |이러한 할당량을 초과하는 들어오는 메시지는 거부되고 호출 코드에서 예외를 받습니다. |최대 메시지 크기: 표준 [계층의](../articles/service-bus-messaging/service-bus-premium-messaging.md)경우 256KB, [프리미엄 계층의](../articles/service-bus-messaging/service-bus-premium-messaging.md)경우 1MB입니다. <br /><br />시스템 오버헤드로 인해, 이 제한이 이러한 값보다 작습니다.<br /><br />최대 헤더 크기: 64KB.<br /><br />속성 가방에서 헤더 속성의 최대 수: **바이트/int. 맥스 밸류.**<br /><br />속성 모음의 최대 속성 크기: 명시적 제한은 없습니다. 최대 헤더 크기로 제한됩니다. |
| 큐, 토픽 또는 구독 엔터티에 대한 메시지 속성 크기 |엔터티 | 예외 **직렬화예외가** 생성됩니다. |각 속성의 최대 메시지 속성 크기는 32,000입니다. 모든 속성의 누적 크기는 64,000을 초과할 수 없습니다. 이 제한은 [SequenceNumber,](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)및 [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid)와 같은 사용자 속성과 시스템 속성을 모두 포함하는 [BrokeredMessage의](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)전체 헤더에 적용됩니다. |
| 토픽당 구독 수 |엔터티 |토픽에 추가 구독을 만들기 위한 후속 요청이 거부됩니다. 따라서 포털을 통해 구성된 경우 오류 메시지가 표시됩니다. 관리 API에서 호출되는 경우 호출 코드에서 예외가 수신됩니다. |표준 계층의 주제당 2,000개입니다. |
| 토픽당 SQL 필터 수 |엔터티 |토픽에 대한 추가 필터 생성에 대한 후속 요청이 거부되고 호출 코드에서 예외가 수신됩니다. |2,000 |
| 토픽당 상관 관계 필터 수 |엔터티 |토픽에 대한 추가 필터 생성에 대한 후속 요청이 거부되고 호출 코드에서 예외가 수신됩니다. |100,000 |
| SQL 필터 또는 작업의 크기 |네임스페이스 |추가 필터 생성에 대한 후속 요청이 거부되고 호출 코드에서 예외가 수신됩니다. |필터 조건 문자열의 최대 길이: 1,024 (1 K).<br /><br />규칙 동작 문자열의 최대 길이: 1,024 (1 K).<br /><br />규칙 작업당 식의 최대 수: 32 |
| 네임스페이스, 큐 또는 토픽당 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 규칙 수 |엔터티, 네임스페이스 |이후 추가 규칙 만들기 요청이 거부되고 호출 코드에서 예외가 수신됩니다. |엔터티 유형당 최대 규칙 수: 12개. <br /><br /> Service Bus 네임스페이스에서 구성된 규칙은 큐, 토픽 등 모든 유형에 적용됩니다. |
| 트랜잭션당 메시지 수 | 트랜잭션 | 추가 들어오는 메시지가 거부되고 호출 코드에서 "단일 트랜잭션에서 100개 이상의 메시지를 보낼 수 없습니다"라는 예외가 수신됩니다. | 100 <br /><br /> **Send()** 및 **SendAsync()** 작업 모두에 해당합니다. |
| 가상 네트워크 및 IP 필터 규칙 수 | 네임스페이스 | &nbsp; | 128 | 

[Azure portal]: https://portal.azure.com
