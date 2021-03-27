---
title: 이벤트 배달, 관리 서비스 id 및 개인 링크
description: 이 문서에서는 Azure Event Grid 토픽에 대해 관리형 서비스 ID를 사용하도록 설정하는 방법을 설명합니다. 이 ID를 사용하여 지원되는 대상에 이벤트를 전달합니다.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 76f10b4627dc9578b1e616a868eab03431b59b69
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625281"
---
# <a name="event-delivery-with-a-managed-identity"></a>관리 id를 사용 하 여 이벤트 배달
이 문서에서는 Azure event grid 시스템 토픽, 사용자 지정 토픽 또는 도메인에 대해 [관리 서비스 id](../active-directory/managed-identities-azure-resources/overview.md) 를 사용 하는 방법을 설명 합니다. 이 ID를 사용하여 Service Bus 큐 및 토픽, Event Hubs, 스토리지 계정 등의 지원되는 대상으로 이벤트를 전달합니다.



## <a name="prerequisites"></a>전제 조건
1. 시스템에 할당 된 id를 시스템 토픽, 사용자 지정 항목 또는 도메인에 할당 합니다. 
    - 사용자 지정 항목 및 도메인은 [사용자 지정 항목 및 도메인에 대해 관리 되는 Id 사용](enable-identity-custom-topics-domains.md)을 참조 하세요. 
    - 시스템 항목은 [시스템에 관리 되는 Id 사용 항목](enable-identity-system-topics.md) 을 참조 하세요.
1. 대상의 적절 한 역할 Service Bus (예: Service Bus 큐)에 id를 추가 합니다. 자세한 단계는 [대상의 Azure 역할에 Id 추가](add-identity-roles.md) 를 참조 하세요.

    > [!NOTE]
    > 현재 [개인 끝점](../private-link/private-endpoint-overview.md)을 사용 하 여 이벤트를 전달할 수 없습니다. 자세한 내용은이 문서의 끝에 있는 [개인 끝점](#private-endpoints) 섹션을 참조 하세요. 

## <a name="create-event-subscriptions-that-use-an-identity"></a>Id를 사용 하는 이벤트 구독 만들기
시스템 관리 id를 사용 하는 event grid 사용자 지정 토픽 또는 시스템 토픽 또는 도메인을 사용 하 고 대상의 적절 한 역할에 id를 추가한 후에는 해당 id를 사용 하는 구독을 만들 준비가 된 것입니다. 

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
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>배달에 관리 되는 id를 사용 하 여 이벤트 구독 만들기 
이 샘플 명령은 끝점 유형이 **Service Bus queue** 로 설정 된 event grid 사용자 지정 항목에 대 한 이벤트 구독을 만듭니다. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery-and-dead-lettering"></a>배달 및 배달 못 한 편지에 관리 되는 id를 사용 하 여 이벤트 구독 만들기
이 샘플 명령은 끝점 유형이 **Service Bus queue** 로 설정 된 event grid 사용자 지정 항목에 대 한 이벤트 구독을 만듭니다. 또한 시스템 관리 id를 배달 못 한 편지 처리에 사용 하도록 지정 합니다. 

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
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>배달에 관리 되는 id를 사용 하 여 이벤트 구독 만들기 
이 샘플 명령은 끝점 유형이 **Event Hubs** 로 설정 된 event grid 사용자 지정 항목에 대 한 이벤트 구독을 만듭니다. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>배달 및 배달 못한 편지에 관리 id를 사용 하 여 이벤트 구독 만들기 
이 샘플 명령은 끝점 유형이 **Event Hubs** 로 설정 된 event grid 사용자 지정 항목에 대 한 이벤트 구독을 만듭니다. 또한 시스템 관리 id를 배달 못 한 편지 처리에 사용 하도록 지정 합니다. 

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
이 섹션에서는 Azure CLI 사용 하 여 시스템에 할당 된 id를 사용 하 여 Azure Storage 큐에 이벤트를 전달 하도록 설정 하는 방법에 대해 알아봅니다. Id는 저장소 계정에서 **저장소 큐 데이터 메시지 보낸 사람** 역할의 멤버 여야 합니다. 또한 배달 못한 편지 처리에 사용되는 스토리지 계정에서 **Storage Blob 데이터 기여자** 역할의 멤버여야 합니다.

#### <a name="define-variables"></a>변수 정의  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

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

## <a name="private-endpoints"></a>프라이빗 엔드포인트
현재 [개인 끝점](../private-link/private-endpoint-overview.md)을 사용 하 여 이벤트를 전달할 수 없습니다. 즉, 전달 된 이벤트 트래픽이 개인 IP 공간을 떠나지 않아야 하는 엄격한 네트워크 격리 요구 사항이 있는 경우에는 지원 되지 않습니다. 

그러나 요구 사항에 따라 암호화 된 채널을 사용 하 여 이벤트를 전송 하는 안전한 방법을 호출 하 고, 공용 IP 공간을 사용 하는 보낸 사람 (이 경우에는 Event Grid)의 알려진 id를 사용 하는 경우 Azure Event Grid 사용자 지정 토픽 또는이 문서에 표시 된 대로 구성 된 시스템 관리 id의 도메인을 사용 하 여 Event Hubs, Service Bus 또는 Azure Storage 서비스에 이벤트를 그런 다음 Azure Functions에 구성 된 개인 링크나 가상 네트워크에 배포 된 웹 후크를 사용 하 여 이벤트를 끌어올 수 있습니다. 샘플: Azure Functions를 [사용 하 여 전용 끝점에 연결](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/)을 참조 하세요.

이 구성에서 트래픽은 공용 IP/인터넷을 통해 Event Grid에서 Event Hubs, Service Bus 또는 Azure Storage로 이동 하지만 채널을 암호화 하 고 Event Grid의 관리 되는 id를 사용할 수 있습니다. 개인 링크를 통해 Event Hubs, Service Bus 또는 Azure Storage를 사용 하도록 가상 네트워크에 배포 된 Azure Functions 또는 webhook를 구성 하는 경우 해당 트래픽 섹션은 Azure 내에서 분명히 됩니다.


## <a name="next-steps"></a>다음 단계
관리 id에 대 한 자세한 내용은 [Azure 리소스에 대 한 관리 되는 Id 란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조 하세요.
