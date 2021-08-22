---
title: CLI를 사용하여 Azure Event Grid 시스템 토픽 만들기, 보기 및 관리
description: 이 문서에서는 Azure CLI를 사용하여 시스템 토픽을 만들고, 보고, 삭제하는 방법을 보여 줍니다.
ms.topic: conceptual
ms.date: 07/22/2021
ms.openlocfilehash: e8ea2e1351b5faf93e58d5a96561555050ce05b6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469420"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>Azure CLI를 사용하여 Event Grid 시스템 토픽 만들기, 보기 및 관리
이 문서는 Azure CLI를 사용하여 시스템 토픽을 만들고 관리하는 방법을 보여 줍니다. 시스템 토픽의 개요는 [시스템 토픽](system-topics.md)을 참조하세요.

## <a name="install-extension-for-azure-cli"></a>Azure CLI 확장 설치
Azure CLI의 경우 [Event Grid 확장](/cli/azure/azure-cli-extensions-list)이 필요합니다.

Cloud Shell에서

- 이전에 확장을 설치한 경우 `az extension update -n eventgrid`로 업데이트합니다.
- 이전에 확장을 설치하지 않은 경우 `az extension add -n eventgrid`로 업데이트합니다.

로컬 설치의 경우:

1. [Azure CLI를 설치합니다](/cli/azure/install-azure-cli). `az --version`으로 확인하여 최신 버전이 있는지 알아봅니다.
2. 이전 버전의 확장 `az extension remove -n eventgrid`를 제거합니다.
3. `az extension add -n eventgrid`를 사용하여 eventgrid 확장을 설치합니다.

## <a name="create-a-system-topic"></a>시스템 토픽 만들기

- 먼저 Azure 원본에서 시스템 토픽을 만든 다음, 해당 토픽에 대한 이벤트 구독을 만들려면 다음 참조 토픽을 참조하세요.
    - [az eventgrid system-topic create](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_create)

        ```azurecli-interactive
        # Get the ID of the Azure source (for example: Azure Storage account)
        storageid=$(az storage account show \
                --name <AZURE STORAGE ACCOUNT NAME> \
                --resource-group <AZURE RESOURCE GROUP NAME> \
                    --query id --output tsv)
    
        # Create the system topic on the Azure source (example: Azure Storage account)
        az eventgrid system-topic create \
            -g <AZURE RESOURCE GROUP NAME> \
            --name <SPECIFY SYSTEM TOPIC NAME> \
            --location <LOCATION> \
            --topic-type microsoft.storage.storageaccounts \
            --source $storageid
        ```           

        시스템 토픽을 만드는 데 사용할 수 있는 `topic-type` 값 목록은 다음 명령을 실행합니다. 이러한 토픽 유형 값은 시스템 토픽 만들기를 지원하는 이벤트 원본을 나타냅니다. 목록에서 `Microsoft.EventGrid.Topics` 및 `Microsoft.EventGrid.Domains`를 무시하세요. 

        ```azurecli-interactive
        az eventgrid topic-type  list --output json | grep -w id
        ```
    - [az eventgrid system-topic event-subscription create](/cli/azure/eventgrid/system-topic/event-subscription#az_eventgrid_system_topic_event-subscription-create)

        ```azurecli-interactive
        az eventgrid system-topic event-subscription create --name <SPECIFY EVENT SUBSCRIPTION NAME> \
            -g rg1 --system-topic-name <SYSTEM TOPIC NAME> \
            --endpoint <ENDPOINT URL>         
        ```
- Azure 원본에 대한 이벤트 구독을 만들 때 시스템 토픽을 암시적으로 만들려면 [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) 메서드를 사용합니다. 예를 들면 다음과 같습니다.
    
    ```azurecli-interactive
    storageid=$(az storage account show --name <AZURE STORAGE ACCOUNT NAME> --resource-group <AZURE RESOURCE GROUP NAME> --query id --output tsv)
    endpoint=<ENDPOINT URL>

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <EVENT SUBSCRIPTION NAME> \
      --endpoint $endpoint
    ```
    단계별 지침이 포함된 자습서는 [스토리지 계정 구독](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account)을 참조하세요.

## <a name="view-all-system-topics"></a>모든 시스템 토픽 보기
모든 시스템 토픽 및 선택한 시스템 토픽의 세부 정보를 보려면 다음 명령을 사용합니다.

- [az eventgrid system-topic list](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_list)

    ```azurecli-interactive
    az eventgrid system-topic list   
     ```
- [az eventgrid system-topic show](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_show)

    ```azurecli-interactive
    az eventgrid system-topic show -g <AZURE RESOURCE GROUP NAME> -n <SYSTEM TOPIC NAME>     
     ```

## <a name="delete-a-system-topic"></a>시스템 토픽 삭제
시스템 토픽을 삭제하려면 다음 명령을 사용합니다. 

- [az eventgrid system-topic delete](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_delete)

    ```azurecli-interactive
    az eventgrid system-topic delete -g <AZURE RESOURCE GROUP NAME> --name <SYSTEM TOPIC NAME>   
     ```

## <a name="next-steps"></a>다음 단계
Azure Event Grid에서 지원되는 시스템 토픽 및 토픽 유형에 대한 자세한 정보는 [Azure Event Grid의 시스템 토픽](system-topics.md) 섹션을 참조하세요. 