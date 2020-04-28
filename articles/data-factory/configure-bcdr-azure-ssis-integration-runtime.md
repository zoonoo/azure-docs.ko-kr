---
title: SQL Database 장애 조치 (failover)에 대 한 Azure-SSIS Integration Runtime 구성
description: 이 문서에서는 SSISDB 데이터베이스에 대해 Azure SQL Database 지역에서 복제 및 장애 조치(failover)를 사용하여 Azure SSIS Integration Runtime을 구성하는 방법을 설명합니다.
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
ms.openlocfilehash: 39d55d4372f03a1625bb04d8377ed6533401e281
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188725"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Azure SQL Database 지역에서 복제 및 장애 조치(failover)를 사용하여 Azure-SSIS Integration Runtime 구성

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 SSISDB 데이터베이스에 대해 Azure SQL Database 지역에서 복제를 사용하여 Azure SSIS Integration Runtime을 구성하는 방법을 설명합니다. 장애 조치(Failover)가 발생하는 경우 Azure-SSIS IR이 보조 데이터베이스를 계속 사용하는지 확인할 수 있습니다.

SQL Database의 지역에서 복제 및 장애 조치(Failover)에 대한 자세한 내용은 [개요: 활성 지역 복제 및 자동 장애 조치(Failover) 그룹](../sql-database/sql-database-geo-replication-overview.md)을 참조하세요.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-azure-sql-database-managed-instance"></a>Azure-SSIS IR 장애 조치 (failover) Azure SQL Database Managed Instance

### <a name="prerequisites"></a>사전 요구 사항
1. 주 인스턴스의 SSISDB에서 아래 명령을 실행 합니다. 이 단계에서는 새 암호화 암호를 추가 합니다.
```sql
  ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
```

2. Azure SQL Database Managed Instance에서 장애 조치 (failover) 그룹을 만듭니다.

3. 새 암호화 암호를 사용 하 여 보조 인스턴스에서 **sp_control_dbmasterkey_password** 를 실행 합니다.
```sql
  EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
    @password = N'<password>', @action = N'add';  
  GO
```

### <a name="solution"></a>솔루션
장애 조치 (failover)가 발생 하는 경우 주 지역에서 기존 Azure-SSIS IR를 사용 하려면 다음을 수행 합니다.
1. 주 지역에서 Azure-SSIS IR를 중지 합니다.

2. 보조 인스턴스의 새 지역, 끝점 및 VNET 정보를 사용 하 여 Azure-SSIS IR를 편집 합니다.

```powershell
  Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
```

3. Azure-SSIS IR를 다시 시작 합니다.

4. SSIS 패키지의 **ConnectionManager** 에서 서버 이름을 보조 인스턴스 서버 이름으로 변경한 다음 이러한 패키지를 다시 배포 하 고 실행 합니다.

보조 지역에 새 Azure-SSIS IR를 프로 비전 하려는 경우:
> [!NOTE]
> 4 단계 (IR 생성)는 PowerShell을 통해 수행 해야 합니다. Azure Portal은 SSISDB가 이미 있다는 오류를 보고 합니다.
1. 주 지역에서 Azure-SSIS IR를 중지 합니다.

2. ** \<New_data_factory_name\> ** **및 \<new_integration_runtime_name\>** 의 연결을 허용 하도록 SSISDB의 메타 데이터를 업데이트 하는 저장 프로시저를 실행 합니다.
   
```SQL
  EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
```

3. 새 지역에 ** \<new_data_factory_name\> ** 라는 새 데이터 팩터리를 만듭니다. 자세한 내용은 데이터 팩터리 만들기를 참조 하세요.

```powershell
  Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
```
  이 PowerShell 명령에 대 한 자세한 내용은 [powershell을 사용 하 여 Azure 데이터 팩터리 만들기](quickstart-create-data-factory-powershell.md) 를 참조 하세요.

4. Azure PowerShell를 사용 하 여 새 지역에 ** \<new_integration_runtime_name\> ** 라는 새 Azure-SSIS IR를 만듭니다.

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

  이 PowerShell 명령에 대한 자세한 내용은 [Azure Data Factory에서 Azure-SSIS 통합 런타임 만들기](create-azure-ssis-integration-runtime.md) 참조

5. SSIS 패키지의 **ConnectionManager** 에서 서버 이름을 보조 인스턴스 서버 이름으로 변경한 다음 이러한 패키지를 다시 배포 하 고 실행 합니다.



