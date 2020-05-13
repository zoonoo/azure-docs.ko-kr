---
title: SQL Database 장애 조치 (failover)를 위한 Azure SSIS 통합 런타임 구성
description: 이 문서에서는 SSISDB 데이터베이스에 대 한 Azure SQL Database 지역 복제 및 장애 조치 (failover)를 사용 하 여 Azure SSIS 통합 런타임을 구성 하는 방법을 설명 합니다.
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
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201136"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-sql-database-geo-replication-and-failover"></a>SQL Database 지역에서 복제 및 장애 조치 (failover)를 사용 하 여 Azure SSIS 통합 런타임 구성

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 SSISDB 데이터베이스에 대 한 Azure SQL Database 지역에서 복제를 사용 하 여 Azure SSIS IR (통합 런타임)을 구성 하는 방법을 설명 합니다. 장애 조치(Failover)가 발생하는 경우 Azure-SSIS IR이 보조 데이터베이스를 계속 사용하는지 확인할 수 있습니다.

SQL Database의 지역에서 복제 및 장애 조치(Failover)에 대한 자세한 내용은 [개요: 활성 지역 복제 및 자동 장애 조치(Failover) 그룹](../sql-database/sql-database-geo-replication-overview.md)을 참조하세요.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-a-sql-database-managed-instance"></a>SQL Database 관리 되는 인스턴스를 사용 하 여 장애 조치 (failover) Azure-SSIS IR

### <a name="prerequisites"></a>필수 구성 요소

Azure SQL Database 관리 되는 인스턴스는 데이터베이스에 저장 되는 데이터, 자격 증명 및 연결 정보를 보호 하기 위해 *데이터베이스 마스터 키 (DMK)* 를 사용 합니다. DMK의 자동 암호 해독을 사용 하도록 설정 하기 위해 키의 복사본은 *SMK (서버 마스터 키)* 를 통해 암호화 됩니다. 

SMK가 장애 조치 (failover) 그룹에 복제 되지 않습니다. 장애 조치 (failover) 후 DMK 암호 해독에 대 한 기본 및 보조 인스턴스에 암호를 추가 해야 합니다.

1. 주 인스턴스의 SSISDB에 대해 다음 명령을 실행 합니다. 이 단계에서는 새 암호화 암호를 추가 합니다.

    ```sql
    ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
    ```

2. Azure SQL Database 관리 되는 인스턴스에서 장애 조치 (failover) 그룹을 만듭니다.

3. 새 암호화 암호를 사용 하 여 보조 인스턴스에서 **sp_control_dbmasterkey_password** 를 실행 합니다.

    ```sql
    EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
        @password = N'<password>', @action = N'add';  
    GO
    ```

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>시나리오 1: Azure-SSIS IR 읽기/쓰기 수신기 끝점을 가리키고 있습니다.

Azure-SSIS IR 읽기/쓰기 수신기 끝점을 가리키도록 하려면 먼저 주 서버 끝점을 가리켜야 합니다. SSISDB를 장애 조치 (failover) 그룹에 추가한 후에는 읽기/쓰기 수신기 끝점으로 변경 하 고 Azure-SSIS IR를 다시 시작할 수 있습니다.

#### <a name="solution"></a>솔루션

장애 조치 (failover)가 발생 하는 경우 다음 단계를 수행 합니다.

1. 주 지역에서 Azure-SSIS IR를 중지 합니다.

2. 보조 인스턴스에서 사용자 지정 설치에 대 한 새 지역, 가상 네트워크 및 SAS (공유 액세스 서명) URI 정보를 사용 하 여 Azure-SSIS IR를 편집 합니다. Azure-SSIS IR는 읽기/쓰기 수신기를 가리키고 끝점이 Azure-SSIS IR에 투명 하기 때문에 끝점을 편집할 필요가 없습니다.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Azure-SSIS IR를 다시 시작 합니다.

### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>시나리오 2: Azure-SSIS IR 주 서버 끝점을 가리키고 있습니다.

