---
title: 관리형 서비스 ID를 사용하여 이벤트 전달
description: 이 문서에서는 Azure Event Grid 토픽에 대해 관리형 서비스 ID를 사용하도록 설정하는 방법을 설명합니다. 이 ID를 사용하여 지원되는 대상에 이벤트를 전달합니다.
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: c05eb2e78595e962494a60b1ffa8ead899aa0109
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371263"
---
# <a name="event-delivery-with-a-managed-identity"></a>관리 id를 사용 하 여 이벤트 배달
이 문서에서는 Azure event grid 토픽 또는 도메인에 대해 [관리 서비스 id](../active-directory/managed-identities-azure-resources/overview.md) 를 사용 하도록 설정 하는 방법을 설명 합니다. 이 ID를 사용하여 Service Bus 큐 및 토픽, Event Hubs, 스토리지 계정 등의 지원되는 대상으로 이벤트를 전달합니다.

이 문서에서 자세히 설명하는 단계는 다음과 같습니다.
1. 시스템이 할당 한 id를 사용 하 여 토픽 또는 도메인을 만들거나, id를 사용 하도록 기존 토픽 또는 도메인을 업데이트 합니다. 
1. 대상의 적절 한 역할 Service Bus (예: Service Bus 큐)에 id를 추가 합니다.
1. 이벤트 구독을 만들 때 id를 사용 하 여 대상에 이벤트를 전달 하도록 설정 합니다. 

## <a name="create-a-topic-or-domain-with-an-identity"></a>ID를 사용하여 토픽 또는 도메인 만들기
먼저 시스템 관리 ID를 사용하여 토픽 또는 도메인을 만드는 방법을 살펴보겠습니다.

### <a name="use-the-azure-portal"></a>Azure Portal 사용
Azure Portal에서 항목 또는 도메인을 만드는 동안 시스템 할당 id를 사용 하도록 설정할 수 있습니다. 다음 이미지는 항목에 대해 시스템 관리 id를 사용 하도록 설정 하는 방법을 보여 줍니다. 기본적으로 토픽 만들기 마법사의 **고급** 페이지에서 **시스템 할당 ID 사용** 옵션을 선택합니다. 도메인 만들기 마법사의 **고급** 페이지에도이 옵션이 표시 됩니다. 

![토픽을 만드는 동안 ID 사용](./media/managed-service-identity/create-topic-identity.png)

### <a name="use-the-azure-cli"></a>Azure CLI 사용
Azure CLI를 사용 하 여 시스템 할당 id로 토픽 또는 도메인을 만들 수도 있습니다. `--identity` 매개 변수를 `systemassigned`로 설정하여 `az eventgrid topic create` 명령을 사용합니다. 이 매개 변수에 대한 값을 지정하지 않으면 기본값 `noidentity`가 사용됩니다. 

