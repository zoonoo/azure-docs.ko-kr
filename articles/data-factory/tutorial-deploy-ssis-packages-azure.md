---
title: "Azure에 SSIS 패키지 배포 | Microsoft Docs"
description: "이 문서에서는 Azure Data Factory에서 제공하는 Azure-SSIS 통합 런타임에 SSIS 패키지를 배포하는 방법을 설명합니다."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: hero-article
ms.date: 09/06/2017
ms.author: spelluru
ms.openlocfilehash: 85777e2a4d1dea5d148a543acd068f8aa1a2335c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-sql-server-integration-services-packages-to-azure"></a>Azure에 SQL Server Integration Services 패키지 배포
Azure Data Factory는 데이터 이동 및 데이터 변환을 오케스트레이션하고 자동화하기 위해 클라우드에서 데이터 기반 워크플로를 만들 수 있는 클라우드 기반 데이터 통합 서비스입니다. Azure Data Factory를 사용하여 서로 다른 데이터 저장소에서 데이터를 수집할 수 있는 데이터 기반 워크플로(파이프라인이라고 함)를 만들고 일정을 조정하며, Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics 및 Azure Machine Learning과 같은 계산 서비스를 사용하여 데이터를 처리/변환하고, 사용할 BI(비즈니스 인텔리전스) 응용 프로그램의 Azure SQL Data Warehouse와 같은 데이터 저장소에 출력 데이터를 게시할 수 있습니다. 

