<properties 
	pageTitle="Azure Data Factory 파이프라인에서 사용자 지정 작업 사용" 
	description="사용자 지정 작업을 만들고 Azure Data Factory 파이프라인에서 사용하는 방법에 대해 알아봅니다." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/06/2015" 
	ms.author="spelluru"/>

# Azure Data Factory 파이프라인에서 사용자 지정 작업 사용
Azure Data Factory는 파이프라인에서 데이터를 이동 및 처리하는 데 사용되는 **복사 작업** 및 **HDInsight 작업**과 같은 기본 제공 작업을 지원합니다. 고유한 변환/처리 논리를 포함하는 사용자 지정 .NET 작업을 만들어 파이프라인에서 사용할 수도 있습니다. **Azure HDInsight** 클러스터 또는 **Azure 배치** 서비스를 사용하여 실행되도록 작업을 구성할 수 있습니다.

이 문서에서는 사용자 지정 작업을 만들고 Azure Data Factory 파이프라인에서 사용하는 방법을 설명합니다. 사용자 지정 작업을 만들고 사용하기 위한 단계별 지침이 포함된 자세한 연습도 제공합니다. 이 연습에서는 HDInsight 연결된 서비스를 사용합니다. Azure Batch 연결된 서비스를 대신 사용하려면 **AzureBatch** 형식의 연결된 서비스를 만들고 파이프라인 JSON(**linkedServiceName**)의 작업 섹션에서 사용합니다. 사용자 지정 작업과 함께 Azure 배치를 사용하는 방법에 대한 자세한 내용은 [Azure 배치 연결된 서비스](#AzureBatch) 섹션을 참조하세요.


## <a name="walkthrough" /> 연습
이 연습에서는 사용자 지정 작업을 만들고 Azure Data Factory 파이프라인에서 작업을 사용하는 단계별 지침을 제공합니다. 이 연습은 [Azure Data Factory 시작][adfgetstarted] 자습서에서 이어지는 내용입니다. 사용자 지정 작업의 작동을 확인하려면 먼저 시작 자습서를 살펴본 다음 이 연습을 수행해야 합니다.

**필수 조건:**


- [Azure Data Factory 시작][adfgetstarted] 자습서. 이 연습을 수행하기 전에 이 문서에 나온 자습서를 완료해야 합니다.
- Visual Studio 2012 또는 2013
- [Azure .NET SDK][azure-developer-center] 다운로드 및 설치
- 최신 [Azure Data Factory용 NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories/)(영문)를 다운로드하여 설치합니다. 지침은 연습에 있습니다.
- Azure 저장소용 NuGet 패키지 다운로드 및 설치. 지침이 이 연습에 나오므로 이 단계는 건너뛰어도 됩니다.

## 1단계: 사용자 지정 작업 만들기

1.	.NET 클래스 라이브러리 프로젝트를 만듭니다.
	<ol type="a">
	<li><b>Visual Studio 2012</b> 또는 <b>Visual Studio 2013</b>을 시작합니다.</li>
	<li><b>파일</b>을 클릭하고 <b>새로 만들기</b>를 가리킨 다음 <b>프로젝트</b>를 클릭합니다.</li> 
	<li><b>템플릿</b>을 확장하고 <b>Visual C#</b>을 선택합니다. 이 연습에서는 C#을 사용하지만 다른 .NET 언어를 사용하여 사용자 지정 작업을 개발할 수도 있습니다.</li> 
	<li>오른쪽의 프로젝트 형식 목록에서 <b>클래스 라이브러리</b>를 선택합니다.</li>
	<li><b>MyDotNetActivity</b>를 <b>이름</b>으로 입력합니다.</li> 
	<li><b>C:\ADFGetStarted</b>를 <b>위치</b>로 선택합니다.</li>
	<li><b>확인</b>을 클릭하여 프로젝트를 만듭니다.</li>
</ol>
2.  <b>도구</b>를 클릭하고 <b>NuGet 패키지 관리자</b>를 가리킨 다음 <b>패키지 관리자 콘솔</b>을 클릭합니다.
3.	<b>패키지 관리자 콘솔</b>에서 다음 명령을 실행하여 <b>Microsoft.Azure.Management.DataFactories</b>를 가져옵니다. 

		Install-Package Microsoft.Azure.Management.DataFactories

4. Azure 저장소 NuGet 패키지를 프로젝트로 가져옵니다.

		Install-Package Azure.Storage

5. 다음 **using** 문을 프로젝트의 원본 파일에 추가합니다.

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
		using System.Linq;
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.Azure.Management.DataFactories.Runtime;

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
  
6. **네임스페이스** 이름을 **MyDotNetActivityNS**로 변경합니다.

		namespace MyDotNetActivityNS

7. 클래스 이름을 **MyDotNetActivity**로 변경하고 아래와 같이 **IDotNetActivity** 인터페이스에서 클래스를 파생합니다.

		public class MyDotNetActivity : IDotNetActivity

8. **IDotNetActivity** 인터페이스의 **Execute** 메서드를 **MyDotNetActivity** 클래스에 구현(추가)하고 다음 샘플 코드를 메서드에 복사합니다.

	다음 샘플 코드에서는 입력 Blob의 줄 수를 계산하고 출력 Blob에 Blob 경로, Blob의 줄 수, 작업이 실행된 컴퓨터, 현재 날짜-시간 등과 같은 내용을 생성합니다.

		public IDictionary<string, string> Execute(IEnumerable<LinkedService> linkedServices, IEnumerable<Dataset> datasets, Activity activity, IActivityLogger logger)
        {
            IDictionary<string, string> extendedProperties = ((DotNetActivity)activity.TypeProperties).ExtendedProperties;

            AzureStorageLinkedService inputLinkedService, outputLinkedService;
            CustomDataset inputLocation;
            AzureBlobDataset outputLocation;

            Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
            inputLocation = inputDataset.Properties.TypeProperties as CustomDataset;

            // using First method instead of Single since we are using the same 
            // Azure Storage linked service for input and output. 
            inputLinkedService = linkedServices.First(linkedService => linkedService.Name == inputDataset.Properties.LinkedServiceName).Properties.TypeProperties as AzureStorageLinkedService;

            string output = string.Empty;

            logger.Write("Before anything...");

            logger.Write("Printing dictionary entities if any...");
            foreach (KeyValuePair<string, string> entry in extendedProperties)
            {
                logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
            }

            string connectionString = GetConnectionString(inputLinkedService);
            string folderPath = GetFolderPath(inputDataset);

            logger.Write("Reading blob from: {0}", folderPath);

            CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
            CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

            BlobContinuationToken continuationToken = null;

            do
            {
                BlobResultSegment result = inputClient.ListBlobsSegmented(folderPath,
                                            true,
                                            BlobListingDetails.Metadata,
                                            null,
                                            continuationToken,
                                            null,
                                            null);
                foreach (IListBlobItem listBlobItem in result.Results)
                {
                    CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
                    int count = 0;
                    if (inputBlob != null)
                    {
                        using (StreamReader sr = new StreamReader(inputBlob.OpenRead()))
                        {
                            while (!sr.EndOfStream)
                            {
                                string line = sr.ReadLine();
                                if (count == 0)
                                {
                                    logger.Write("First line: [{0}]", line);
                                }
                                count++;
                            }

                        }

                    }
                    output += string.Format(CultureInfo.InvariantCulture,
                                    "{0},{1},{2},{3},{4}\n",
                                    folderPath,
                                    inputBlob.Name,
                                    count,
                                    Environment.MachineName,
                                    DateTime.UtcNow);

                }
                continuationToken = result.ContinuationToken;

            } while (continuationToken != null);

            Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
            outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;
            outputLinkedService = linkedServices.First(linkedService => linkedService.Name == outputDataset.Properties.LinkedServiceName).Properties.TypeProperties as AzureStorageLinkedService;

            connectionString = GetConnectionString(outputLinkedService);
            folderPath = GetFolderPath(outputDataset);

            logger.Write("Writing blob to: {0}", folderPath);

            CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
            Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + Guid.NewGuid() + ".csv");

            CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
            outputBlob.UploadText(output);

            return new Dictionary<string, string>();

        }

