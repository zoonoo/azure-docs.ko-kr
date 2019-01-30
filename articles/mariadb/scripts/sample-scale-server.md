---
title: Azure CLI 스크립트 - Azure Database for MariaDB 서버 크기 조정
description: 이 샘플 CLI 스크립트는 메트릭을 쿼리한 후에 Azure Database for MariaDB 서버를 다양한 성능 수준으로 확장합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 11/28/2018
ms.openlocfilehash: ef01c38dddee0a74477ef331f935bda2787866df
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53546372"
---
# <a name="monitor-and-scale-an-azure-database-for-mariadb-server-using-azure-cli"></a>Azure CLI를 사용하여 Azure Database for MariaDB 서버 모니터링 및 확장
이 샘플 CLI 스크립트는 메트릭을 쿼리한 후에 단일 Azure Database for MariaDB 서버를 다양한 성능 수준으로 확장합니다.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 실행하도록 선택한 경우 이 문서에 Azure CLI 버전 2.0 이상이 필요합니다. `az --version`을 실행하여 버전을 확인합니다. Azure CLI 버전을 설치하거나 업그레이드하려면 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="sample-script"></a>샘플 스크립트
이 샘플 스크립트에서 강조 표시된 줄을 편집하여 관리자 사용자 이름 및 암호를 자신의 이름과 암호로 업데이트합니다. `az monitor` 명령에 사용된 구독 ID를 자신의 구독 ID로 바꿉니다. [!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/scale-mariadb-server.sh?highlight=15-16 "Create and scale Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>배포 정리
스크립트가 실행 된 후 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거합니다. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>스크립트 설명
이 스크립트에는 다음 표에 설명된 명령이 사용됩니다.

| **명령** | **참고 사항** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | 데이터베이스를 호스팅하는 MariaDB 서버를 만듭니다. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | 리소스에 대한 메트릭 값을 나열합니다. |
| [az group delete](/cli/azure/group#az-group-delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계
- Azure CLI에 대한 자세한 내용: [Azure CLI 설명서](/cli/azure)
- 추가 스크립트 시도: [Azure Database for MariaDB용 Azure CLI 샘플](../sample-scripts-azure-cli.md)
- 크기 조정에 대한 자세한 내용은 [가격 책정 계층](../concepts-pricing-tiers.md)을 참조하세요.
