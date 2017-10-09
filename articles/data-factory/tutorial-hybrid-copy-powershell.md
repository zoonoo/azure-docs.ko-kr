---
title: "Azure Data Factory를 사용하여 클라우드에 온-프레미스 데이터 복사 | Microsoft Docs"
description: "Azure Data Factory에서 자체 호스팅 통합 런타임을 사용하여 온-프레미스 데이터 저장소에서 Azure 클라우드로 데이터를 복사하는 방법을 알아봅니다."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/14/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: 60641ddfef7846f0e8b5d850e716b2652bf62367
ms.contentlocale: ko-kr
ms.lasthandoff: 09/26/2017

---

# <a name="copy-data-between-on-premises-and-cloud"></a>온-프레미스 및 클라우드 간 데이터 복사
Azure Data Factory는 데이터 이동 및 데이터 변환을 오케스트레이션하고 자동화하기 위해 클라우드에서 데이터 기반 워크플로를 만들 수 있는 클라우드 기반 데이터 통합 서비스입니다. Azure Data Factory를 사용하여 서로 다른 데이터 저장소에서 데이터를 수집할 수 있는 데이터 기반 워크플로(파이프라인이라고 함)를 만들고 일정을 조정하며, Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics 및 Azure Machine Learning과 같은 계산 서비스를 사용하여 데이터를 처리/변환하고, 사용할 BI(비즈니스 인텔리전스) 응용 프로그램의 Azure SQL Data Warehouse와 같은 데이터 저장소에 출력 데이터를 게시할 수 있습니다. 

