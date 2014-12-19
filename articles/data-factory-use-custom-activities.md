<properties title="Use custom activities in an Azure Data Factory pipeline" pageTitle="Azure 데이터 팩터리 파이프라인에서 사용자 지정 작업 사용" description="Learn how to create custom activities and use them in an Azure Data Factory pipeline." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Azure 데이터 팩터리 파이프라인에서 사용자 지정 작업 사용
Azure 데이터 팩터리는 파이프라인에서 데이터를 이동 및 처리하는 데 사용되는 **복사 작업** 및 **HDInsight 작업**과 같은 기본 제공 작업을 지원합니다. 고유한 변환/처리 논리를 포함하는 사용자 지정 작업을 만들어 파이프라인에서 사용할 수도 있습니다. 사용자 지정 작업은 HDInsight 클러스터에서 맵 전용 작업으로 실행되므로 파이프라인에서 사용자 지정 작업에 대한 HDInsight 클러스터를 연결해야 합니다.
 
이 문서에서는 사용자 지정 작업을 만들고 Azure 데이터 팩터리 파이프라인에서 사용하는 방법을 설명합니다. 사용자 지정 작업을 만들고 사용하기 위한 단계별 지침이 포함된 자세한 연습도 제공합니다.

## 사용자 지정 작업 만들기

사용자 지정 작업을 만들려면 다음을 수행합니다.
 
1.	Visual Studio 2013에서 **클래스 라이브러리** 프로젝트를 만듭니다.
2.	[Azure 데이터 팩터리용 NuGet 패키지][nuget-package](영문)를 다운로드하여 설치합니다. 설치 지침은 연습에 나옵니다.
3. 클래스 라이브러리의 원본 파일 맨 위에 다음 using 문을 추가합니다.
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 

4. 클래스를 업데이트하여 **ICustomActivity** 인터페이스를 구현합니다.
	<ol type='a'>
		<li>
			 <b>ICustomActivity</b>.에서 클래스를 파생합니다.
			<br/>
			예제: <br/>
			public class <b>MyCustomActivity : ICustomActivity</b>
		</li>

		<li>
			<b>ICustomActivity</b> 인터페이스의  <b>Execute</b> 메서드를 구현합니다.
		</li>

	</ol>
5. 프로젝트를 컴파일합니다.


## 파이프라인에서 사용자 지정 작업 사용
파이프라인에서 사용자 지정 작업을 사용하려면 다음을 수행합니다.