이 시나리오는 Azure-SSIS IR 주 서버 끝점을 가리키는 경우에 적합 합니다.

#### <a name="solution"></a>솔루션

장애 조치 (failover)가 발생 하는 경우 다음 단계를 수행 합니다.

1. 주 지역에서 Azure-SSIS IR를 중지 합니다.

2. 보조 인스턴스에 대 한 새 지역, 끝점 및 가상 네트워크 정보를 사용 하 여 Azure-SSIS IR를 편집 합니다.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Azure-SSIS IR를 다시 시작 합니다.

### <a name="scenario-3-azure-ssis-ir-is-pointing-to-a-public-endpoint-of-a-sql-database-managed-instance"></a>시나리오 3: Azure-SSIS IR SQL Database 관리 되는 인스턴스의 공용 끝점을 가리키고 있습니다.

이 시나리오는 Azure-SSIS IR Azure SQL Database 관리 되는 인스턴스의 공용 끝점을 가리키고 가상 네트워크에 연결 되지 않는 경우에 적합 합니다. 시나리오 2의 유일한 차이점은 장애 조치 (failover) 후 Azure-SSIS IR의 가상 네트워크 정보를 편집할 필요가 없다는 것입니다.

#### <a name="solution"></a>솔루션

장애 조치 (failover)가 발생 하는 경우 다음 단계를 수행 합니다.

1. 주 지역에서 Azure-SSIS IR를 중지 합니다.

2. 보조 인스턴스에 대 한 새 지역 및 끝점 정보를 사용 하 여 Azure-SSIS IR를 편집 합니다.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Azure-SSIS IR를 다시 시작 합니다.

### <a name="scenario-4-attach-an-existing-ssisdb-instance-ssis-catalog-to-a-new-azure-ssis-ir"></a>시나리오 4: 새 Azure-SSIS IR에 기존 SSISDB 인스턴스 (SSIS 카탈로그) 연결

이 시나리오는 현재 지역에서 Azure Data Factory 또는 Azure-SSIS IR 재해가 발생할 때 SSISDB가 새 지역에서 새 Azure-SSIS IR 작동 하도록 하려는 경우에 적합 합니다.

#### <a name="solution"></a>솔루션

장애 조치 (failover)가 발생 하는 경우 다음 단계를 수행 합니다.

> [!NOTE]
> 4 단계 (IR 생성)에 PowerShell을 사용 합니다. 그렇지 않으면 Azure Portal는 SSISDB가 이미 존재 한다는 오류를 보고 합니다.

1. 주 지역에서 Azure-SSIS IR를 중지 합니다.

