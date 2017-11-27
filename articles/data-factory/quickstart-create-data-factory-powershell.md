---
title: "Azure Data Factory를 사용하여 Blob Storage 데이터 복사 | Microsoft Docs"
description: "Azure Data Factory를 만들어서 Azure Blob Storage의 한 폴더에서 동일한 Blob Storage의 다른 위치로 데이터를 복사합니다."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: hero-article
ms.date: 11/16/2017
ms.author: jingwang
ms.openlocfilehash: 254dcb6642afc19f434df837c9073d2dd7314313
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2017
---
# <a name="create-an-azure-data-factory-using-powershell"></a>PowerShell을 사용하여 Azure Data Factory 만들기 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1-GA](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [버전 2- 미리 보기](quickstart-create-data-factory-powershell.md)

이 빠른 시작에서는 PowerShell을 사용하여 Azure Data Factory를 만드는 방법을 설명합니다. 이 데이터 팩터리에서 만든 파이프라인은 Azure Blob Storage의 한 폴더에서 다른 폴더로 데이터를 **복사합니다**. Azure Data Factory를 사용하여 데이터를 **변환**하는 방법에 대한 자습서는 [자습서: Apache Spark를 사용하여 데이터 변환](transform-data-using-spark.md)을 참조하세요. 

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 시작](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)을 참조하세요.
>
> 이 문서는 Data Factory 서비스의 자세한 소개를 제공하지 않습니다. Azure Data Factory 서비스 소개는 [Azure Data Factory 소개](introduction.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

### <a name="azure-subscription"></a>Azure 구독
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

### <a name="azure-roles"></a>Azure 역할
Data Factory 인스턴스를 만들려면 Azure에 로그인하는 데 사용할 사용자 계정은 **참여자** 또는 **소유자** 역할의 구성원이거나, 또는 Azure 구독의 **관리자**이어야 합니다. Azure Portal에서 오른쪽 위 모서리에 있는 **사용자 이름**을 클릭한 다음 **권한**을 선택하면 구독에 따른 사용자 권한을 볼 수입니다. 여러 구독에 액세스할 수 있는 경우 적절한 구독을 선택합니다. 사용자를 역할에 추가하는 방법에 대한 샘플 지침은 [역할 추가](../billing/billing-add-change-azure-subscription-administrator.md) 문서를 참조하세요.

### <a name="azure-storage-account"></a>Azure Storage 계정
이 빠른 시작에서는 범용 Azure Storage 계정(특히 Blob Storage)을 **원본** 및 **대상** 데이터 저장소로 사용합니다. 범용 Azure Storage 계정이 없는 경우 [저장소 계정 만들기](../storage/common/storage-create-storage-account.md#create-a-storage-account)를 참조하여 새로 만듭니다. 

#### <a name="get-storage-account-name-and-account-key"></a>저장소 계정 이름 및 계정 키 가져오기
이 빠른 시작에서 Azure Storage 계정 이름 및 키를 사용합니다. 다음 프로시저에서는 저장소 계정 이름 및 키를 가져오는 단계를 제공합니다. 

1. 웹 브라우저를 시작하고 [Azure Portal](https://portal.azure.com)로 이동합니다. Azure 사용자 이름과 암호를 사용하여 로그인합니다. 
2. 왼쪽 메뉴에서 **더 많은 서비스>**를 클릭하고 **저장소** 키워드를 사용하여 필터링하고 **저장소 계정**을 선택합니다.

    ![저장소 계정 검색](media/quickstart-create-data-factory-powershell/search-storage-account.png)
3. 저장소 계정 목록에서 저장소 계정(필요한 경우)을 필터링한 다음 **저장소 계정**을 선택합니다. 
4. **저장소 계정** 페이지의 메뉴에서 **액세스 키**를 선택합니다.

    ![저장소 계정 이름 및 키 가져오기](media/quickstart-create-data-factory-powershell/storage-account-name-key.png)
5. **저장소 계정 이름** 및 **key1** 필드의 값을 클립보드에 복사합니다. 메모장이나 다른 편집기에 붙여넣고 저장합니다.  

#### <a name="create-input-folder-and-files"></a>입력 폴더 및 파일 만들기
이 섹션에서는 Azure Blob Storage에 **adftutorial**이라는 Blob 컨테이너를 만듭니다. 그런 다음 컨테이너에 **입력**이라는 폴더를 만든 다음 입력 폴더에 샘플 파일을 업로드합니다. 

1. **저장소 계정** 페이지에서 **개요**로 전환한 다음, **Blobs**을 클릭합니다. 

    ![Blob 옵션 선택](media/quickstart-create-data-factory-powershell/select-blobs.png)
2. **Blob service** 페이지의 도구 모음에서 **+ 컨테이너**를 클릭합니다. 

    ![컨테이너 단추 추가](media/quickstart-create-data-factory-powershell/add-container-button.png)    
3. **새 컨테이너** 대화 상자에서 **adftutorial**을 이름으로 입력하고 **확인**을 클릭합니다. 

    ![컨테이너 이름 입력](media/quickstart-create-data-factory-powershell/new-container-dialog.png)
4. 컨테이너 목록에서 **adftutorial**을 클릭합니다. 

    ![컨테이너를 선택합니다.](media/quickstart-create-data-factory-powershell/seelct-adftutorial-container.png)
1. **컨테이너** 페이지의 도구 모음에서 **업로드**를 클릭합니다.  

    ![업로드 단추](media/quickstart-create-data-factory-powershell/upload-toolbar-button.png)
6. **BLOB 업로드** 페이지에서 **고급**을 클릭합니다.

    ![고급 링크 클릭합니다.](media/quickstart-create-data-factory-powershell/upload-blob-advanced.png)
7. **메모장**을 시작하고 다음과 같은 내용으로 **emp.txt**이라는 파일을 만듭니다: 그것을 **c:\ADFv2QuickStartPSH** 폴더에 저장합니다: **ADFv2QuickStartPSH** 폴더가 아직 없는 경우 새로 만듭니다.
    
    ```
    John, Doe
    Jane, Doe
    ```    
8. Azure Portal의 **BLOB 업로드** 페이지에서 **파일** 필드에 대해 **emp.txt** 파일을 탐색 및 선택합니다. 
9. **입력**을 **폴더로 업로드** 필드 값으로 입력합니다. 

    ![BLOB 업로드 설정](media/quickstart-create-data-factory-powershell/upload-blob-settings.png)    
10. 폴더는 **입력**이고 파일은 **emp.txt**인지 확인한 후 **업로드**를 클릭합니다.
11. 목록에서 **emp.txt** 파일 및 업로드 상태를 참조하세요. 
12. 모서리에 있는 **X**를 클릭하여 **BLOB 업로드** 페이지를 닫습니다. 

    ![BLOB 업로드 닫기](media/quickstart-create-data-factory-powershell/close-upload-blob.png)
1. **컨테이너** 페이지를 열린 상태로 유지합니다. 이 빠른 시작의 끝부분에서 출력을 확인하는 데 사용합니다. 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>Azure Powershell 설치
컴퓨터에 최신 Azure PowerShell이 없는 경우 설치합니다. 

1. 웹 브라우저에서 [Azure SDK 다운로드 및 SDKS](https://azure.microsoft.com/downloads/) 페이지로 이동합니다. 
2. **명령줄 도구** -> **PowerShell** 섹션에서 **Windows 설치**를 클릭합니다. 
3. Azure PowerShell을 설치하려면 **MSI** 파일을 실행합니다. 

자세한 지침은 [Microsoft Azure PowerShell 설치 및 구성 방법](/powershell/azure/install-azurerm-ps)을 참조하세요. 

#### <a name="log-in-to-azure-powershell"></a>Azure PowerShell에 로그인합니다.

1. 컴퓨터에서 **PowerShell**을 시작합니다. 이 빠른 시작을 완료할 때까지 Azure PowerShell을 열어 둡니다. 닫은 후 다시 여는 경우 이러한 명령을 다시 실행해야 합니다.

    ![PowerShell 시작](media/quickstart-create-data-factory-powershell/search-powershell.png)
1. 다음 명령을 실행하고 Azure Portal에 로그인하는 데 사용할 동일한 Azure사용자 이름 및 암호를 입력합니다.
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. 여러 Azure 구독이 있는 경우 다음 명령을 실행하여 이 계정의 모든 구독을 확인합니다.

    ```powershell
    Get-AzureRmSubscription
    ```
3. 다음 명령을 실행하여 사용하려는 구독을 선택합니다. **SubscriptionId**를 Azure 구독의 ID로 바꿉니다.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.
1. 나중에 PowerShell 명령에서 사용할 리소스 그룹 이름에 대한 변수를 정의합니다. PowerShell에 다음 명령 텍스트를 복사하고, 큰따옴표에 있는 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)의 이름을 지정하고, 명령을 실행합니다. 예: `"adfrg"`
   
     ```powershell
    $resourceGroupName = "<Specify a name for the Azure resource group>";
    ```
2. 데이터 팩터리 이름에 대한 변수를 정의합니다. 

    ```powershell
    $dataFactoryName = "<Specify a name for the data factory. It must be globally unique.>";
    ```
1. 데이터 팩터리의 위치에 대한 변수를 정의합니다. 

    ```powershell
    $location = "East US"
    ```
4. 새 리소스 그룹을 만들려면 다음 명령을 실행합니다. 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    리소스 그룹이 이미 있는 경우 덮어쓰지 않는 것이 좋습니다. `$resourceGroupName` 변수에 다른 값을 할당하고 명령을 다시 시도하세요. 
5. 데이터 팩터리를 만들려면 다음 **Set-AzureRmDataFactoryV2** cmdlet을 실행합니다. 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

다음 사항에 유의하세요.

* Azure Data Factory 이름은 전역적으로 고유해야 합니다. 다음 오류가 표시되면 이름을 변경하고 다시 시도하세요.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory 인스턴스를 만들려면 Azure에 로그인하는 데 사용할 사용자 계정은 **참여자** 또는 **소유자** 역할의 구성원이거나, 또는 Azure 구독의 **관리자**이어야 합니다.
* 현재 미국 동부, 미국 동부 2 및 유럽 서부 지역에서만 Data Factory 버전 2를 사용하여 데이터 팩터리를 만들 수 있습니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.

## <a name="create-a-linked-service"></a>연결된 서비스 만들기

데이터 팩터리에서 연결된 서비스를 만들어 데이터 저장소 및 계산 서비스를 데이터 팩터리에 연결합니다. 이 빠른 시작에서 원본 및 싱크 저장소로 사용되는 Azure Storage 연결된 서비스를 만듭니다. 연결된 서비스에는 Data Factory 서비스가 런타임에 연결하는 데 사용하는 연결 정보가 있습니다.

1. 다음 콘텐츠가 포함된 **AzureStorageLinkedService.json**이라는 JSON 파일을 **C:\ADFv2QuickStartPSH** 폴더에 만듭니다. (없는 경우 ADFv2QuickStartPSH 폴더를 만듭니다.) 

    > [!IMPORTANT]
    > 파일을 저장하기 전에 &lt;accountName&gt; 및 &lt;accountKey&gt;를 Azure Storage 계정의 이름 및 키로 바꿉니다.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net",
                    "type": "SecureString"
                }
            }
        }
    }
    ```

2. **Azure PowerShell**에서 **ADFv2QuickStartPSH** 폴더로 전환합니다.

3. **Set-AzureRmDataFactoryV2LinkedService** cmdlet을 실행하여 **AzureStorageLinkedService** 연결된 서비스를 만듭니다. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-a-dataset"></a>데이터 집합 만들기
이 단계에서는 원본에서 싱크로 복사할 데이터를 나타내는 데이터 집합을 정의합니다. 데이터 집합은 **AzureBlob** 형식입니다. 이 데이터 집합은 이전 단계에서 만든 **Azure Storage 연결된 서비스**를 참조합니다. **folderPath** 속성을 생성하려면 매개 변수를 사용합니다. 입력 데이터 집합의 경우 파이프라인에서의 복사 작업은 입력 경로를 이 매개 변수에 대한 값으로 전달합니다. 마찬가지로, 출력 데이터 집합의 경우 복사 작업은 출력 경로를 이 매개 변수에 대한 값으로 전달합니다. 

1. 다음 콘텐츠가 포함된 **BlobDataset.json**이라는 JSON 파일을 **C:\ADFv2QuickStartPSH** 폴더에 만듭니다.

    ```json
    {
        "name": "BlobDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": {
                    "value": "@{dataset().path}",
                    "type": "Expression"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "path": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. **BlobDataset** 데이터 집합을 만들려면 **Set-AzureRmDataFactoryV2Dataset** cmdlet을 실행합니다.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "BlobDataset" -DefinitionFile ".\BlobDataset.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```
    DatasetName       : BlobDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>파이프라인을 만듭니다.
  
이 빠른 시작에서 입력 Blob 경로 및 출력 Blob 경로의 두 매개 변수를 취하는 하나의 활동을 사용하여 파이프라인을 만듭니다. 이러한 매개 변수의 값은 파이프라인이 트리거/실행될 때 설정됩니다. 복사 작업은 입력 및 출력 시 이전 단계에서 만든 동일한 Blob 데이터 집합을 사용합니다. 데이터 집합을 입력된 데이터 집합으로 사용하는 경우 입력된 경로가 지정됩니다. 또한 데이터 집합을 출력된 데이터 집합으로 사용하는 경우 출력된 경로가 지정됩니다. 

1. 다음 콘텐츠가 포함된 **Adfv2QuickStartPipeline.json**이라는 JSON 파일을 **C:\ADFv2QuickStartPSH** 폴더에 만듭니다.

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
                    "inputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.inputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.outputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    }
                }
            ],
            "parameters": {
                "inputPath": {
                    "type": "String"
                },
                "outputPath": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. **Adfv2QuickStartPipeline** 파이프라인을 만들려면 **Set-AzureRmDataFactoryV2Pipeline** cmdlet을 실행합니다.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile ".\Adfv2QuickStartPipeline.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

## <a name="create-a-pipeline-run"></a>파이프라인 실행 만들기

이 단계에서 **inputPath** 및 **outputPath** 파이프라인 매개 변수에 대한 값을 원본 및 싱크 Blob 경로의 실제 값으로 설정합니다. 그런 다음 이러한 인수를 사용하여 실행할 파이프라인을 만듭니다. 

1. 다음 콘텐츠가 포함된 **PipelineParameters.json**이라는 JSON 파일을 **C:\ADFv2QuickStartPSH** 폴더에 만듭니다.

    ```json
    {
        "inputPath": "adftutorial/input",
        "outputPath": "adftutorial/output"
    }
    ```
2. **Invoke-AzureRmDataFactoryV2Pipeline** cmdlet을 실행하여 매개 변수 값에서 실행 및 전달할 파이프라인을 만듭니다. Cmdlet은 향후 모니터링을 위해 파이프라인 실행 ID를 캡처합니다.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-the-pipeline-run"></a>파이프라인 실행을 모니터링합니다.

1. 다음 PowerShell 스크립트를 실행하여 데이터 복사가 완료될 때까지 지속적으로 파이프라인 실행 상태를 검사합니다. PowerShell 창에서 다음 스크립트를 복사/붙여넣기하고 ENTER 키를 누릅니다. 

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 10
    }
    ```

    파이프라인 실행의 샘플 출력은 다음과 같습니다.

    ```
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    RunId             : 0000000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 9/28/2017 8:28:38 PM
    Parameters        : {[inputPath, adftutorial/input], [outputPath, adftutorial/output]}
    RunStart          : 9/28/2017 8:28:14 PM
    RunEnd            : 9/28/2017 8:28:38 PM
    DurationInMs      : 24151
    Status            : Succeeded
    Message           :
    ```

    오류가 표시되는 경우
    ```
    Activity CopyFromBlobToBlob failed: Failed to detect region of linked service 'AzureStorage' : 'AzureStorageLinkedService' with error '[Region Resolver] Azure Storage failed to get address for DNS. Warning: System.Net.Sockets.SocketException (0x80004005): No such host is known
    ```
    다음 단계를 수행합니다. 
    1. AzureStorageLinkedService.json에서 Azure Storage 계정의 이름과 키가 정확한지 확인합니다. 
    2. 연결 문자열의 형식이 정확한지 확인합니다. 예를 들어 AccountName 및 AccountKey 속성은 세미콜론 (`;`) 문자로 구분됩니다. 
    3. 계정 이름 및 계정 키를 둘러싼 각괄호가 있다면 제거합니다. 
    4. 다음은 연결 문자열의 예제입니다. 

        ```json
        "connectionString": {
            "value": "DefaultEndpointsProtocol=https;AccountName=mystorageaccountname;AccountKey=mystorageacountkey;EndpointSuffix=core.windows.net",
            "type": "SecureString"
        }
        ```
    5. [연결된 서비스 만들기](#create-a-linked-service) 섹션에서 다음 단계를 통해 연결된 서비스를 다시 만듭니다. 
    6. [파이프라인 실행 만들기](#create-a-pipeline-run) 섹션에서 다음 단계를 통해 파이프라인을 다시 실행합니다. 
    7. 새 파이프라인 실행을 모니터링하기 위해 현재 모니터링 명령을 다시 실행합니다. 
1. 복사 활동 실행 세부 정보(예: 읽고/쓴 데이터의 크기)를 검색하는 다음 스크립트를 실행합니다.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```
3. 활동 실행 결과의 다음 샘플 출력과 유사한 출력이 표시되는지 확인합니다.

    ```json
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, copyDuration, throughput...}
    LinkedServiceName :
    ActivityRunStart  : 9/28/2017 8:28:18 PM
    ActivityRunEnd    : 9/28/2017 8:28:36 PM
    DurationInMs      : 18095
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 38
    "dataWritten": 38
    "copyDuration": 7
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)"
    "usedCloudDataMovementUnits": 2
    "billedDuration": 14
    ```

