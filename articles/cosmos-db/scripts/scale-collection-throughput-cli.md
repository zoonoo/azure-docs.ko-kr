---
title: Azure CLI 스크립트 - Azure Cosmos DB 컨테이너 처리량 확장 | Microsoft Docs
description: Azure CLI 스크립트 샘플 - Azure Cosmos DB 컨테이너 처리량 확장
services: cosmos-db
documentationcenter: cosmosdb
author: SnehaGunda
manager: kfile
tags: azure-service-management
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 05/23/2018
ms.author: sngun
ms.openlocfilehash: d830d8150066acd17cbfca6a05156b33ade240a2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39437817"
---
# <a name="scale-azure-cosmos-db-container-throughput-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure Cosmos DB 컨테이너 처리량 확장

이 샘플에서는 모든 종류의 Azure Cosmos DB 컨테이너에 대한 컨테이너 처리량을 확장합니다.  

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/scale-cosmosdb-throughput/scale-cosmosdb-throughput.sh?highlight=40-46 "Scale Azure Cosmos DB throughput")]

위의 샘플 스크립트를 사용하여 고정 컬렉션을 만들고 확장할 수 있습니다. 저장 용량이 무제한인 컬렉션을 만들고 확장하려면 다음을 수행해야 합니다. 
 
* 1000RU/s 이상을 지원하는 컬렉션을 만들고 
* 컬렉션을 만들 때 파티션 키를 지정해야 합니다. 

다음 명령은 저장 용량이 무제한인 컬렉션을 만드는 예제를 보여 줍니다.

```cli
az cosmosdb collection create \
    --collection-name $collectionName \
    --name $name \
    --db-name $databaseName \
    --resource-group $resourceGroupName \
    --throughput 1000
    --partition-key-path /deviceId

```

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az cosmosdb update](https://docs.microsoft.com/cli/azure/cosmosdb#az-cosmosdb-update) | Azure Cosmos DB 계정을 업그레이드합니다. |
| [az group delete](https://docs.microsoft.com/cli/azure/group#az-group-delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

추가 Azure Cosmos DB CLI 스크립트 샘플은 [Azure Cosmos DB CLI 설명서](../cli-samples.md)에서 확인할 수 있습니다.
