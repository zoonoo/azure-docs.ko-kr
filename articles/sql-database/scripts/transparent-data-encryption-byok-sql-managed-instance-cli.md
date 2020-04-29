---
title: CLI 예제-BYOK TDE를 사용 하도록 설정 Azure SQL Database Managed Instance
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80061728"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Azure Key Vault의 사용자 고유 키를 사용하여 Managed Instance에서 투명한 데이터 암호화 관리

이 Azure CLI 스크립트 예제에서는 Azure Key Vault의 키를 사용 하 여 Azure SQL Managed Instance의 고객 관리 키를 사용 하 여 TDE (투명한 데이터 암호화)를 구성 합니다. 이를 종종 TDE에 대 한 Bring Your Own Key 시나리오 라고 합니다. 고객 관리 키를 사용 하 여 TDE에 대해 자세히 알아보려면 [AZURE SQL에 대 한 tde Bring Your Own Key를](../transparent-data-encryption-byok-azure-sql.md)참조 하세요.

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

### <a name="prerequisites"></a>전제 조건

기존 Managed Instance [Azure CLI를 사용 하 여 Azure SQL Database 관리 되는 인스턴스 만들기를](sql-database-create-configure-managed-instance-cli.md)참조 하세요.

### <a name="sign-in-to-azure"></a>Azure에 로그인

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>스크립트 실행

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.sh "Set up BYOK TDE for SQL Managed Instance")]

### <a name="clean-up-deployment"></a>배포 정리

다음 명령을 사용 하 여 리소스 그룹 및 관련 된 모든 리소스를 제거 합니다.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>샘플 참조

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| | |
|---|---|
| [az sql db](/cli/azure/sql/db) | 데이터베이스 명령입니다. |
| [az sql 장애 조치 (failover)-그룹](/cli/azure/sql/failover-group) | 장애 조치 (Failover) 그룹 명령입니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 SQL Database CLI 스크립트 샘플은 [Azure SQL Database 설명서](../sql-database-cli-samples.md)에서 찾을 수 있습니다.
