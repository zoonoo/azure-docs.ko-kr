---
title: "CLI 예제 SQL 탄력적 풀 크기 조정 - Azure SQL Database | Microsoft Docs"
description: "Azure SQL Database에서 SQL 탄력적 풀의 크기를 조정하는 Azure CLI 예제 스크립트"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 06/23/2017
ms.author: janeng
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: abceba7d250622d8cf27e41d3bb37f3d8a995b5f
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="use-azure-cli-to-scale-a-sql-elastic-pool-in-azure-sql-database"></a>Azure CLI를 사용하여 Azure SQL Database에서 SQL 탄력적 풀의 크기 조정

이 Azure CLI 스크립트 예제는 SQL 탄력적 풀을 만들고 풀된 Database를 이동한하며 탄력적 풀 성능 수준을 변경합니다. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[기본](../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "풀 간에 데이터베이스 이동")]

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 논리 서버, SQL Database 및 방화벽 규칙을 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | SQL Database를 호스팅하는 논리 서버를 만듭니다. |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pool#create) | 논리 서버 내에서 Elastic Database 풀을 만듭니다. |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#create) | 논리 서버에 SQL Database를 만듭니다. |
| [az sql elastic-pools update](https://docs.microsoft.com/cli/azure/sql/elastic-pool#update) | Elastic Database 풀을 업데이트하고 이 예제에서 할당된 eDTU를 변경합니다. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 SQL Database CLI 스크립트 샘플은 [Azure SQL Database 설명서](../sql-database-cli-samples.md)에서 찾을 수 있습니다.

