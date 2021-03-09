---
title: 포함 파일
description: 포함 파일
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 03/08/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: cd4d5de5d93e4aea862aaabd10fb5d3c6d45cb1c
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510866"
---
## <a name="trusted-microsoft-services"></a>신뢰할 수 있는 Microsoft 서비스
**신뢰할 수 있는 Microsoft 서비스에서이 방화벽을 무시 하도록 허용** 설정을 사용 하도록 설정 하면 다음 서비스에 Service Bus 리소스에 대 한 액세스 권한이 부여 됩니다.

| 신뢰할 수 있는 서비스 | 지원 되는 사용 시나리오 | 
| --------------- | ------------------------- | 
| Azure Event Grid | Azure Event Grid Service Bus 네임 스페이스의 큐 또는 토픽에 이벤트를 보낼 수 있습니다. 또한 다음 단계를 수행 해야 합니다. <ul><li>토픽 또는 도메인에 대해 시스템이 할당 한 id 사용</li><li>Service Bus 네임 스페이스의 Azure Service Bus 데이터 발신자 역할에 id를 추가 합니다.</li><li>그런 다음 Service Bus 큐 또는 토픽을 사용 하는 이벤트 구독을 끝점으로 구성 하 여 시스템 할당 id를 사용 합니다.</li></ul> <p>자세한 내용은 [관리 되는 id를 사용 하 여 이벤트 전달](../articles/event-grid/managed-service-identity.md) 을 참조 하세요.</p>|
| Azure API Management | <p>API Management 서비스를 사용 하면 Service Bus 네임 스페이스의 Service Bus 큐/항목에 메시지를 보낼 수 있습니다.</p><ul><li>[송신 요청 정책을](../articles/api-management/api-management-sample-send-request.md)사용 하 여 API를 호출할 때 Service Bus 큐/토픽에 메시지를 전송 하 여 사용자 지정 워크플로를 트리거할 수 있습니다.</li><li>Service Bus 큐/항목을 API의 백 엔드로 취급할 수도 있습니다. 샘플 정책은 [관리 되는 id를 사용 하 여 인증 Service Bus 큐 또는 토픽에 액세스](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Service%20Bus.xml)를 참조 하세요. 또한 다음 단계를 수행 해야 합니다.<ol><li>API Management 인스턴스에서 시스템이 할당 한 id를 사용 하도록 설정 합니다. 지침은 [Azure API Management에서 관리 되는 Id 사용](../articles/api-management/api-management-howto-use-managed-service-identity.md)을 참조 하세요.</li><li>Service Bus 네임 스페이스의 **Azure Service Bus 데이터 발신자** 역할에 id를 추가 합니다.</li></ol></li></ul> | 