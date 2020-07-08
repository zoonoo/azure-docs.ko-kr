---
title: CLI를 사용 하 여 Azure Event Grid 시스템 항목 만들기, 보기 및 관리
description: 이 문서에서는 Azure CLI 사용 하 여 시스템 항목을 만들고, 확인 하 고, 삭제 하는 방법을 보여 줍니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: spelluru
ms.openlocfilehash: d9ba83d12e73b3ebceaee3167cdcf7f27922b686
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84885250"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>Azure CLI을 사용 하 여 Event Grid 시스템 항목 만들기, 보기 및 관리
이 문서에서는 Azure CLI를 사용 하 여 시스템 항목을 만들고 관리 하는 방법을 보여 줍니다. 시스템 항목의 개요는 [시스템 항목](system-topics.md)을 참조 하십시오.

## <a name="install-extension-for-azure-cli"></a>Azure CLI 확장 설치
Azure CLI의 경우 [Event Grid 확장](/cli/azure/azure-cli-extensions-list)이 필요합니다.

Cloud Shell:

- 이전에 확장을 설치한 경우 다음을 업데이트 합니다.`az extension update -n eventgrid`
- 이전에 확장을 설치 하지 않은 경우 설치 합니다.`az extension add -n eventgrid`

로컬 설치의 경우:

1. [Azure CLI를 설치합니다](/cli/azure/install-azure-cli). `az --version`으로 확인하여 최신 버전이 있는지 알아봅니다.
2. 이전 버전의 확장을 제거 합니다.`az extension remove -n eventgrid`
3. 를 사용 하 여 event grid 확장 설치`az extension add -n eventgrid`

## <a name="create-a-system-topic"></a>시스템 항목 만들기

- 먼저 Azure 원본에서 시스템 항목을 만든 다음 해당 토픽에 대 한 이벤트 구독을 만들려면 다음 참조 항목을 참조 하세요.
    - [az event grid 시스템-토픽 만들기](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-create)

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

        `topic-type`시스템 토픽을 만드는 데 사용할 수 있는 값 목록은 다음 명령을 실행 합니다. 이러한 토픽 유형 값은 시스템 항목 만들기를 지 원하는 이벤트 원본을 나타냅니다. `Microsoft.EventGrid.Topics`목록에서 및를 무시 하세요 `Microsoft.EventGrid.Domains` . 

        ```azurecli-interactive
        az eventgrid topic-type  list --output json | grep -w id
        ```
    - [az event grid system 토픽 이벤트-구독 만들기](/cli/azure/ext/eventgrid/eventgrid/system-topic/event-subscription?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-event-subscription-create)

        ```azurecli-interactive
        az eventgrid system-topic event-subscription create --name <SPECIFY EVENT SUBSCRIPTION NAME> \
            -g rg1 --system-topic-name <SYSTEM TOPIC NAME> \
            --endpoint <ENDPOINT URL>         
        ```
- Azure source에 대 한 이벤트 구독을 만들 때 시스템 항목을 암시적으로 만들려면 [az event grid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription?view=azure-cli-latest#ext-eventgrid-az-eventgrid-event-subscription-create) 메서드를 사용 합니다. 예를 들면 다음과 같습니다.
    
    ```azurecli-interactive
    storageid=$(az storage account show --name <AZURE STORAGE ACCOUNT NAME> --resource-group <AZURE RESOURCE GROUP NAME> --query id --output tsv)
    endpoint=<ENDPOINT URL>

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <EVENT SUBSCRIPTION NAME> \
      --endpoint $endpoint
    ```
    단계별 지침이 포함 된 자습서는 [저장소 계정 구독](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account)을 참조 하세요.

## <a name="view-all-system-topics"></a>모든 시스템 항목 보기
선택한 시스템 항목의 모든 시스템 항목 및 세부 정보를 보려면 다음 명령을 사용 합니다.

- [az event grid system-항목 목록](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-list)

    ```azurecli-interactive
    az eventgrid system-topic list   
     ```
- [az event grid system-토픽 show](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-show)

    ```azurecli-interactive
    az eventgrid system-topic show -g <AZURE RESOURCE GROUP NAME> -n <SYSTEM TOPIC NAME>     
     ```

## <a name="delete-a-system-topic"></a>시스템 항목 삭제
시스템 항목을 삭제 하려면 다음 명령을 사용 합니다. 

- [az event grid 시스템 항목 삭제](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-delete)

    ```azurecli-interactive
    az eventgrid system-topic delete -g <AZURE RESOURCE GROUP NAME> --name <SYSTEM TOPIC NAME>   
     ```

## <a name="next-steps"></a>다음 단계
Azure Event Grid에서 지 원하는 시스템 항목과 항목 유형에 대 한 자세한 내용은 [Azure Event Grid의 시스템 항목](system-topics.md) 을 참조 하십시오. 