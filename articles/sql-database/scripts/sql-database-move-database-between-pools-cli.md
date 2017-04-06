---
title: "Azure CLI 스크립트 - SQL Database 및 탄력적 풀 이동 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - Azure CLI를 사용하여 탄력적 풀 간에 SQL Database 이동"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 04/04/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 15d8f075a21c335de862dc004fc4e6a47d8bc38b
ms.lasthandoff: 03/30/2017

---

# <a name="create-elastic-pools-and-move-databases-between-pools-and-out-of-a-pool-using-the-azure-cli"></a>Azure CLI를 사용하여 탄력적 풀 만들기 및 풀 간에 데이터베이스 이동

이 샘플 CLI 스크립트는 두 개의 탄력적 풀을 만들고 한 탄력적 풀에서 다른 탄력적 풀로 데이터베이스를 이동한 다음 탄력적 풀에서 단일 데이터베이스 성능 수준으로 데이터베이스를 이동합니다. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[기본](../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "풀 간에 데이터베이스 이동")]

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | 데이터베이스 또는 탄력적 풀을 호스트하는 논리 서버를 만듭니다. |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pools#create) | 논리 서버 내에 탄력적 풀을 만듭니다. |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#create) | 논리 서버에 데이터베이스를 단일 데이터베이스 또는 풀링된 데이터베이스로 만듭니다. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#update) | 데이터베이스 속성을 업데이트하거나 탄력적 풀로/에서 또는 탄력적 풀 간에 데이터베이스를 이동합니다. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 SQL Database CLI 스크립트 샘플은 [Azure SQL Database 설명서](../sql-database-cli-samples.md)에서 찾을 수 있습니다.



