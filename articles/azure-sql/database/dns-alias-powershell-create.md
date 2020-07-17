---
title: DNS 별칭 (PowerShell & Azure CLI)
description: PowerShell 및 Azure CLI cmdlet을 사용 하면 클라이언트 구성을 건드리지 않고도 새 클라이언트 연결을 Azure의 다른 SQL server로 리디렉션할 수 있습니다.
keywords: dns sql database
ms.custom: seo-lt-2019 sqldbrb=1
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: 3acbdd5c933bc2010a26e2039ece378774055d30
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84038764"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>Azure SQL Database의 DNS 별칭에 대한 PowerShell
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

이 문서에서는 Azure SQL Database을 호스트 하는 [SQL server](logical-servers.md) 에 대 한 DNS 별칭을 관리 하는 방법을 보여 주는 PowerShell 스크립트를 제공 합니다.

> [!NOTE]
> 이 문서는 Azure PowerShell Az module 또는 Azure CLI를 사용 하도록 업데이트 되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다.
>
> Az module 및 AzureRM compatibility에 대해 자세히 알아보려면 [Azure PowerShell az Module 소개](/powershell/azure/new-azureps-module-az)를 참조 하세요. 설치 지침은 [Azure PowerShell 설치](/powershell/azure/install-az-ps) 또는 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조 하세요.

## <a name="dns-alias-in-connection-string"></a>연결 문자열의 DNS 별칭

[논리 SQL server](logical-servers.md)를 연결 하기 위해 SSMS (SQL Server Management Studio)와 같은 클라이언트는 실제 서버 이름 대신 DNS 별칭 이름을 제공할 수 있습니다. 다음 예제의 서버 문자열에서 별칭 *any-unique-alias-name*은 4개 노드 서버 문자열에서 점으로 구분된 첫 번째 노드를 대신합니다.

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>사전 요구 사항

이 문서에 제공된 데모 PowerShell 스크립트를 실행하려는 경우 다음과 같은 필수 조건이 적용됩니다.

- Azure 구독 및 계정, 무료 평가판의 경우 [azure 평가판](https://azure.microsoft.com/free/) 을 참조 하세요.
- 두 대의 서버

## <a name="example"></a>예제

다음 코드 예제에서는 여러 변수에 리터럴 값을 할당 하 여를 시작 합니다.

코드를 실행 하려면 시스템의 실제 값과 일치 하도록 자리 표시자 값을 편집 합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

사용 되는 cmdlet은 다음과 같습니다.

- [AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Azure SQL Database 서비스 시스템에 DNS 별칭을 만듭니다. 별칭은 서버 1을 참조 합니다.
- [AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): 서버 1에 할당 된 모든 별칭을 가져오고 나열 합니다.
- [AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): 별칭이 참조 하도록 구성 된 서버 이름을 서버 1에서 서버 2로 수정 합니다.
- [AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): 별칭 이름을 사용 하 여 서버 2에서 별칭을 제거 합니다.

설치 또는 업그레이드하려면 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

`Get-Module -ListAvailable Az` *Powershell \_ise.exe*에서를 사용 하 여 버전을 찾습니다.

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

사용 되는 명령은 다음과 같습니다.

- [az sql server dns-alias create](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): 서버에 대 한 dns 별칭을 만듭니다. 별칭은 서버 1을 참조 합니다.
- [az sql server dns-alias show](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): 서버 1에 할당 된 모든 별칭을 가져오고 나열 합니다.
- [az sql server dns-alias set](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): 별칭이 구성 된 서버 이름을 서버 1에서 서버 2로 수정 합니다.
- [az sql server dns-alias delete](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): 별칭 이름을 사용 하 여 서버 2에서 별칭을 제거 합니다.

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

SQL Database에 대 한 DNS 별칭 기능에 대 한 자세한 내용은 [Azure SQL Database에 대 한 dns 별칭](../../sql-database/dns-alias-overview.md)을 참조 하세요.
