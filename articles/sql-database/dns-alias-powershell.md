---
title: DNS 별칭용 파워쉘
description: New-AzSqlServerDNSAlias와 같은 PowerShell cmdlet을 사용하면 클라이언트 구성을 건드리지 않고도 새 클라이언트 연결을 다른 Azure SQL Database 서버로 리디렉션할 수 있습니다.
keywords: dns sql database
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: 9232a99ddd29201e6743c09455d79e9ba22b3b9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74420410"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>Azure SQL Database의 DNS 별칭에 대한 PowerShell

이 문서에서는 Azure SQL Database에 대한 DNS 별칭을 관리하는 방법을 보여 주는 PowerShell 스크립트를 제공합니다.

> [!NOTE]
> 이 문서는 Azure PowerShell Az 모듈 또는 Azure CLI를 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다.
>
> Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [Azure PowerShell Az 소개 모듈을](/powershell/azure/new-azureps-module-az)참조하십시오. 설치 지침은 [Azure PowerShell 설치](/powershell/azure/install-az-ps) 또는 Azure [CLI 설치를](/cli/azure/install-azure-cli)참조하십시오.

## <a name="dns-alias-in-connection-string"></a>연결 문자열의 DNS 별칭

특정 Azure SQL Database 서버에 연결하려면 SSMS(SQL Server Management Studio)와 같은 클라이언트가 실제 서버 이름 대신 DNS 별칭 이름을 제공할 수 있습니다. 다음 예제의 서버 문자열에서 별칭 *any-unique-alias-name*은 4개 노드 서버 문자열에서 점으로 구분된 첫 번째 노드를 대신합니다.

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>사전 요구 사항

이 문서에 제공된 데모 PowerShell 스크립트를 실행하려는 경우 다음과 같은 필수 조건이 적용됩니다.

- 무료 평가판의 경우 Azure 구독 및 계정은 [Azure 평가판을](https://azure.microsoft.com/free/) 참조하십시오.
- 두 개의 Azure SQL 데이터베이스 서버

## <a name="example"></a>예제

다음 코드 예제는 리터럴 값을 여러 변수에 할당하는 것으로 시작합니다.

코드를 실행하려면 자리 표시자 값을 편집하여 시스템의 실제 값과 일치합니다.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

사용되는 cmdlet은 다음과 같습니다.

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Azure SQL Database 서비스 시스템에서 DNS 별칭을 만듭니다. 별칭은 데이터베이스 서버 1을 나타냅니다.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): SQL DB 서버 1에 할당된 모든 별칭을 가져옵니다.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): 별칭이 참조하도록 구성된 서버 이름을 서버 1에서 서버 2로 수정합니다.
- [제거-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias)제거 : 별칭의 이름을 사용하여 데이터베이스 서버 2에서 별칭을 제거합니다.

설치 또는 업그레이드하려면 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

버전을 `Get-Module -ListAvailable Az` 찾으려면 *powershell\_ise.exe에서*사용합니다.

```powershell
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
Connect-AzAccount -SubscriptionName $subscriptionName;
$subscriptionId = Get-AzSubscription -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
New-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName `
    -Name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
Set-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -TargetServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName `
    -SourceServerResourceGroup $resourceGroupName -SourceServerName $sqlServerName `
    -SourceServerSubscriptionId $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
Remove-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName;
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

사용되는 명령은 다음과 같습니다.

- [az SQL 서버 dns-별칭 만들기](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Azure SQL Database 서비스 시스템에서 DNS 별칭을 만듭니다. 별칭은 데이터베이스 서버 1을 나타냅니다.
- [az SQL 서버 dns-별칭 표시](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): SQL DB 서버 1에 할당 된 모든 별칭을 가져옵니다.
- [az sql server dns-별칭 집합](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): 별칭이 참조하도록 구성된 서버 이름을 서버 1에서 서버 2로 수정합니다.
- [az sql server dns-별칭 삭제](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): 별칭의 이름을 사용하여 데이터베이스 서버 2에서 별칭을 제거합니다.

설치하거나 업그레이드하려면 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

```azurecli-interactive
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
az login -SubscriptionName $subscriptionName;
$subscriptionId = az account list[0].i -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
az sql server dns-alias create –-resource-group $resourceGroupName --server $sqlServerName `
    --name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
az sql server dns-alias show –-resource-group $resourceGroupName --server $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
az sql server dns-alias set –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName `
    --original-resource-group $resourceGroupName --original-server $sqlServerName `
    --original-subscription-id $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
az sql server dns-alias show –-resource-group $resourceGroupName2 --server $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
az sql server dns-alias delete –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName;
```

* * *

## <a name="next-steps"></a>다음 단계

SQL Database에 대한 DNS 별칭 기능에 대한 자세한 설명은 [Azure SQL 데이터베이스에 대한 DNS 별칭을](dns-alias-overview.md)참조하십시오.