```azurecli-interactive
# create a topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

마찬가지로, `az eventgrid domain create` 명령을 사용하여 시스템 관리 ID로 도메인을 만들 수 있습니다.

## <a name="enable-an-identity-for-an-existing-topic-or-domain"></a>기존 토픽 또는 도메인에 대해 id 사용
이전 섹션에서는 토픽 또는 도메인을 만드는 동안 시스템 관리 id를 사용 하도록 설정 하는 방법을 알아보았습니다. 이 섹션에서는 기존 토픽 또는 도메인에 대해 시스템 관리 id를 사용 하도록 설정 하는 방법에 대해 알아봅니다. 

### <a name="use-the-azure-portal"></a>Azure Portal 사용
다음 절차에서는 항목에 대해 시스템 관리 id를 사용 하도록 설정 하는 방법을 보여 줍니다. 도메인에 대 한 id를 설정 하는 단계는 유사 합니다. 

1. [Azure 포털](https://portal.azure.com)로 이동합니다.
2. 위쪽의 검색 창에서 **event grid 항목** 을 검색 합니다.
3. 관리 ID를 사용하도록 설정할 **토픽**을 선택합니다. 
4. **ID** 탭으로 전환합니다. 
5. 스위치 **를 설정 하** 여 id를 사용 하도록 설정 합니다. 
1. 도구 모음에서 **저장** 을 선택 하 여 설정을 저장 합니다. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="항목에 대 한 id 페이지"::: 

비슷한 단계를 사용 하 여 event grid 도메인에 대 한 id를 설정할 수 있습니다.

### <a name="use-the-azure-cli"></a>Azure CLI 사용
`--identity`를 `systemassigned`로 설정하여 `az eventgrid topic update` 명령을 사용하면 기존 토픽에 대해 시스템 할당 ID를 사용하도록 설정할 수 있습니다. ID를 사용하지 않도록 설정하려면 `noidentity`를 값으로 지정합니다. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

기존 도메인을 업데이트하는 명령은 유사합니다(`az eventgrid domain update`).

## <a name="supported-destinations-and-rbac-roles"></a>지원 되는 대상 및 RBAC 역할
Event grid 토픽 또는 도메인에 대해 id를 사용 하도록 설정 하면 Azure에서 자동으로 Azure Active Directory에 id를 만듭니다. 토픽 또는 도메인에서 지원 되는 대상으로 이벤트를 전달할 수 있도록 적절 한 역할 기반 액세스 제어 (RBAC) 역할에이 id를 추가 합니다. 예를 들어 event grid 토픽에서 해당 네임 스페이스의 event Hubs로 이벤트를 전달할 수 있도록 Azure Event Hubs 네임 스페이스에 대 한 **azure Event Hubs 데이터 발신자** 역할에 id를 추가 합니다. 

현재 Azure event grid는 시스템 할당 관리 id로 구성 된 토픽 또는 도메인을 지원 하 여 이벤트를 다음 대상으로 전달 합니다. 또한 이 테이블은 토픽이 이벤트를 전달하기 위해 ID가 속해야 하는 역할을 제공합니다.

| 대상 | RBAC 역할 | 
| ----------- | --------- | 
| Service Bus 큐 및 토픽 | [Azure Service Bus 데이터 보낸 사람](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Azure Event Hubs | [Azure Event Hubs 데이터 보내는 사람](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Azure Blob Storage | [Storage Blob 데이터 기여자](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) |
| Azure Queue 스토리지 |[Storage 큐 데이터 메시지 보내는 사람](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) | 

## <a name="add-an-identity-to-rbac-roles-on-destinations"></a>대상의 RBAC 역할에 id 추가
이 섹션에서는 토픽 또는 도메인의 ID를 RBAC 역할에 추가하는 방법을 설명합니다. 

### <a name="use-the-azure-portal"></a>Azure Portal 사용
토픽 또는 도메인에서 대상으로 이벤트를 전달할 수 있도록 토픽 또는 도메인 id를 적절 한 역할에 할당 하는 Azure Portal를 사용할 수 있습니다. 

다음 예제에서는 **msitesttopic**이라는 Event Grid 토픽의 관리 ID를 큐 또는 토픽 리소스가 포함된 Service Bus 네임스페이스에 대한 **Azure Service Bus 데이터 보낸 사람** 역할에 추가합니다. 네임 스페이스 수준에서 역할에를 추가할 때 토픽은 네임 스페이스 내의 모든 엔터티에 이벤트를 전달할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com)에서 **Service Bus 네임 스페이스로** 이동 합니다. 
1. 왼쪽 창에서 **Access Control** 을 선택 합니다. 
1. **역할 할당 추가** 섹션에서 **추가**를 선택합니다. 
1. **역할 할당 추가** 페이지에서 다음 단계를 수행합니다.
    1. 역할을 선택합니다. 이 경우 **Azure Service Bus 데이터 보낸 사람**입니다. 
    1. 토픽 또는 도메인에 대한 **ID**를 선택합니다. 
    1. **저장** 을 선택 하 여 구성을 저장 합니다.

이 단계는 테이블에 설명된 다른 역할에 ID를 추가하는 경우와 비슷합니다. 

### <a name="use-the-azure-cli"></a>Azure CLI 사용
이 섹션의 예제에서는 Azure CLI를 사용 하 여 RBAC 역할에 id를 추가 하는 방법을 보여 줍니다. 샘플 명령은 Event Grid 토픽에 대한 것입니다. Event Grid 도메인에 대한 명령도 비슷합니다. 

#### <a name="get-the-principal-id-for-the-topics-system-identity"></a>항목의 시스템 id에 대 한 보안 주체 ID를 가져옵니다. 
먼저 토픽의 시스템 관리 ID에 대한 보안 주체 ID를 가져오고 해당 역할에 ID를 할당합니다.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

#### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>다양한 범위에서 Event Hubs에 대한 역할 할당 만들기 
다음 CLI 예제에서는 네임스페이스 수준이나 이벤트 허브 수준에서 **Azure Event Hubs 데이터 보낸 사람** 역할에 토픽의 ID를 추가하는 방법을 보여 줍니다. 네임 스페이스 수준에서 역할 할당을 만드는 경우 토픽은 해당 네임 스페이스의 모든 이벤트 허브에 이벤트를 전달할 수 있습니다. 이벤트 허브 수준에서 역할 할당을 만드는 경우 토픽은 특정 이벤트 허브에만 이벤트를 전달할 수 있습니다. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

#### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>다양 한 범위의 Service Bus 항목에 대 한 역할 할당 만들기 
다음 CLI 예제에서는 네임스페이스 수준이나 Service Bus 토픽 수준에서 **Azure Service Bus 데이터 보낸 사람** 역할에 토픽의 ID를 추가하는 방법을 보여 줍니다. 네임 스페이스 수준에서 역할 할당을 만드는 경우 event grid 토픽은 해당 네임 스페이스 내의 모든 엔터티 (Service Bus 큐 또는 토픽)에 이벤트를 전달할 수 있습니다. Service Bus 큐 또는 토픽 수준에서 역할 할당을 만드는 경우 event grid 토픽은 특정 Service Bus 큐 또는 토픽에만 이벤트를 전달할 수 있습니다. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="create-event-subscriptions-that-use-an-identity"></a>Id를 사용 하는 이벤트 구독 만들기
항목이 나 시스템 관리 id를 사용 하는 도메인을 사용 하 고 대상의 적절 한 역할에 id를 추가한 후에는 해당 id를 사용 하는 구독을 만들 준비가 된 것입니다. 

### <a name="use-the-azure-portal"></a>Azure Portal 사용
이벤트 구독을 만들 때 **끝점 세부 정보** 섹션에서 끝점에 대 한 시스템 할당 id를 사용 하도록 설정 하는 옵션이 표시 됩니다. 

![Service Bus 큐에 대 한 이벤트 구독을 만드는 동안 id 사용](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

또한 **추가 기능** 탭의 배달 못 한 편지에 사용할 시스템 할당 id를 사용 하도록 설정할 수 있습니다. 

![배달 못한 편지에 대해 시스템 할당 ID 사용](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="use-the-azure-cli---service-bus-queue"></a>Azure CLI Service Bus 큐 사용 
이 섹션에서는 Azure CLI 사용 하 여 시스템에 할당 된 id를 사용 하 여 Service Bus 큐에 이벤트를 전달 하도록 설정 하는 방법에 대해 알아봅니다. ID는 **Azure Service Bus 데이터 보낸 사람** 역할의 멤버여야 합니다. 또한 배달 못한 편지 처리에 사용되는 스토리지 계정에서 **Storage Blob 데이터 기여자** 역할의 멤버여야 합니다. 

#### <a name="define-variables"></a>변수 정의
먼저 CLI 명령에 사용할 다음 변수의 값을 지정합니다. 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>배달에 관리 되는 id를 사용 하 여 이벤트 구독 만들기 
이 샘플 명령은 끝점 유형이 **Service Bus queue**로 설정 된 event grid 토픽에 대 한 이벤트 구독을 만듭니다. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery-and-dead-lettering"></a>배달 및 배달 못 한 편지에 관리 되는 id를 사용 하 여 이벤트 구독 만들기
이 샘플 명령은 끝점 유형이 **Service Bus queue**로 설정 된 event grid 토픽에 대 한 이벤트 구독을 만듭니다. 또한 시스템 관리 id를 배달 못 한 편지 처리에 사용 하도록 지정 합니다. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="use-the-azure-cli---event-hubs"></a>Azure CLI 사용 Event Hubs 
이 섹션에서는 Azure CLI 사용 하 여 시스템 할당 id를 사용 하 여 이벤트 허브에 이벤트를 전달 하도록 설정 하는 방법에 대해 알아봅니다. ID는 **Azure Event Hubs 데이터 보낸 사람** 역할의 멤버여야 합니다. 또한 배달 못한 편지 처리에 사용되는 스토리지 계정에서 **Storage Blob 데이터 기여자** 역할의 멤버여야 합니다. 

#### <a name="define-variables"></a>변수 정의
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>배달에 관리 되는 id를 사용 하 여 이벤트 구독 만들기 
이 샘플 명령은 끝점 유형이 **Event Hubs**로 설정 된 event grid 토픽에 대 한 이벤트 구독을 만듭니다. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>배달 및 배달 못한 편지에 관리 id를 사용 하 여 이벤트 구독 만들기 
이 샘플 명령은 끝점 유형이 **Event Hubs**로 설정 된 event grid 토픽에 대 한 이벤트 구독을 만듭니다. 또한 시스템 관리 id를 배달 못 한 편지 처리에 사용 하도록 지정 합니다. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="use-the-azure-cli---azure-storage-queue"></a>Azure CLI Azure Storage 큐 사용 
이 섹션에서는 Azure CLI 사용 하 여 시스템에 할당 된 id를 사용 하 여 Azure Storage 큐에 이벤트를 전달 하도록 설정 하는 방법에 대해 알아봅니다. 이 ID는 스토리지 계정의 **Storage Blob 데이터 기여자** 역할의 멤버여야 합니다.

#### <a name="define-variables"></a>변수 정의  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>배달에 관리 되는 id를 사용 하 여 이벤트 구독 만들기 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>배달 및 배달 못한 편지에 관리 id를 사용 하 여 이벤트 구독 만들기 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```



## <a name="next-steps"></a>다음 단계
관리형 서비스 ID에 대한 자세한 내용은 [Azure 리소스용 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요. 
