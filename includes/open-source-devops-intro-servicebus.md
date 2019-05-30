---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/22/2019
ms.author: tarcher
ms.openlocfilehash: eb96027351cf244e9cd4404f702544411130db5e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66142179"
---
[Azure Service Bus](/azure/service-bus-messaging/service-bus-messaging-overview)는 엔터프라이즈 [통합](https://azure.microsoft.com/product-categories/integration/) 메시지 브로커입니다. Service Bus는 두 가지 유형의 통신, 즉 큐와 항목을 지원합니다. 

큐는 애플리케이션 간의 비동기 통신을 지원합니다. 앱은 메시지를 저장하는 큐로 메시지를 보냅니다. 그러면 수신 애플리케이션이 큐에 연결되고 메시지를 읽습니다.

항목은 게시-구독 패턴을 지원합니다. 이 패턴에서는 메시지 발신자와 메시지 수신자 간에 일대다 관계를 설정할 수 있습니다.