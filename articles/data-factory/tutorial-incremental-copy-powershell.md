---
title: "Azure Data Factory를 사용하여 증분 방식으로 데이터 복사 | Microsoft Docs"
description: "이 자습서에서는 Azure SQL Database에서 Azure Blob Storage로 데이터를 증분 방식으로 복사하는 Azure Data Factory 파이프라인을 만듭니다."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/10/2017
ms.author: shlo
ms.openlocfilehash: a9d59e8ad2669b7a00ec83e019148bbac96f679f
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2017
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage"></a>Azure SQL Database에서 Azure Blob Storage로 증분 방식으로 데이터 로드
Azure Data Factory는 데이터 이동 및 데이터 변환을 오케스트레이션하고 자동화하기 위해 클라우드에서 데이터 기반 워크플로를 만들 수 있는 클라우드 기반 데이터 통합 서비스입니다. Azure Data Factory를 사용하여 서로 다른 데이터 저장소에서 데이터를 수집할 수 있는 데이터 기반 워크플로(파이프라인이라고 함)를 만들고 일정을 조정하며, Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics 및 Azure Machine Learning과 같은 계산 서비스를 사용하여 데이터를 처리/변환하고, 사용할 BI(비즈니스 인텔리전스) 응용 프로그램의 Azure SQL Data Warehouse와 같은 데이터 저장소에 출력 데이터를 게시할 수 있습니다. 

데이터 통합 과정에서 널리 사용되는 시나리오 중 하나는 정기적으로 데이터를 증분 방식으로 로드하여 초기 데이터 로드 및 분석 후에 업데이트된 분석 결과를 새로 고치는 것입니다. 이 자습서에서는 데이터 원본의 새 레코드 또는 업데이트된 레코드만 데이터 싱크로 로드하는 데 집중합니다. 특히 대규모 데이터 집합의 경우 전체 로드와 비교할 때 더 효율적으로 실행됩니다.    

Data Factory를 통해 파이프라인에서 조회, 복사 및 저장 프로시저 작업을 사용하여 증분 데이터 로드를 수행하는 상위 워터마크 솔루션을 만들 수 있습니다.  

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 워터마크 값을 저장할 데이터 저장소를 준비합니다.   
> * 데이터 팩터리를 만듭니다.
> * 연결된 서비스 만들기. 
> * 원본, 싱크, 워터마크 데이터 집합을 만듭니다.
> * 파이프라인을 만듭니다.
> * 파이프라인을 실행합니다.
> * 파이프라인 실행을 모니터링합니다. 

## <a name="overview"></a>개요
대략적인 솔루션 다이어그램은 다음과 같습니다. 

![데이터 증분 로드](media\tutorial-Incrementally-load-data-from-azure-sql-to-blob\incrementally-load.png)

이 솔루션을 만드는 중요한 단계는 다음과 같습니다. 

1. **워터마크 열을 선택합니다**.
    원본 데이터 저장소에서 하나의 열을 선택합니다. 이 열은 모든 실행에 대해 새 레코드 또는 업데이트된 레코드를 분할하는 데 사용할 수 있습니다. 선택한 이 열의 데이터(예: last_modify_time 또는 ID)는 일반적으로 행을 만들거나 업데이트할 때 계속 증가합니다. 이 열의 최대 값은 워터마크로 사용됩니다.
2. **워터마크 값을 저장할 데이터 저장소를 준비합니다**.   
    이 자습서에서는 Azure SQL 데이터베이스에 워터마크 값을 저장합니다.
3. **다음 워크플로를 사용하여 파이프라인을 만듭니다.** 
    
    이 솔루션의 파이프라인에 포함되는 작업은 다음과 같습니다.
  
    1. 두 개의 **조회** 작업을 만듭니다. 첫 번째 조회 작업을 사용하여 마지막 워터마크 값을 검색합니다. 두 번째 조회 작업을 사용하여 새 워터마크 값을 검색합니다. 이러한 워터마크 값은 복사 작업에 전달됩니다. 
    2. 이전 워터마크 값보다 크고, 새 워터마크 값보다 작은 워터마크 열 값으로 원본 데이터 저장소의 행을 복사하는 **복사 작업**을 만듭니다. 그런 다음 원본 데이터 저장소의 델타 데이터를 새 파일로 Blob 저장소에 복사합니다. 
    3. 다음에 실행되는 파이프라인에 대한 워터마크 값을 업데이트하는 **저장 프로시저 작업**을 만듭니다. 


Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건
* 
            **Azure SQL Database**. 데이터베이스를 **원본** 데이터 저장소로 사용합니다. 아직 없는 경우 Azure SQL Database를 만드는 단계는 [Azure SQL Database 만들기](../sql-database/sql-database-get-started-portal.md) 문서를 참조하세요.
