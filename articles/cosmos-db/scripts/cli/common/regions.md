---
title: 영역 추가, 장애 조치(failover) 우선 순위 변경, Azure Cosmos 계정에 대한 장애 조치(failover) 트리거
description: 영역 추가, 장애 조치(failover) 우선 순위 변경, Azure Cosmos 계정에 대한 장애 조치(failover) 트리거
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: b7b6be0ce781debcb19b5c0fb7b6a4b0123ef366
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275110"
---
# <a name="add-regions-change-failover-priority-trigger-failover-for-an-azure-cosmos-account-using-azure-cli"></a>Azure CLI를 사용하여 영역 추가, 장애 조치(failover) 우선 순위 변경, Azure Cosmos 계정에 대한 장애 조치(failover) 트리거

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0.73 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

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
