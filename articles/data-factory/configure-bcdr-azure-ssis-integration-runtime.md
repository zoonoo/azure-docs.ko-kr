---
title: SQL Database 장애 조치(Failover)를 위해 Azure-SSIS 통합 런타임 구성
description: 이 문서에서는 SSISDB 데이터베이스의 Azure SQL Database 지역에서 복제 및 장애 조치(failover)를 사용하여 Azure-SSIS 통합 런타임을 구성하는 방법을 설명합니다.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: 795247cd0d6adfd27115b73c1d0de02e6810d670
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201136"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-sql-database-geo-replication-and-failover"></a>SQL Database 지역에서 복제 및 장애 조치(failover)를 사용하여 Azure-SSIS 통합 런타임 구성

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 SSISDB 데이터베이스에 대해 Azure SQL Database 지역에서 복제를 사용하여 Azure-SSIS 통합 런타임(IR)을 구성하는 방법을 설명합니다. 장애 조치(Failover)가 발생하는 경우 Azure-SSIS IR이 보조 데이터베이스를 계속 사용하는지 확인할 수 있습니다.

Microsoft SQL Database의 지역에서 복제 및 장애 조치(Failover)에 대한 자세한 내용은 [개요: 활성 지역 복제 및 자동 장애 조치(Failover) 그룹](../sql-database/sql-database-geo-replication-overview.md)을 참조하세요.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-a-sql-database-managed-instance"></a>SQL Database 관리형 인스턴스를 사용한 Azure-SSIS IR 장애 조치(Failover)

### <a name="prerequisites"></a>사전 요구 사항

Azure SQL Database 관리형 인스턴스는 *DMK(데이터베이스 마스터 키)* 를 사용하여 데이터베이스에 저장된 데이터, 자격 증명 및 연결 정보를 보호합니다. DMK의 자동 암호 해독을 사용하기 위해 *SMK(서버 마스터 키)* 를 통해 키의 복사본이 암호화됩니다. 

SMK는 장애 조치(failover) 그룹에 복제되지 않습니다. 장애 조치(failover) 후 DMK 암호 해독을 위해 기본 및 보조 인스턴스 모두에서 암호를 추가해야 합니다.

1. 기본 인스턴스에서 SSISDB에 대해 다음 명령을 실행합니다. 이 단계에서는 새 암호화 암호를 추가합니다.

    ```sql
    ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
    ```

2. Azure SQL Database 관리형 인스턴스에서 장애 조치(failover) 그룹을 만듭니다.

3. 새 암호화 암호를 사용하여 보조 인스턴스에서 **sp_control_dbmasterkey_password**를 실행합니다.

    ```sql
    EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
        @password = N'<password>', @action = N'add';  
    GO
    ```

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>시나리오 1: Azure-SSIS IR이 읽기/쓰기 수신기 엔드포인트를 가리킴

Azure-SSIS IR이 읽기/쓰기 수신기 엔드포인트를 가리키게 하려면 먼저 기본 서버 엔드포인트를 가리켜야 합니다. SSISDB를 장애 조치(failover) 그룹에 넣은 후 읽기/쓰기 수신기 엔드포인트로 변경하고 Azure-SSIS IR을 다시 시작할 수 있습니다.

#### <a name="solution"></a>해결 방법

장애 조치(failover)가 발생하는 경우 다음 단계를 수행합니다.

1. 주 지역에서 Azure-SSIS IR을 중지합니다.

2. 보조 인스턴스에서 사용자 지정 설치에 대한 새 지역, 가상 네트워크 및 SAS(공유 액세스 서명) URI 정보를 사용하여 Azure-SSIS IR을 편집합니다. Azure-SSIS IR이 읽기/쓰기 수신기를 가리키고 엔드포인트가 Azure-SSIS IR에 투명하므로 엔드포인트를 편집할 필요가 없습니다.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Azure-SSIS IR을 다시 시작합니다.

### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>시나리오 2: Azure-SSIS IR이 주 서버 엔드포인트를 가리킴

