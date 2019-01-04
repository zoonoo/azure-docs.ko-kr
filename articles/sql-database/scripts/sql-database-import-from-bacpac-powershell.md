---
title: PowerShell 예제 BACPAC 파일 Azure SQL Database 가져오기 | Microsoft Docs
description: SQL Database로 BACPAC 파일을 가져오는 Azure PowerShell 예제 스크립트
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: load & move data
ms.devlang: PowerShell
ms.topic: sample
author: douglaslMS
ms.author: douglasl
ms.reviewer: carlrab
manager: craigg
ms.date: 09/07/2018
ms.openlocfilehash: fdea8511036fc3c1d0298fa132de27c126917580
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/20/2018
ms.locfileid: "53654005"
---
# <a name="use-powershell-to-import-a-bacpac-file-into-an-azure-sql-database"></a>PowerShell을 사용하여 BACPAC 파일을 Azure SQL Database에 가져오기

이 PowerShell 스크립트 예제는 BACPAC 파일의 데이터베이스를 Azure SQL Database로 가져옵니다.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에서는 Azure PowerShell 모듈 버전이 5.7.0 이상이어야 합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/import-from-bacpac/import-from-bacpac.ps1?highlight=18-19 "Create SQL Database")]

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | SQL Database를 호스팅하는 논리 서버를 만듭니다. |
| [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) | 입력한 IP 주소 범위의 서버에서 모든 SQL Database에 액세스할 수 있도록 방화벽 규칙을 만듭니다. |
| [New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) | BACPAC 파일을 가져오고 서버에 새 데이터베이스를 만듭니다. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 SQL Database PowerShell 스크립트 샘플은 [Azure SQL Database PowerShell 스크립트](../sql-database-powershell-samples.md)에 있습니다.