## <a name="verify-the-output"></a>출력 확인
파이프라인은 자동으로 adftutorial Blob 컨테이너에서 출력 폴더를 만듭니다. 그런 다음 입력 폴더에서 출력 폴더로 emp.txt 파일을 복사합니다. 

1. Azure Portal의 **adftutorial** 컨테이너 페이지에서 출력 폴더를 보려면 **새로 고침**을 클릭합니다. 
    
    ![새로 고침](media/quickstart-create-data-factory-powershell/output-refresh.png)
2. 폴더 목록에서 **출력**을 클릭합니다. 
2. **emp.txt**가 출력 폴더에 복사되었는지 확인합니다. 

    ![새로 고침](media/quickstart-create-data-factory-powershell/output-file.png)

## <a name="clean-up-resources"></a>리소스 정리
빠른 시작에서 만든 리소스는 두 가지 방법으로 정리할 수 있습니다. 리소스 그룹의 모든 리소스를 포함하고 있는 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 삭제할 수 있습니다. 다른 리소스를 그대로 유지하려면 이 자습서에서 만든 데이터 팩터리만 삭제합니다.

리소스 그룹을 삭제하면 그 안에 포함된 데이터 팩터리를 포함한 모든 리소스가 삭제됩니다. 다음 명령을 실행하여 전체 리소스 그룹을 삭제합니다. 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

전체 리소스 그룹이 아니라 데이터 팩터리만 삭제하려면 다음 명령을 실행 합니다. 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>다음 단계
이 샘플의 파이프라인은 Azure Blob 저장소의 한 위치에서 다른 위치로 데이터를 복사합니다. [자습서](tutorial-copy-data-dot-net.md)를 통해 더 많은 시나리오에서의 데이터 팩터리 사용에 관해 알아보세요. 
