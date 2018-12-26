---
title: Azure CLI 스크립트 - 서버 구성 변경
description: 이 샘플 CLI 스크립트는 모든 사용 가능한 서버 구성을 나열하고 innodb_lock_wait_timeout의 값을 업데이트 합니다.
services: mariadb
author: ajlam
ms.author: andrela
editor: jasonwhowell
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 11/28/2018
ms.openlocfilehash: a2104b4a2a80dc7ca3f76edb6757a59fcc6c7a4f
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52585198"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Azure CLI를 사용하여 Azure Database for MariaDB 서버의 구성 나열 및 업데이트
이 샘플 CLI 스크립트는 사용 가능한 모든 구성 매개 변수와 Azure Database for MariaDB 서버에 허용되는 값을 나열하고 *innodb_lock_wait_timeout*을 기본값 이외의 값으로 설정합니다.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 실행하도록 선택한 경우 이 문서에 Azure CLI 버전 2.0 이상이 필요합니다. `az --version`을 실행하여 버전을 확인합니다. Azure CLI 버전을 설치하거나 업그레이드하려면 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="sample-script"></a>샘플 스크립트
이 샘플 스크립트에서 강조 표시된 줄을 편집하여 관리자 사용자 이름 및 암호를 자신의 이름과 암호로 업데이트합니다.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>배포 정리
스크립트가 실행 된 후 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거합니다.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>스크립트 설명
이 스크립트에는 다음 표에 설명된 명령이 사용됩니다.

| **명령** | **참고 사항** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | 데이터베이스를 호스팅하는 MariaDB 서버를 만듭니다. |
| [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | Azure Database for MariaDB 서버의 구성을 나열합니다. |
| [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Azure Database for MariaDB 서버의 구성을 업데이트합니다. |
| [az mariadb server configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) | Azure Database for MariaDB 서버의 구성을 표시합니다. |
| [az group delete](/cli/azure/group#az-group-delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계
- Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.
- 추가 스크립트 시도: [Azure Database for MariaDB용 Azure CLI 샘플](../sample-scripts-azure-cli.md)
- 서버 매개 변수에 대한 자세한 내용은 [Azure Database for MariaDB에서 서버 매개 변수 구성 방법](../howto-server-parameters.md)을 참조하세요.
