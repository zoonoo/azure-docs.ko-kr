---
title: "Azure PowerShell 스크립트 - bacpac에서 SQL Database로 가져오기 | Microsoft Docs"
description: "Azure PowerShell 스크립트 샘플 - PowerShell을 사용하여 bacpac에서 SQL Database로 가져오기"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: PowerShell
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/07/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 6ca1560d6d3fa313bd24cb6226cf1e78992af257
ms.lasthandoff: 03/10/2017

---

# <a name="import-from-a-bacpac-into-a-sql-database-using-powershell"></a>PowerShell을 사용하여 bacpac에서 SQL Database로 가져오기

이 샘플 PowerShell 스크립트는 bacpac에서 데이터베이스를 가져옵니다.  

이 스크립트를 실행하기 전에 `Add-AzureRmAccount` cmdlet을 사용하여 Azure와의 연결이 만들어졌는지 확인합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[기본](../../../powershell_scripts/sql-database/import-from-bacpac/import-from-bacpac.ps1 "SQL Database 만들기")]

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [New-AzureRmResourceGroup]() | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzureRmSqlServer]() | SQL Database를 호스팅하는 논리 서버를 만듭니다. |
| [New-AzureRmSqlServerFirewallRule]() | 입력한 IP 주소 범위의 서버에서 모든 SQL Database에 액세스할 수 있도록 방화벽 규칙을 만듭니다. |
| [New-AzureRmSqlDatabase]() | 논리 서버에 SQL Database를 만듭니다. |
| [Remove-AzureRmResourceGroup]() | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.

추가 SQL Database PowerShell 스크립트 샘플은 [Azure SQL Database PowerShell 스크립트](../sql-database-powershell-samples.md)에 있습니다.