* **Azure Storage 계정**. Blob 저장소를 **싱크** 데이터 저장소로 사용합니다. 아직 없는 경우 Azure Storage 계정을 만드는 단계는 [저장소 계정 만들기](../storage/common/storage-create-storage-account.md#create-a-storage-account) 문서를 참조하세요. **adftutorial**이라는 컨테이너를 만듭니다. 
* **Azure PowerShell**. [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-azurerm-ps)의 지침을 따르세요.

### <a name="create-a-data-source-table-in-your-azure-sql-database"></a>Azure SQL 데이터베이스에 데이터 원본 테이블 만들기
1. **SQL Server Management Studio**를 열고 **서버 탐색기**에서 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 선택합니다.
2. Azure SQL 데이터베이스에 대해 다음 SQL 명령을 실행하여 데이터 원본 저장소로 `data_source_table`이라는 테이블을 만듭니다.  
    
    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    이 자습서에서는 **LastModifytime**을 **워터마크** 열로 사용합니다.  데이터 원본 저장소의 데이터는 다음 테이블에 표시됩니다.

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-sql-database-to-store-the-high-watermark-value"></a>SQL 데이터베이스에 상위 워터마크 값을 저장하기 위한 다른 테이블 만들기
1. Azure SQL 데이터베이스에 대해 다음 SQL 명령을 실행하여 워터마크 값을 저장하는 `watermarktable` 테이블을 만듭니다.  
    
    ```sql
    create table watermarktable
    (
    
    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
3. 원본 데이터 저장소의 테이블 이름으로 상위 워터마크의 기본 **값**을 설정합니다.  이 자습서에서 테이블 이름은 **data_source_table**입니다.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
4. `watermarktable` 테이블의 데이터를 검토합니다.
    
    ```sql
    Select * from watermarktable
    ```
    출력: 

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-azure-sql-database"></a>Azure SQL 데이터베이스에 저장 프로시저 만들기 

다음 명령을 실행하여 Azure SQL 데이터베이스에 저장 프로시저를 만듭니다.

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN
    
    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName
    
END
```

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. **PowerShell**을 시작합니다. 이 자습서를 마칠 때까지 Azure PowerShell을 열어 두세요. 닫은 후 다시 여는 경우 명령을 다시 실행해야 합니다.

    다음 명령을 실행하고 Azure Portal에 로그인하는 데 사용할 사용자 이름 및 암호를 입력합니다.
        
    ```powershell
    Login-AzureRmAccount
    ```        
    다음 명령을 실행하여 이 계정의 모든 구독을 확인합니다.

    ```powershell
    Get-AzureRmSubscription
    ```
    다음 명령을 실행하여 사용하려는 구독을 선택합니다. **SubscriptionId**를 Azure 구독의 ID로 바꿉니다.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```
2. **Set-AzureRmDataFactoryV2** cmdlet을 실행하여 데이터 팩터리를 만듭니다. 이 명령을 실행하기 전에 자리 표시자를 사용자 고유의 값으로 바꿉니다.

    ```powershell
    Set-AzureRmDataFactoryV2 -ResourceGroupName "<your resource group to create the factory>" -Location "East US" -Name "<specify the name of data factory to create. It must be globally unique.>" 
    ```

    다음 사항에 유의하세요.

    * Azure Data Factory 이름은 전역적으로 고유해야 합니다. 다음 오류가 표시되면 이름을 변경하고 다시 시도하세요.

        ```
        The specified Data Factory name '<data factory name>' is already in use. Data Factory names must be globally unique.
        ```

    * Data Factory 인스턴스를 만들려면 Azure 구독의 참가자 또는 관리자여야 합니다.
    * 현재 미국 동부 지역에서만 Data Factory V2를 사용하여 데이터 팩터리를 만들 수 있습니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.


## <a name="create-linked-services"></a>연결된 서비스 만들기
데이터 팩터리에서 연결된 서비스를 만들어 데이터 저장소를 연결하고 계산 서비스를 데이터 팩터리에 연결합니다. 이 섹션에서는 Azure Storage 계정과 Azure SQL 데이터베이스에 연결된 서비스를 만듭니다. 

### <a name="create-azure-storage-linked-service"></a>Azure Storage 연결된 서비스를 만듭니다.
1. **C:\ADF** 폴더에 다음 내용이 포함된 **AzureStorageLinkedService.json**이라는 JSON 파일을 만듭니다. 아직 없는 경우 ADF 폴더를 만듭니다. 파일을 저장하기 전에 `<accountName>`과 `<accountKey>`를 Azure Storage 계정의 이름과 키로 바꿉니다.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. **Azure PowerShell**에서 **ADF** 폴더로 전환합니다.
3. **Set-AzureRmDataFactoryV2LinkedService** cmdlet을 실행하여 **AzureStorageLinkedService** 연결된 서비스를 만듭니다. 다음 예제에서는 **ResourceGroupName** 및 **DataFactoryName** 매개 변수에 대한 값을 전달합니다. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-azure-sql-database-linked-service"></a>Azure SQL Database 연결된 서비스 만들기
1. **C:\ADF** 폴더에 다음 내용이 포함된 **AzureSQLDatabaseLinkedService.json**이라는 JSON 파일을 만듭니다. 아직 없는 경우 ADF 폴더를 만듭니다 파일을 저장하기 전에 **&lt;server&gt; 및 &lt;user id&gt; 및 &lt;password&gt;**를 Azure SQL 서버 이름, 사용자 ID 및 암호의 값으로 바꿉니다. 

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "value": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database name>; Persist Security Info=False; User ID=<user name> ; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. **Azure PowerShell**에서 **ADF** 폴더로 전환합니다.
3. **Set-AzureRmDataFactoryV2LinkedService** cmdlet을 실행하여 **AzureSQLDatabaseLinkedService** 연결된 서비스를 만듭니다. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ProvisioningState :
    ```

## <a name="create-datasets"></a>데이터 집합 만들기
이 단계에서는 원본 및 싱크 데이터를 나타내는 데이터 집합을 만듭니다. 

### <a name="create-a-source-dataset"></a>원본 데이터 집합 만들기

1. 동일한 폴더에 다음 내용이 포함된 SourceDataset.json이라는 JSON 파일을 만듭니다. 

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "data_source_table"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
   
    ```
    이 자습서에서는 테이블 이름으로 **data_source_table**을 사용합니다. 다른 이름의 테이블을 사용하는 경우 해당 이름을 바꿉니다. 
2.  Set-AzureRmDataFactoryV2Dataset cmdlet을 실행하여 SourceDataset 데이터 집합을 만듭니다.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    cmdlet의 샘플 출력은 다음과 같습니다.
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>싱크 데이터 집합 만들기

1. 동일한 폴더에 다음 내용이 포함된 SinkDataset.json이라는 JSON 파일을 만듭니다. 

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incrementalcopy",
                "fileName": "@CONCAT('Incremental-', pipeline().RunId, '.txt')", 
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }   
    ```

    > [!IMPORTANT]
    > 이 코드 조각에서는 Azure Blob Storage에 **adftutorial**이라는 Blob 컨테이너가 있다고 가정합니다. 아직 없는 경우 컨테이너를 만들거나 기존 컨테이너의 이름으로 설정합니다. `incrementalcopy` 출력 폴더가 컨테이너에 없는 경우 자동으로 만들어집니다. 이 자습서에서 파일 이름은 `@CONCAT('Incremental-', pipeline().RunId, '.txt')` 식을 사용하여 동적으로 생성됩니다.
2.  Set-AzureRmDataFactoryV2Dataset cmdlet을 실행하여 SinkDataset 데이터 집합을 만듭니다.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    cmdlet의 샘플 출력은 다음과 같습니다.
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset    
    ```

## <a name="create-a-dataset-for-watermark"></a>워터마크에 대한 데이터 집합 만들기
이 단계에서는 상위 워터마크 값을 저장하기 위한 데이터 집합을 만듭니다. 

1. 동일한 폴더에 다음 내용이 포함된 WatermarkDataset.json이라는 JSON 파일을 만듭니다. 

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
2.  Set-AzureRmDataFactoryV2Dataset cmdlet을 실행하여 WatermarkDataset 데이터 집합을 만듭니다.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    cmdlet의 샘플 출력은 다음과 같습니다.
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>파이프라인을 만듭니다.
이 자습서에서는 하나의 파이프라인에 연결되는 두 개의 조회 작업, 하나의 복사 작업 및 하나의 저장 프로시저 작업이 있는 파이프라인을 만듭니다. 


1. 동일한 폴더에 다음 내용이 포함된 IncrementalCopyPipeline.json이라는 JSON 파일을 만듭니다. 

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "LookupOldWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from watermarktable"
                        },
    
                        "dataset": {
                        "referenceName": "WatermarkDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                {
                    "name": "LookupNewWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select MAX(LastModifytime) as NewWatermarkvalue from data_source_table"
                        },
    
                        "dataset": {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                
                {
                    "name": "IncrementalCopyActivity",
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupNewWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        },
                        {
                            "activity": "LookupOldWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ],
    
                    "inputs": [
                        {
                            "referenceName": "SourceDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "SinkDataset",
                            "type": "DatasetReference"
                        }
                    ]
                },
    
                {
                    "name": "StoredProceduretoWriteWatermarkActivity",
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
    
                        "storedProcedureName": "sp_write_watermark",
                        "storedProcedureParameters": {
                            "LastModifiedtime": {"value": "@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}", "type": "datetime" },
                            "TableName":  { "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}", "type":"String"}
                        }
                    },
    
                    "linkedServiceName": {
                        "referenceName": "AzureSQLDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
    
                    "dependsOn": [
                        {
                            "activity": "IncrementalCopyActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ]
                }
            ]
            
        }
    }
    ```
    

2. Set-AzureRmDataFactoryV2Pipeline cmdlet을 실행하여 IncrementalCopyPipeline 파이프라인을 만듭니다.
    
   ```powershell
   Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   샘플 출력은 다음과 같습니다. 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Activities        : {LookupOldWaterMarkActivity, LookupNewWaterMarkActivity, IncrementalCopyActivity, StoredProceduretoWriteWatermarkActivity}
    Parameters        :
   ```
 
## <a name="run-the-pipeline"></a>파이프라인 실행

1. **Invoke-AzureRmDataFactoryV2Pipeline** cmdlet을 사용하여 **IncrementalCopyPipeline** 파이프라인을 실행합니다. 자리 표시자를 사용자 고유의 리소스 그룹 및 데이터 팩터리 이름으로 바꿉니다.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup "<your resource group>" -dataFactoryName "<your data factory name>"
    ``` 
2. Get-AzureRmDataFactoryV2ActivityRun cmdlet을 실행하여 모든 작업이 성공적으로 실행되었다고 표시될 때까지 파이프라인 상태를 확인합니다. 자리 표시자를 RunStartedAfter 및 RunStartedBefore 매개 변수에 대한 사용자 고유의 적절한 시간으로 바꿉니다.  이 자습서에서는 -RunStartedAfter "2017/09/14" -RunStartedBefore "2017/09/15"를 사용합니다.

    ```powershell
    Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    샘플 출력은 다음과 같습니다.
 
    ```json
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:42:50 AM
    DurationInMs      : 7777
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:43:07 AM
    DurationInMs      : 25437
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:10 AM
    ActivityRunEnd    : 9/14/2017 7:43:29 AM
    DurationInMs      : 19769
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:32 AM
    ActivityRunEnd    : 9/14/2017 7:43:47 AM
    DurationInMs      : 14467
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```

## <a name="review-the-results"></a>결과 검토

1. Azure Blob 저장소(싱크 저장소)에서 SinkDataset에 정의된 파일에 데이터가 복사되었는지 확인해야 합니다.  현재 자습서에서 파일 이름은 `Incremental- d4bf3ce2-5d60-43f3-9318-923155f61037.txt`입니다.  이 파일을 열면 Azure SQL 데이터베이스의 데이터와 동일한 파일의 레코드를 볼 수 있습니다.

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ``` 
2. `watermarktable`에서 최신 값을 확인하면 업데이트된 워터마크 값이 표시됩니다.

    ```sql
    Select * from watermarktable
    ```
    
    샘플 출력은 다음과 같습니다.
 
    TableName | WatermarkValue
    --------- | --------------
    data_source_table   2017-09-05  8:06:00.000

### <a name="insert-data-into-data-source-store-to-verify-delta-data-loading"></a>데이터 원본 저장소에 데이터를 삽입하여 델타 데이터 로드 확인

1. Azure SQL 데이터베이스(데이터 원본 저장소)에 새 데이터를 삽입합니다.

    ```sql
    INSERT INTO data_source_table
    VALUES (6, 'newdata','9/6/2017 2:23:00 AM')
    
    INSERT INTO data_source_table
    VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
    ``` 

    Azure SQL 데이터베이스에서 업데이트된 데이터는 다음과 같습니다.

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    6 | newdata | 2017-09-06 02:23:00.000
    7 | newdata | 2017-09-07 09:01:00.000
    ```
2. **Invoke-AzureRmDataFactoryV2Pipeline** cmdlet을 사용하여 **IncrementalCopyPipeline** 파이프라인을 다시 실행합니다. 자리 표시자를 사용자 고유의 리소스 그룹 및 데이터 팩터리 이름으로 바꿉니다.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup "<your resource group>" -dataFactoryName "<your data factory name>"
    ```
3. **Get-AzureRmDataFactoryV2ActivityRun** cmdlet을 실행하여 모든 작업이 성공적으로 실행되었다고 표시될 때까지 파이프라인 상태를 확인합니다. 자리 표시자를 RunStartedAfter 및 RunStartedBefore 매개 변수에 대한 사용자 고유의 적절한 시간으로 바꿉니다.  이 자습서에서는 -RunStartedAfter "2017/09/14" -RunStartedBefore "2017/09/15"를 사용합니다.

    ```powershell
    Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    샘플 출력은 다음과 같습니다.
 
    ```json
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:58 AM
    DurationInMs      : 31758
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:52 AM
    DurationInMs      : 25497
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:00 AM
    ActivityRunEnd    : 9/14/2017 8:53:20 AM
    DurationInMs      : 20194
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:23 AM
    ActivityRunEnd    : 9/14/2017 8:53:41 AM
    DurationInMs      : 18502
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```
4.  Azure Blob 저장소에서 다른 파일이 Azure Blob 저장소에 만들어졌는지 확인해야 합니다. 이 자습서에서 새 파일 이름은 `Incremental-2fc90ab8-d42c-4583-aa64-755dba9925d7.txt`입니다.  해당 파일을 열면 2개 행의 레코드가 표시됩니다.
5.  `watermarktable`의 최신 값을 확인하면 워터마크 값이 다시 업데이트되었음을 알 수 있습니다.

    ```sql
    Select * from watermarktable
    ```
    샘플 출력은 다음과 같습니다. 
    
    TableName | WatermarkValue
    --------- | ---------------
    data_source_table | 2017-09-07 09:01:00.000

     
## <a name="next-steps"></a>다음 단계
이 자습서에서 다음 단계를 수행했습니다. 

> [!div class="checklist"]
> * **워터마크** 열을 정의하고 Azure SQL Database에 저장합니다.  
> * 데이터 팩터리를 만듭니다.
> * SQL Database 및 Blob Storage에 대한 연결된 서비스를 만듭니다. 
> * 원본 및 싱크 데이터 집합을 만듭니다.
> * 파이프라인을 만듭니다.
> * 파이프라인을 실행합니다.
> * 파이프라인 실행을 모니터링합니다. 

Azure에서 Spark 클러스터를 사용하여 데이터를 변환하는 방법을 알아보려면 다음 자습서로 진행하세요.

> [!div class="nextstepaction"]
>[클라우드에서 Spark 클러스터를 사용하여 데이터 변환](tutorial-transform-data-spark-powershell.md)