이 시나리오는 Azure-SSIS IR이 주 서버 엔드포인트를 가리키고 있는 경우 적합합니다.

#### <a name="solution"></a>해결 방법

장애 조치(failover)가 발생하는 경우 다음 단계를 수행합니다.

1. 주 지역에서 Azure-SSIS IR을 중지합니다.

2. 보조 인스턴스에 대한 새 지역, 엔드포인트 및 가상 네트워크 정보를 사용하여 Azure-SSIS IR을 편집합니다.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Azure-SSIS IR을 다시 시작합니다.

### <a name="scenario-3-azure-ssis-ir-is-pointing-to-a-public-endpoint-of-a-sql-database-managed-instance"></a>시나리오 3: Azure-SSIS IR이 SQL Database 관리형 인스턴스의 퍼블릭 엔드포인트를 가리킴

이 시나리오는 Azure-SSIS IR이 Azure SQL Database 관리형 인스턴스의 퍼블릭 엔드포인트를 가리키고 가상 네트워크에 연결하지 않는 경우에 적합합니다. 시나리오 2와의 유일한 차이점은 장애 조치(failover) 후 Azure-SSIS IR의 가상 네트워크 정보를 편집할 필요가 없다는 것입니다.

#### <a name="solution"></a>해결 방법

장애 조치(failover)가 발생하는 경우 다음 단계를 수행합니다.

1. 주 지역에서 Azure-SSIS IR을 중지합니다.

2. 보조 인스턴스에 대한 새 지역 및 엔드포인트 정보를 사용하여 Azure-SSIS IR을 편집합니다.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Azure-SSIS IR을 다시 시작합니다.

### <a name="scenario-4-attach-an-existing-ssisdb-instance-ssis-catalog-to-a-new-azure-ssis-ir"></a>시나리오 4: 새 Azure-SSIS IR에 기존 SSISDB 인스턴스(SSIS 카탈로그) 연결

이 시나리오는 현재 지역에서 Azure Data Factory 또는 Azure-SSIS IR 재해가 발생할 때 SSISDB가 새 지역의 새 Azure-SSIS IR로 작업하도록 하려는 경우에 적합합니다.

#### <a name="solution"></a>해결 방법

장애 조치(failover)가 발생하는 경우 다음 단계를 수행합니다.

> [!NOTE]
> IR를 만드는 4단계에서는 PowerShell을 사용하세요. 그렇지 않으면 Microsoft Azure Portal에서 SSISDB가 이미 존재한다는 오류를 보고합니다.

1. 주 지역에서 Azure-SSIS IR을 중지합니다.

