---
title: "Azure CLI 스크립트 - Azure Cosmos DB 컨테이너 처리량 확장 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - Azure Cosmos DB 컨테이너 처리량 확장"
services: cosmosdb
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmosdb
ms.custom: sample
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 04/20/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: cfa23a21a10f53a042b3a5cd85265b0ed1130d07
ms.contentlocale: ko-kr
ms.lasthandoff: 05/15/2017

---

# <a name="scale-azure-cosmos-db-container-throughput-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure Cosmos DB 컨테이너 처리량 확장

이 샘플에서는 모든 종류의 Azure Cosmos DB 컨테이너에 대한 컨테이너 처리량을 확장합니다.  

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[기본](../../../cli_scripts/cosmosdb/scale-cosmosdb-throughput/scale-cosmosdb-throughput.sh?highlight=40-46 "Azure Cosmos DB 처리량 확장")]

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az group create](/cli/azure/group#create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az cosmosdb update](/cli/azure/cosmosdb/name#update) | Azure Cosmos DB 계정을 업그레이드합니다. |
| [az group delete](/cli/azure/resource#delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 Azure Cosmos DB CLI 스크립트 샘플은 [Azure Cosmos DB CLI 설명서](../cli-samples.md)에서 확인할 수 있습니다.