1.	**프로젝트의 **bin\debug** 또는 **bin\release** 출력 폴더에서 이진 파일을 모두  압축**합니다.
**Azure blob storage**에 **Blob으로 2.	**Zip 파일을 업로드 합니다.
3.	**파이프라인 JSON** 파일을 업데이트하여 파이프라인 JSON에서 Zip 파일, 사용자 지정 작업 DLL, 작업 클래스 및 Zip 파일이 포함된 Blob을 참조하게 합니다. JSON 파일에서 다음을 확인합니다.
	<ol type ="a">
		<li><b>작업 유형</b>을 <b>CustomActivity</b>로 설정해야 합니다.</li>
		<li><b>AssemblyName</b>은 Visual Studio 프로젝트의 출력 DLL 이름입니다.</li>
		<li><b>EntryPoint</b>는 <b>ICustomActivity</b> 인터페이스를 구현하는 <b>클래스</b>의 <b>네임스페이스</b> 및 <b>이름</b>입니다.</li>
		<li><b>PackageLinkedService</b>는 Zip 파일이 포함된 Blob을 참조하는 연결된 서비스입니다. </li>
		<li><b>PackageFile</b>은 Azure Blob 저장소에 업로드된 Zip 파일의 위치와 이름을 지정합니다.</li>
		<li><b>LinkedServiceName</b>은 HDInsight 클러스터(주문형 또는 사용자 고유)를 데이터 팩터리에 연결하는 연결된 서비스의 이름입니다. 사용자 지정 작업은 지정된 HDInsight 클러스터에서 맵 전용 작업으로 실행됩니다.</li>
	</ol>

	

	**부분 JSON 예제**

		"Name": "MyCustomActivity",
    	"Type": "CustomActivity",
    	"Inputs": [{"Name": "EmpTableFromBlob"}],
    	"Outputs": [{"Name": "OutputTableForCustom"}],
		"LinkedServiceName": "myhdinsightcluster",
    	"Transformation":
    	{
	    	"AssemblyName": "MyCustomActivity.dll",
    	    "EntryPoint": "MyCustomActivityNS.MyCustomActivity",
    	    "PackageLinkedService": "MyBlobStore",
    	    "PackageFile": "customactivitycontainer/MyCustomActivity.zip",

## 사용자 지정 작업을 업데이트하려면
사용자 지정 작업의 코드를 업데이트하고, 코드를 작성하고, 새 이진 파일이 포함된 Zip 파일을 Blob 저장소로 업로드하는 경우 **New-AzureDataFactoryPipeline**을 실행하여 사용자 지정 작업을 사용하는 기존 파이프라인을 재정의해야 합니다. 사용자 지정 작업을 사용하는 파이프라인을 만드는 경우 지정한 Blob 저장소의 Zip 파일이 HDInsight 클러스터에 연결된 Blob 저장소의 Azure 데이터 팩터리 컨테이너로 복사됩니다. 이 작업은 파이프라인을 만들 때만 수행됩니다. 따라서 사용자 지정 작업을 업데이트하는 경우에는 파이프라인을 다시 만들어야 합니다. 

다음 연습에서는 사용자 지정 작업을 만들고 Azure 데이터 팩터리 파이프라인에서 작업을 사용하는 단계별 지침을 제공합니다. 이 연습은 [Azure 데이터 팩터리 시작][adfgetstarted]에 나온 자습서에서 이어지는 내용입니다. 사용자 지정 작업의 작동을 확인하려면 먼저 시작 자습서를 살펴본 다음 이 연습을 수행해야 합니다. 

## 연습
**필수 조건:**


- [Azure 데이터 팩터리 시작][adfgetstarted]의 자습서. 이 연습을 계속하기 전에 이 문서에 나온 자습서를 완료해야 합니다.
- Visual Studio 2012 또는 2013
- [Microsoft Azure .NET SDK][azure-developer-center] 다운로드 및 설치
- [Azure 데이터 팩터리용 NuGet 패키지][nuget-package] 다운로드
- Azure 저장소용 NuGet 패키지 다운로드 및 설치. 지침이 이 연습에 나오므로 이 단계는 건너뛰어도 됩니다.

### 1단계: 사용자 지정 작업 만들기

1.	.NET 클래스 라이브러리 프로젝트를 만듭니다.
	<ol type="a">
		<li><b>Visual Studio 2012</b> 또는 <b>Visual Studio 2013</b>을 시작합니다.</li>
		<li><b>파일</b>을 클릭하고 <b>새로 만들기</b>를 가리킨 다음 <b>프로젝트</b>를 클릭합니다.</li> 
		<li><b>템플릿</b>을 확장하고 <b>Visual C#</b>을 선택합니다. 이 연습에서는 C#을 사용하지만 다른 .NET 언어를 사용하여 사용자 지정 작업을 개발할 수도 있습니다.</li> 
		<li>오른쪽의 프로젝트 유형 목록에서 <b>클래스 라이브러리</b>를 선택합니다.</li>
		<li><b>MyCustomActivity</b>를 <b>이름</b>에 입력합니다.</li> 
		<li><b>C:\ADFGetStarted</b>를 <b>위치</b>로 선택합니다.</li>
		<li><b>확인</b>을 클릭하여 프로젝트를 만듭니다.</li>
	</ol>
2.  <b>도구</b>를 클릭하고 <b>NuGet 패키지 관리자</b>를 가리킨 다음 <b>패키지 관리자 콘솔</b>을 클릭합니다.
3.	<b>패키지 관리자 콘솔</b>에서 이전에 다운로드한 <b>Microsoft.Azure.Management.DataFactories</b>를 다음 명령을 실행하여 가져옵니다. 폴더는 다운로드한 데이터 팩터리 NuGet 패키지가 포함된 위치로 바꿉니다.

		Install-Package Microsoft.Azure.Management.DataFactories -Source d:\packages -Pre

3.	<b>패키지 관리자 콘솔</b>에서 다음 명령을 실행하여 <b>Microsoft.DataFactories.Runtime</b>을 가져옵니다. 폴더는 다운로드한 데이터 팩터리 NuGet 패키지가 포함된 위치로 바꿉니다.

		Install-Package Microsoft.DataFactories.Runtime -Source d:\packages -Pre

4. Microsoft Azure 저장소 NuGet 패키지를 프로젝트로 가져옵니다.

		Install-Package WindowsAzure.Storage

5. 프로젝트의 원본 파일에 다음 **using** 문을 추가합니다.

		using System.IO;
		using System.Globalization;
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 
	
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
  
6. **네임스페이스** 이름을 **MyCustomActivityNS**로 변경합니다.

		namespace MyCustomActivityNS

7. 클래스 이름을 **MyCustomActivity**로 변경하고 아래와 같이 **ICustomActivity** 인터페이스에서 클래스를 파생합니다.

		public class MyCustomActivity : ICustomActivity

8. **ICustomActivity** 인터페이스의 **Execute** 메서드를 **MyCustomActivity** 클래스에 구현(추가)하고 다음 샘플 코드를 메서드에 복사합니다. 

	**inputTables** 및 **outputTables** 매개 변수는 이름에서 알 수 있듯이 작업의 입력 및 출력 테이블을 나타냅니다. Azure 포털을 통해서나 cmdlet을 사용하여 다운로드할 수 있는 로그 파일에서 **로거** 개체를 사용하여 기록하는 메시지를 볼 수 있습니다. **extendedProperties** 사전에는 작업의 JSON 파일에서 지정한 확장 속성과 해당 값의 목록이 포함되어 있습니다. 

	다음 샘플 코드에서는 입력 Blob의 줄 수를 계산하고 출력 Blob에 Blob 경로, Blob의 줄 수, 작업이 실행된 컴퓨터, 현재 날짜-시간 등과 같은 내용을 생성합니다.

        public IDictionary<string, string> Execute(
                    IEnumerable<ResolvedTable> inputTables, 
                    IEnumerable<ResolvedTable> outputTables, 
                    IDictionary<string, string> extendedProperties, 
                    IActivityLogger logger)
        {
            string output = string.Empty;

            logger.Write(TraceEventType.Information, "Before anything...");

            logger.Write(TraceEventType.Information, "Printing dictionary entities if any...");
            foreach (KeyValuePair<string, string> entry in extendedProperties)
            {
                logger.Write(TraceEventType.Information, "<key:{0}> <value:{1}>", entry.Key, entry.Value);
            }

            foreach (ResolvedTable inputTable in inputTables)
            {
                string connectionString = GetConnectionString(inputTable.LinkedService);
                string folderPath = GetFolderPath(inputTable.Table);

                if (String.IsNullOrEmpty(connectionString) ||
                    String.IsNullOrEmpty(folderPath))
                {
                    continue;
                }

                logger.Write(TraceEventType.Information, "Reading blob from: {0}", folderPath);

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
                                        logger.Write(TraceEventType.Information, "First line: [{0}]", line);
                                    }
                                    count++;
                                }

                            }

                        }
                        output += string.Format(CultureInfo.InvariantCulture, 
										"{0},{1},{2},{3}\n", 
										folderPath, 
										count, 
										Environment.MachineName, 
										DateTime.UtcNow);
                    }
                    continuationToken = result.ContinuationToken;

                } while (continuationToken != null);
            }

            foreach (ResolvedTable outputTable in outputTables)
            {
                string connectionString = GetConnectionString(outputTable.LinkedService);
                string folderPath = GetFolderPath(outputTable.Table);

                if (String.IsNullOrEmpty(connectionString) ||
                    String.IsNullOrEmpty(folderPath))
                {
                    continue;
                }

                logger.Write(TraceEventType.Information, "Writing blob to: {0}", folderPath);

                CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
                Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + Guid.NewGuid() + ".csv");

                CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
                outputBlob.UploadText(output);

            }
            return new Dictionary<string, string>();

        }

