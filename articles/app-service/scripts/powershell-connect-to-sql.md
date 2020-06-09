---
title: 'PowerShell: SQL Database에 연결'
description: Azure PowerShell을 사용하여 App Service의 배포 및 관리를 자동화하는 방법을 알아봅니다. 이 샘플에서는 앱을 SQL Database에 연결하는 방법을 보여줍니다.
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: e050675282d8a7784d31aaf45a564da991ff6e63
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020033"
---
# <a name="connect-an-app-service-app-to-a-sql-database"></a>SQL Database에 App Service 앱 연결

이 시나리오에서는 Azure SQL 데이터베이스 및 App Service 앱을 만드는 방법을 알아봅니다. 그런 다음, 앱 설정을 사용하여 SQL Database를 앱에 연결합니다.

필요한 경우 [Azure PowerShell 가이드](/powershell/azure/overview)에 있는 지침을 사용하여 Azure PowerShell을 설치한 다음, `Connect-AzAccount`를 실행하여 Azure에 연결합니다.

## <a name="sample-script"></a>샘플 스크립트

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect an app to a SQL database")]

## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, App Service 앱 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | App Service 계획을 만듭니다. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | App Service 앱을 만듭니다. |
| [New-AzSQLServer](/powershell/module/az.sql/new-azsqlserver) | 서버를 만듭니다. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | 서버 수준 방화벽 규칙을 만듭니다. |
| [New-AzSQLDatabase](/powershell/module/az.sql/new-azsqldatabase) | 데이터베이스 또는 Elastic Database를 만듭니다. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | App Service 앱의 구성을 수정합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

Azure App Service에 대한 추가 Azure PowerShell 샘플은 [Azure PowerShell 샘플](../samples-powershell.md)에서 확인할 수 있습니다.
