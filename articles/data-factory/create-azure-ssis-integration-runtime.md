---
title: "Azure Data Factory에서 자체 호스팅 통합 런타임 만들기 | Microsoft Docs"
description: "SQL Server 저장 프로시저 작업을 사용하여 데이터 팩터리 파이프라인으로 Azure SQL 데이터베이스 또는 Azure SQL 데이터 웨어하우스에서 저장 프로시저를 호출하는 방법을 알아봅니다."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ef600fc3727f1da3203e075714282a6505bc98d3
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Azure Data Factory에서 Azure Integration Runtime 만들기 | Microsoft Docs
이 자습서에서는 Azure Data Factory에서 Azure-SSIS Integration Runtime을 프로비전하는 단계를 제공합니다. 그런 다음 SSDT(SQL Server Data Tools) 또는 SSMS(SQL Server Management Studio)를 사용하여 Azure에서 이 런타임에 SSIS(SQL Server Integration Services) 패키지를 배포할 수 있습니다. 이 자습서에서 수행하는 단계는 다음과 같습니다.

## <a name="prerequisites"></a>필수 조건

- **Azure 구독**. 구독이 없는 경우 [평가판](http://azure.microsoft.com/pricing/free-trial/) 계정을 만들 수 있습니다.
- **Azure SQL Database 서버** 또는 **SQL Server 관리되는 인스턴스(비공개 미리 보기)(확장된 비공개 미리 보기)** - 데이터베이스 서버가 아직 없는 경우 시작하기 전에 Azure Portal에서 이 서버를 만듭니다. 이 서버는 SSISDB(SSIS 카탈로그 데이터베이스)를 호스팅합니다. Integration Runtime과 동일한 Azure 지역에 데이터베이스 서버를 만드는 것이 좋습니다. 이 구성을 사용하면 Integration Runtime에서 Azure 지역을 벗어나지 않고 SSISDB에 실행 로그를 쓸 수 있습니다. 
- **클래식 가상 네트워크(VNet)(선택 사항)** 다음 조건 중 하나 이상에 해당하는 경우 Azure Virtual Network(VNet)가 있어야 합니다.
    - VNet의 일부인 SQL Server 관리되는 인스턴스(비공개 미리 보기)에서 SSIS 카탈로그 데이터베이스를 호스팅합니다.
    - Azure-SSIS Integration Runtime에서 실행되는 SSIS 패키지에서 온-프레미스 데이터 저장소에 연결하려고 합니다.
- **Azure PowerShell**. [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-azurerm-ps)의 지침을 따르세요. PowerShell을 사용하여 클라우드에서 SSIS 패키지를 실행하는 Azure-SSIS 통합 런타임을 프로비전하는 스크립트를 실행합니다. 

## <a name="create-variables"></a>변수 만들기
이 자습서에서 스크립트에 사용할 변수를 정의합니다.

```powershell
# Azure Data Factory version 2 information
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" # In public preview, only EastUS|EastUS2 are supported.

# Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS integration runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS integration runtime"
$AzureSSISLocation = "EastUS" # In public preview, only EastUS|NorthEurope are supported.
$AzureSSISNodeSize = "Standard_A4_v2" # In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeNumber = 2 # In public preview, only 1-10 nodes are supported.
$AzureSSISMaxParallelExecutionsPerNode = 2 # In public preview, only 1-8 parallel executions per node are supported.
$VnetId = "[your VNet resource ID or leave it empty]" # OPTIONAL: In public preview, only classic virtual network (VNET) is supported.
$SubnetName = "[your subnet name or leave it empty]" # OPTIONAL: In public preview, only classic VNet is supported.

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (private preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"
$SSISDBPricingTier = "[your Azure SQL Database pricing tier, e.g. S3, or leave it empty for Azure SQL Managed Instance (private preview)]" # Not applicable for Azure SQL Managed Instance (private preview)
```

## <a name="validate-the-connection-to-database"></a>데이터베이스에 대한 연결 유효성 검사
다음 스크립트를 추가하여 server.database.windows.net Azure SQL Database 서버 끝점 또는 Azure SQL 관리되는 인스턴스(비공개 미리 보기) 서버 끝점의 유효성을 검사합니다. 

```powershell
$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}
```

## <a name="login-and-select-subscription"></a>로그인 및 구독 선택
스크립트에 다음 코드를 추가하여 로그인하고 Azure 구독을 선택합니다. 

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

## <a name="configure-virtual-network"></a>가상 네트워크 구성
다음 스크립트를 추가하여 조인할 Azure-SSIS Integration Runtime에 대한 가상 네트워크 권한/설정을 자동으로 구성합니다.

```powershell
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    # Assign VM contributor role to Microsoft.Batch
    New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
}
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 명령을 사용하여 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다. 다음 예제는 `westeurope` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.
다음 명령을 실행하여 데이터 팩터리를 만듭니다.

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>Integration Runtime 만들기
다음 명령을 실행하여 Azure에서 SSIS 패키지를 실행하는 Azure-SSIS Integration Runtime을 만듭니다. 

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

## <a name="start-integration-runtime"></a>Integration Runtime 시작
다음 명령을 실행하여 Azure-SSIS Integration Runtime을 시작합니다. 

```powershell
write-host("##### Starting #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
이 명령은 완료하는 데 **20-30분** 정도 걸립니다. 

## <a name="deploy-ssis-packages"></a>SSIS 패키지 배포
이제 SSDT(SQL Server Data Tools) 또는 SSMS(SQL Server Management Studio)를 사용하여 Azure에 SSIS 패키지를 배포합니다. SSISDB(SSIS 카탈로그 데이터베이스)를 호스팅하는 Azure SQL Server에 연결합니다. Azure SQL Server 이름의 형식은 &lt;servername&gt;.database.windows.net(Azure SQL Database의 경우)입니다. 자세한 지침은 [패키지 배포](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server) 문서를 참조하세요. 

## <a name="full-script"></a>전체 스크립트
이 릴리스에서는 PowerShell을 사용하여 클라우드에서 SSIS 패키지를 실행하는 Azure-SSIS Integration Runtime 인스턴스를 프로비전해야 합니다. 현재 이 런타임은 Azure Portal을 사용하여 프로비전할 수 없습니다. 

이 섹션의 PowerShell 스크립트는 클라우드에서 SSIS 패키지를 실행하는 Azure-SSIS Integration Runtime 인스턴스를 구성합니다. 이 스크립트가 성공적으로 실행되면 Azure SQL Database 또는 SQL Server 관리되는 인스턴스(비공개 미리 보기)에서 호스팅되는 SSISDB를 사용하여 Microsoft Azure 클라우드에서 SSIS 패키지를 배포하고 실행할 수 있습니다.

1. Windows PowerShell ISE(통합 스크립팅 환경)를 시작합니다.
2. ISE의 명령 프롬프트에서 다음 명령을 실행합니다.    
    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```
3. 이 섹션의 PowerShell 스크립트를 복사하여 ISE에 붙여넣습니다.
4. 스크립트 시작 부분의 "Azure 사양의 SSIS" 섹션에 있는 스크립트 매개 변수에 적절한 값을 제공합니다. 이러한 매개 변수는 다음 섹션에서 설명합니다.
5. 스크립트를 실행합니다. 스크립트 끝 부분의 `Start-AzureRmDataFactoryV2IntegrationRuntime` 명령은 **20-30분** 동안 실행됩니다.

> [!NOTE]
> 이 스크립트는 SSISDB(SSIS 카탈로그 데이터베이스)를 준비하기 위해 Azure SQL Database 또는 SQL Server 관리되는 인스턴스(비공개 미리 보기)에 연결합니다. 또한 이 스크립트는 VNet에 대한 권한 및 설정을 구성하고(지정된 경우) Azure-SSIS Integration Runtime의 새 인스턴스를 VNet에 조인합니다.


```powershell
# If your inputs contain PSH special characters, e.g. "$", please precede it with the escape character "`" like "`$". 
# Azure Data Factory v2 information
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" # In Public Preview, only EastUS|EastUS2 are supported for now

# Azure-SSIS Integration Runtime info - This is Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS Integration Runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS Integration Runtime"
$AzureSSISLocation = "EastUS" # In Public Preview, only EastUS|NorthEurope are supported for now
$AzureSSISNodeSize = "Standard_A4_v2" # In Public Preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported for now
$AzureSSISNodeNumber = 2 # In Public Preview, only 1-10 nodes are supported for now
$AzureSSISMaxParallelExecutionsPerNode = 2 # In Public Preview, only 1-8 parallel executions per node are supported for now
$VnetId = "[your VNet resource ID or leave it empty]" # OPTIONAL: In Public Preview, only Classic VNet is supported for now
$SubnetName = "[your subnet name or leave it empty]" # OPTIONAL: In Public Preview, only Classic VNet is supported for now

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (private preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"
$SSISDBPricingTier = "[your Azure SQL Database pricing tier, e.g. S3, or leave it empty for Azure SQL Managed Instance (private preview)]" # Not applicable for Azure SQL Managed Instance (private preview)

##### Validate your Azure SQL Database/Managed Instance (private preview) server ##### 
$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}

##### Login and and select your Azure subscription #####
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

##### Automatically configure VNet permissions/settings for your Azure-SSIS Integration Runtime to join ##### 
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    # Assign VM contributor role to Microsoft.Batch
    New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
}

##### Provision your Azure Data Factory  + Azure-SSIS Integration Runtime ##### 
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory

$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
write-host("##### Starting #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force
write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")

##### Query/monitor your Azure-SSIS Integration Runtime #####
#Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status

##### Reconfigure your Azure-SSIS Integration Runtime, e.g. stopping/scaling out to 5 nodes/starting #####
# Stopping your Azure-SSIS Integration Runtime will release its nodes and stop billing
#Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 

# Scaling out your Azure-SSIS Integration Runtime to 5 nodes
#Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5

# Starting your Azure-SSIS Integration Runtime will allocate its nodes and start billing
#Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName

##### Clean up ######
#Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
#Remove-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
#Remove-AzureRmDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
#Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force

```

