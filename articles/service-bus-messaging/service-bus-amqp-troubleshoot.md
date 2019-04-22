---
title: Azure Service Bus의 AMQP 오류 문제 해결 | Microsoft Docs
description: Azure Service Bus를 사용 하는 경우 수신 하 고 발생할 수 있습니다 AMQP 오류 목록을 제공 오류입니다.
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
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58910047"
---
# <a name="amqp-errors-in-azure-service-bus"></a>Azure Service Bus의 AMQP 오류
이 문서에서는 Azure Service Bus와 AMQP를 사용 하는 경우 수신 오류 중 일부를 제공 합니다. 서비스의 모든 표준 동작 됩니다. 이러한 호출을 통해 보내기/받기를 연결/링크를 자동으로 다시 연결/링크를 방지할 수 있습니다.

## <a name="link-is-closed"></a>링크 닫혀 있습니다. 
AMQP 연결 및 링크는 활성 호출 하지 않습니다 하는 경우 다음 오류를 참조 하세요 (예를 들어, 송신 또는 수신) 10 분에 대 한 링크를 사용 하 여 이루어집니다. 따라서 링크 닫혀 있습니다. 연결이 열려 있습니다.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>연결이 닫혀
작업 없음 (유휴 상태)가 5 분 내에 대 한 새 링크가 만들어지지 않은 것 이므로 연결에 대 한 모든 링크 닫혔을 때 AMQP 연결에 다음과 같은 오류가 나타납니다.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>링크 생성 되지 않습니다. 
링크의 AMQP 연결 만들기 1 분 내에 만들어지지 새 AMQP 연결을 만들 때이 오류가 표시 됩니다.

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
