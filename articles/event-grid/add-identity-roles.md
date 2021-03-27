---
title: Azure Event Grid 대상의 역할에 관리 id 추가
description: 이 문서에서는 Azure Service Bus 및 Azure Event Hubs와 같은 대상에서 관리 되는 id를 Azure 역할에 추가 하는 방법을 설명 합니다.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 1bcef878c982122d80980dd7194fae2de6fc8762
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630454"
---
# <a name="add-an-identity-to-azure-roles-on-azure-event-grid-destinations"></a>Azure Event Grid 대상의 Azure 역할에 id 추가
이 섹션에서는 Azure 역할에 시스템 토픽, 사용자 지정 항목 또는 도메인의 id를 추가 하는 방법에 대해 설명 합니다. 

## <a name="prerequisites"></a>전제 조건
다음 문서의 지침을 사용 하 여 시스템 할당 관리 id를 할당 합니다.

- [사용자 지정 토픽 또는 도메인](enable-identity-custom-topics-domains.md)
- [시스템 항목](enable-identity-system-topics.md)

## <a name="supported-destinations-and-azure-roles"></a>지원 되는 대상 및 Azure 역할
Event grid 사용자 지정 토픽 또는 도메인에 대해 id를 사용 하도록 설정 하면 Azure에서 자동으로 Azure Active Directory에 id를 만듭니다. 사용자 지정 토픽 또는 도메인에서 지원 되는 대상으로 이벤트를 전달할 수 있도록 적절 한 Azure 역할에이 id를 추가 합니다. 예를 들어 event grid 사용자 지정 항목이 해당 네임 스페이스의 event Hubs로 이벤트를 전달할 수 있도록 Azure Event Hubs 네임 스페이스에 대 한 **azure Event Hubs 데이터 발신자** 역할에 id를 추가 합니다. 

현재 Azure event grid는 다음 대상에 이벤트를 전달 하기 위해 시스템 할당 관리 id로 구성 된 사용자 지정 토픽 또는 도메인을 지원 합니다. 또한이 테이블은 사용자 지정 항목이 이벤트를 전달할 수 있도록 id가 있어야 하는 역할을 제공 합니다.

| 대상 | Azure 역할 | 
| ----------- | --------- | 
| Service Bus 큐 및 토픽 | [Azure Service Bus 데이터 보낸 사람](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Azure Event Hubs | [Azure Event Hubs 데이터 보내는 사람](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Azure Blob Storage | [Storage Blob 데이터 기여자](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) |
| Azure Queue 스토리지 |[Storage 큐 데이터 메시지 보내는 사람](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) | 


## <a name="use-the-azure-portal"></a>Azure Portal 사용
사용자 지정 토픽 또는 도메인에서 대상으로 이벤트를 전달할 수 있도록 Azure Portal를 사용 하 여 사용자 지정 토픽 또는 도메인 id를 적절 한 역할에 할당할 수 있습니다. 

다음 예에서는 **msitesttopic** 라는 event grid 사용자 지정 항목에 대 한 관리 되는 id를 큐 또는 토픽 리소스가 포함 된 Service Bus 네임 스페이스에 대 한 **Azure Service Bus 데이터 발신자** 역할에 추가 합니다. 네임 스페이스 수준에서 역할에를 추가 하면 event grid 사용자 지정 항목에서 네임 스페이스 내의 모든 엔터티에 이벤트를 전달할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com)에서 **Service Bus 네임 스페이스로** 이동 합니다. 
1. 왼쪽 창에서 **Access Control** 을 선택 합니다. 
1. **역할 할당 추가** 섹션에서 **추가** 를 선택합니다. 
1. **역할 할당 추가** 페이지에서 다음 단계를 수행합니다.
    1. 역할을 선택합니다. 이 경우 **Azure Service Bus 데이터 보낸 사람** 입니다. 
    1. Event grid 사용자 지정 토픽 또는 도메인에 대 한 **id** 를 선택 합니다. 
    1. **저장** 을 선택 하 여 구성을 저장 합니다.

이 단계는 테이블에 설명된 다른 역할에 ID를 추가하는 경우와 비슷합니다. 

## <a name="use-the-azure-cli"></a>Azure CLI 사용
이 섹션의 예제에서는 Azure CLI를 사용 하 여 Azure 역할에 id를 추가 하는 방법을 보여 줍니다. 샘플 명령은 event grid 사용자 지정 항목에 대 한 것입니다. Event Grid 도메인에 대한 명령도 비슷합니다. 

### <a name="get-the-principal-id-for-the-custom-topics-system-identity"></a>사용자 지정 토픽의 시스템 id에 대 한 보안 주체 ID 가져오기 
먼저 사용자 지정 토픽의 시스템 관리 id의 보안 주체 ID를 가져오고 해당 역할에 id를 할당 합니다.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>다양한 범위에서 Event Hubs에 대한 역할 할당 만들기 
다음 CLI 예제에서는 네임 스페이스 수준이 나 이벤트 허브 수준에서 **Azure Event Hubs 데이터 발신자** 역할에 사용자 지정 항목의 id를 추가 하는 방법을 보여 줍니다. 네임 스페이스 수준에서 역할 할당을 만드는 경우 사용자 지정 항목은 해당 네임 스페이스의 모든 이벤트 허브에 이벤트를 전달할 수 있습니다. 이벤트 허브 수준에서 역할 할당을 만드는 경우 사용자 지정 토픽은 특정 이벤트 허브에만 이벤트를 전달할 수 있습니다. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>다양 한 범위의 Service Bus 항목에 대 한 역할 할당 만들기 
다음 CLI 예제에서는 네임 스페이스 수준 또는 Service Bus 토픽 수준에서 **Azure Service Bus 데이터 발신자** 역할에 event grid 사용자 지정 항목의 id를 추가 하는 방법을 보여 줍니다. 네임 스페이스 수준에서 역할 할당을 만드는 경우 event grid 토픽은 해당 네임 스페이스 내의 모든 엔터티 (Service Bus 큐 또는 토픽)에 이벤트를 전달할 수 있습니다. Service Bus 큐 또는 토픽 수준에서 역할 할당을 만드는 경우 event grid 사용자 지정 토픽은 특정 Service Bus 큐 또는 토픽에만 이벤트를 전달할 수 있습니다. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="next-steps"></a>다음 단계
시스템 항목, 사용자 지정 항목 또는 도메인에 시스템 할당 id를 할당 하 고 대상의 적절 한 역할에 id를 추가 했으므로 이제 id를 사용 하 여 대상에 이벤트를 전달 하는 데 id를 사용 하는 [Devlier 이벤트](managed-service-identity.md) 를 참조 하세요.