9. 다음과 같은 도우미 메서드를 추가합니다. **Execute** 메서드는 이러한 도우미 메서드를 호출합니다. **GetConnectionString** 메서드는 Azure 저장소 연결 문자열을 검색하고 **GetFolderPath** 메서드는 Blob 위치를 검색합니다. 


        private static string GetConnectionString(LinkedService asset)
        {
            AzureStorageLinkedService storageAsset;
            if (asset == null)
            {
                return null;
            }

            storageAsset = asset.Properties as AzureStorageLinkedService;
            if (storageAsset == null)
            {
                return null;
            }

            return storageAsset.ConnectionString;
        }

        private static string GetFolderPath(Table dataArtifact)
        {
            AzureBlobLocation blobLocation;
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            blobLocation = dataArtifact.Properties.Location as AzureBlobLocation;
            if (blobLocation == null)
            {
                return null;
            }

            return blobLocation.FolderPath;
        }
   


10. 프로젝트를 컴파일합니다. 메뉴에서 **빌드**를 클릭하고 **솔루션 빌드**를 클릭합니다.
11. **Windows 탐색기**를 시작하고 빌드 유형에 따라 **bin\debug** 또는 **bin\release** 폴더로 이동합니다.
12. <프로젝트 폴더>\bin\Debug 폴더의 이진 파일을 모두 포함하는 Zip 파일 **MyCustomActivity.zip**을 만듭니다.
	![zip output binaries][image-data-factory-zip-output-binaries]
