---
title: CLI 예제 - BYOK TDE 사용 - Azure SQL 데이터베이스 관리 인스턴스
description: PowerShell을 사용하여 저장 데이터 암호화에 BYOK TDE(투명한 데이터 암호화)를 사용하도록 Azure SQL Managed Instance를 구성하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: azurecli
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: 8e8c0e2db1f87cca52c44d33ce14d7ce4f00e895
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061728"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Azure Key Vault의 사용자 고유 키를 사용하여 Managed Instance에서 투명한 데이터 암호화 관리

이 Azure CLI 스크립트 예제에서는 Azure 키 자격 증명 모음의 키를 사용하여 Azure SQL 관리 인스턴스에 대한 고객 관리 키로 투명 데이터 암호화(TDE)를 구성합니다. 이를 TDE에 대한 사용자 고유의 키 가져오기 시나리오라고도 합니다. 고객 관리 키가 있는 TDE에 대해 자세히 알아보려면 [Azure SQL에 사용자 고유키 가져오기](../transparent-data-encryption-byok-azure-sql.md)를 참조하십시오.

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

### <a name="prerequisites"></a>사전 요구 사항

기존 관리되는 인스턴스는 [Azure CLI 를 사용하여 Azure SQL Database 관리 인스턴스를 만듭니다.](sql-database-create-configure-managed-instance-cli.md)

### <a name="sign-in-to-azure"></a>Azure에 로그인

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>스크립트 실행

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.sh "Set up BYOK TDE for SQL Managed Instance")]

### <a name="clean-up-deployment"></a>배포 정리

다음 명령을 사용하여 리소스 그룹과 연결된 모든 리소스를 제거합니다.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>샘플 참조

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| | |
|---|---|
| [아즈 SQL DB](/cli/azure/sql/db) | 데이터베이스 명령. |
| [az sql 장애 조치 그룹](/cli/azure/sql/failover-group) | 장애 조치 그룹 명령입니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 SQL Database CLI 스크립트 샘플은 [Azure SQL Database 설명서](../sql-database-cli-samples.md)에서 찾을 수 있습니다.
