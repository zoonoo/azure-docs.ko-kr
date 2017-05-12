---
title: "자습서: Azure PowerShell을 사용하여 데이터를 이동하는 파이프라인 만들기 | Microsoft Docs"
description: "이 자습서에서는 Azure PowerShell을 사용하여 복사 작업을 사용하는 Azure Data Factory 파이프라인을 만듭니다."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 71087349-9365-4e95-9847-170658216ed8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/11/2017
ms.author: spelluru
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 8b5cb66ea958cf6643fa34abb8d484b97b212373
ms.contentlocale: ko-kr
ms.lasthandoff: 04/27/2017


---
# <a name="tutorial-create-a-data-factory-pipeline-that-moves-data-by-using-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 데이터를 이동하는 Data Factory 파이프라인 만들기
> [!div class="op_single_selector"]
> * [개요 및 필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [복사 마법사](data-factory-copy-data-wizard-tutorial.md)
> * [Azure 포털](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager 템플릿](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
>
>

이 자습서에서는 Azure PowerShell cmdlet을 사용하여 Azure Data Factory 인스턴스를 만들고 모니터링합니다. 이 자습서에서 만든 Data Factory의 파이프라인은 복사 작업을 사용하여 Azure Blob에서 Azure SQL Database로 데이터를 복사합니다.

복사 작업 기능은 Data Factory에서 데이터 이동을 수행합니다. 이 작업은 다양한 데이터 저장소 간에 데이터를 안전하고 안정적이며 확장성 있는 방법으로 복사할 수 있는 전역적으로 사용 가능한 서비스를 통해 이루어집니다. 복사 작업에 대한 자세한 내용은 [데이터 이동 작업](data-factory-data-movement-activities.md)을 참조하세요.   

> [!NOTE]
> 이 문서는 모든 데이터 팩터리 cmdlet을 다루지 않습니다. 이러한 cmdlet에 대한 포괄적인 설명서는 [Data Factory Cmdlet 참조](/powershell/module/azurerm.datafactories)를 참조하세요.
>
> 이 자습서에서 데이터 파이프라인은 원본 데이터 저장소의 데이터를 대상 데이터 저장소로 복사합니다. 출력 데이터를 생성하기 위해 입력 데이터를 변환하지 않습니다. Azure Data Factory를 사용하여 데이터를 변환하는 방법에 대한 자습서는 [자습서: Hadoop 클러스터를 사용하여 데이터를 변환하도록 파이프라인 빌드](data-factory-build-your-first-pipeline.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
- [자습서 개요 및 필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) 를 살펴보고 자습서 개요를 가져와서 **필수 구성 요소** 를 완료합니다.
- Azure PowerShell을 설치합니다. [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)의 지침을 따르세요.

## <a name="in-this-tutorial"></a>자습서 내용
다음 테이블에서는 자습서의 일부로 수행하는 단계를 보여 줍니다.

| 단계 | 설명 |
| --- | --- |
| [Azure Data Factory 만들기](#create-data-factory) |이 단계에서는 **ADFTutorialDataFactoryPSH**라는 Azure Data Factory를 만듭니다. |
| [연결된 서비스 만들기](#create-linked-services) |이 단계에서는 2개의 연결된 서비스인 **StorageLinkedService** 및 **AzureSqlLinkedService**를 만듭니다. StorageLinkedService는 Azure Storage 서비스를 연결하고, AzureSqlLinkedService는 Azure SQL Database를 ADFTutorialDataFactoryPSH에 연결합니다. |
| [입력 및 출력 데이터 집합을 만듭니다.](#create-datasets) |이 단계에서는 두 데이터 집합(EmpTableFromBlob 및 EmpSQLTable)을 정의합니다. 이러한 데이터 집합은 다음 단계에서 만들 ADFTutorialPipeline의 **복사 작업** 에 대한 입력 및 출력 테이블로 사용됩니다. |
| [파이프라인 만들기 및 실행](#create-pipeline) |이 단계에서는 ADFTutorialDataFactoryPSH Data Factory에 **ADFTutorialPipeline**이라는 파이프라인을 만듭니다. 이 파이프라인에서는 복사 작업을 사용하여 Azure BLOB에서 출력 Azure 데이터베이스 테이블로 데이터를 복사합니다. |
| [데이터 집합 및 파이프라인 모니터링](#monitor-pipeline) |Azure PowerShell을 사용하여 데이터 집합 및 파이프라인을 모니터링합니다. |

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.
이 단계에서는 Azure PowerShell을 사용하여 **ADFTutorialDataFactoryPSH**라는 Azure Data Factory를 만듭니다.

1. **PowerShell**을 시작합니다. 이 자습서를 마칠 때까지 Azure PowerShell을 열어 두세요. 닫은 후 다시 여는 경우 명령을 다시 실행해야 합니다.

    다음 명령을 실행하고 Azure Portal에 로그인하는 데 사용할 사용자 이름 및 암호를 입력합니다.

    ```PowerShell
    Login-AzureRmAccount
    ```   
   
    다음 명령을 실행하여 이 계정의 모든 구독을 확인합니다.

    ```PowerShell
    Get-AzureRmSubscription
    ```

    다음 명령을 실행하여 사용하려는 구독을 선택합니다. **&lt;NameOfAzureSubscription**&gt;을 Azure 구독의 이름으로 바꿉니다.

    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
    ```
2. 다음 명령을 실행하여 **ADFTutorialResourceGroup** 이라는 Azure 리소스 그룹을 만듭니다.

    ```PowerShell
    New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```
    
    이 자습서의 일부 단계에서는 **ADFTutorialResourceGroup**이라는 리소스 그룹을 사용한다고 가정합니다. 다른 리소스 그룹을 사용하는 경우 이 자습서에서 ADFTutorialResourceGroup 대신 해당 리소스 그룹을 사용해야 합니다.
3. **New-AzureRmDataFactory** cmdlet을 실행하여 **ADFTutorialDataFactoryPSH**라는 Data Factory를 만듭니다.  

    ```PowerShell
    New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"
    ```
다음 사항에 유의하세요.

* Azure Data Factory 이름은 전역적으로 고유해야 합니다. 다음과 같은 오류가 발생하면 이름(예: yournameADFTutorialDataFactoryPSH)을 변경합니다. 이 자습서의 단계를 수행하는 동안 ADFTutorialFactoryPSH 대신 이 이름을 사용합니다. Data Factory 아티팩트에 대한 내용은 [Data Factory - 명명 규칙](data-factory-naming-rules.md)을 참조하세요.

    ```
    Data factory name “ADFTutorialDataFactoryPSH” is not available
    ```
* Data Factory 인스턴스를 만들려면 Azure 구독의 참가자 또는 관리자여야 합니다.
* Data Factory의 이름은 나중에 DNS 이름으로 표시되므로 공개적으로 등록될 수도 있습니다.
* "**구독이 Microsoft.DataFactory 네임스페이스를 사용하도록 등록되어 있지 않습니다.**"라는 오류가 발생할 수 있습니다. 다음 중 하나를 수행하고 다시 게시해 보세요.

  * Azure PowerShell에서 다음 명령을 실행하여 데이터 팩터리 공급자를 등록합니다.

    ```PowerShell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

    데이터 팩터리 공급자가 등록되어 있는지 확인하려면 다음 명령을 실행합니다.

    ```PowerShell
    Get-AzureRmResourceProvider
    ```
  * Azure 구독을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다. Azure Portal에서 Data Factory 블레이드로 이동하거나 Data Factory를 만듭니다. 이 작업은 공급자를 자동으로 등록합니다.

## <a name="create-linked-services"></a>연결된 서비스 만들기
연결된 서비스는 데이터 저장소 또는 계산 서비스를 Azure Data Factory에 연결합니다. 데이터 저장소는 Data Factory 파이프라인에 대한 입력 데이터를 포함하거나 출력 데이터를 저장하는 Azure Storage 서비스, Azure SQL Database 또는 온-프레미스 SQL Server Database일 수 있습니다. 계산 서비스는 입력 데이터를 처리하고 출력 데이터를 생성하는 서비스입니다.

이 단계에서는 2개의 연결된 서비스인 **StorageLinkedService** 및 **AzureSqlLinkedService**를 만듭니다. StorageLinkedService는 Azure Storage 계정을 연결하고, AzureSqlLinkedService는 Azure SQL Database를 **ADFTutorialDataFactoryPSH** Data Factory에 연결합니다. 이 자습서 뒷부분에서는 StorageLinkedService의 Blob 컨테이너에서 AzureSqlLinkedService의 SQL 테이블로 데이터를 복사하는 파이프라인을 만듭니다.

### <a name="create-a-linked-service-for-an-azure-storage-account"></a>Azure 저장소 계정에 대한 연결된 서비스 만들기
1. **C:\ADFGetStartedPSH** 폴더에 다음과 같은 내용으로 **StorageLinkedService.json**이라는 JSON 파일을 만듭니다. (아직 없는 경우 ADFGetStartedPSH 폴더를 만듭니다.)

    ```json
    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
     }
    ```
   **accountname** 및 **accountkey**를 Azure Storage 계정의 이름 및 키로 바꿉니다.
2. **Azure PowerShell**에서 **ADFGetStartedPSH** 폴더로 전환합니다.
3. **New-AzureRmDataFactoryLinkedService** cmdlet을 사용하여 연결된 서비스를 만들 수 있습니다. 이 자습서에서 사용하는 이 cmdlet 및 다른 Data Factory cmdlet의 경우 **ResourceGroupName** 및 **DataFactoryName** 매개 변수의 값을 전달해야 합니다. 또는 **Get-AzureRmDataFactory**를 사용하여 DataFactory 개체를 가져온 다음 cmdlet을 실행할 때마다 ResourceGroupName 및 DataFactoryName을 입력하지 않고 개체를 전달할 수 있습니다. 다음 명령을 실행하여 **Get-AzureRmDataFactory** cmdlet의 출력을 **$df** 변수에 할당합니다.

    ```PowerShell   
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
    ```

4. 이제 **New-AzureRmDataFactoryLinkedService** cmdlet을 실행하여 **StorageLinkedService** 연결된 서비스를 만듭니다.

    ```PowerShell
    New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json
    ```

    **Get-AzureRmDataFactory** cmdlet을 실행하고 출력을 **$df** 변수에 할당하지 않은 경우 ResourceGroupName 및 DataFactoryName 매개 변수의 값을 다음과 같이 지정해야 합니다.   

    ```PowerShell
    New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json
    ```

자습서 도중에 Azure PowerShell을 닫은 경우 자습서를 완료하려면 다음에 Azure PowerShell을 시작할 때 Get-AzureRmDataFactory cmdlet을 실행해야 합니다.

### <a name="create-a-linked-service-for-an-azure-sql-database"></a>Azure SQL Database에 대한 연결된 서비스 만들기
1. 다음과 같은 내용으로 AzureSqlLinkedService.json이라는 JSON 파일을 만듭니다.

    ```json
    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=<user>@<server>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
     }
    ```
   **servername**, **databasename**, **username@servername** 및 **password**를 Azure SQL Server의 이름, 데이터베이스, 사용자 계정 및 암호로 바꿉니다.
2. 다음 명령을 실행하여 연결된 서비스를 만듭니다.

    ```PowerShell
    New-AzureRmDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json
    ```

   SQL Database Server에 대해 **Azure 서비스에 대한 액세스 허용** 설정이 켜져 있는지 확인합니다. 이 설정을 확인하고 켜려면 다음 단계를 수행합니다.

   1. 왼쪽의 **찾아보기** 허브를 클릭하고 **SQL Server**를 클릭합니다.
   2. 서버를 선택하고 **SQL SERVER** 블레이드에서 **설정**을 클릭합니다.
   3. **설정** 블레이드에서 **방화벽**을 클릭합니다.
   4. **방화벽 설정** 블레이드에서 **Azure 서비스에 대한 액세스 허용**에 대해 **켜기**를 클릭합니다.
   5. 왼쪽의 **활성** 허브를 클릭하여 이전에 있던 **Data Factory** 블레이드로 전환합니다.

## <a name="create-datasets"></a>데이터 집합 만들기
이전 단계에서는 Azure Storage 계정과 Azure SQL Database를 데이터 Data Factory에 연결하는 서비스를 만들었습니다. 이 단계에서는 다음 단계에서 만들 파이프라인에 있는 복사 작업의 입력 및 출력 데이터를 나타내는 데이터 집합을 만듭니다.

테이블은 사각형 데이터 집합입니다. 현재 이 데이터 집합 유형만 지원됩니다. 이 자습서의 입력 테이블은 Azure Storage의 BLOB 컨테이너를 참조합니다. 출력 테이블은 Azure SQL Database의 SQL 테이블을 참조합니다.  

### <a name="prepare-azure-blob-storage-and-azure-sql-database-for-the-tutorial"></a>자습서에서 사용할 Azure BLOB Storage 및 Azure SQL Database 준비
[BLOB Storage에서 SQL Database에 데이터 복사](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)에서 자습서를 완료한 경우 이 단계를 건너뜁니다.

다음 단계를 수행하여 이 자습서에서 사용할 BLOB Storage 및 SQL Database를 준비합니다.

1. **StorageLinkedService**가 가리키는 BLOB Storage에서 **adftutorial**이라는 BLOB 컨테이너를 만듭니다.
2. **adftutorial** 컨테이너에 대한 BLOB으로 **emp.txt**라는 텍스트 파일을 만들어 업로드합니다.
3. **AzureSqlLinkedService**가 가리키는 SQL Database에 **emp**라는 테이블을 만듭니다.

4. 메모장을 시작합니다. 다음 텍스트를 복사하여 **emp.txt**로 하드 드라이브의 **C:\ADFGetStartedPSH** 폴더에 저장합니다.

    ```
    John, Doe
    Jane, Doe
    ```
5. [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)와 같은 도구를 사용하여 **adftutorial** 컨테이너를 만들고 **emp.txt** 파일을 이 컨테이너에 업로드합니다.

    ![Azure Storage 탐색기](media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png)
6. 다음 SQL 스크립트를 사용하여 SQL Database에 **emp** 테이블을 만듭니다.  

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

    SQL Server 2014가 컴퓨터에 설치된 경우: [2단계: SQL Server Management Studio를 사용하여 Azure SQL Database 관리의 SQL Database에 연결](../sql-database/sql-database-manage-azure-ssms.md)의 지침에 따라 SQL Database 서버에 연결하고 SQL 스크립트를 실행합니다.

    클라이언트가 SQL Database 서버에 액세스할 수 없는 경우 컴퓨터(IP 주소)의 액세스를 허용하도록 SQL Database 서버의 방화벽을 설정해야 합니다. 단계는 [이 문서](../sql-database/sql-database-configure-firewall-settings.md)를 참조하세요.

### <a name="create-an-input-dataset"></a>입력 데이터 집합 만들기
테이블은 사각형 데이터 집합이고 스키마가 있습니다. 이 단계에서는 **EmpBlobTable**이라는 테이블을 만듭니다. 이 테이블은 **StorageLinkedService**에 연결된 서비스가 나타내는 Azure Storage의 BLOB 컨테이너를 가리킵니다. 이 Blob 컨테이너(adftutorial)는 **emp.txt** 파일에 입력 데이터를 포함합니다.

1. **C:\ADFGetStartedPSH** 폴더에 다음과 같은 내용으로 **EmpBlobTable.json**이라는 JSON 파일을 만듭니다.

    ```json
    {
        "name": "EmpTableFromBlob",
        "properties": {
            "structure": [
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "fileName": "emp.txt",
                "folderPath": "adftutorial/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```

   다음 사항에 유의하세요.

   * 데이터 집합 **형식**을 **AzureBlob**로 설정합니다.
   * **linkedServiceName**을 **StorageLinkedService**로 설정합니다.
   * **folderPath**를 **adftutorial** 컨테이너로 설정합니다.
   * **fileName**을 **emp.txt**로 설정합니다. BLOB 이름을 지정하지 않으면 컨테이너에 있는 모든 BLOB의 데이터가 입력 데이터로 간주됩니다.  
   * 서식 **형식**을 **TextFormat**으로 설정합니다.
   * 텍스트 파일에는 **FirstName**과 **LastName**의 두 필드가 쉼표(columnDelimiter)로 구분되어 있습니다.    
   * **가용성**은 **매시간**으로 설정됩니다(빈도는 매시간으로, 간격은 1로 설정). 따라서 Data Factory는 Blob 컨테이너(adftutorial)의 루트 폴더에서 한 시간마다 입력 데이터를 찾습니다.

   **입력 테이블**의 **fileName**을 지정하지 않을 경우 입력 폴더(folderPath)의 모든 파일/BLOB이 입력으로 간주됩니다. JSON에 fileName을 지정할 경우 지정한 파일/BLOB만 입력으로 간주됩니다.

   **출력 테이블**의 **fileName**을 지정하지 않는 경우, **folderPath**에 생성되는 파일의 이름은 다음과 같은 형식으로 지정됩니다. Data.<Guid\>.txt(예: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).

   **SliceStart** 시간을 기반으로 **folderPath** 및 **fileName**을 동적으로 설정하려면 **partitionedBy** 속성을 사용합니다. 다음 예제에서 folderPath는 SliceStart(처리 중인 조각의 시작 시간)의 연도, 월 및 일을 사용하고 fileName은 SliceStart의 시간을 사용합니다. 예를 들어 조각이 2016-10-20T08:00:00에 생성되는 경우 folderName은 wikidatagateway/wikisampledataout/2016/10/20으로 설정되고 fileName은 08.csv로 설정됩니다.

    ```json
     "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
     "fileName": "{Hour}.csv",
     "partitionedBy":
     [
         { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
         { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
         { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
         { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
     ],
    ```

   JSON 속성에 대한 자세한 내용은 [JSON 스크립트 참조](data-factory-data-movement-activities.md)를 참조하세요.
2. 다음 명령을 실행하여 데이터 팩터리 데이터 집합을 만듭니다.

    ```PowerShell  
    New-AzureRmDataFactoryDataset $df -File .\EmpBlobTable.json
    ```

### <a name="create-an-output-dataset"></a>출력 데이터 집합 만들기
이 단계에서는 **EmpSQLTable**라는 출력 데이터 집합을 만듭니다. 이 데이터 집합은 **AzureSqlLinkedService**가 나타내는 Azure SQL Database에서 SQL 테이블(emp)을 가리킵니다. 파이프라인은 입력 Blob에서 **emp** 테이블로 데이터를 복사합니다.

1. **C:\ADFGetStartedPSH** 폴더에 다음과 같은 내용으로 **EmpSQLTable.json**이라는 JSON 파일을 만듭니다.

    ```json
    {
        "name": "EmpSQLTable",
        "properties": {
            "structure": [
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```

   다음 사항에 유의하세요.

   * 데이터 집합 **형식**을 **AzureSqlTable**로 설정합니다.
   * **linkedServiceName**을 **AzureSqlLinkedService**로 설정합니다.
   * **tablename**을 **emp**로 설정합니다.
   * 데이터베이스의 emp 테이블에 **ID**, **FirstName** 및 **LastName**이라는 세 개의 열이 있습니다. ID는 ID 열이므로 여기서 **FirstName** 및 **LastName**만 지정해야 합니다.
   * **가용성**은 **매시간**으로 설정됩니다(빈도는 매시간으로, 간격은 1로 설정). 데이터 팩터리 서비스는 Azure SQL 데이터베이스의 **emp** 테이블에 출력 데이터 조각을 1시간마다 생성합니다.
2. 다음 명령을 실행하여 Data Factory 데이터 집합을 만듭니다.

    ```PowerShell   
    New-AzureRmDataFactoryDataset $df -File .\EmpSQLTable.json
    ```

## <a name="create-a-pipeline"></a>파이프라인을 만듭니다.
이 단계에서는 **복사 작업**을 사용하여 파이프라인을 만듭니다. 이 파이프라인은 **EmpTableFromBlob**을 입력으로 사용하고 **EmpSQLTable**을 출력으로 사용합니다.

1. **C:\ADFGetStartedPSH** 폴더에 다음과 같은 내용으로 **ADFTutorialPipeline.json**이라는 JSON 파일을 만듭니다.

    ```json
    {
        "name": "ADFTutorialPipeline",
        "properties": {
            "description": "Copy data from a blob to Azure SQL table",
            "activities": [
                {
                    "name": "CopyFromBlobToSQL",
                    "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
                    "type": "Copy",
                    "inputs": [{ "name": "EmpTableFromBlob" }],
                    "outputs": [{ "name": "EmpSQLTable" }],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "SqlSink"
                        }
                    },
                    "Policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "style": "StartOfInterval",
                        "retry": 0,
                        "timeout": "01:00:00"
                    }
                }
            ],
            "start": "2016-08-09T00:00:00Z",
            "end": "2016-08-10T00:00:00Z",
            "isPaused": false
        }
     }
    ```
   다음 사항에 유의하세요.

   * 작업 섹션에는 **형식**이 **복사**로 설정된 작업만 있습니다.
   * 작업에 대한 입력을 **EmpTableFromBlob**으로 설정하고 작업에 대한 출력을 **EmpSQLTable**로 설정합니다.
   * **변환** 섹션에서 **BlobSource**를 원본 유형으로 지정하고 **SqlSink**를 싱크 유형으로 지정합니다.

   **start** 속성 값을 현재 날짜로 바꾸고 **end** 속성 값을 다음 날짜로 바꿉니다. start 및 end 날짜/시간은 둘 다 [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601)(영문)이어야 합니다. 예를 들어 2016-10-14T16:32:41Z입니다. 이 자습서에서는 **end** 시간이 사용되었지만 선택 사항입니다.

   **종료** 속성 값을 지정하지 않는 경우 "**시작 + 48시간**"으로 계산됩니다. 파이프라인을 무기한 실행하려면 **종료** 속성 값으로 **9/9/9999**를 지정합니다.

   이 예에서는 각 데이터 조각이 1시간마다 생성되므로 24개 데이터 조각이 있게 됩니다.

   JSON 속성에 대한 자세한 내용은 [JSON 스크립트 참조](data-factory-data-movement-activities.md)를 참조하세요.
2. 다음 명령을 실행하여 Data Factory 테이블을 만듭니다.

    ```PowerShell   
    New-AzureRmDataFactoryPipeline $df -File .\ADFTutorialPipeline.json
    ```

축하합니다. Azure Data Factory, 연결된 서비스, 테이블 및 파이프라인을 성공적으로 만들었습니다. 파이프라인도 예약했습니다.

## <a name="monitor-the-pipeline"></a>파이프라인 모니터링
이 단계에서는 Azure PowerShell을 사용하여 Azure Data Factory에서 어떤 일이 일어나는지 모니터링합니다.

1. **Get-AzureRmDataFactory**를 실행하고 출력을 $df 변수에 할당합니다.

    ```PowerShell  
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
    ```

2. **Get-AzureRmDataFactorySlice**를 실행하여 파이프라인의 출력 테이블인 **EmpSQLTable**의 모든 조각에 대한 세부 정보를 가져옵니다.  

    ```PowerShell   
    Get-AzureRmDataFactorySlice $df -DatasetName EmpSQLTable -StartDateTime 2016-08-09T00:00:00
    ```

   **StartDateTime** 매개 변수의 연도, 월 및 날짜 부분을 현재 연도, 월 및 날짜로 바꿉니다. 이 설정은 파이프라인 JSON의 **시작** 값과 일치해야 합니다.

   현재 날짜의 오전 12시부터 다음 날짜의 오전 12시까지 각 시간마다 하나씩, 24개의 조각이 표시됩니다.

   **샘플 출력:**

    ``` 
     ResourceGroupName : ADFTutorialResourceGroup
     DataFactoryName   : ADFTutorialDataFactoryPSH
     TableName         : EmpSQLTable
     Start             : 8/9/2016 12:00:00 AM
     End               : 8/9/2016 1:00:00 AM
     RetryCount        : 0
     Status            : Waiting
     LatencyStatus     :
     LongRetryCount    : 0
    ```
3. **Get-AzureRmDataFactoryRun**을 실행하여 **특정** 조각에 대한 작업 실행의 세부 정보를 가져옵니다. 출력에서 조각의 **시작** 시간과 일치하도록 **StartDateTime** 매개 변수 값을 변경합니다. **StartDateTime** 값은 [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601)(영문)이어야 합니다.

    ```PowerShell  
    Get-AzureRmDataFactoryRun $df -DatasetName EmpSQLTable -StartDateTime 2016-08-09T00:00:00
    ```

   다음 샘플과 비슷한 결과가 나타나야 합니다.

    ```  
    Id                  : 3404c187-c889-4f88-933b-2a2f5cd84e90_635614488000000000_635614524000000000_EmpSQLTable
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : ADFTutorialDataFactoryPSH
    TableName           : EmpSQLTable
    ProcessingStartTime : 8/9/2016 11:03:28 PM
    ProcessingEndTime   : 8/9/2016 11:04:36 PM
    PercentComplete     : 100
    DataSliceStart      : 8/9/2016 10:00:00 PM
    DataSliceEnd        : 8/9/2016 11:00:00 PM
    Status              : Succeeded
    Timestamp           : 8/9/2016 11:03:28 PM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : CopyFromBlobToSQL
    PipelineName        : ADFTutorialPipeline
    Type                : Copy
    ```

Data Factory cmdlet에 대한 포괄적인 설명서는 [Data Factory Cmdlet 참조][cmdlet-reference]를 참조하세요.

## <a name="summary"></a>요약
이 자습서에서는 Azure Blob에서 Azure SQL 데이터베이스로 데이터를 복사하는 Azure Data Factory를 만들었습니다. PowerShell를 사용하여 데이터 팩터리, 연결된 서비스, 데이터 집합 및 파이프라인을 만들었습니다. 이 자습서에서 수행한 단계를 요약하면 다음과 같습니다.  

1. Azure **Data Factory**를 만들었습니다.
2. **연결된 서비스**를 만들었습니다.

   a. 입력 데이터를 보유하는 Azure Storage 계정을 연결하는 **Azure Storage**에 연결된 서비스입니다.     
   b. 출력 데이터를 보유하는 SQL Database를 연결하는 **Azure SQL** 연결된 서비스입니다.
3. 파이프라인의 입력 데이터와 출력 데이터를 설명하는 **데이터 집합** 을 만들었습니다.
4. 원본으로 **BlobSource**를 사용하고 싱크로 **SqlSink**를 사용하는 **복사 작업**으로 **파이프라인**을 만들었습니다.

## <a name="see-also"></a>참고 항목
| 항목 | 설명 |
|:--- |:--- |
| [데이터 팩터리 cmdlet 참조](/powershell/module/azurerm.datafactories) | 이 섹션에서는 모든 데이터 팩터리 cmdlet에 대한 정보를 제공합니다. |
| [파이프라인](data-factory-create-pipelines.md) |이 문서는 Azure Data Factory에서 파이프라인 및 작업을 이해하는 데 도움이 됩니다. |
| [datasets](data-factory-create-datasets.md) |이 문서는 Azure Data Factory의 데이터 집합을 이해하는 데 도움이 됩니다. |
| [예약 및 실행](data-factory-scheduling-and-execution.md) |이 문서에서는 Azure Data Factory 응용 프로그램 모델의 예약 및 실행에 대한 내용을 설명합니다. |

[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md