## <a name="azure-ssis-ir-failover-with-azure-sql-database"></a>Azure-SSIS IR 장애 조치 (failover) Azure SQL Database

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>시나리오 1 - Azure-SSIS IR이 읽기-쓰기 수신기 엔드포인트를 가리킴

#### <a name="conditions"></a>조건

이 섹션은 다음 조건이 충족되면 적용됩니다.

- Azure-SSIS IR이 장애 조치(Failover) 그룹의 읽기-쓰기 수신기 엔드포인트를 가리키고 있습니다.

  AND

- SQL Database 서버가 가상 네트워크 서비스 엔드포인트 규칙으로 구성되지 *않았습니다*.

#### <a name="solution"></a>솔루션

장애 조치(Failover)가 발생하는 경우 Azure-SSIS IR에 투명하게 진행됩니다. Azure-SSIS IR은 장애 조치(Failover) 그룹의 새로운 주 데이터베이스에 자동으로 연결됩니다.


### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>시나리오 2 - Azure-SSIS IR이 주 서버 엔드포인트를 가리킴

#### <a name="conditions"></a>조건

이 섹션은 다음 조건 중 하나라도 충족되면 적용됩니다.

- Azure-SSIS IR이 장애 조치(Failover) 그룹의 주 서버 엔드포인트를 가리키고 있습니다. 이 엔드포인트는 장애 조치(Failover)가 발생하면 변경됩니다.

  또는

- Azure SQL Database 서버가 가상 네트워크 서비스 엔드포인트 규칙으로 구성되어 있습니다.


#### <a name="solution"></a>솔루션

1. 주 지역에서 Azure-SSIS IR를 중지 합니다.

2. 보조 인스턴스의 새 지역, 끝점 및 VNET 정보를 사용 하 여 Azure-SSIS IR를 편집 합니다.

```powershell
  Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
```

3. Azure-SSIS IR를 다시 시작 합니다.

4. SSIS 패키지의 **ConnectionManager** 에서 서버 이름을 보조 인스턴스 서버 이름으로 변경한 다음 이러한 패키지를 다시 배포 하 고 실행 합니다.


### <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>시나리오 3-새 Azure-SSIS IR에 기존 SSISDB (SSIS 카탈로그) 연결

현재 지역에서 ADF 또는 Azure-SSIS IR 재해가 발생 하면 SSISDB가 새 지역에서 새 Azure-SSIS IR를 계속 사용 하도록 할 수 있습니다.

#### <a name="solution"></a>솔루션

> [!NOTE]
> 4 단계 (IR 생성)는 PowerShell을 통해 수행 해야 합니다. Azure Portal은 SSISDB가 이미 있다는 오류를 보고 합니다.

1. 주 지역에서 Azure-SSIS IR를 중지 합니다.

2. ** \<New_data_factory_name\> ** **및 \<new_integration_runtime_name\>** 의 연결을 허용 하도록 SSISDB의 메타 데이터를 업데이트 하는 저장 프로시저를 실행 합니다.
   
```SQL
  EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
```

3. 새 지역에 ** \<new_data_factory_name\> ** 라는 새 데이터 팩터리를 만듭니다. 자세한 내용은 데이터 팩터리 만들기를 참조 하세요.

```powershell
  Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
```
  이 PowerShell 명령에 대 한 자세한 내용은 [powershell을 사용 하 여 Azure 데이터 팩터리 만들기](quickstart-create-data-factory-powershell.md) 를 참조 하세요.

4. Azure PowerShell를 사용 하 여 새 지역에 ** \<new_integration_runtime_name\> ** 라는 새 Azure-SSIS IR를 만듭니다.

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

  이 PowerShell 명령에 대한 자세한 내용은 [Azure Data Factory에서 Azure-SSIS 통합 런타임 만들기](create-azure-ssis-integration-runtime.md) 참조

5. SSIS 패키지의 **ConnectionManager** 에서 서버 이름을 보조 인스턴스 서버 이름으로 변경한 다음 이러한 패키지를 다시 배포 하 고 실행 합니다.


## <a name="next-steps"></a>다음 단계

Azure-SSIS IR에 대한 이러한 기타 구성 옵션이 좋습니다.

- [고성능을 위해 Azure-SSIS Integration Runtime 구성](configure-azure-ssis-integration-runtime-performance.md)

- [Azure-SSIS 통합 런타임을 위한 사용자 지정 설치](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Azure-SSIS Integration Runtime Enterprise 버전 프로비전](how-to-configure-azure-ssis-ir-enterprise-edition.md)
