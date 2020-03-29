---
title: Azure 서비스 버스에서 AMQP 오류 해결 | 마이크로 소프트 문서
description: Azure Service Bus를 사용할 때 발생할 수 있는 AMQP 오류 목록 및 이러한 오류의 원인을 제공합니다.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2019
ms.author: aschhab
ms.openlocfilehash: 85d24a9e7c753ec5dba80c4f259dd3fb51d9c14b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "60402788"
---
# <a name="amqp-errors-in-azure-service-bus"></a>Azure 서비스 버스의 AMQP 오류
이 문서에서는 Azure Service Bus에서 AMQP를 사용할 때 받는 몇 가지 오류를 제공합니다. 그들은 모두 서비스의 표준 동작입니다. 연결/링크에서 송수신 전화를 걸면 자동으로 연결/링크를 다시 만들 수 있습니다.

## <a name="link-is-closed"></a>링크가 닫혔습니다. 
AMQP 연결 및 링크가 활성화되어 있지만 10분 동안 링크를 사용하여 호출(예: 송수신)이 이루어지지 않으면 다음 오류가 표시됩니다. 따라서 링크가 닫힙됩니다. 연결이 계속 열려 있습니다.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>연결이 닫혀 있습니다.
활동(유휴)이 없고 5분 안에 새 링크가 만들어지지 않았기 때문에 연결의 모든 링크가 닫혔을 때 AMQP 연결에 다음과 같은 오류가 표시됩니다.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>링크가 만들어지지 않음 
새 AMQP 연결이 만들어지지만 AMQP 연결을 만든 후 1분 이내에 링크가 만들어지지 않은 경우 이 오류가 표시됩니다.

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
[Windows Server용 Service Bus의 AMQP]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