13. **MyCustomActivity.zip** 파일을 **ADFTutorialDataFactory**의 **MyBlobStore** 연결된 서비스가 사용하는 Azure Blob 저장소의 Blob 컨테이너 **customactvitycontainer**에 Blob으로 업로드합니다.  Blob 컨테이너 **blobcustomactivitycontainer**가 아직 없는 경우 새로 만듭니다. 
    ![upload zip to blob][image-data-factory-upload-zip-to-blob]

### 사용자 지정 작업을 실행하는 데 사용할  HDInsight 클러스터에 대한 연결된 서비스 만들기
Azure 데이터 팩터리 서비스는 주문형 클러스터 만들기를 지원하며 이 클러스터를 사용하여 입력을 처리하고 출력 데이터를 생성합니다. 또한 고유한 클러스터를 사용하여 같은 작업을 할 수도 있습니다. 주문형 HDInsight 클러스터를 사용하면 각 조각에 대해 클러스터가 생성됩니다. 반면 고유한 HDInsight 클러스터를 사용하는 경우에는 클러스터에서 조각을 즉시 처리할 수 있습니다. 따라서 주문형 클러스터를 사용하는 경우 출력 데이터가 고유한 클러스터를 사용할 때처럼 빠르게 표시되지 않을 수 있습니다. 샘플을 보여 주기 위해 주문형 클러스터를 사용해 보겠습니다. 

> [WACOM.NOTE] [Azure 데이터 팩터리 시작][adfgetstarted] 자습서에 이어 [Azure 데이터 팩터리에서 Pig 및 Hive 사용][hivewalkthrough]의 연습을 진행한 경우 이 연결된 서비스 만들기를 건너뛰고 ADFTutorialDataFactory에 이미 있는 연결된 서비스를 사용할 수 있습니다. 

#### 주문형 HDInsight 클러스터를 사용하려면

1. 다음과 같은 내용으로 JSON 파일 **HDInsightOnDemandCluster.json**을 만들어 **C:\ADFGetStarted\Custom** 폴더에 저장합니다.


		{
    		"name": "HDInsightOnDemandCluster",
    		"properties": 
    		{
        		"type": "HDInsightOnDemandLinkedService",
				"clusterSize": 4,
        		"jobsContainer": "adftutorialjobscontainer",
        		"timeToLive": "00:05:00",
        		"linkedServiceName": "MyBlobStore"
    		}
		}

2. **Azure PowerShell**을 시작하고 다음 명령을 실행하여 **AzureResourceManager** 모드로 전환합니다. Azure 데이터 팩터리 cmdlet은 **AzureResourceManager** 모드에서 사용할 수 있습니다.

         switch-azuremode AzureResourceManager
		

3. **C:\ADFGetstarted\Custom** 폴더로 전환합니다.
4. 다음 명령을 실행하여 주문형 HDInsight 클러스터에 대한 연결된 서비스를 만듭니다.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HDInsightOnDemandCluster.json
  
   
#### 고유한 HDInsight 클러스터를 사용하려면 

1. 다음과 같은 내용으로 JSON 파일 **MyHDInsightCluster.json**을 만들어 **C:\ADFGetStarted\Custom** 폴더에 저장합니다. JSON 파일을 저장하기 전에 clustername, username 및 password를 적절한 값으로 바꿉니다.  

		{
   			"Name": "MyHDInsightCluster",
    		"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "MyBlobStore"
    		}
		}

2. **Azure PowerShell**을 시작하고 다음 명령을 실행하여 **AzureResourceManager** 모드로 전환합니다. Azure 데이터 팩터리 cmdlet은 **AzureResourceManager** 모드에서 사용할 수 있습니다.

         switch-azuremode AzureResourceManager
		

3. **C:\ADFGetstarted\Custom** 폴더로 전환합니다.
4. 다음 명령을 실행하여 주문형 HDInsight 클러스터에 대한 연결된 서비스를 만듭니다.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\MyHDInsightCluster.json



