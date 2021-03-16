---
title: Azure Event Hubs에서 AMQP 오류 문제 해결 | Microsoft Docs
description: Azure Event Hubs을 사용 하는 경우 발생할 수 있는 AMQP 오류 목록 및 해당 오류를 표시 합니다.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 51b96792f6921bae9364212c6e5f9c987ff05e2a
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466068"
---
# <a name="amqp-errors-in-azure-event-hubs"></a>Azure Event Hubs의 AMQP 오류
이 문서에서는 Azure Event Hubs에서 AMQP를 사용할 때 발생 하는 오류 중 일부를 제공 합니다. 서비스의 모든 표준 동작입니다. 연결/링크에 대 한 송신/수신 호출을 수행 하 여이를 방지할 수 있습니다. 그러면 연결/링크가 자동으로 다시 만들어집니다.

## <a name="link-is-closed"></a>링크가 닫혔습니다. 
AMQP 연결 및 링크가 활성 상태 이지만 30 분 동안 링크를 사용 하 여 호출 (예: 송신 또는 수신)이 없는 경우 다음과 같은 오류가 표시 됩니다. 링크를 닫습니다. 연결이 아직 열려 있습니다.

"AMQP: link: detach-강제: 게시자에서 오류가 발생 하 여 ' G2:7223832: user.tenant0.cud_00000000000-0000-0000-00000000000000 ' 링크를 broker에서 강제로 분리 합니다 (link164614). 원본 분리: AmqpMessagePublisher: 유휴 시간 제한: 00:30:00. TrackingId: 00000000000000000000000000000000000000_G2_B3, SystemTracker: mynamespace: 토픽: MyTopic, 타임 스탬프: 2/16/2018 11:10:40 PM "

## <a name="connection-is-closed"></a>연결이 닫혔습니다.
작업 (유휴)이 없고 5 분 내에 새 링크가 만들어지지 않았기 때문에 연결의 모든 링크가 닫히면 AMQP 연결에 다음 오류가 표시 됩니다.

"오류 {condition = amqp: connection: 강제, 설명 = ' 연결이 허용 된 30만 밀리초를 초과 하 여 비활성화 되었고 ' LinkTracker ' 컨테이너에 의해 닫혔습니다. TrackingId: 00000000000000000000000000000000000_G21, SystemTracker: gateway5, Timestamp: 2019-03-06T17:32:00 ', info = null} "

## <a name="link-isnt-created"></a>링크가 생성 되지 않음 
새 AMQP 연결을 만들 때이 오류가 표시 되지만, AMQP 연결을 만든 후 1 분 내에 링크를 만들지 않습니다.

"오류 {condition = amqp: connection: 강제, 설명 = ' 연결이 허용 된 6만 밀리초를 초과 하 여 비활성화 되었고 ' LinkTracker ' 컨테이너에 의해 닫혔습니다. TrackingId: 0000000000000000000000000000000000000_G21, SystemTracker: gateway5, Timestamp: 2019-03-06T18:41:51 ', info = null} "

## <a name="next-steps"></a>다음 단계
AMQP에 대해 자세히 알아보려면 [amqp 1.0 프로토콜 가이드](../service-bus-messaging/service-bus-amqp-protocol-guide.md)를 참조 하세요.
