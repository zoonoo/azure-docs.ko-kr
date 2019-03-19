---
title: Azure SQL의 단일 및 풀링된 데이터베이스에 대한 VNet 엔드포인트 및 규칙을 위한 PowerShell | Microsoft Docs
description: Azure SQL Database 및 SQL Data Warehouse에 대한 가상 서비스 엔드포인트를 만들고 관리할 수 있는 PowerShell 스크립트를 제공합니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: genemi, vanto
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 6713182003a280c1d53e904209159b55b4ad01c6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57855574"
---
# <a name="powershell--create-a-virtual-service-endpoint-and-vnet-rule-for-sql"></a>PowerShell:  SQL에 대한 가상 서비스 엔드포인트 및 VNet 규칙 만들기

*가상 네트워크 규칙*은 Azure [SQL Database](sql-database-technical-overview.md)의 단일 데이터베이스 및 탄력적 풀 또는 [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)의 데이터베이스에 대한 데이터베이스 서버가 가상 네트워크의 특정 서브넷에서 보낸 통신을 수락할지 여부를 제어하는 하나의 방화벽 보안 기능입니다.

> [!IMPORTANT]
> 이 문서는 Azure SQL 서버 및 Azure SQL 서버에서 생성된 SQL Database와 SQL Data Warehouse 데이터베이스에 적용됩니다. 간단히 하기 위해 SQL Database는 SQL Database와 SQL Data Warehouse를 참조할 때 사용됩니다. Azure SQL Database의 **관리되는 인스턴스** 배포는 서비스 엔드포인트와 연결되지 않으므로 이 문서는 해당 배포에 적용되지 *않습니다*.

이 문서에서는 다음 작업을 수행하는 PowerShell 스크립트를 제공하고 설명합니다.

1. 서브넷에서 Microsoft Azure *가상 서비스 엔드포인트*를 만듭니다.
2. Azure SQL Database 서버의 방화벽에 엔드포인트를 추가하여 *가상 네트워크 규칙*을 만듭니다.

규칙을 만드는 동기는 [Azure SQL Database에 대한 가상 서비스 엔드포인트][sql-db-vnet-service-endpoint-rule-overview-735r]에서 설명하고 있습니다.

