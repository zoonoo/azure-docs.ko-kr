---
title: Azure Service Bus에서 AMQP 오류 문제 해결 | Microsoft Docs
description: Azure Service Bus를 사용할 때 나타날 수 있는 AMQP 오류 목록과 이러한 오류의 원인을 제공 합니다.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 88b10940e0b910f50e6ccf7f8c53134fa7f0ba2f
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88064352"
---
# <a name="amqp-errors-in-azure-service-bus"></a>Azure Service Bus의 AMQP 오류
이 문서에서는 Azure Service Bus에서 AMQP를 사용할 때 발생 하는 오류 중 일부를 제공 합니다. 서비스의 모든 표준 동작입니다. 연결/링크에 대 한 송신/수신 호출을 수행 하 여이를 방지할 수 있습니다. 그러면 연결/링크가 자동으로 다시 만들어집니다.

## <a name="link-is-closed"></a>링크가 닫혔습니다. 
AMQP 연결 및 링크가 활성 상태 이지만 10 분 동안 링크를 사용 하 여 호출 (예: 송신 또는 수신)이 없는 경우 다음과 같은 오류가 표시 됩니다. 링크를 닫습니다. 연결이 아직 열려 있습니다.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>연결이 닫혔습니다.
작업 (유휴)이 없고 5 분 내에 새 링크가 만들어지지 않았기 때문에 연결의 모든 링크가 닫히면 AMQP 연결에 다음 오류가 표시 됩니다.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>링크가 만들어지지 않음 
새 AMQP 연결을 만들 때이 오류가 표시 되지만 AMQP 연결을 만든 후 1 분 내에 링크를 만들지 않습니다.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>다음 단계

AMQP 및 Service Bus에 대해 자세히 알아보려면 다음 링크를 방문하세요.

* [Service Bus AMQP 개요]
* [AMQP 1.0 프로토콜 가이드]
* [Windows Server용 Service Bus의 AMQP]

[Service Bus AMQP 개요]: service-bus-amqp-overview.md
[AMQP 1.0 프로토콜 가이드]: service-bus-amqp-protocol-guide.md
[Windows Server용 Service Bus의 AMQP]: /previous-versions/service-bus-archive/dn282144(v=azure.100)