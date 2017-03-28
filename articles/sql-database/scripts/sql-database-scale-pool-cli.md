---
title: "Azure CLI 스크립트 - 탄력적 풀 규모 조정 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - Elastic Database 풀 크기 조정"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: CLI
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/16/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 9125fd5a780322ea5bde5a688fa381f3fe116ce6
ms.lasthandoff: 03/17/2017

---

# <a name="scale-an-elastic-pool-in-azure-sql-database-using-the-azure-cli"></a>Azure SQL Database에서 Azure CLI를 사용하여 탄력적 풀 크기 조정

이 샘플 CLI는 탄력적 풀을 만들고 풀된 데이터베이스를 이동한 후 성능 수준을 변경합니다. 

필요한 경우 [Azure CLI 설치 가이드](https://docs.microsoft.com/cli/azure/install-azure-cli)에 있는 지침을 사용하여 Azure CLI를 설치한 다음, `az login`을 실행하여 Azure와 연결합니다.

이 샘플은 Bash 셸에서 작동합니다. Windows에서 Azure CLI 스크립트 실행과 관련된 옵션은 [Windows에서 Azure CLI 실행](../../virtual-machines/virtual-machines-windows-cli-options.md)을 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[기본](../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "풀 간에 데이터베이스 이동")]

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 논리 서버, SQL Database 및 방화벽 규칙을 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | SQL Database를 호스팅하는 논리 서버를 만듭니다. |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pools#create) | 논리 서버 내에서 Elastic Database 풀을 만듭니다. |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#create) | 논리 서버에 SQL Database를 만듭니다. |
| [az sql elastic-pools update](https://docs.microsoft.com/cli/azure/sql/elastic-pools#update) | Elastic Database 풀을 업데이트하고 이 예제에서 할당된 eDTU를 변경합니다. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 SQL Database CLI 스크립트 샘플은 [Azure SQL Database 설명서](../sql-database-cli-samples.md)에서 찾을 수 있습니다.

