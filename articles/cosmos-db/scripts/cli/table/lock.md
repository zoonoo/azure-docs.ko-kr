---
title: Azure Cosmos DB Table API 테이블에 대한 리소스 잠금 만들기
description: Azure Cosmos DB Table API 테이블에 대한 리소스 잠금 만들기
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 6df5f3842ba08b04dd82910d3762472f4ff4febd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94566812"
---
# <a name="create-resource-lock-for-a-azure-cosmos-db-table-api-table-using-azure-cli"></a>Azure CLI를 사용하여 Azure Cosmos DB Table API 테이블에 대한 리소스 잠금 만들기
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.9.1 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

> [!IMPORTANT]
> `disableKeyBasedMetadataWriteAccess` 속성을 사용하도록 설정된 상태에서 먼저 Cosmos DB 계정이 잠겨 있지 않는 한 Cosmos DB Table SDK, Azure Storage Table SDK, 계정 키를 통해 연결되는 모든 도구 또는 Azure Portal을 연결하는 사용자가 변경한 내용에 대해서는 리소스 잠금이 작동하지 않습니다. 이 속성을 사용하도록 설정하는 방법에 대한 자세한 내용은[Sdk변경 방지](../../../role-based-access-control.md#prevent-sdk-changes)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/lock.sh "Create a resource lock for an Azure Cosmos DB Table API table.")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az lock create](/cli/azure/lock#az-lock-create) | 잠금을 만듭니다. |
| [az lock list](/cli/azure/lock#az-lock-list) | 잠금 정보를 나열합니다. |
| [az lock show](/cli/azure/lock#az-lock-show) | 잠금의 속성을 표시합니다. |
| [az lock delete](/cli/azure/lock#az-lock-delete) | 잠금을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

- [예기치 않은 변경을 방지하기 위해 리소스 잠그기](../../../../azure-resource-manager/management/lock-resources.md)

- [Azure Cosmos DB CLI 설명서](/cli/azure/cosmosdb).

- [Azure Cosmos DB CLI GitHub 리포지토리](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