9. 다음과 같은 도우미 메서드를 추가합니다. **Execute** 메서드는 이러한 도우미 메서드를 호출합니다. **GetConnectionString** 메서드는 Azure 저장소 연결 문자열을 검색하고 **GetFolderPath** 메서드는 Blob 위치를 검색합니다.


        private static string GetConnectionString(AzureStorageLinkedService asset)
        {

            if (asset == null)
            {
                return null;
            }

            return asset.ConnectionString;
        }

        private static string GetFolderPath(Dataset dataArtifact)
        {
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
            if (blobDataset == null)
            {
                return null;
            }

            return blobDataset.FolderPath;
        }
   

10. 프로젝트를 컴파일합니다. 메뉴에서 **빌드**를 클릭하고 **솔루션 빌드**를 클릭합니다.
11. **Windows 탐색기**를 시작하고 빌드 유형에 따라 **bin\\debug** 또는 **bin\\release** 폴더로 이동합니다.
12. <project folder>\\bin\\Debug 폴더의 이진을 모두 포함하는 zip 파일 **MyDotNetActivity.zip**을 만듭니다. 오류가 발생할 경우 문제를 발생시킨 소스 코드의 줄 번호 같은 추가 정보를 받을 수 있도록 MyDotNetActivity.pdb 파일을 포함할 수 있습니다. 
13. **ADFTutorialDataFactory**의 연결된 서비스 **StorageLinkedService**가 사용하는 Azure Blob 저장소의 Blob 컨테이너 **customactvitycontainer**에 Blob으로 **MyDotNetActivity.zip**을 업로드합니다. Blob 컨테이너 **customactivitycontainer**가 아직 없는 경우 새로 만듭니다.