2. ** \< New_data_factory_name \> ** 및 ** \< NEW_INTEGRATION_RUNTIME_NAME \> **의 연결을 허용 하도록 SSISDB의 메타 데이터를 업데이트 하는 저장 프로시저를 실행 합니다.
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. 새 지역에 ** \< new_data_factory_name \> ** 라는 새 데이터 팩터리를 만듭니다.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
    ```
    
    이 PowerShell 명령에 대 한 자세한 내용은 [powershell을 사용 하 여 Azure 데이터 팩터리 만들기](quickstart-create-data-factory-powershell.md)를 참조 하세요.

4. Azure PowerShell를 사용 하 여 새 지역에 ** \< new_integration_runtime_name \> ** 라는 새 Azure-SSIS IR를 만듭니다.

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

    이 PowerShell 명령에 대 한 자세한 내용은 [Azure Data Factory에서 AZURE SSIS 통합 런타임 만들기](create-azure-ssis-integration-runtime.md)를 참조 하세요.



## <a name="azure-ssis-ir-failover-with-sql-database"></a>Azure-SSIS IR 장애 조치 (failover) SQL Database

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>시나리오 1: Azure-SSIS IR 읽기/쓰기 수신기 끝점을 가리키고 있습니다.

이 시나리오는 다음과 같은 경우에 적합 합니다.

- Azure-SSIS IR는 장애 조치 (failover) 그룹의 읽기/쓰기 수신기 끝점을 가리킵니다.
- SQL Database 서버가 가상 네트워크 서비스 끝점에 대 한 규칙으로 구성 되어 *있지 않습니다* .

Azure-SSIS IR 읽기/쓰기 수신기 끝점을 가리키도록 하려면 먼저 주 서버 끝점을 가리켜야 합니다. SSISDB를 장애 조치 (failover) 그룹에 추가한 후에는 읽기/쓰기 수신기 끝점으로 변경 하 고 Azure-SSIS IR를 다시 시작할 수 있습니다.

#### <a name="solution"></a>솔루션

장애 조치 (failover)가 발생 하면 Azure-SSIS IR에 투명 하 게 됩니다. Azure-SSIS IR은 장애 조치(Failover) 그룹의 새로운 주 데이터베이스에 자동으로 연결됩니다. 

Azure-SSIS IR에서 지역 또는 기타 정보를 업데이트 하려는 경우 중지 하 고, 편집 하 고, 다시 시작할 수 있습니다.


### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>시나리오 2: Azure-SSIS IR 주 서버 끝점을 가리키고 있습니다.

이 시나리오는 Azure-SSIS IR 주 서버 끝점을 가리키는 경우에 적합 합니다.

#### <a name="solution"></a>솔루션

장애 조치 (failover)가 발생 하는 경우 다음 단계를 수행 합니다.

1. 주 지역에서 Azure-SSIS IR를 중지 합니다.

2. 보조 인스턴스에 대 한 새 지역, 끝점 및 가상 네트워크 정보를 사용 하 여 Azure-SSIS IR를 편집 합니다.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Azure-SSIS IR를 다시 시작 합니다.

### <a name="scenario-3-attach-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>시나리오 3: 새 Azure-SSIS IR에 기존 SSISDB (SSIS 카탈로그) 연결

이 시나리오는 보조 지역에 새 Azure-SSIS IR를 프로 비전 하려는 경우에 적합 합니다. 현재 지역에서 Azure Data Factory 또는 Azure-SSIS IR 재해가 발생할 때 새 지역에서 새 Azure-SSIS IR를 계속 사용 하려는 경우에도 적합 합니다.

#### <a name="solution"></a>솔루션

장애 조치 (failover)가 발생 하는 경우 다음 단계를 수행 합니다.

> [!NOTE]
> 4 단계 (IR 생성)에 PowerShell을 사용 합니다. 그렇지 않으면 Azure Portal는 SSISDB가 이미 존재 한다는 오류를 보고 합니다.

1. 주 지역에서 Azure-SSIS IR를 중지 합니다.

2. ** \< New_data_factory_name \> ** 및 ** \< NEW_INTEGRATION_RUNTIME_NAME \> **의 연결을 허용 하도록 SSISDB의 메타 데이터를 업데이트 하는 저장 프로시저를 실행 합니다.
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. 새 지역에 ** \< new_data_factory_name \> ** 라는 새 데이터 팩터리를 만듭니다.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
    ```
    
    이 PowerShell 명령에 대 한 자세한 내용은 [powershell을 사용 하 여 Azure 데이터 팩터리 만들기](quickstart-create-data-factory-powershell.md)를 참조 하세요.

4. Azure PowerShell를 사용 하 여 새 지역에 ** \< new_integration_runtime_name \> ** 라는 새 Azure-SSIS IR를 만듭니다.

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

    이 PowerShell 명령에 대 한 자세한 내용은 [Azure Data Factory에서 AZURE SSIS 통합 런타임 만들기](create-azure-ssis-integration-runtime.md)를 참조 하세요.


## <a name="next-steps"></a>다음 단계

Azure-SSIS IR에 대한 이러한 기타 구성 옵션이 좋습니다.

- [고성능을 위해 Azure SSIS 통합 런타임 구성](configure-azure-ssis-integration-runtime-performance.md)

- [Azure-SSIS 통합 런타임을 위한 사용자 지정 설치](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Azure SSIS 통합 런타임에 대 한 Enterprise Edition 프로 비전](how-to-configure-azure-ssis-ir-enterprise-edition.md)