이 자습서에서는 Azure Data Factory에서 Azure-SSIS IR(통합 런타임)을 프로비전하는 단계를 제공합니다. 그런 다음 SSDT(SQL Server Data Tools) 또는 SSMS(SQL Server Management Studio)를 사용하여 Azure에서 이 런타임에 SSIS(SQL Server Integration Services) 패키지를 배포할 수 있습니다. 이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * Azure-SSIS 통합 런타임 만들기
> * Azure-SSIS 통합 런타임 시작
> * SSIS 패키지 배포
> * 전체 스크립트 검토

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다. Azure-SSIS IR의 개념 정보는 [Azure-SSIS 통합 런타임 개요](concepts-integration-runtime.md#azure-ssis-integration-runtime)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
- **Azure SQL Database 서버**. 데이터베이스 서버가 아직 없는 경우 시작하기 전에 Azure Portal에서 이 서버를 만듭니다. 이 서버는 SSISDB(SSIS 카탈로그 데이터베이스)를 호스팅합니다. Integration Runtime과 동일한 Azure 지역에 데이터베이스 서버를 만드는 것이 좋습니다. 이 구성을 사용하면 Integration Runtime에서 Azure 지역을 벗어나지 않고 SSISDB에 실행 로그를 쓸 수 있습니다. 
- **Azure PowerShell**. [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-azurerm-ps)의 지침을 따르세요. PowerShell을 사용하여 클라우드에서 SSIS 패키지를 실행하는 Azure-SSIS 통합 런타임을 프로비전하는 스크립트를 실행합니다. 

## <a name="launch-windows-powershell-ise"></a>Windows PowerShell ISE 시작
관리자 권한으로 **Windows PowerShell ISE**를 시작합니다. 

## <a name="create-variables"></a>변수 만들기
다음 스크립트를 복사하여 붙여넣습니다. 변수에 대한 값을 지정합니다. Azure SQL Database에 지원되는 **가격 책정 계층**의 목록은 [SQL Database 리소스 제한](../sql-database/sql-database-resource-limits.md)을 참조하세요.

```powershell
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
# In public preview, only EastUS amd EastUS2 are supported.
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# In public preview, only EastUS and NorthEurope are supported.
$AzureSSISLocation = "EastUS" 
 # In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_A4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# In public preview, only 1-8 parallel executions per node are supported.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 
```

## <a name="validate-the-connection-to-database"></a>데이터베이스에 대한 연결 유효성 검사
Azure SQL Database 서버 server.database.windows.net의 유효성을 검사하려면 다음 스크립트를 추가합니다. 

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

## <a name="log-in-and-select-subscription"></a>로그인 및 구독 선택
스크립트에 다음 코드를 추가하여 로그인하고 Azure 구독을 선택합니다. 

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 명령을 사용하여 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다. 다음 예제는 `westeurope` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

리소스 그룹이 이미 있는 경우 스크립트에 이 코드를 복사하지 마세요. 

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
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode 
```

## <a name="start-integration-runtime"></a>Integration Runtime 시작
다음 명령을 실행하여 Azure-SSIS Integration Runtime을 시작합니다. 

```powershell
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
이 명령은 완료하는 데 **20-30분** 정도 걸립니다. 

## <a name="deploy-ssis-packages"></a>SSIS 패키지 배포
이제 SSDT(SQL Server Data Tools) 또는 SSMS(SQL Server Management Studio)를 사용하여 Azure에 SSIS 패키지를 배포합니다. SSISDB(SSIS 카탈로그 데이터베이스)를 호스팅하는 Azure SQL Server에 연결합니다. Azure SQL Server 이름의 형식은 `<servername>.database.windows.net`(Azure SQL Database의 경우)입니다. 

SSIS 설명서에서 다음 문서를 참조하세요. 

- [Azure에서 SSIS 패키지 배포, 실행 및 모니터링](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Azure에서 SSIS 카탈로그에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Azure에서 패키지 실행 예약](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Windows 인증을 사용하는 온-프레미스 데이터 원본에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="full-script"></a>전체 스크립트
이 릴리스에서는 PowerShell을 사용하여 클라우드에서 SSIS 패키지를 실행하는 Azure-SSIS Integration Runtime 인스턴스를 프로비전해야 합니다. 현재 이 런타임은 Azure Portal을 사용하여 프로비전할 수 없습니다. 

이 섹션의 PowerShell 스크립트는 클라우드에서 SSIS 패키지를 실행하는 Azure-SSIS Integration Runtime 인스턴스를 구성합니다. 이 스크립트가 성공적으로 실행되면 Azure SQL Database에서 호스팅되는 SSISDB를 사용하여 Microsoft Azure 클라우드에서 SSIS 패키지를 배포하고 실행할 수 있습니다.

1. Windows PowerShell ISE(통합 스크립팅 환경)를 시작합니다.
2. ISE의 명령 프롬프트에서 다음 명령을 실행합니다.    
    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```
3. 이 섹션의 PowerShell 스크립트를 복사하여 ISE에 붙여넣습니다.
4. 스크립트 시작 부분의 "Azure 사양의 SSIS" 섹션에 있는 스크립트 매개 변수에 적절한 값을 제공합니다. 이러한 매개 변수는 다음 섹션에서 설명합니다.
5. 스크립트를 실행합니다. 스크립트 끝 부분의 `Start-AzureRmDataFactoryV2IntegrationRuntime` 명령은 **20-30분** 동안 실행됩니다.

> [!NOTE]
> 이 스크립트는 SSISDB(SSIS 카탈로그 데이터베이스)를 준비하기 위해 Azure SQL Database에 연결합니다. 또한 이 스크립트는 VNet에 대한 권한 및 설정을 구성하고(지정된 경우) Azure-SSIS Integration Runtime의 새 인스턴스를 VNet에 조인합니다.

Azure SQL Database에 지원되는 **가격 책정 계층**의 목록은 [SQL Database 리소스 제한](../sql-database/sql-database-resource-limits.md)을 참조하세요.

```powershell
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
# In public preview, only EastUS amd EastUS2 are supported.
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS (IR)>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# In public preview, only EastUS and NorthEurope are supported.
$AzureSSISLocation = "EastUS" 
 # In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_A4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# In public preview, only 1-8 parallel executions per node are supported.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 

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

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                        -Location $DataFactoryLocation `
                        -Name $DataFactoryName

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
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="join-azure-ssis-ir-to-a-vnet"></a>Azure-SSIS IR을 VNet에 조인
VNet(가상 네트워크) 내에서 SSIS(SQL Server Integration Services) 카탈로그를 호스팅하는 데 Azure SQL 관리되는 인스턴스(비공개 미리 보기)를 사용하는 경우 Azure-SSIS 통합 런타임을 동일한 가상 네트워크에 조인해야 합니다. Azure Data Factory 버전 2(미리 보기)를 사용하면 Azure-SSIS 통합 런타임을 클래식 VNet에 조인할 수 있습니다. 자세한 내용은 [Azure-SSIS 런타임을 VNet에 조인](join-azure-ssis-integration-runtime-virtual-network.md)을 참조하세요.

VNet을 조인하는 Azure-SSIS 런타임을 만드는 전체 스크립트는 [Azure-SSIS 통합 런타임 만들기](create-azure-ssis-integration-runtime.md)를 참조하세요.

## <a name="monitor-and-manage-azure-ssis-ir"></a>Azure-SSIS IR 모니터링 및 관리
Azure-SSIS IR 모니터링 및 관리에 대한 자세한 내용은 다음 문서를 참조하세요. 

- [Azure-SSIS 통합 런타임 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Azure-SSIS 통합 런타임 관리](manage-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다. 

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * Azure-SSIS 통합 런타임 만들기
> * Azure-SSIS 통합 런타임 시작
> * SSIS 패키지 배포
> * 전체 스크립트 검토

온-프레미스에서 클라우드로 데이터를 복사하는 방법을 알아보려면 다음 자습서로 진행하세요. 

> [!div class="nextstepaction"]
>[클라우드에서 데이터 복사](tutorial-copy-data-dot-net.md)