### 2단계: 파이프라인에서 사용자 지정 작업 사용
[Azure 데이터 팩터리 시작][adfgetstarted]의 자습서에 이어서 다른 파이프라인을 만들어 이 사용자 지정 작업을 테스트해 보겠습니다.

#### 사용자 지정 작업을 실행하는 데 사용할 HDInsight 클러스터에 대한 연결된 서비스 만들기


1.	다음 예제에 나온 대로 파이프라인에 대한 JSON을 만들고 **ADFTutorialPipelineCustom.json**으로 **C:\ADFGetStarted\Custom** 폴더에 저장합니다. **LinkedServiceName**의 이름을 HDInsight 클러스터의 이름(**HDInsightOnDemandCluster** 또는 **MyHDInsightCluster**)으로 변경합니다.


		{
    		"name": "ADFTutorialPipelineCustom",
    		"properties":
    		{
        		"description" : "Use custom activity",
        		"activities":
        		[
					{
                		"Name": "MyCustomActivity",
                     	"Type": "CustomActivity",
                     	"Inputs": [{"Name": "EmpTableFromBlob"}],
                     	"Outputs": [{"Name": "OutputTableForCustom"}],
						"LinkedServiceName": "MyHDInsightCluster",
                     	"Transformation":
                     	{
                        	"AssemblyName": "MyCustomActivity.dll",
                            "EntryPoint": "MyCustomActivityNS.MyCustomActivity",
                            "PackageLinkedService": "MyBlobStore",
                            "PackageFile": "customactivitycontainer/MyCustomActivity.zip",
                            "ExtendedProperties":
							{
								"SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
							}
                      	},
                        "Policy":
                        {
                        	"Concurrency": 1,
                            "ExecutionPriorityOrder": "OldestFirst",
                            "Retry": 3,
                            "Timeout": "00:30:00",
                            "Delay": "00:00:00"		
						}
					}
				]
			}
		}

	다음 사항에 유의하세요. 

	- 작업 섹션에 작업이 하나 있고 유형은 **CustomActivity**입니다.
	- 시작 자습서에서 사용한 입력 테이블 **EmpTableFromBlob**을 동일하게 사용합니다.
	- 다음 단계에서 만들 새 출력 테이블 **OutputTableForCustom**을 사용합니다.
	- **AssemblyName**을 DLL의 이름 **MyActivities.dll**로 설정합니다.
	- **EntryPoint**를 **MyCustomActivityNS.MyCustomActivity**로 설정합니다.
	- **PackageLinkedService**를 **MyBlobStore**로 설정합니다. 이 Blob 저장소는 [Azure 데이터 팩터리 시작][adfgetstarted]의 자습서에서 만들었고, 사용자 지정 작업 Zip 파일을 포함합니다.
	- **PackageFile**을 **customactivitycontainer/MyCustomActivity.zip**로 설정합니다.
     

