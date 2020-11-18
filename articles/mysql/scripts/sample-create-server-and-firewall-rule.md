---
title: CLI 스크립트 - 서버 만들기 - Azure Database for MySQL
description: 이 샘플 CLI 스크립트는 MySQL 서버용 Azure Database를 만들고 서버 수준 방화벽 규칙을 구성합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.custom: mvc, devx-track-azurecli
ms.topic: sample
ms.date: 12/02/2019
ms.openlocfilehash: 6e87771867c8001e64b214544a7bb3dcf68ff2b5
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94539399"
---
# <a name="create-a-mysql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Azure CLI를 사용하여 MySQL 서버 만들기 및 방화벽 규칙 구성
이 샘플 CLI 스크립트는 MySQL 서버용 Azure Database를 만들고 서버 수준 방화벽 규칙을 구성합니다. 스크립트가 성공적으로 실행되면 모든 Azure 서비스 및 구성된 IP 주소에서 MySQL 서버에 액세스할 수 있습니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다. 

## <a name="sample-script"></a>샘플 스크립트
이 샘플 스크립트에서 강조 표시된 줄을 편집하여 관리자 사용자 이름 및 암호를 자신의 이름과 암호로 업데이트합니다.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/create-mysql-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for MySQL, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>배포 정리
스크립트가 실행 된 후 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거합니다. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/delete-mysql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>스크립트 설명
이 스크립트에는 다음 표에 설명된 명령이 사용됩니다.

| **명령** | **참고** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | 데이터베이스를 호스팅하는 MySQL 서버를 만듭니다. |
| [az mysql server firewall create](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create) | 입력한 IP 주소 범위의 서버에서 서버 및 서버의 데이터베이스에 액세스할 수 있도록 방화벽 규칙을 만듭니다. |
| [az group delete](/cli/azure/group#az-group-delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계
- Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.
- 추가 스크립트 시도: [MySQL용 Azure Database에 대한 Azure CLI 샘플](../sample-scripts-azure-cli.md)
