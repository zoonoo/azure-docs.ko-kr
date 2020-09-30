---
title: 'Azure CLI: Azure SQL Database의 단일 데이터베이스 모니터링 및 크기 조정'
description: Azure CLI 예제 스크립트를 사용하여 Azure SQL Database의 단일 데이터베이스를 모니터링하고 크기를 조정합니다.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: sstein
ms.date: 06/25/2019
ms.openlocfilehash: c4df9ecc025bbffb63730273be06f54cf46f613c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319400"
---
# <a name="use-the-azure-cli-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>Azure CLI를 사용하여 Azure SQL Database의 단일 데이터베이스 모니터링 및 크기 조정

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

이 Azure CLI 스크립트 예제는 데이터베이스의 크기 정보를 쿼리한 후에 Azure SQL Database의 단일 데이터베이스를 다양한 컴퓨팅 크기로 확장합니다.

Azure CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

### <a name="sign-in-to-azure"></a>Azure에 로그인

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>스크립트 실행

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale a database in Azure SQL Database")]

> [!TIP]
> [az sql db op list](/cli/azure/sql/db/op?#az-sql-db-op-list)를 사용하여 데이터베이스에서 수행된 작업 목록을 가져오고 [az sql db op cancel](/cli/azure/sql/db/op#az-sql-db-op-cancel)을 사용하여 데이터베이스에서 업데이트 작업을 취소합니다.

### <a name="clean-up-deployment"></a>배포 정리

다음 명령을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>샘플 참조

이 스크립트는 다음 명령을 사용합니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 스크립트 | Description |
|---|---|
| [az sql server](/cli/azure/sql/server) | 서버 명령입니다. |
| [az sql db show-usage](/cli/azure/sql#az-sql-show-usage) | 데이터베이스의 크기 사용량 정보가 표시됩니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 CLI 스크립트 샘플은 [Azure CLI 샘플 스크립트](../az-cli-script-samples-content-guide.md)에서 찾을 수 있습니다.
