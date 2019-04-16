---
title: CLI 예제 Azure SQL Database 만들기 | Microsoft Docs
description: 다음 Azure CLI 예제 스크립트를 사용하여 SQL Database를 만듭니다.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: CarlRabeler
manager: craigg
ms.author: carlrab
ms.reviewer: ''
ms.date: 02/08/2019
ms.openlocfilehash: 9ed1512053c06108178275b0523beede470325a5
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361425"
---
# <a name="use-cli-to-create-a-single-azure-sql-database-and-configure-a-firewall-rule"></a>CLI를 사용하여 단일 Azure SQL Database 만들기 및 방화벽 규칙 구성

이 Azure CLI 스크립트 예제는 Azure SQL Database를 만들고 서버 수준 방화벽 규칙을 구성합니다. 스크립트가 성공적으로 실행되면 모든 Azure 서비스 및 구성된 IP 주소에서 SQL Database에 액세스할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh?highlight=9-10 "Create SQL Database")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az 그룹 만들기](/cli/azure/group#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az sql 서버 만들기](/cli/azure/sql/server#az-sql-server-create) | 단일 데이터베이스 또는 탄력적 풀을 호스트하는 SQL Database 서버를 만듭니다. |
| [az sql 서버 방화벽 만들기](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create) | 입력한 IP 주소 범위의 SQL Database 서버에 있는 모든 단일 데이터베이스와 탄력적 풀에 액세스가 가능하도록 방화벽 규칙을 만듭니다. |
| [az sql db 만들기](/cli/azure/sql/db#az-sql-db-create) | 단일 데이터베이스 또는 탄력적 풀을 만듭니다. |
| [az 그룹 삭제](/cli/azure/resource#az-resource-delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

추가 SQL Database CLI 스크립트 샘플은 [Azure SQL Database 설명서](../sql-database-cli-samples.md)에서 찾을 수 있습니다.