> [AZURE.NOTE]데이터 팩터리 프로젝트를 포함하는 Visual Studio의 솔루션에 이 .NET 작업 프로젝트를 추가하는 경우에는 zip 파일을 만들고 Azure Blob 저장소에 수동으로 업로드하는 마지막 두 단계를 수행할 필요가 없습니다. Visual Studio를 사용하여 데이터 팩터리 엔터티를 게시하면 이러한 단계가 게시 프로세스에서 자동으로 수행됩니다. Visual Studio를 사용하여 데이터 팩터리 엔터티를 만들고 게시하는 방법에 대한 자세한 내용은 [Visual Studio를 사용하여 첫 번째 파이프라인 빌드](data-factory-build-your-first-pipeline-using-vs.md) 및 [Azure Blob에서 Azure SQL로 데이터 복사](data-factory-get-started-using-vs.md) 문서를 참조하세요.


## 2단계: 파이프라인에서 사용자 지정 작업 사용
이 단계에서 수행하는 단계는 다음과 같습니다.

1. 사용자 지정 작업이 맵 전용 작업으로 실행되는 HDInsight 클러스터에 대한 연결된 서비스를 만듭니다. 
2. 이 샘플의 파이프라인에서 생성하는 출력 테이블을 만듭니다.
3. 1단계에서 만든 사용자 지정 작업을 사용하는 파이프라인을 만들고 실행합니다. 
 
### 사용자 지정 활동을 실행하는데 사용할 수 있는 HDInsight 클러스터에 대한 연결된 서비스 만들기
Azure Data Factory 서비스는 주문형 클러스터 만들기를 지원하며 이 클러스터를 사용하여 입력을 처리하고 출력 데이터를 생성합니다. 또한 고유한 클러스터를 사용하여 같은 작업을 할 수도 있습니다. 주문형 HDInsight 클러스터를 사용하면 각 조각에 대해 클러스터가 생성됩니다. 반면 고유한 HDInsight 클러스터를 사용하는 경우에는 클러스터에서 조각을 즉시 처리할 수 있습니다. 따라서 주문형 클러스터를 사용하는 경우 출력 데이터가 고유한 클러스터를 사용할 때처럼 빠르게 표시되지 않을 수 있습니다.

> [AZURE.NOTE]런타임에 .NET 작업의 한 인스턴스는 HDInsight 클러스터의 작업자 노드 하나에서만 실행됩니다. 여러 노드에서 실행되도록 확장할 수 없습니다. .NET 작업의 여러 인스턴스는 HDInsight 클러스터의 서로 다른 노드에서 병렬로 실행될 수 있습니다.

[Azure Data Factory에서 Pig 및 Hive 사용][hivewalkthrough]의 연습와 함께 [Azure Data Factory 시작][adfgetstarted] 자습서를 확장하는 경우, 이 연결된 서비스 만들기를 건너뛰고 ADFTutorialDataFactory에 이미 있는 연결된 서비스를 사용할 수 있습니다.


#### 주문형 HDInsight 클러스터를 사용하려면

