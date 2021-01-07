---
title: Azure CLI 스크립트 - Azure Database for PostgreSQL 서버 복원
description: 이 샘플 Azure CLI 스크립트는 Azure Database for PostgreSQL 서버 및 해당 데이터베이스를 이전 시점으로 복원하는 방법을 보여 줍니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 02/28/2018
ms.openlocfilehash: 9af4a3d4a484ef22e147640add455741c533e9b7
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660477"
---
# <a name="restore-an-azure-database-for-postgresql-server-using-azure-cli"></a>Azure CLI를 사용하여 Azure Database for PostgreSQL 서버 복원
이 샘플 CLI 스크립트는 단일 Azure Database for PostgreSQL 서버를 이전 시점으로 복원합니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="sample-script"></a>샘플 스크립트
이 샘플 스크립트에서 강조 표시된 줄을 편집하여 관리자 사용자 이름 및 암호를 자신의 이름과 암호로 업데이트합니다. `az monitor` 명령에 사용된 구독 ID를 자신의 구독 ID로 바꿉니다.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/backup-restore.sh?highlight=15-16 "Restore Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>배포 정리
스크립트가 실행 된 후 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거합니다. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>스크립트 설명
이 스크립트에는 다음 표에 설명된 명령이 사용됩니다.

| **명령** | **참고** |
|---|---|
| [az group create](/cli/azure/group) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az postgresql server create](/cli/azure/postgres/server#az-postgres-server-create) | 데이터베이스를 호스팅하는 PostgreSQL 서버를 만듭니다. |
| [az postgresql server restore](/cli/azure/postgres/server#az-postgres-server-restore) | 백업에서 서버를 복원합니다. |
| [az group delete](/cli/azure/group) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계
- Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.
- 추가 스크립트 시도: [PostgreSQL용 Azure Database에 대한 Azure CLI 샘플](../sample-scripts-azure-cli.md)
- [Azure Portal을 사용하여 Azure Database for PostgreSQL에서 서버를 백업 및 복원하는 방법](../howto-restore-server-portal.md)
