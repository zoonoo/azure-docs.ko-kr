---
title: ".NET을 사용하여 Azure Data Factory 만들기 | Microsoft Docs"
description: "Azure Data Factory를 만들어서 Azure Blob Storage의 한 위치에서 동일한 Blob Storage의 다른 위치로 데이터를 복사합니다."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/06/2017
ms.author: jingwang
ms.openlocfilehash: e27c1a8e130d20eb0ba0e5c001fc9a435e07c1cd
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2017
---
# <a name="create-a-data-factory-and-pipeline-using-net-sdk"></a>.NET SDK를 사용하여 데이터 팩터리 및 파이프라인 만들기
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [버전 2 - 미리 보기](quickstart-create-data-factory-dot-net.md)

Azure Data Factory는 데이터 이동 및 데이터 변환을 오케스트레이션하고 자동화하기 위해 클라우드에서 데이터 기반 워크플로를 만들 수 있는 클라우드 기반 데이터 통합 서비스입니다. Azure Data Factory를 사용하여 서로 다른 데이터 저장소에서 데이터를 수집할 수 있는 데이터 기반 워크플로(파이프라인이라고 함)를 만들고 일정을 조정하며, Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics 및 Azure Machine Learning과 같은 계산 서비스를 사용하여 데이터를 처리/변환하고, 사용할 BI(비즈니스 인텔리전스) 응용 프로그램의 Azure SQL Data Warehouse와 같은 데이터 저장소에 출력 데이터를 게시할 수 있습니다. 

