---
title: Azure Cosmos DB용 Cassandra 키스페이스 및 테이블에 대한 RU/s 업데이트
description: Azure Cosmos DB용 Cassandra 키스페이스 및 테이블에 대한 RU/s 업데이트
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 3615157543d826050b8adf0e6ae59bf62ad9cb1b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "71827310"
---
# <a name="update-rus-for-a-cassandra-keyspace-and-table-using-azure-cli"></a>Azure CLI를 사용하여 Cassandra 키스페이스 및 테이블에 대한 RU/s 업데이트

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0.73 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

이 스크립트는 공유 처리량이 있는 Cassandra 키스페이스와 전용 처리량이 있는 Cassandra 테이블을 만든 다음, 키스페이스와 테이블 모두에 대한 처리량을 업데이트합니다.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/throughput.sh "Update RU/s for Cassandra keyspace and table.")]

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Azure Cosmos DB 계정을 만듭니다. |
| [az cosmosdb cassandra keyspace create](/cli/azure/cosmosdb/cassandra/keyspace#az-cosmosdb-cassandra-keyspace-create) | Azure Cosmos Cassandra 키스페이스를 만듭니다. |
| [az cosmosdb cassandra table create](/cli/azure/cosmosdb/cassandra/table#az-cosmosdb-cassandra-table-create) | Azure Cosmos Cassandra 테이블을 만듭니다. |
| [az cosmosdb cassandra keyspace throughput update](/cli/azure/cosmosdb/cassandra/keyspace/throughput?view=azure-cli-latest#az-cosmosdb-cassandra-keyspace-throughput-update) | Azure Cosmos Cassandra 키스페이스에 대한 RU/s를 업데이트합니다. |
| [az cosmosdb cassandra table throughput update](/cli/azure/cosmosdb/cassandra/table/throughput#az-cosmosdb-cassandra-table-throughput-update) | Azure Cosmos Cassandra 테이블에 대한 RU/s를 업데이트합니다. |
| [az group delete](/cli/azure/resource#az-resource-delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB CLI에 대한 자세한 내용은 [Azure Cosmos DB CLI 설명서](/cli/azure/cosmosdb)를 참조하세요.

모든 Azure Cosmos DB CLI 스크립트 샘플은 [Azure Cosmos DB CLI GitHub 리포지토리](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)에서 확인할 수 있습니다.