2. 저장 프로시저를 실행하여 **\<new_data_factory_name\>** 과 **\<new_integration_runtime_name\>** 의 연결을 허용하도록 SSISDB의 메타데이터를 업데이트합니다.
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. 새 지역에 **\<new_data_factory_name\>** 이라는 새 데이터 팩터리를 만듭니다.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
    ```
    
    이 PowerShell 명령에 대한 자세한 내용은 [PowerShell을 사용하여 Azure Data Factory 만들기](quickstart-create-data-factory-powershell.md)를 참조하세요.

4. Microsoft Azure PowerShell을 사용하여 새 지역에 **\<new_integration_runtime_name\>** 이라는 새 Azure-SSIS IR을 만듭니다.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    이 PowerShell 명령에 대한 자세한 내용은 [Azure Data Factory에서 Azure-SSIS 통합 런타임 만들기](create-azure-ssis-integration-runtime.md)를 참조하세요.



## <a name="azure-ssis-ir-failover-with-sql-database"></a>SQL Database를 사용한 Azure-SSIS IR 장애 조치(Failover)

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>시나리오 1: Azure-SSIS IR이 읽기/쓰기 수신기 엔드포인트를 가리킴

이 시나리오는 다음과 같은 경우에 적합합니다.

- Azure-SSIS IR이 장애 조치(Failover) 그룹의 읽기/쓰기 수신기 엔드포인트를 가리키고 있습니다.
- SQL Database 서버가 가상 네트워크 서비스 엔드포인트의 규칙으로 구성되지 *않았습니다*.

Azure-SSIS IR이 읽기/쓰기 수신기 엔드포인트를 가리키게 하려면 먼저 기본 서버 엔드포인트를 가리켜야 합니다. SSISDB를 장애 조치(failover) 그룹에 넣은 후 읽기/쓰기 수신지 엔드포인트로 변경하고 Azure-SSIS IR을 다시 시작할 수 있습니다.

#### <a name="solution"></a>해결 방법

장애 조치(Failover)가 발생하는 경우 이는 Azure-SSIS IR에 투명하게 진행됩니다. Azure-SSIS IR은 장애 조치(Failover) 그룹의 새로운 주 데이터베이스에 자동으로 연결됩니다. 

Azure-SSIS IR에서 지역 또는 기타 정보를 업데이트하려는 경우 이를 중지하고, 편집하고, 다시 시작할 수 있습니다.


### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>시나리오 2: Azure-SSIS IR이 주 서버 엔드포인트를 가리킴

이 시나리오는 Azure-SSIS IR이 주 서버 엔드포인트를 가리키고 있는 경우 적합합니다.

#### <a name="solution"></a>해결 방법

장애 조치(failover)가 발생하는 경우 다음 단계를 수행합니다.

1. 주 지역에서 Azure-SSIS IR을 중지합니다.

2. 보조 인스턴스에 대한 새 지역, 엔드포인트 및 가상 네트워크 정보를 사용하여 Azure-SSIS IR을 편집합니다.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Azure-SSIS IR을 다시 시작합니다.

### <a name="scenario-3-attach-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>시나리오 3: 새 Azure-SSIS IR에 기존 SSISDB(SSIS 카탈로그) 연결

이 시나리오는 보조 지역에서 새 Azure-SSIS IR을 프로비저닝하려는 경우에 적합합니다. 현재 지역에서 Azure Data Factory 또는 Azure-SSIS IR 재해가 발생할 때 SSISDB가 새 지역의 새 Azure-SSIS IR로 계속 작업하도록 하려는 경우에도 적합합니다.

#### <a name="solution"></a>해결 방법

장애 조치(failover)가 발생하는 경우 다음 단계를 수행합니다.

> [!NOTE]
> IR를 만드는 4단계에서는 PowerShell을 사용하세요. 그렇지 않으면 Microsoft Azure Portal에서 SSISDB가 이미 존재한다는 오류를 보고합니다.

1. 주 지역에서 Azure-SSIS IR을 중지합니다.

2. 저장 프로시저를 실행하여 **\<new_data_factory_name\>** 과 **\<new_integration_runtime_name\>** 의 연결을 허용하도록 SSISDB의 메타데이터를 업데이트합니다.
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. 새 지역에 **\<new_data_factory_name\>** 이라는 새 데이터 팩터리를 만듭니다.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
    ```
    
    이 PowerShell 명령에 대한 자세한 내용은 [PowerShell을 사용하여 Azure Data Factory 만들기](quickstart-create-data-factory-powershell.md)를 참조하세요.

4. Microsoft Azure PowerShell을 사용하여 새 지역에 **\<new_integration_runtime_name\>** 이라는 새 Azure-SSIS IR을 만듭니다.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    이 PowerShell 명령에 대한 자세한 내용은 [Azure Data Factory에서 Azure-SSIS 통합 런타임 만들기](create-azure-ssis-integration-runtime.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

Azure-SSIS IR에 대한 이러한 기타 구성 옵션이 좋습니다.

- [고성능을 위한 Azure-SSIS 통합 런타임 구성](configure-azure-ssis-integration-runtime-performance.md)

- [Azure-SSIS 통합 런타임을 위한 사용자 지정 설정](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Azure-SSIS 통합 런타임의 Enterprise 버전 프로비저닝](how-to-configure-azure-ssis-ir-enterprise-edition.md)
