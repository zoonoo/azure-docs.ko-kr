---
title: "Azure CLI 스크립트 - 서버 구성 변경 | Microsoft Docs"
description: "이 샘플 CLI 스크립트는 모든 사용 가능한 서버 구성을 나열하고 innodb_lock_wait_timeout의 값을 업데이트 합니다."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 11/03/2017
ms.openlocfilehash: 286b16148d28509714862714dcc5986496d9af44
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2017
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Azure CLI를 사용하여 MySQL 서버용 Azure Database의 구성 나열 및 업데이트
이 샘플 CLI 스크립트는 사용 가능한 모든 구성 매개 변수와 MySQL 서버용 Azure Database에 대해 허용되는 값을 나열하고 *innodb_lock_wait_timeout*을 기본값 이외의 값으로 설정합니다.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="sample-script"></a>샘플 스크립트
이 샘플 스크립트에서 강조 표시된 줄을 변경하여 관리자 사용자 이름 및 암호를 사용자 지정합니다.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>배포 정리
스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>스크립트 설명
이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| **명령** | **참고 사항** |
|---|---|
| [az group create](/cli/azure/group#create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az mysql server create](/cli/azure/mysql/server#create) | 데이터베이스를 호스팅하는 MySQL 서버를 만듭니다. |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#list) | MySQL 서버용 Azure Database의 구성을 나열합니다. |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#set) | MySQL 서버용 Azure Database의 구성을 업데이트합니다. |
| [az mysql server configuration show](/cli/azure/mysql/server/configuration#show) | MySQL 서버용 Azure Database의 구성을 보여 줍니다. |
| [az group delete](/cli/azure/group#delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계
- Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure/overview)를 참조하세요.
- 추가 스크립트 시도: [MySQL용 Azure 데이터베이스 대한 Azure CLI 샘플](../sample-scripts-azure-cli.md)
- 서버 매개 변수에 대한 자세한 내용은 [MySQL용 Azure Database에서 서버 매개 변수 구성 방법](../howto-server-parameters.md)을 참조하세요.
