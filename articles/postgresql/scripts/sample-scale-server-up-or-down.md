---
title: Azure CLI 스크립트 - Azure Database for PostgreSQL 크기 조정 및 모니터링
description: Azure CLI 스크립트 샘플 - 메트릭을 쿼리한 후에 PostgreSQL 서버용 Azure Database를 다양한 성능 수준으로 확장합니다.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc, devx-track-azurecli
ms.topic: sample
ms.date: 08/07/2019
ms.openlocfilehash: d5f08e764ae44b11ab5838689e7f8a70c89b3122
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708631"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Azure CLI를 사용하여 단일 PostgreSQL 서버 모니터링 및 확장
이 샘플 CLI 스크립트는 메트릭을 쿼리한 후에 단일 Azure Database for PostgreSQL 서버에 대한 컴퓨팅 및 스토리지를 확장합니다. 컴퓨팅을 확장하거나 축소할 수 있습니다. 스토리지는 확장만 가능합니다. 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 실행하도록 선택한 경우 이 문서에 Azure CLI 버전 2.0 이상이 필요합니다. `az --version`을 실행하여 버전을 확인합니다. Azure CLI 버전을 설치하거나 업그레이드하려면 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트
구독 ID로 스크립트를 업데이트합니다.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>배포 정리
스크립트가 실행 된 후 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거합니다. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>스크립트 설명
이 스크립트에는 다음 표에 설명된 명령이 사용됩니다.

| **명령** | **참고 사항** |
|---|---|
| [az group create](/cli/azure/group) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az postgres server create](/cli/azure/postgres/server#az-postgres-server-create) | 데이터베이스를 호스팅하는 PostgreSQL 서버를 만듭니다. |
| [az postgres server update](/cli/azure/postgres/server#az-postgres-server-update) | PostgreSQL 서버의 속성을 업데이트합니다. |
| [az monitor metrics list](/cli/azure/monitor/metrics) | 리소스에 대한 메트릭 값을 나열합니다. |
| [az group delete](/cli/azure/group) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계
- [Azure Database for PostgreSQL 컴퓨팅 및 스토리지](../concepts-pricing-tiers.md)에 대해 자세히 알아보기
- 추가 스크립트 시도: [PostgreSQL용 Azure 데이터베이스 대한 Azure CLI 샘플](../sample-scripts-azure-cli.md)
- [Azure CLI](/cli/azure)에 대한 자세한 정보
