---
title: Azure Cosmos DB Core(SQL) API 리소스에 대한 처리량(RU/s) 작업을 위한 Azure CLI 스크립트
description: Azure Cosmos DB Core(SQL) API 리소스에 대한 처리량(RU/s) 작업을 위한 Azure CLI 스크립트
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 5ad1e40f12609e708a2bb818175146967b7dccf2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91839472"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-container-for-azure-cosmos-db-core-sql-api"></a>데이터베이스용 Azure CLI 또는 Azure Cosmos DB Core(SQL) API용 컨테이너를 사용한 처리량(RU/s) 작업

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.12.1 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

이 스크립트는 공유 처리량이 있는 Core(SQL) API 데이터베이스와 전용 처리량이 있는 Core(SQL) API 컨테이너를 만든 다음, 데이터베이스와 컨테이너 모두에 대한 처리량을 업데이트합니다. 그런 다음, 이 스크립트는 표준에서 자동 크기 조정 처리량으로 마이그레이션한 다음, 마이그레이션된 후 자동 크기 조정 처리량 값을 읽습니다.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/throughput.sh "Throughput operations for a SQL database and container.")]

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
| [az cosmosdb sql database create](/cli/azure/cosmosdb/sql/database#az-cosmosdb-sql-database-create) | Azure Cosmos Core(SQL) 데이터베이스를 만듭니다. |
| [az cosmosdb sql container create](/cli/azure/cosmosdb/sql/container#az-cosmosdb-sql-container-create) | Azure Cosmos Core(SQL) 컨테이너를 만듭니다. |
| [az cosmosdb sql database throughput update](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-update) | Azure Cosmos Core(SQL) 데이터베이스에 대한 처리량을 업데이트합니다. |
| [az cosmosdb sql container throughput update](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-update) | Azure Cosmos Core(SQL) 컨테이너에 대한 처리량을 업데이트합니다. |
| [az cosmosdb sql database throughput migrate](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-migrate) | Azure Cosmos Core(SQL) 데이터베이스에 대한 처리량을 마이그레이션합니다. |
| [az cosmosdb sql container throughput migrate](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-migrate) | Azure Cosmos Core(SQL) 컨테이너에 대한 처리량을 마이그레이션합니다. |
| [az group delete](/cli/azure/resource#az-resource-delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB CLI에 대한 자세한 내용은 [Azure Cosmos DB CLI 설명서](/cli/azure/cosmosdb)를 참조하세요.

모든 Azure Cosmos DB CLI 스크립트 샘플은 [Azure Cosmos DB CLI GitHub 리포지토리](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)에서 확인할 수 있습니다.
