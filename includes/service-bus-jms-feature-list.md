---
title: 포함 파일
description: 포함 파일
services: service-bus-messaging
author: axisc
ms.service: service-bus-messaging
ms.topic: include
ms.date: 6/9/2020
ms.author: aschhab
ms.custom: include file
ms.openlocfilehash: 9030080d0b8c8e032cb2992a62275efcdb04aabc
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87798140"
---
다음 표에서는 현재 지원 Azure Service Bus는 JMS (Java Message Service) 기능을 보여 줍니다. 또한 지원 되지 않는 기능을 보여 줍니다.


| 기능 | API |상태 |
|---|---|---|
| 큐   | <ul> <li> JMSContext. createQueue (String queueName) </li> </ul>| **지원됨** |
| 토픽   | <ul> <li> JMSContext (String topicName) </li> </ul>| **지원됨** |
| 임시 큐 |<ul> <li> JMSContext () </li> </ul>| **지원됨** |
| 임시 항목 |<ul> <li> JMSContext () </li> </ul>| **지원됨** |
| 메시지 생산자/<br/> JMSProducer |<ul> <li> JMSContext. createProducer () </li> </ul>| **지원됨** |
| 큐 브라우저 |<ul> <li> JMSContext. createBrowser (큐 큐) </li> <li> JMSContext. createBrowser (큐 큐, 문자열 messageSelector) </li> </ul> | **지원됨** |
| 메시지 소비자/ <br/> JMSConsumer | <ul> <li> JMSContext (대상 대상) </li> <li> JMSContext (대상 대상, 문자열 messageSelector) </li> <li> JMSContext (대상 대상, 문자열 messageSelector, 부울 noLocal)</li> </ul>  <br/> noLocal은 현재 지원 되지 않습니다. | **지원됨** |
| 공유 내구성이 있는 구독 | <ul> <li> JMSContext (토픽 항목, 문자열 이름) </li> <li> JMSContext (토픽 항목, 문자열 이름, 문자열 messageSelector) </li> </ul>| **지원됨**|
| 공유 되지 않은 내구성이 있는 구독 | <ul> <li> JMSContext (토픽 항목, 문자열 이름) </li> <li> createDurableConsumer (토픽 항목, 문자열 이름, 문자열 messageSelector, 부울 noLocal) </li> </ul> <br/> noLocal은 현재 지원 되지 않으므로 false로 설정 해야 합니다. | **지원됨** |
| 영구 공유 되지 않는 공유 구독 |<ul> <li> JMSContext (토픽 항목, 문자열 sharedSubscriptionName) </li> <li> JMSContext (토픽 항목, String sharedSubscriptionName, String messageSelector) </li> </ul> | **지원됨** |
| 공유 되지 않은 내구성이 없는 구독 |<ul> <li> JMSContext (대상 대상) </li> <li> JMSContext (대상 대상, 문자열 messageSelector) </li> <li> JMSContext (대상 대상, 문자열 messageSelector, 부울 noLocal) </li> </ul> <br/> noLocal은 현재 지원 되지 않으므로 false로 설정 해야 합니다. | **지원됨** |
| 메시지 선택기 | 만든 소비자에 따라 다릅니다. | **지원됨** |
| 배달 지연 (예약 된 메시지) | <ul> <li> JMSProducer. setDeliveryDelay (long deliveryDelay) </li> </ul>|**지원됨**|
| 메시지 작성 |<ul> <li> JMSContext () </li> <li> JMSContext. createBytesMessage () </li> <li> JMSContext () </li> <li> JMSContext. createObjectMessage (직렬화 가능 개체) </li> <li> JMSContext () </li> <li> JMSContext () </li> <li> JMSContext (문자열 텍스트) </li> </ul>| **지원됨** |
| 분산 트랜잭션 || 지원 안 함 |