이 빠른 시작에서는 .NET SDK를 사용하여 Azure Data Factory를 만드는 방법을 설명합니다. 이 데이터 팩터리의 파이프라인은 Azure Blob Storage의 한 폴더에서 다른 폴더로 데이터를 복사합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 시작](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건
* **Azure Storage 계정**. Blob 저장소를 **원본** 및 **싱크** 데이터 저장소 모두로 사용합니다. Azure Storage 계정이 없는 경우 [저장소 계정 만들기](../storage/common/storage-create-storage-account.md#create-a-storage-account)를 참조하여 하나 만듭니다. 
* Blob Storage에 **Blob 컨테이너**를 만들고 컨테이너에 입력 **폴더**를 만들고 폴더에 일부 파일을 업로드합니다. [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)와 같은 도구를 사용하여 Azure Blob Storage에 연결, Blob 컨테이너 만들기, 입력 파일 업로드 및 출력 파일 확인을 수행할 수 있습니다.
* **Visual Studio** 2013, 2015 또는 2017 - 이 문서의 연습에서는 Visual Studio 2017을 사용합니다.
* **[Azure .NET SDK](http://azure.microsoft.com/downloads/)를 다운로드 및 설치합니다**.
* [이러한 지침](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)에 따라 **Azure Active Directory에 응용 프로그램을 만듭니다**. 나중에 사용하는 다음 값을 기록해 둡니다. **응용 프로그램 ID**, **인증 키** 및 **테넌트 ID**. 동일한 문서의 지침에 따라 응용 프로그램을 “**참가자**” 역할에 할당합니다. 
*  

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

Visual Studio 2013/2015/2017을 사용하여 C# .NET 콘솔 응용 프로그램을 만듭니다.

1. **Visual Studio**를 시작합니다.
2. **파일**을 클릭하고 **새로 만들기**를 가리킨 다음 **프로젝트**를 클릭합니다.
3. 오른쪽의 프로젝트 형식 목록에서 **Visual C#** -> **콘솔 앱(.NET Framework)**을 차례로 선택합니다. .NET 버전 4.5.2 이상이 필요합니다.
4. 이름에 **ADFv2QuickStart**를 입력합니다.
5. **확인** 을 클릭하여 프로젝트를 만듭니다.

## <a name="install-nuget-packages"></a>NuGet 패키지 설치

1. **도구** -> **NuGet 패키지 관리자** -> **패키지 관리자 콘솔**을 클릭합니다.
2. **패키지 관리자 콘솔**에서 다음 명령을 실행하여 패키지를 설치합니다.

    ```
    Install-Package Microsoft.Azure.Management.DataFactory -Prerelease
    Install-Package Microsoft.Azure.Management.ResourceManager -Prerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

    ```

## <a name="create-a-data-factory-client"></a>데이터 팩터리 클라이언트 만들기

1. **Program.cs**를 열고 다음 문을 포함시켜서 네임스페이스에 대한 참조를 추가합니다.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. 변수를 설정하는 **Main** 메서드에 다음 코드를 추가합니다. 자리 표시자를 고유한 값으로 바꿉니다. 현재 미국 동부, 미국 동부 2 및 유럽 서부 지역에서만 Data Factory V2를 사용하여 데이터 팩터리를 만들 수 있습니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID where the data factory resides>";
    string resourceGroup = "<your resource group where the data factory resides>";
    string region = "East US 2";
    string dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>";
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    // specify the container and input folder from which all files need to be copied to the output folder. 
    string inputBlobPath = "<the path to existing blob(s) to copy data from, e.g. containername/foldername>";
    //specify the contains and output folder where the files are copied
    string outputBlobPath = "<the blob path to copy data to, e.g. containername/foldername>";

    string storageLinkedServiceName = "AzureStorageLinkedService";  // name of the Azure Storage linked service
    string blobDatasetName = "BlobDataset";             // name of the blob dataset
    string pipelineName = "Adfv2QuickStartPipeline";    // name of the pipeline
    ```

3. **Main** 메서드에 **DataFactoryManagementClient** 클래스의 인스턴스를 만드는 다음 코드를 추가합니다. 이 개체를 사용하여 데이터 팩터리, 연결된 서비스, 데이터 집합 및 파이프라인을 만듭니다. 또한 이 개체를 사용하여 파이프라인 실행 세부 정보를 모니터링합니다.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

**Main** 메서드에 **데이터 팩터리**를 만드는 다음 코드를 추가합니다. 

```csharp
// Create a data factory
Console.WriteLine("Creating data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-a-linked-service"></a>연결된 서비스 만들기

**Main** 메서드에 **Azure Storage 연결된 서비스**를 만드는 다음 코드를 추가합니다.

데이터 팩터리에서 연결된 서비스를 만들어 데이터 저장소를 연결하고 계산 서비스를 데이터 팩터리에 연결합니다. 이 빠른 시작에서는 복사 원본 및 싱크 저장소 모두에 대해 샘플의 “AzureStorageLinkedService”라는 하나의 Azure Storage 연결된 서비스를 만들기만 하면 됩니다.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
```

## <a name="create-a-dataset"></a>데이터 집합 만들기

**Azure Blob Storage 데이터 집합**을 만드는 **Main** 메서드에 다음 코드를 추가합니다.

원본에서 싱크로 복사할 데이터를 나타내는 데이터 집합을 정의합니다. 이 예에서 이 Blob 데이터 집합은 이전 단계에서 만든 Azure Storage 연결된 서비스를 참조합니다. 데이터 집합은 값이 데이터 집합을 사용하는 활동에 설정되어 있는 매개 변수를 사용합니다. 매개 변수는 데이터가 상주/저장되는 위치를 가리키는 “folderPath”를 구성하는 데 사용됩니다.

```csharp
// Create a Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = new Expression { Value = "@{dataset().path}" },
        Parameters = new Dictionary<string, ParameterSpecification>
        {
            { "path", new ParameterSpecification { Type = ParameterType.String } }

        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>파이프라인을 만듭니다.

**Main** 메서드에 **복사 작업이 있는 파이프라인**을 만드는 다음 코드를 추가합니다.

이 예제에서 이 파이프라인은 하나의 활동을 포함하고 입력 Blob 경로 및 출력 Blob 경로의 두 매개 변수를 사용합니다. 이러한 매개 변수의 값은 파이프라인이 트리거/실행될 때 설정됩니다. 복사 활동은 입력 및 출력 시 이전 단계에서 만든 동일한 Blob 데이터 집합을 참조합니다. 데이터 집합을 입력된 데이터 집합으로 사용하는 경우 입력된 경로가 지정됩니다. 또한 데이터 집합을 출력된 데이터 집합으로 사용하는 경우 출력된 경로가 지정됩니다. 

```csharp
// Create a pipeline with a copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "inputPath", new ParameterSpecification { Type = ParameterType.String } },
        { "outputPath", new ParameterSpecification { Type = ParameterType.String } }
    },
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToBlob",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.inputPath" }
                    }
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.outputPath" }
                    }
                }
            },
            Source = new BlobSource { },
            Sink = new BlobSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>파이프라인 실행 만들기

**파이프라인 실행을 트리거하는** **Main** 메서드에 다음 코드를 추가합니다.

이 코드는 파이프라인에 지정된 **inputPath** 및 **outputPath** 매개 변수의 값을 원본 및 싱크 Blob 경로의 실제 값으로 설정합니다.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> parameters = new Dictionary<string, object>
{
    { "inputPath", inputBlobPath },
    { "outputPath", outputBlobPath }
};
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>파이프라인 실행 모니터링

1. **Main** 메서드에 다음 코드를 추가하여 데이터 복사가 완료될 때까지 지속적으로 파이프라인 실행의 상태를 검사합니다.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. **Main** 메서드에 복사 작업 실행 세부 정보(예: 읽고/쓴 데이터의 크기)를 검색하는 다음 코드를 추가합니다.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>코드 실행

응용 프로그램을 빌드하고 시작한 다음 파이프라인 실행을 확인합니다.

콘솔에서 데이터 팩터리, 연결된 서비스, 데이터 집합, 파이프라인 및 파이프라인 실행 만들기에 대한 진행 상황을 출력합니다. 그런 다음 파이프라인 실행 상태를 확인합니다. 데이터 읽기/쓰기 크기와 함께 복사 활동 실행 세부 정보가 표시될 때까지 기다립니다. 그런 다음 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)와 같은 도구를 사용하여 Blob이 변수에 지정한 대로 “inputBlobPath”에서 “outputBlobPath”로 복사되었는지 검사합니다.

### <a name="sample-output"></a>샘플 출력: 
```json
Creating data factory SPv2Factory0907...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
        "type": "SecureString"
      }
    }
  }
}
Creating dataset BlobDataset...
{
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
Creating pipeline Adfv2QuickStartPipeline...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
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
        "name": "CopyFromBlobToBlob"
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
Creating pipeline run...
Pipeline run ID: 308d222d-3858-48b1-9e66-acd921feaa09
Checking pipeline run status...
Status: InProgress
Status: InProgress
Checking copy activity run details...
{
    "dataRead": 331452208,
    "dataWritten": 331452208,
    "copyDuration": 23,
    "throughput": 14073.209,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedCloudDataMovementUnits": 2,
    "billedDuration": 23
}

Press any key to exit...

```

## <a name="clean-up-resources"></a>리소스 정리
데이터 팩터리를 프로그래밍 방식으로 삭제하려면 프로그램에 다음 코드 줄을 추가합니다. 

```csharp
            Console.WriteLine("Deleting the data factory");
            client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>다음 단계
이 샘플의 파이프라인은 Azure Blob 저장소의 한 위치에서 다른 위치로 데이터를 복사합니다. [자습서](tutorial-copy-data-dot-net.md)를 통해 더 많은 시나리오에서의 데이터 팩터리 사용에 관해 알아보세요. 
