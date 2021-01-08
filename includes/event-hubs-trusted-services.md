---
title: 포함 파일
description: 포함 파일
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/05/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 743d7d46474b4ba55df50398f29cbdb964b5ff08
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97978799"
---
## <a name="trusted-microsoft-services"></a>신뢰할 수 있는 Microsoft 서비스
**신뢰할 수 있는 Microsoft 서비스에서이 방화벽을 무시 하도록 허용** 설정을 사용 하도록 설정 하면 다음 서비스에 Event Hubs 리소스에 대 한 액세스 권한이 부여 됩니다.

| 신뢰할 수 있는 서비스 | 지원 되는 사용 시나리오 | 
| --------------- | ------------------------- | 
| Azure Event Grid | Azure Event Grid Event Hubs 네임 스페이스의 Event hubs로 이벤트를 보낼 수 있습니다. 또한 다음 단계를 수행 해야 합니다. <ul><li>토픽 또는 도메인에 대해 시스템이 할당 한 id 사용</li><li>Event Hubs 네임 스페이스의 Azure Event Hubs 데이터 발신자 역할에 id를 추가 합니다.</li><li>그런 다음 이벤트 허브를 사용 하는 이벤트 구독을 시스템 할당 id를 사용 하는 끝점으로 구성 합니다.</li></ul> <p>자세한 내용은 [관리 되는 id를 사용 하 여 이벤트 전달](../articles/event-grid/managed-service-identity.md) 을 참조 하세요.</p>|
| Azure Monitor (진단 설정) | Azure Monitor Event Hubs 네임 스페이스의 event hubs에 진단 정보를 보낼 수 있습니다. 이벤트 허브에서 데이터를 읽고 이벤트 허브에 데이터를 쓸 수 Azure Monitor. |
| Azure Stream Analytics | Azure Stream Analytics 작업이 Event Hubs 네임 스페이스에서 데이터를 읽거나[(](../articles/stream-analytics/stream-analytics-add-inputs.md)[출력](../articles/stream-analytics/event-hubs-output.md)) 이벤트 허브에 데이터를 쓸 수 있습니다. <p>**중요**: **관리 되는 id** 를 사용 하 여 이벤트 허브에 액세스 하도록 Stream Analytics 작업을 구성 해야 합니다. 자세한 내용은 [관리 되는 id를 사용 하 여 Azure Stream Analytics 작업에서 이벤트 허브에 액세스 (미리 보기)](../articles/stream-analytics/event-hubs-managed-identity.md)를 참조 하세요. </p>|
| Azure IoT Hub | IoT Hub를 사용 하 여 이벤트 허브 네임 스페이스의 event hubs로 메시지를 보낼 수 있습니다. 또한 다음 단계를 수행 해야 합니다. <ul><li>IoT hub에 대 한 시스템 할당 id 사용</li><li>Event Hubs 네임 스페이스의 Azure Event Hubs 데이터 발신자 역할에 id를 추가 합니다.</li><li>그런 다음 id 기반 인증을 사용 하는 사용자 지정 끝점으로 이벤트 허브를 사용 하는 IoT Hub를 구성 합니다.</li></ul>
