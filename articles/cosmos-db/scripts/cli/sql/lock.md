---
title: Azure Cosmos DB Core(SQL) API 데이터베이스 및 컨테이너에 대한 리소스 잠금 만들기
description: Azure Cosmos DB Core(SQL) API 데이터베이스 및 컨테이너에 대한 리소스 잠금 만들기
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 92912475b6420c0fd3ffc3a31cefb269475431f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87431802"
---
# <a name="create-resource-lock-for-a-azure-cosmos-db-core-sql-api-database-and-container-using-azure-cli"></a>Azure CLI를 사용하여 Azure Cosmos DB SQL(Core) API 데이터베이스 및 컨테이너에 대한 리소스 잠금 만들기

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.9.1 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

> [!IMPORTANT]
> Cosmos DB SDK를 사용하여 연결하는 사용자가 만든 변경 내용, 계정 키를 통해 연결하는 모든 도구 또는 `disableKeyBasedMetadataWriteAccess` 속성을 사용하도록 설정하여 Cosmos DB 계정이 처음 잠겨 있지 않는 한 Azure Portal에서 리소스 잠금이 작동하지 않습니다. 이 속성을 사용하도록 설정하는 방법에 대한 자세한 내용은[Sdk변경 방지](../../../role-based-access-control.md#prevent-sdk-changes)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/lock.sh "Create a resource lock for an Azure Cosmos DB Core (SQL) API database and container.")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az lock create](/cli/azure/lock#az-lock-create) | 잠금을 만듭니다. |
| [az lock list](/cli/azure/lock#az-lock-list) | 잠금 정보를 나열합니다. |
| [az lock show](/cli/azure/lock#az-lock-show) | 잠금의 속성을 표시합니다. |
| [az lock delete](/cli/azure/lock#az-lock-delete) | 잠금을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

-[예기치 않은 변경을 방지하기 위해 리소스 잠그기](../../../../azure-resource-manager/management/lock-resources.md)

-[Azure Cosmos DB CLI 설명서](/cli/azure/cosmosdb).

-[Azure Cosmos DB CLI GitHub 리포지토리](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