이 자습서에서는 Azure PowerShell을 사용하여 온-프레미스 SQL Server 데이터베이스에서 Azure Blob 저장소로 데이터를 복사하는 Data Factory 파이프라인을 만듭니다. Azure Data Factory의 자체 호스팅 IR(통합 런타임)을 만들어 사용하면 온-프레미스 데이터 저장소와 클라우드 데이터 저장소를 통합할 수 있습니다.  다른 도구/SDK를 사용하여 데이터 팩터리를 만드는 방법을 알아보려면 [빠른 시작](quickstart-create-data-factory-dot-net.md)을 참조하세요. 

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 자체 호스팅 통합 런타임을 만듭니다.
> * 자체 호스팅 통합 런타임에서 온-프레미스 SQL Server 연결된 서비스를 만들고 암호화합니다.
> * Azure Storage 연결된 서비스를 만듭니다.
> * SQL Server 및 Azure Storage 데이터 집합을 만듭니다.
> * 데이터를 이동하는 복사 작업으로 파이프라인을 만듭니다.
> * 파이프라인 실행을 시작합니다.
> * 파이프라인 및 작업 실행을 모니터링합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* **SQL Server**. 이 자습서에서는 온-프레미스 SQL Server 데이터베이스를 **원본** 데이터 저장소로 사용합니다. 
* **Azure Storage 계정**. 이 자습서에서는 Azure Blob 저장소를 **대상/싱크** 데이터 저장소로 사용합니다. Azure Storage 계정이 없는 경우 새로 만드는 단계는 [저장소 계정 만들기](../storage/common/storage-create-storage-account.md#create-a-storage-account) 문서를 참조하세요.
* **Azure PowerShell**. [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-azurerm-ps)의 지침을 따르세요.

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
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    $df = Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    다음 사항에 유의하세요.

    * Azure Data Factory 이름은 전역적으로 고유해야 합니다. 다음 오류가 표시되면 이름을 변경하고 다시 시도하세요.

        ```
        Data factory name "<data factory name>" is not available.
        ```

    * Data Factory 인스턴스를 만들려면 Azure 구독의 참가자 또는 관리자여야 합니다.
    * 현재 미국 동부 및 미국 동부 2 지역에서만 Data Factory를 사용하여 데이터 팩터리를 만들 수 있습니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.

## <a name="create-a-self-hosted-ir"></a>자체 호스팅 IR 만들기

이 섹션에서는 자체 호스팅 통합 런타임을 만들고 이를 온-프레미스 노드(컴퓨터)와 연결할 수 있습니다.

1. 자체 호스팅 통합 런타임을 만듭니다. 동일한 이름의 다른 통합 런타임이 있는 경우 고유한 이름을 사용합니다.

   ```powershell
   $integrationRuntimeName = "<your integration runtime name>"
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   샘플 출력은 다음과 같습니다.

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. 다음 명령을 실행하여 만든 통합 런타임의 상태를 검색합니다.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   샘플 출력은 다음과 같습니다.

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption : 
   Version                   : 
   Capabilities              : {}
   ScheduledUpdateDate       : 
   UpdateDelayOffset         : 
   LocalTimeZoneOffset       : 
   AutoUpdate                : 
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. 다음 명령을 실행하여 인증 키를 검색하여 자체 호스팅 통합 런타임을 클라우드의 Data Factory 서비스에 등록합니다. 자체 호스팅 통합 런타임을 등록하기 위한 키 중 하나를 복사합니다.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   샘플 출력은 다음과 같습니다. 

   ```json
   {
       "AuthKey1":  "IR@8437c862-d6a9-4fb3-87dd-7d4865a9e845@ab1@eu@VDnzgySwUfaj3pfSUxpvfsXXXXXXx4GHiyF4wboad0Y=",
       "AuthKey2":  "IR@8437c862-d6a9-4fb3-85dd-7d4865a9e845@ab1@eu@sh+k/QNJGBltXL46vXXXXXXXXOf/M1Gne5aVqPtbweI="
   }
   ```

4. 로컬 Windows 컴퓨터에서 자체 호스팅 통합 런타임을 [다운로드](https://www.microsoft.com/download/details.aspx?id=39717)하고, 이전 단계에서 얻은 인증 키를 사용하여 자체 호스팅 통합 런타임을 수동으로 등록합니다. 

   ![통합 런타임 등록](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

   자체 호스팅 통합 런타임이 성공적으로 등록되면 다음 메시지가 표시됩니다. 

   ![성공적으로 등록되었습니다.](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

   노드가 클라우드 서비스에 연결되면 다음 페이지가 표시됩니다. 
    
   ![노드가 연결됨](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

## <a name="create-linked-services"></a>연결된 서비스 만들기 

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Azure Storage 연결된 서비스(대상/싱크) 만들기

1. **C:\ADFv2Tutorial** 폴더에 다음 내용이 포함된 **AzureStorageLinkedService.json**이라는 JSON 파일을 만듭니다. 아직 없는 경우 ADFv2Tutorial 폴더를 만듭니다.  &lt;accountname&gt; 및 &lt;accountkey&gt;를 Azure Storage 계정의 이름과 키로 바꿉니다.

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": { 
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```

2. **Azure PowerShell**에서 **ADFv2Tutorial** 폴더로 전환합니다.

   **Set-AzureRmDataFactoryV2LinkedService** cmdlet을 실행하여 **AzureStorageLinkedService** 연결된 서비스를 만듭니다. 이 자습서에서 사용되는 cmdlet은 **ResourceGroupName** 및 **DataFactoryName** 매개 변수에 대한 값을 가져옵니다. 또는 cmdlet을 실행할 때마다 ResourceGroupName 및 DataFactoryName을 입력하지 않고 Set-AzureRmDataFactoryV2 cmdlet에서 반환한 **DataFactory** 개체를 전달할 수 있습니다.

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   샘플 출력은 다음과 같습니다.

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>SQL Server 연결된 서비스(원본) 만들기 및 암호화

1. **C:\ADFv2Tutorial** 폴더에 다음 내용이 포함된 **SqlServerLinkedService.json**이라는 JSON 파일을 만듭니다. 파일을 저장하기 전에 **&lt;servername>**, **&lt;databasename>**, **&lt;username>**, **&lt;servername>** 및 **&lt;password>**를 SQL Server의 값으로 바꿉니다. **&lt;integration** **runtime** **name>**을 통합 런타임 이름으로 바꿉니다. 

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```
2. 온-프레미스 자체 호스팅 통합 런타임에서 JSON 페이로드의 중요한 데이터를 암호화하기 위해 **New-AzureRmDataFactoryV2LinkedServiceEncryptCredential**을 실행하여 위의 JSON 페이로드를 전달할 수 있습니다. 이 암호화를 사용하면 DPAPI(데이터 보호 API)를 사용하여 자격 증명을 암호화하고 자체 호스팅 통합 런타임 노드에 로컬로 저장합니다. 출력 페이로드는 암호화된 자격 증명을 포함하는 다른 JSON 파일(이 경우 'encryptedLinkedService.json')로 리디렉션될 수 있습니다. 

    명령을 실행하기 전에 **&lt;integration runtime name&gt;**을 통합 런타임 이름으로 바꿉니다.

   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName <integration runtime name> -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. 이전 단계의 JSON을 통해 다음 명령을 실행하여 **SqlServerLinkedService**를 만듭니다.

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json" 
   ```


## <a name="create-datasets"></a>데이터 집합 만들기

### <a name="prepare-an-on-premises-sql-server-for-the-tutorial"></a>자습서에서 사용할 온-프레미스 SQL Server 준비

이 단계에서는 복사 작업(온-프레미스 SQL Server 데이터베이스 = > Azure Blob Storage)의 입력 및 출력 데이터를 나타내는 입력 및 출력 데이터 집합을 만듭니다. 데이터 집합을 만들기 전에 다음 단계를 수행합니다(목록 뒤에 자세한 단계가 나옴).

- 데이터 팩터리에 연결된 서비스로 추가한 SQL Server 데이터베이스에서 **emp**라는 테이블을 만들고 테이블에 몇 가지 샘플 항목을 삽입합니다.
- 데이터 팩터리에 연결된 서비스로 추가한 Azure BLOB 저장소 계정에서 **adftutorial**로 명명된 BLOB 컨테이너를 만듭니다.


1. 온-프레미스 SQL Server 연결된 서비스(**SqlServerLinkedService**)에 대해 지정된 데이터베이스에서, 다음 SQL 스크립트를 사용하여 데이터베이스에 **emp** 테이블을 만듭니다.

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. 테이블에 일부 샘플을 삽입합니다.

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```



### <a name="create-a-dataset-for-source-sql-database"></a>원본 SQL Database에 대한 데이터 집합 만들기

1. **C:\ADFv2Tutorial** 폴더에 다음 내용이 포함된 **SqlServerDataset.json**이라는 JSON 파일을 만듭니다.  

    ```json
    {
       "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dbo.emp"
            },
            "structure": [
                 {
                    "name": "ID",
                    "type": "String"
                },
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "linkedServiceName": {
                "referenceName": "EncryptedSqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "SqlServerDataset"
    }
    ```

2. **Set-AzureRmDataFactoryV2Dataset** cmdlet을 실행하여 **SqlServerDataset** 데이터 집합을 만듭니다.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         : {"name": "ID" "type": "String", "name": "FirstName" "type": "String", "name": "LastName" "type": "String"}
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-blob-storage"></a>싱크 Azure Blob Storage에 대한 데이터 집합 만들기

1. **C:\ADFv2Tutorial** 폴더에 다음 내용이 포함된 **AzureBlobDataset.json**이라는 JSON 파일을 만듭니다. 

    > [!IMPORTANT]
    > 이 샘플 코드에서는 Azure Blob Storage에 **adftutorial**이라는 컨테이너가 있다고 가정합니다.

    ```json
    {
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/fromonprem",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "AzureBlobDataset"
    }
    ```

2. **Set-AzureRmDataFactoryV2Dataset** cmdlet을 실행하여 **AzureBlobDataset** 데이터 집합을 만듭니다.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-pipelines"></a>파이프라인 만들기

### <a name="create-the-pipeline-sqlservertoblobpipeline"></a>"SqlServerToBlobPipeline" 파이프라인 만들기

1. **C:\ADFv2Tutorial** 폴더에 다음 내용이 포함된 **SqlServerToBlobPipeline.json**이라는 JSON 파일을 만듭니다.

    ```json
    {
       "name": "SQLServerToBlobPipeline",
        "properties": {
            "activities": [       
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource"
                        },
                        "sink": {
                            "type":"BlobSink"
                        }
                    },
                    "name": "CopySqlServerToAzureBlobActivity",
                    "inputs": [
                        {
                            "referenceName": "SqlServerDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "AzureBlobDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ]
        }
    }
    ```

2. **Set-AzureRmDataFactoryV2Pipeline** cmdlet을 실행하여 **SQLServerToBlobPipeline** 파이프라인을 만듭니다.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```



## <a name="start-and-monitor-a-pipeline-run"></a>파이프라인 실행 시작 및 모니터링

1. "SQLServerToBlobPipeline" 파이프라인에 대한 파이프라인 실행을 시작하고 이후의 모니터링에 대한 파이프라인 실행 ID를 캡처합니다.  

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
    ```

2. 다음 스크립트를 실행하여 "**SQLServerToBlobPipeline**" 파이프라인의 실행 상태를 계속 확인하고 최종 결과를 출력합니다.

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    } 
    ```

    샘플 실행의 출력은 다음과 같습니다.

    ```jdon
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : copy
    PipelineRunId     : 4ec8980c-62f6-466f-92fa-e69b10f33640
    PipelineName      : SQLServerToBlobPipeline
    Input             :  
    Output            :  
    LinkedServiceName : 
    ActivityRunStart  : 9/13/2017 1:35:22 PM
    ActivityRunEnd    : 9/13/2017 1:35:42 PM
    DurationInMs      : 20824
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. 다음과 같이 "**SQLServerToBlobPipeline**" 파이프라인의 실행 ID를 가져오고, 자세한 작업 실행 결과를 확인할 수 있습니다.

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    샘플 실행의 출력은 다음과 같습니다.

    ```json
    {
      "dataRead": 36,
      "dataWritten": 24,
      "rowsCopied": 2,
      "copyDuration": 4,
      "throughput": 0.01,
      "errors": []
    }
    ```
4. 싱크 Azure Blob 저장소에 연결하여 Azure SQL Database의 데이터가 제대로 복사되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계
이 샘플의 파이프라인은 Azure Blob 저장소의 한 위치에서 다른 위치로 데이터를 복사합니다. 다음 방법에 대해 알아보았습니다. 

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 자체 호스팅 통합 런타임을 만듭니다.
> * 자체 호스팅 통합 런타임에서 온-프레미스 SQL Server 연결된 서비스를 만들고 암호화합니다.
> * Azure Storage 연결된 서비스를 만듭니다.
> * SQL Server 및 Azure Storage 데이터 집합을 만듭니다.
> * 데이터를 이동하는 복사 작업으로 파이프라인을 만듭니다.
> * 파이프라인 실행을 시작합니다.
> * 파이프라인 및 작업 실행을 모니터링합니다.

Azure Data Factory에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 문서를 참조하세요.

원본에서 대상으로 데이터를 대량으로 복사하는 방법을 알아보려면 다음 자습서로 진행하세요.

> [!div class="nextstepaction"]
>[대량 데이터 복사](tutorial-bulk-copy.md)
