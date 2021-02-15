---
title: 개인 링크 서비스를 사용 하 여 이벤트 제공
description: 이 문서에서는 개인 링크 서비스를 사용 하 여 이벤트를 배달할 수 없다는 제한을 해결 하는 방법을 설명 합니다.
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: ad6f500830383f60e0350a297d2650bfbeae2f6f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417588"
---
# <a name="deliver-events-using-private-link-service"></a>개인 링크 서비스를 사용 하 여 이벤트 제공
현재 [개인 끝점](../private-link/private-endpoint-overview.md)을 사용 하 여 이벤트를 전달할 수 없습니다. 즉, 전달 된 이벤트 트래픽이 개인 IP 공간을 떠나지 않아야 하는 엄격한 네트워크 격리 요구 사항이 있는 경우에는 지원 되지 않습니다. 

## <a name="use-managed-identity"></a>관리 ID 사용
그러나 요구 사항에 따라 암호화 된 채널을 사용 하 여 이벤트를 전송 하는 안전한 방법, 그리고 공용 IP 공간을 사용 하는 보낸 사람의 알려진 id (이 경우에는 Event Grid)를 호출 하는 경우 Azure Event Grid 사용자 지정 토픽 또는 시스템 관리 id를 사용 하는 도메인을 사용 하 여 Event Hubs, Service Bus 또는 Azure Storage 서비스에 이벤트를 전달할 수 있습니다. 관리 id를 사용 하 여 이벤트를 배달 하는 방법에 대 한 자세한 내용은 [관리 되는 id를 사용 하 여 이벤트 배달](managed-service-identity.md) 

그런 다음 Azure Functions에 구성 된 개인 링크나 가상 네트워크에 배포 된 웹 후크를 사용 하 여 이벤트를 끌어올 수 있습니다. 샘플: Azure Functions를 [사용 하 여 전용 끝점에 연결](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/)을 참조 하세요.


:::image type="content" source="./media/consume-private-endpoints/deliver-private-link-service.png" alt-text="개인 링크 서비스를 통해 제공":::


이 구성에서 트래픽은 공용 IP/인터넷을 통해 Event Grid에서 Event Hubs, Service Bus 또는 Azure Storage로 이동 하지만 채널을 암호화 하 고 Event Grid의 관리 되는 id를 사용할 수 있습니다. 개인 링크를 통해 Event Hubs, Service Bus 또는 Azure Storage를 사용 하도록 가상 네트워크에 배포 된 Azure Functions 또는 webhook를 구성 하는 경우 해당 트래픽 섹션은 Azure 내에서 분명히 됩니다.

## <a name="deliver-events-to-event-hubs-using-managed-identity"></a>관리 id를 사용 하 여 Event Hubs에 이벤트 제공
관리 id를 사용 하 여 Event Hubs 네임 스페이스의 event hubs에 이벤트를 전달 하려면 다음 단계를 수행 합니다.

1. [토픽 또는 도메인에 대해 시스템이 할당 한 id를 사용 하도록 설정](managed-service-identity.md#create-a-custom-topic-or-domain-with-an-identity)합니다. 
1. [Event Hubs 네임 스페이스의 **Azure Event Hubs 데이터 발신자** 역할에 id를 추가](../event-hubs/authenticate-managed-identity.md#to-assign-azure-roles-using-the-azure-portal)합니다.
1. [Event Hubs 네임 스페이스에 대해 **신뢰할 수 있는 Microsoft 서비스에서이 방화벽을 무시 하도록 허용** 설정을 사용 하도록 설정](../event-hubs/event-hubs-service-endpoints.md#trusted-microsoft-services)합니다. 
1. 시스템 할당 id를 사용 하는 끝점으로 이벤트 허브를 사용 하는 [이벤트 구독을 구성](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) 합니다.

## <a name="deliver-events-to-service-bus-using-managed-identity"></a>관리 id를 사용 하 여 Service Bus에 이벤트 제공
관리 id를 사용 하 여 Service Bus 네임 스페이스의 Service Bus 큐 또는 토픽에 이벤트를 전달 하려면 다음 단계를 수행 합니다.

1. [토픽 또는 도메인에 대해 시스템이 할당 한 id를 사용 하도록 설정](managed-service-identity.md#create-a-custom-topic-or-domain-with-an-identity)합니다. 
1. Service Bus 네임 스페이스의 [Azure Service Bus 데이터 발신자](/service-bus-messaging/service-bus-managed-service-identity.md#azure-built-in-roles-for-azure-service-bus) 역할에 id를 추가 합니다.
1. [Service Bus 네임 스페이스에 대해 **신뢰할 수 있는 Microsoft 서비스에서이 방화벽을 무시 하도록 허용** 설정을 사용 하도록 설정](../service-bus-messaging/service-bus-service-endpoints.md#trusted-microsoft-services)합니다. 
1. 시스템이 할당 한 id를 사용 하려면 Service Bus 큐 또는 토픽을 끝점으로 사용 하는 [이벤트 구독을 구성](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) 합니다.

## <a name="deliver-events-to-storage"></a>저장소에 이벤트 제공 
관리 id를 사용 하 여 저장소 큐에 이벤트를 전달 하려면 다음 단계를 수행 합니다.

1. [토픽 또는 도메인에 대해 시스템이 할당 한 id를 사용 하도록 설정](managed-service-identity.md#create-a-custom-topic-or-domain-with-an-identity)합니다.
1. Azure Storage 큐의 [저장소 큐 데이터 메시지 발신자](../storage/common/storage-auth-aad-rbac-portal.md) 역할에 id를 추가 합니다.
1. 시스템이 할당 한 id를 사용 하려면 Service Bus 큐 또는 토픽을 끝점으로 사용 하는 [이벤트 구독을 구성](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) 합니다.


## <a name="next-steps"></a>다음 단계
관리 id를 사용 하 여 이벤트를 배달 하는 방법에 대 한 자세한 내용은 [관리 id를 사용 하 여 이벤트 배달](managed-service-identity.md)을 참조 하세요. 
