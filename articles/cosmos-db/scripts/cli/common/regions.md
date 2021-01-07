---
title: 영역 추가, 장애 조치(failover) 우선 순위 변경, Azure Cosmos 계정에 대한 장애 조치(failover) 트리거
description: 영역 추가, 장애 조치(failover) 우선 순위 변경, Azure Cosmos 계정에 대한 장애 조치(failover) 트리거
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 0ae724f9900eea713af7d295eba26bf03cdf471a
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563242"
---
# <a name="add-regions-change-failover-priority-trigger-failover-for-an-azure-cosmos-account-using-azure-cli"></a>Azure CLI를 사용하여 영역 추가, 장애 조치(failover) 우선 순위 변경, Azure Cosmos 계정에 대한 장애 조치(failover) 트리거
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.9.1 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="sample-script"></a>샘플 스크립트

이 스크립트는 세 가지 작업을 보여 줍니다.

- 기존 Azure Cosmos 계정에 지역을 추가합니다.
- 지역별 장애 조치(failover) 우선 순위 변경(자동 장애 조치(failover)를 사용하는 계정에 적용)
- 주 지역에서 보조 지역으로 수동 장애 조치(failover) 트리거(수동 장애 조치(failover)를 사용하는 계정에 적용)

> [!NOTE]
> 다른 속성을 변경하는 동안에는 Cosmos 계정에 대한 지역 추가 및 제거 작업을 수행할 수 없습니다.

> [!NOTE]
> 이 샘플에서는 SQL(Core) API 계정을 사용하는 방법을 보여 주지만 이러한 작업은 Cosmos DB의 모든 데이터베이스 API에서 동일합니다.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/regions.sh "Regional operations for Cosmos DB.")]

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
| [az cosmosdb update](/cli/azure/cosmosdb#az-cosmosdb-update) | Azure Cosmos DB 계정을 업데이트합니다(지역 추가 또는 제거). |
| [az cosmosdb failover-priority-change](/cli/azure/cosmosdb#az-cosmosdb-failover-priority-change) | 장애 조치(failover) 우선 순위를 업데이트하거나 Azure Cosmos DB 계정에서 장애 조치(failover)를 트리거합니다. |
| [az group delete](/cli/azure/resource#az-resource-delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB CLI에 대한 자세한 내용은 [Azure Cosmos DB CLI 설명서](/cli/azure/cosmosdb)를 참조하세요.

모든 Azure Cosmos DB CLI 스크립트 샘플은 [Azure Cosmos DB CLI GitHub 리포지토리](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)에서 확인할 수 있습니다.