1. **Azure 포털**의 데이터 팩터리 홈페이지에서 **작성자 및 배포**를 클릭합니다.
2. 데이터 팩터리 편집기의 명령 모음에서 **새 계산**을 클릭하고 메뉴에서 **주문형 HDInsight 클러스터**를 선택합니다.
2. JSON 스크립트에서 다음을 수행합니다. 
	1. **clusterSize** 속성에 대해 HDInsight 클러스터의 크기를 지정합니다.
	2. **jobsContainer** 속성에 대해 클러스터 로그를 저장할 기본 컨테이너의 이름을 지정합니다. 이 자습서에서는 **adfjobscontainer**를 지정합니다.
	3. **timeToLive** 속성에 대해 고객이 삭제되기 전에 유휴 상태로 유지될 수 있는 기간을 지정합니다. 
	4. **version** 속성에 대해 사용할 HDInsight 버전을 지정합니다. 이 속성을 제외하면 최신 버전이 사용됩니다.  
	5. **linkedServiceName**에 대해 시작 자습서에서 만든 **StorageLinkedService**를 지정합니다. 

			{
			  "name": "HDInsightOnDemandLinkedService",
			  "properties": {
			    "type": "HDInsightOnDemand",
			    "typeProperties": {
			      "clusterSize": "1",
			      "timeToLive": "00:05:00",
			      "version": "3.1",
			      "linkedServiceName": "StorageLinkedService"
			    }
			  }
			}

2. 명령 모음에서 **배포**를 클릭하여 연결된 서비스를 배포합니다.
   
#### 고유한 HDInsight 클러스터를 사용하려면 

1. **Azure 포털**의 데이터 팩터리 홈페이지에서 **작성자 및 배포**를 클릭합니다.
2. **데이터 팩터리 편집기**의 명령 모음에서 **새 계산**을 클릭하고 메뉴에서 **HDInsight 클러스터**를 선택합니다.
2. JSON 스크립트에서 다음을 수행합니다. 
	1. **clusterUri** 속성에 대해 HDInsight의 URL을 입력합니다. 예를 들어 https://<clustername>.azurehdinsight.net/을 입력합니다.     
	2. **UserName** 속성에 대해 HDInsight 클러스터에 액세스할 수 있는 사용자 이름을 입력합니다.
	3. **password** 속성에 대해 사용자 암호를 입력합니다. 
	4. **LinkedServiceName** 속성에 대해 **StorageLinkedService**를 입력합니다. 시작 자습서에서 만든 연결된 서비스입니다.. 

2. 명령 모음에서 **배포**를 클릭하여 연결된 서비스를 배포합니다.

### 출력 테이블 만들기