> [!TIP]
> 서브넷에서 SQL Database에 대한 가상 서비스 엔드포인트 *형식 이름*을 평가하거나 추가하기만 할 경우에는 더 많은 [직접 PowerShell 스크립트](#a-verify-subnet-is-endpoint-ps-100)로 바로 건너뛸 수 있습니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database, Azure Resource Manager PowerShell 모듈은 계속 지원 하지만 Az.Sql 모듈에 대 한 모든 향후 개발 됩니다. 이러한 cmdlet에 대 한 참조 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)합니다. Az 모듈에는 AzureRm 모듈의 명령에 대 한 인수를 실질적으로 동일합니다.

## <a name="major-cmdlets"></a>주요 cmdlet

이 문서에서는 합니다 **새로 만들기-AzSqlServerVirtualNetworkRule** 규칙을 만드는 Azure SQL Database 서버의 서브넷 끝점 액세스 제어 목록 (ACL)에 추가 하는 cmdlet입니다.

다음은 다른 순서를 보여 줍니다 *주요* 대 한 호출을 준비 하기 위해 실행 해야 하는 cmdlet **새로 만들기-AzSqlServerVirtualNetworkRule**합니다. 이 문서에서 이러한 호출은 [스크립트 3 “가상 네트워크 규칙”](#a-script-30)에서 수행됩니다.

1. [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig): 서브넷 개체를 만듭니다.
2. [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork): 가상 네트워크에 서브넷을 제공하는 가상 네트워크를 만듭니다.
3. [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig): 서브넷에 가상 서비스 엔드포인트를 할당합니다.
4. [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetwork): 가상 네트워크에 대한 업데이트를 유지합니다.
5. [New-AzSqlServerVirtualNetworkRule](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlservervirtualnetworkrule): 서브넷이 엔드포인트가 된 후 서브넷을 가상 네트워크 규칙으로 Azure SQL Database 서버의 ACL에 추가합니다.
   - 이 cmdlet은 Azure RM PowerShell 모듈 버전 5.1.1에서 시작하는 **-IgnoreMissingVNetServiceEndpoint** 매개 변수를 제공합니다.

## <a name="prerequisites-for-running-powershell"></a>PowerShell을 실행하기 위한 필수 구성 요소

- [Azure Portal][http-azure-portal-link-ref-477t] 등을 통해 Azure에 이미 로그인할 수 있습니다.
- 이미 PowerShell 스크립트를 실행할 수 있습니다.

> [!NOTE]
> 서버에 추가할 VNet/서브넷에 대해 서비스 엔드포인트가 설정되어 있는지 확인하세요. 그렇지 않으면 VNet 방화벽 규칙 만들기는 실패하게 됩니다.

## <a name="one-script-divided-into-four-chunks"></a>4개 청크로 구분된 하나의 스크립트

데모 PowerShell 스크립트는 더 작은 스크립트 시퀀스로 구분됩니다. 구분을 통해 쉽게 알아볼 수 있고 유연성이 제공됩니다. 스크립트는 지정된 시퀀스로 실행되어야 합니다. 지금 스크립트를 실행할 시간이 없는 경우 실제 테스트 출력이 스크립트 4 뒤에 표시되어 있습니다.

<a name="a-script-10" />

### <a name="script-1-variables"></a>스크립트 1: variables

이 첫 번째 PowerShell 스크립트는 값을 변수에 할당합니다. 후속 스크립트는 이러한 변수에 따라 결정됩니다.

> [!IMPORTANT]
> 이 스크립트를 실행하기 전에 필요한 경우 값을 편집할 수 있습니다. 예를 들어 이미 리소스 그룹이 있는 경우 리소스 그룹 이름을 할당된 값으로 편집할 수 있습니다.
>
> 구독 이름은 스크립트로 편집되어야 합니다.

### <a name="powershell-script-1-source-code"></a>PowerShell 스크립트 1 소스 코드

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.

$SubscriptionName = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName $SubscriptionName;

$ResourceGroupName = 'RG-YourNameHere';
$Region            = 'westcentralus';

$VNetName            = 'myVNet';
$SubnetName          = 'mySubnet';
$VNetAddressPrefix   = '10.1.0.0/16';
$SubnetAddressPrefix = '10.1.1.0/24';
$VNetRuleName        = 'myFirstVNetRule-ForAcl';

$SqlDbServerName         = 'mysqldbserver-forvnet';
$SqlDbAdminLoginName     = 'ServerAdmin';
$SqlDbAdminLoginPassword = 'ChangeYourAdminPassword1';

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Official type name.

Write-Host 'Completed script 1, the "Variables".';
```

<a name="a-script-20" />

### <a name="script-2-prerequisites"></a>스크립트 2: 필수 조건

이 스크립트는 엔드포인트 작업을 나타내는 다음 스크립트를 준비합니다. 이 스크립트는 다음과 같은 나열된 항목이 없는 경우 해당 항목을 만듭니다. 이러한 항목이 이미 있다고 확신할 경우 스크립트 2를 건너뛸 수 있습니다.

- Azure 리소스 그룹
- Azure SQL Database 서버

### <a name="powershell-script-2-source-code"></a>PowerShell 스크립트 2 소스 코드

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists.";

$gottenResourceGroup = $null;

$gottenResourceGroup = Get-AzResourceGroup `
  -Name        $ResourceGroupName `
  -ErrorAction SilentlyContinue;

if ($null -eq $gottenResourceGroup)
{
    Write-Host "Creating your missing Resource Group - $ResourceGroupName.";

    $gottenResourceGroup = New-AzResourceGroup `
      -Name $ResourceGroupName `
      -Location $Region;

    $gottenResourceGroup;
}
else { Write-Host "Good, your Resource Group already exists - $ResourceGroupName."; }

$gottenResourceGroup = $null;

###########################################################
## Ensure your Azure SQL Database server already exists. ##
###########################################################

Write-Host "Check whether your Azure SQL Database server already exists.";

$sqlDbServer = $null;

$sqlDbServer = Get-AzSqlServer `
  -ResourceGroupName $ResourceGroupName `
  -ServerName        $SqlDbServerName `
  -ErrorAction       SilentlyContinue;

if ($null -eq $sqlDbServer)
{
    Write-Host "Creating the missing Azure SQL Database server - $SqlDbServerName.";

    Write-Host "Gather the credentials necessary to next create an Azure SQL Database server.";

    $sqlAdministratorCredentials = New-Object `
      -TypeName     System.Management.Automation.PSCredential `
      -ArgumentList `
        $SqlDbAdminLoginName, `
        $(ConvertTo-SecureString `
            -String      $SqlDbAdminLoginPassword `
            -AsPlainText `
            -Force `
         );

    if ($null -eq $sqlAdministratorCredentials)
    {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending.";
        return;
    }

    Write-Host "Create your Azure SQL Database server.";

    $sqlDbServer = New-AzSqlServer `
      -ResourceGroupName $ResourceGroupName `
      -ServerName        $SqlDbServerName `
      -Location          $Region `
      -SqlAdministratorCredentials $sqlAdministratorCredentials;

    $sqlDbServer;
}
else { Write-Host "Good, your Azure SQL Database server already exists - $SqlDbServerName."; }

$sqlAdministratorCredentials = $null;
$sqlDbServer                 = $null;

Write-Host 'Completed script 2, the "Prerequisites".';
```

<a name="a-script-30" />

## <a name="script-3-create-an-endpoint-and-a-rule"></a>스크립트 3: 엔드포인트 및 규칙 만들기

이 스크립트는 서브넷이 있는 가상 네트워크를 만듭니다. 그런 다음 **Microsoft.Sql** 엔드포인트 형식을 서브넷에 할당합니다. 마지막으로 이 스크립트는 규칙을 만드는 데 사용되는 SQL Database 서버의 ACL(액세스 제어 목록)에 서브넷을 추가합니다.

### <a name="powershell-script-3-source-code"></a>PowerShell 스크립트 3 소스 코드

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network.";

$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Create a virtual network '$VNetName'." `
  "  Give the subnet to the virtual network that we created.";

$vnet = New-AzVirtualNetwork `
  -Name              $VNetName `
  -AddressPrefix     $VNetAddressPrefix `
  -Subnet            $subnet `
  -ResourceGroupName $ResourceGroupName `
  -Location          $Region;

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.";

$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Persist the updates made to the virtual network > subnet.";

$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.";

$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnet.Id;

$vnetRuleObject1;

Write-Host "Verify that the rule is in the SQL DB ACL.";

$vnetRuleObject2 = Get-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName;

$vnetRuleObject2;

Write-Host 'Completed script 3, the "Virtual-Network-Rule".';
```

<a name="a-script-40" />

## <a name="script-4-clean-up"></a>스크립트 4: 정리

이 마지막 스크립트는 이전 스크립트를 데모용으로 만든 리소스를 삭제합니다. 그러나 이 스크립트는 다음 항목을 삭제하기 전에 확인 메시지를 표시합니다.

- Azure SQL Database 서버
- Azure 리소스 그룹

스크립트 1이 완료된 후 언제든지 스크립트 4를 실행할 수 있습니다.

### <a name="powershell-script-4-source-code"></a>PowerShell 스크립트 4 소스 코드

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL DB ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL DB ACL.";

Remove-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -ErrorAction            SilentlyContinue;

Write-Host "Delete the endpoint from the subnet.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

Remove-AzVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Delete the virtual network (thus also deletes the subnet).";

Remove-AzVirtualNetwork `
  -Name              $VNetName `
  -ResourceGroupName $ResourceGroupName `
  -ErrorAction       SilentlyContinue;

###########################################################
##   Clean-up phase B:  Conditional deletes.             ##
##                                                       ##
##   These might have already existed, so user might     ##
##   want to keep.                                       ##
##                                                       ##
##   1. Azure SQL Database server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]';
if ('yes' -eq $yesno)
{
    Write-Host "Remove the Azure SQL DB server.";

    Remove-AzSqlServer `
      -ServerName        $SqlDbServerName `
      -ResourceGroupName $ResourceGroupName `
      -ErrorAction       SilentlyContinue;

    Write-Host "Remove the Azure Resource Group.";

    Remove-AzResourceGroup `
      -Name        $ResourceGroupName `
      -ErrorAction SilentlyContinue;
}
else
{
    Write-Host "Skipped over the DELETE of SQL Database and resource group.";
}

Write-Host 'Completed script 4, the "Clean-Up".';
```

<a name="a-actual-output" />

## <a name="actual-output-from-scripts-1-through-4"></a>스크립트 1~4의 실제 출력

다음에 테스트 실행의 출력이 요약된 형식으로 표시됩니다. 지금 실제로 PowerShell 스크립트를 실행하지 않으려는 경우 출력이 도움이 될 수 있습니다.

```cmd
[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s1-variables.ps1
Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]: yes


Environment           : AzureCloud
Account               : xx@microsoft.com
TenantId              : 11111111-1111-1111-1111-111111111111
SubscriptionId        : 22222222-2222-2222-2222-222222222222
SubscriptionName      : MySubscriptionName
CurrentStorageAccount :



[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s2-prerequisites.ps1
Check whether your Resource Group already exists.
Creating your missing Resource Group - RG-YourNameHere.


ResourceGroupName : RG-YourNameHere
Location          : westcentralus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/22222222-2222-2222-2222-222222222222/resourceGroups/RG-YourNameHere

Check whether your Azure SQL Database server already exists.
Creating the missing Azure SQL Database server - mysqldbserver-forvnet.
Gather the credentials necessary to next create an Azure SQL Database server.
Create your Azure SQL Database server.

ResourceGroupName        : RG-YourNameHere
ServerName               : mysqldbserver-forvnet
Location                 : westcentralus
SqlAdministratorLogin    : ServerAdmin
SqlAdministratorPassword :
ServerVersion            : 12.0
Tags                     :
Identity                 :

Completed script 2, the "Prerequisites".

[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s3-vnet-rule.ps1
Define a subnet 'mySubnet', to be given soon to a virtual network.
Create a virtual network 'myVNet'.   Give the subnet to the virtual network that we created.
WARNING: The output object type of this cmdlet will be modified in a future release.
Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
Persist the updates made to the virtual network > subnet.

Get the subnet object.
Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.
ProvisioningState Service       Locations
----------------- -------       ---------
Succeeded         Microsoft.Sql {westcentralus}

Verify that the rule is in the SQL DB ACL.

Completed script 3, the "Virtual-Network-Rule".

[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s4-clean-up.ps1
Delete the rule from the SQL DB ACL.

Delete the endpoint from the subnet.


Delete the virtual network (thus also deletes the subnet).
CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]: yes
Remove the Azure SQL DB server.

ResourceGroupName        : RG-YourNameHere
ServerName               : mysqldbserver-forvnet
Location                 : westcentralus
SqlAdministratorLogin    : ServerAdmin
SqlAdministratorPassword :
ServerVersion            : 12.0
Tags                     :
Identity                 :

Remove the Azure Resource Group.
True
Completed script 4, the "Clean-Up".
```

기본 PowerShell 스크립트를 마쳤습니다.

<a name="a-verify-subnet-is-endpoint-ps-100" />

## <a name="verify-your-subnet-is-an-endpoint"></a>서브넷이 엔드포인트인지 확인

이미 가상 서비스 엔드포인트임을 의미하는 **Microsoft.Sql** 형식 이름이 할당된 서브넷이 있을 수 있습니다. [Azure Portal][http-azure-portal-link-ref-477t]을 사용하여 엔드포인트에서 가상 네트워크 규칙을 만들 수 있습니다.

또는 서브넷에 **Microsoft.Sql** 형식 이름이 있는지 확실하지 않을 수 있습니다. 다음 PowerShell 스크립트를 실행하여 이러한 작업을 수행합니다.

1. 서브넷에 **Microsoft.Sql** 형식 이름이 있는지 확인합니다.
2. 선택적으로 형식 이름을 할당합니다(없는 경우).
    - 스크립트가 없는 형식 이름을 적용하기 전에 *확인* 메시지를 표시합니다.

### <a name="phases-of-the-script"></a>스크립트의 단계

다음은 PowerShell 스크립트의 단계입니다.

1. Azure 계정에 로그인합니다(PS 세션당 한 번만 필요).  변수를 할당합니다.
2. 가상 네트워크를 검색하고 서브넷을 검색합니다.
3. **Microsoft.Sql** 엔드포인트 서버 형식으로 태그가 지정된 서브넷이 있나요?
4. 서브넷에서 **Microsoft.Sql** 형식 이름의 가상 서비스 엔드포인트를 추가합니다.

> [!IMPORTANT]
> 이 스크립트를 실행하기 전에 스크립트 위쪽에서 $ 변수에 할당된 값을 편집해야 합니다.

### <a name="direct-powershell-source-code"></a>직접 PowerShell 소스 코드

확인 메시지가 표시될 때 예로 응답하지 않으면 이 PowerShell 스크립트는 아무것도 업데이트하지 않습니다. 이 스크립트는 서브넷에 **Microsoft.Sql** 형식 이름을 추가할 수 있습니다. 하지만 서브넷에 형식 이름이 없는 경우에만 추가를 시도합니다.

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName  = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName "$SubscriptionName";

$ResourceGroupName   = 'yourRGName';
$VNetName            = 'yourVNetName';
$SubnetName          = 'yourSubnetName';
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.'; # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.

# Search for the virtual network.
$vnet = $null;
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

if ($vnet -eq $null)
{
    Write-Host "Caution: No virtual network found by the name '$VNetName'.";
    Return;
}

$subnet = $null;
for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++)
{
    $subnet = $vnet.Subnets[$nn];
    if ($subnet.Name -eq $SubnetName)
    { break; }
    $subnet = $null;
}

if ($subnet -eq $null)
{
    Write-Host "Caution: No subnet found by the name '$SubnetName'";
    Return;
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?

$endpointMsSql = $null;
for ($nn=0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++)
{
    $endpointMsSql = $subnet.ServiceEndpoints[$nn];
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb)
    {
        $endpointMsSql;
        break;
    }
    $endpointMsSql = $null;
}

if ($endpointMsSql -ne $null)
{
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";
    Return;
}
else
{
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]';
    if ('no' -eq $yesno) { Return; }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.

$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

# Persist the subnet update.
$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++)
{ $vnet.Subnets[0].ServiceEndpoints; }  # Display.
```

### <a name="actual-output"></a>실제 출력

다음 블록에는 실제 피드백이 모양이 편집되어 표시됩니다.

```powershell
<# Our output example (with cosmetic edits), when the subnet was already tagged:

Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]: no


Environment           : AzureCloud
Account               : xx@microsoft.com
TenantId              : 11111111-1111-1111-1111-111111111111
SubscriptionId        : 22222222-2222-2222-2222-222222222222
SubscriptionName      : MySubscriptionName
CurrentStorageAccount :


ProvisioningState : Succeeded
Service           : Microsoft.Sql
Locations         : {westcentralus}

Good: Subnet found, and is already tagged as an endpoint of type 'Microsoft.Sql'.
#>
```

<!-- Link references: -->

[sql-db-vnet-service-endpoint-rule-overview-735r]: sql-database-vnet-service-endpoint-rule-overview.md

[http-azure-portal-link-ref-477t]: https://portal.azure.com/
