---
title: 감사 및 Advanced Threat Protection의 CLI 예제 - Azure SQL Database
description: Azure SQL Database에서 감사 및 Advanced Threat Protection을 구성하는 Azure CLI 예제 스크립트
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: security
ms.devlang: azurecli
ms.topic: sample
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 6791691be976e541c6400e89dde8f892fe50c6dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336176"
---
# <a name="use-cli-to-configure-sql-database-auditing-and-advanced-threat-protection"></a>CLI를 사용하여 SQL Database 감사 및 Advanced Threat Protection 구성

이 Azure CLI 스크립트 예제는 SQL Database 감사 및 Advanced Threat Protection을 구성합니다.

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

### <a name="sign-in-to-azure"></a>Azure에 로그인

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>스크립트 실행

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/database-auditing-and-threat-detection/database-auditing-and-threat-detection.sh "Configure auditing and threat detection")]

### <a name="clean-up-deployment"></a>배포 정리

다음 명령을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>샘플 참조

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 설명 |
|---|---|
| [az sql db audit-policy](/cli/azure/sql/db/audit-policy) | 데이터베이스에 대한 감사 정책을 설정합니다. |
| [az sql db threat-policy](/cli/azure/sql/db/threat-policy) | 데이터베이스에 대한 Advanced Threat Protection 정책을 설정합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 SQL Database CLI 스크립트 샘플은 [Azure SQL Database 설명서](../../azure-sql/database/az-cli-script-samples-content-guide.md)에서 찾을 수 있습니다.