4. 출력 테이블 (파이프라인 JSON에서 참조하는 **OutputTableForCustom**)에 대한 JSON 파일을 만들고 C:\ADFGetStarted\Custom\OutputTableForCustom.json으로 저장합니다.

		

		{
    		"name": "OutputTableForCustom",
    		"properties":
    		{
        		"location": 
        		{
					"type": "AzureBlobLocation",
					"folderPath": "adftutorial/customactivityoutput/{Slice}",
					"partitionedBy": [ { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } }],

					"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "hour",
            		"interval": 1
        		}   
    		}
		}

 	출력 위치는 **adftutorial/customactivityoutput/YYYYMMDDHH/**입니다. 여기서 YYYYMMDDHH는 조각이 생성되는 연도, 월, 날짜 및 시간입니다. 자세한 내용은 [개발자 참조][adf-developer-reference](영문)를 참조하세요. 

5. 다음 명령을 실행하여 **ADFTutorialDataFactory**에 **출력 테이블을 만듭니다**.
		
		

		New-AzureDataFactoryTable  -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\Custom\OutputTableForCustom.json -ResourceGroupName ADFTutorialResourceGroup


6. 이제 다음 명령을 실행하여 **파이프라인을 만듭니다**. 파이프라인 JSON 파일은 이전 단계에서 만들었습니다.

		New-AzureDataFactoryPipeline  -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\Custom\ADFTutorialPipelineCustom.json -ResourceGroupName ADFTutorialResourceGroup



7. 다음 PowerShell 명령을 실행하여 만든 파이프라인의 **활성 기간을 설정**합니다.

		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipelineCustom

	> [WACOM.NOTE] **StartDateTime** 값을 현재 날짜로, **EndDateTime** 값을 다음 날짜로 바꿉니다. StartDateTime 및 EndDateTime은 UTC 시간이며 [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601)이어야 합니다. 예를 들면 다음과 같습니다. 2014-10-14T16:32:41Z. **EndDateTime**은 선택 사항이지만 이 자습서에서는 사용합니다. 
	> **EndDateTime**을 지정하지 않는 경우 "**StartDateTime + 48시간**"으로 계산됩니다. 파이프라인을 무기한 실행하려면 **9/9/9999**를 **EndDateTime**으로 지정합니다.



8. 출력 파일이 **adftutorial** 컨테이너의 Blob 저장소에 생성되었는지 확인합니다.

	![output from custom activity][image-data-factory-ouput-from-custom-activity]

9. 출력 파일을 열면 다음과 유사한 출력이 표시됩니다.
	
	adftutorial/input,2,WORKERNODE0,10/07/2014 18:34:33 

	(Blob 위치), (Blob의 줄 수), (작업이 실행된 노드), (날짜 시간 스탬프)

10.	[Azure 포털][azure-preview-portal] 또는 Azure PowerShell cmdlet을 사용하여 데이터 팩터리, 파이프라인 및 데이터 집합을 모니터링합니다. 포털을 통해서나 cmdlet을 사용하여 다운로드할 수 있는 로그에 있는 사용자 지정 작업의 코드에서 **ActivityLogger**의 메시지를 확인할 수 있습니다.

	![download logs from custom activity][image-data-factory-download-logs-from-custom-activity]
   
데이터 집합 및 파이프라인을 모니터링하는 자세한 단계는 [Azure 데이터 팩터리 시작][adfgetstarted]을 참조하세요.      
    
## 참고 항목

문서 | 설명
------ | ---------------
[파이프라인에서 온-프레미스 데이터를 사용할 수 있도록 설정][use-onpremises-datasources] | 이 문서의 연습에서는 온-프레미스 SQL Server 데이터베이스에서 Azure Blob으로 데이터를 복사하는 방법을 보여 줍니다.
[데이터 팩터리에서 Pig 및 Hive 사용][use-pig-and-hive-with-data-factory] | 이 문서의 연습에서는 HDInsight 작업을 사용하여 hive/pig 스크립트로 입력 데이터를 처리하고 출력 데이터를 생성하는 방법을 보여 줍니다. 
[자습서: 데이터 팩터리를 사용하여 로그 파일 이동 및 처리][adf-tutorial] | 이 문서에서는 Azure 데이터 팩터리를 사용하는 실제 시나리오를 구현하여 로그 파일의 데이터에서 통찰력을 얻는 방법을 보여 주는 완전한 연습을 제공합니다.
[데이터 팩터리에서 사용자 지정 작업 사용][use-custom-activities] | 이 문서에서는 사용자 지정 작업을 만들어 파이프라인에서 사용하는 방법에 대한 단계별 지침이 포함된 연습을 제공합니다. 
[PowerShell을 사용하여 Azure 데이터 팩터리 모니터링 및 관리][monitor-manage-using-powershell] | 이 문서에서는 Azure PowerShell cmdlet을 사용하여 Azure 데이터 팩터리를 모니터링하는 방법에 대해 설명합니다. 문서에 있는 예제를 ADFTutorialDataFactory에 대해 사용해 볼 수 있습니다.
[데이터 팩터리 문제 해결][troubleshoot] | 이 문서에서는 Azure 데이터 팩터리 문제를 해결하는 방법에 대해 설명합니다. Azure SQL 데이터베이스의 테이블을 삭제하는 등 오류를 유발하여 이 문서의 연습을 ADFTutorialDataFactory에 대해 사용해 볼 수 있습니다. 
[Azure 데이터 팩터리 개발자 참조][developer-reference] | 개발자 참조에는 cmdlet, JSON 스크립트, 함수 등에 대한 포괄적인 참조 콘텐츠가 포함되어 있습니다. 


[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456




[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/ko-kr/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: ../data-factory-get-started
[hivewalkthrough]: ../data-factory-pig-hive-activities

[image-data-factory-zip-output-binaries]: ./media/data-factory-use-custom-activities/ZipOuputBinaries.png

[image-data-factory-upload-zip-to-blob]: ./media/data-factory-use-custom-activities/UploadZipToBlob.png

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png
