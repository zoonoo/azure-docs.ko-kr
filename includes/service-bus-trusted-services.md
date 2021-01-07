---
title: 포함 파일
description: 포함 파일
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/05/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bb6f2281cc115904db6babcf31f1af387b254d48
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94426571"
---
## <a name="trusted-microsoft-services"></a>신뢰할 수 있는 Microsoft 서비스
**신뢰할 수 있는 Microsoft 서비스에서이 방화벽을 무시 하도록 허용** 설정을 사용 하도록 설정 하면 다음 서비스에 Service Bus 리소스에 대 한 액세스 권한이 부여 됩니다.

| 신뢰할 수 있는 서비스 | 지원 되는 사용 시나리오 | 
| --------------- | ------------------------- | 
| Azure Event Grid | Azure Event Grid Service Bus 네임 스페이스의 큐 또는 토픽에 이벤트를 보낼 수 있습니다. 또한 다음 단계를 수행 해야 합니다. <ul><li>토픽 또는 도메인에 대해 시스템이 할당 한 id 사용</li><li>Service Bus 네임 스페이스의 Azure Service Bus 데이터 발신자 역할에 id를 추가 합니다.</li><li>그런 다음 Service Bus 큐 또는 토픽을 사용 하는 이벤트 구독을 끝점으로 구성 하 여 시스템 할당 id를 사용 합니다.</li></ul> <p>자세한 내용은 [관리 되는 id를 사용 하 여 이벤트 전달](../articles/event-grid/managed-service-identity.md) 을 참조 하세요.</p>|
