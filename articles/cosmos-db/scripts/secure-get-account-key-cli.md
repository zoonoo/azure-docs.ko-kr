---
title: "Azure CLI 스크립트 - Azure Cosmos DB에 대한 계정 키 가져오기 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - Azure Cosmos DB의 계정 키 가져오기"
services: cosmos-db
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 21da79a674a55fad910b0b059508acc026f8bd84
ms.contentlocale: ko-kr
ms.lasthandoff: 05/15/2017

---

# <a name="get-account-keys-for-azure-cosmos-db-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure Cosmos DB의 계정 키 가져오기

이 샘플은 모든 종류의 Azure Cosmos DB 계정의 계정 키를 가져옵니다.  

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[기본](../../../cli_scripts/cosmosdb/scale-cosmosdb-get-account-key/secure-cosmosdb-get-account-key.sh?highlight=22-25 "Azure Cosmos DB 계정 키 가져오기")]

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
| [az cosmosdb list-keys](/cli/azure/sql/server#create) | SQL Database를 호스팅하는 논리 서버를 만듭니다. |
| [az group delete](/cli/azure/resource#delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 Azure Cosmos DB CLI 스크립트 샘플은 [Azure Cosmos DB CLI 설명서](../cli-samples.md)에서 확인할 수 있습니다.