1. **데이터 팩터리 편집기**에서 **새 데이터 집합**을 클릭하고 명령 모음에서 **Azure Blob 저장소**를 클릭합니다.
2. 오른쪽 창의 JSON 스크립트를 다음 JSON 스크립트로 바꿉니다.

		{
		  "name": "OutputTableForCustom",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/customactivityoutput/{Slice}",
		      "partitionedBy": [
		        {
		          "name": "Slice",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "yyyyMMddHH"
		          }
		        }
		      ]
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

 	출력 위치는 **adftutorial/customactivityoutput/YYYYMMDDHH/**입니다. 여기서 YYYYMMDDHH는 조각이 생성되는 연도, 월, 날짜 및 시간입니다. 자세한 내용은 [개발자 참조][adf-developer-reference](영문)를 참조하세요.

2. 명령 모음에서 **배포**를 클릭하여 테이블을 배포합니다.


### 사용자 지정 작업을 사용하는 파이프라인 만들기 및 실행
   
1. 데이터 팩터리 편집기의 명령 모음에서 **새 파이프라인**을 클릭합니다. 명령이 표시되지 않으면 **... (줄임표)**을 클릭하여 표시합니다. 
2. 오른쪽 창의 JSON을 다음 JSON 스크립트로 바꿉니다. 사용자 고유의 클러스터를 사용하려고 하며 **HDInsightLinkedService** 연결된 서비스를 만드는 단계를 따른 경우 다음 JSON에서 **HDInsightOnDemandLinkedService**를 **HDInsightLinkedService**로 바꿉니다. 
		
		{
		  "name": "ADFTutorialPipelineCustom",
		  "properties": {
		    "description": "Use custom activity",
		    "activities": [
		      {
		        "Name": "MyDotNetActivity",
		        "Type": "DotNetActivity",
		        "Inputs": [
		          {
		            "Name": "EmpTableFromBlob"
		          }
		        ],
		        "Outputs": [
		          {
		            "Name": "OutputTableForCustom"
		          }
		        ],
		        "LinkedServiceName": "HDInsightOnDemandLinkedService",
		        "typeProperties": {
		          "AssemblyName": "MyDotNetActivity.dll",
		          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
		          "PackageLinkedService": "StorageLinkedService",
		          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
		          "extendedProperties": {
		            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
		          }
		        },
		        "Policy": {
		          "Concurrency": 1,
		          "ExecutionPriorityOrder": "OldestFirst",
		          "Retry": 3,
		          "Timeout": "00:30:00",
		          "Delay": "00:00:00"
		        }
		      }
		    ],
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z",
		    "isPaused": false
		  }
		}

	**StartDateTime** 값을 현재 날짜 3일 전 날짜로, **EndDateTime** 값을 현재 날짜로 바꿉니다. StartDateTime 및 EndDateTime은 둘 다 [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601)(영문)이어야 합니다. 예: 2014-10-14T16:32:41Z. 출력 테이블이 매일 생성되도록 예약되었으므로 3개의 조각이 생성됩니다.

	다음 사항에 유의하세요.

	- activities 섹션에는 **DotNetActivity** 유형의 작업 하나밖에 없습니다.
	- 시작 자습서에서 사용한 입력 테이블 **EmpTableFromBlob**을 동일하게 사용합니다.
	- 다음 단계에서 만들 새 출력 테이블 **OutputTableForCustom**을 사용합니다.
	- **AssemblyName**을 DLL의 이름 **MyActivities.dll**로 설정합니다.
	- **EntryPoint**를 **MyDotNetActivityNS.MyDotNetActivity**로 설정합니다.
	- **PackageLinkedService**를 **MyBlobStore**로 설정합니다. 이 Blob 저장소는 [Azure Data Factory 시작][adfgetstarted] 자습서에서 만들었고, 사용자 지정 작업 Zip 파일을 포함합니다.
	- **PackageFile**을 **customactivitycontainer/MyDotNetActivity.zip**으로 설정합니다.
     
4. 명령 모음에서 **배포**를 클릭하여 파이프라인을 배포합니다.
8. 출력 파일이 **adftutorial** 컨테이너의 Blob 저장소에 생성되었는지 확인합니다.

	![사용자 지정 작업의 출력][image-data-factory-ouput-from-custom-activity]

9. 출력 파일을 열면 다음과 유사한 출력이 표시됩니다.
	
	adftutorial/,emp.txt,2,WORKERNODE0,03/27/2015 19:23:28

	(Blob 위치), (Blob의 이름), (Blob의 줄 수), (작업이 실행된 노드), (날짜 시간 스탬프)

10.	[Azure 포털][azure-preview-portal] 또는 Azure PowerShell cmdlet을 사용하여 데이터 팩터리, 파이프라인 및 데이터 집합을 모니터링합니다. 포털을 통해서나 cmdlet을 사용하여 다운로드할 수 있는 로그(특히 user-0.log)에 있는 사용자 지정 활동의 코드에서 **ActivityLogger**의 메시지를 확인할 수 있습니다.

	![사용자 지정 작업의 로그 다운로드][image-data-factory-download-logs-from-custom-activity]
	
   
데이터 집합 및 파이프라인 모니터링에 대한 자세한 단계는 [파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md)를 참조하세요.

## 사용자 지정 작업 업데이트
사용자 지정 작업의 코드를 업데이트하는 경우 코드를 작성하고 새 이진이 포함된 zip 파일을 Blob 저장소로 업로드합니다.
    
## <a name="AzureBatch"></a> Azure 배치 연결된 서비스 사용 
> [AZURE.NOTE]Azure 배치 서비스에 대한 개요는 [Azure Batch Technical Overview][batch-technical-overview]를 참조하고, Azure 배치 서비스를 빨리 시작하려면 [.NET용 Azure 배치 라이브러리 시작][batch-get-started]을 참조하세요.

컴퓨팅 리소스로 Azure Batch를 사용하여 사용자 지정.NET 작업을 실행할 수 있습니다. 자체적인 Azure Batch 풀을 만들고 다른 구성과 함께 몇 가지 VM을 지정해야 합니다. Azure Batch 풀이 고객에게 제공하는 기능은 다음과 같습니다.

1. 하나에서 수천 개의 코어를 포함하는 풀을 만듭니다.
2. 수식에 따라 VM 수를 자동으로 조정
3. 모든 크기의 VM 지원
4. VM마다 몇 가지 작업을 구성 가능
5. 무제한의 작업 큐


이전 섹션에서 설명한 연습에서 Azure 배치 연결된 서비스를 사용하는 대략적인 단계는 다음과 같습니다.

1. Azure 관리 포털을 사용하여 Azure 배치 계정을 만듭니다. 지침은 [Azure 배치 기술 개요][batch-create-account] 문서를 참조하세요. Azure 배치 계정 이름 및 계정 키를 적어둡니다. 

	[New-AzureBatchAccount][new-azure-batch-account] cmdlet을 사용하여 Azure 배치 계정을 만들 수도 있습니다. 이 cmdlet 사용에 관한 자세한 지침은 [Azure PowerShell을 사용하여 Azure 배치 계정 관리][azure-batch-blog]를 참조하세요. 
2. Azure 배치 풀을 만듭니다. [Azure Batch 탐색기 도구][batch-explorer]의 소스 코드를 다운로드하여 컴파일하여 사용하거나 [.NET용 Azure Batch 라이브러리][batch-net-library]를 사용하여 Azure Batch 풀을 만들 수 있습니다. Azure 배치 탐색기를 사용하는 단계별 지침은 [Azure 배치 탐색기 샘플 연습][batch-explorer-walkthrough]을 참조하세요.
	
	[New-AzureBatchPool][new-azure-batch-pool] cmdlet을 사용하여 Azure 배치 풀을 만들 수도 있습니다.

2. 다음 JSON 템플릿을 사용하여 Azure 배치 연결된 서비스를 만듭니다. 데이터 팩터리 편집기에 표시되는 유사한 템플릿을 사용하여 시작할 수 있습니다. JSON 조각에서 Azure 배치 계정 이름, 계정 키 및 풀 이름을 지정합니다.

		{
		  "name": "AzureBatchLinkedService",
		  "properties": {
		    "type": "AzureBatch",
		    "typeProperties": {
		      "accountName": "<Azure Batch account name>",
			  "batchUri": "https://<region>.batch.azure.com",
		      "accessKey": "<Azure Batch account key>",
		      "poolName": "<Azure Batch pool name>",
		      "linkedServiceName": "<Specify associated storage linked service reference here>"
		    }
		  }
		}

	> [AZURE.IMPORTANT]**Azure 배치 계정 블레이드**의 **URL**은 accountname.region.batch.azure.com 형식을 사용합니다. JSON **batchUri** 속성의 경우 URL에서 **"accountname."을 제거**하고 **accountName** JSON 속성에 대해 **accountname**을 사용해야 합니다.
	  
	**poolName** 속성의 경우 풀 이름 대신 풀 ID를 지정할 수도 있습니다.

	이러한 속성에 대한 설명은 [Azure 배치 연결된 서비스 MSDN 토픽](https://msdn.microsoft.com/library/mt163609.aspx)을 참조하세요.

2.  데이터 팩터리 편집기에서 연습에서 만든 파이프라인에 대한 JSON 정의를 열고 **HDInsightLinkedService**를 **AzureBatchLinkedService**로 바꿉니다.
3.  Azure 배치 서비스를 사용한 시나리오를 테스트할 수 있도록 파이프라인의 시작 시간과 종료 시간을 변경하는 것이 좋습니다. 
4.  다음 다이어그램과 같이 Azure 배치 탐색기에서 조각 처리와 관련된 Azure 배치 작업을 확인할 수 있습니다.

	![Azure 배치 작업][image-data-factory-azure-batch-tasks]

> [AZURE.NOTE]데이터 팩터리 서비스는 HDInsight에 대해서와 마찬가지로 Azure Batch에 대한 주문형 옵션을 지원하지 않습니다. Azure Data Factory에서는 사용자 고유의 Azure Batch 풀만 사용할 수 있습니다.

## 참고 항목

[Azure 데이터 팩터리 업데이트: Azure 배치를 사용하여 ADF 사용자 지정 .NET 작업 실행](http://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/)

[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch-create-account]: ../batch/batch-technical-overview.md/#batch-concepts
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-get-started.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

[image-data-factory-azure-batch-tasks]: ./media/data-factory-use-custom-activities/AzureBatchTasks.png
 

<!---HONumber=Oct15_HO2-->