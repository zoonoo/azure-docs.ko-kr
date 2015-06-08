<properties 
	pageTitle="Azure 데이터 팩터리 파이프라인에서 사용자 지정 작업 사용" 
	description="사용자 지정 작업을 만들고 Azure 데이터 팩터리 파이프라인에서 사용하는 방법에 대해 알아봅니다." 
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
	ms.date="06/02/2015" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리 파이프라인에서 사용자 지정 작업 사용
Azure 데이터 팩터리와 같은 기본 제공 활동을 지원 **복사 활동** 및 **HDInsight 활동** 이동 하 고 데이터를 처리 파이프라인에서 사용할 수 있습니다. 고유한 변환/처리 논리는 사용자 지정.NET 활동 만들기 및 파이프라인에서 활동을 사용 수도 있습니다. 중 하나를 사용 하 여 실행할 활동을 구성할 수는 **Azure HDInsight** 클러스터 또는 **Azure 일괄 처리** 서비스입니다.

이 문서에서는 사용자 지정 작업을 만들고 Azure 데이터 팩터리 파이프라인에서 사용하는 방법을 설명합니다. 사용자 지정 작업을 만들고 사용하기 위한 단계별 지침이 포함된 자세한 연습도 제공합니다. 이 연습에서는 연결 된 HDInsight 서비스를 사용합니다. Azure 일괄 처리를 사용 하 여 연결 된 서비스 대신, 형식의 연결 된 서비스를 만들면 **AzureBatchLinkedService** JSON 파이프라인의 활동 섹션에서 사용 합니다 (* * linkedServiceName * *). 참조는 [Azure 일괄 처리에 대 한 연결 된 서비스](#AzureBatch) Azure 일괄 처리를 사용 하 여 사용자 지정 활동에 대 한 자세한 내용은 섹션입니다.

## 필수 조건
최신 다운로드 [Azure 데이터 팩터리에 대 한 NuGet 패키지][nuget-package] 하 고 설치 합니다. 지침에는 [연습](#SupportedSourcesAndSinks) 이 문서에서.

## 사용자 지정 작업 만들기

사용자 지정 작업을 만들려면 다음을 수행합니다.
 
1.	만들기는 **클래스 라이브러리** Visual Studio 2013에서 프로젝트.
3. 클래스 라이브러리의 원본 파일 맨 위에 다음 using 문을 추가합니다.
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 

4. 업데이트를 구현 하는 클래스는 **IDotNetActivity** 인터페이스입니다.
	<ol type='a'>
	<li>
		클래스를 파생 <b>IDotNetActivity</b>.
		<br/>
		예: <br/>
		공용 클래스 <b>MyDotNetActivity: IDotNetActivity</b>
	</li>

	<li>
		구현 된 <b>Execute</b> 메서드의 <b>IDotNetActivity</b> 인터페이스
	</li>

</ol>
5. 프로젝트를 컴파일합니다.


## 파이프라인에서 사용자 지정 활동 사용
파이프라인에서 사용자 지정 작업을 사용하려면 다음을 수행합니다.

1.	**를 압축** 에서 모든 이진 파일의 **bin\debug** 또는 **bin\release** 프로젝트에 대 한 폴더를 출력 합니다. 
2.	**Zip 업로드** 파일을 blob으로 프로그램 **Azure blob 저장소**. 
3.	업데이트는 **JSON 파이프라인** 파일을 zip 파일, 사용자 지정 활동 DLL, 활동 클래스 및 JSON 파이프라인에서 zip 파일이 포함 된 blob를 참조 하십시오. JSON 파일에서 다음을 확인합니다.
	<ol type ="a">
	<li><b>활동 유형</b> 로 설정 해야 <b>DotNetActivity</b>.</li>
	<li><b>AssemblyName</b> Visual Studio 프로젝트에서 출력 DLL의 이름입니다.</li>
	<li><b>EntryPoint</b> 지정는 <b>네임 스페이스</b> 및 <b>이름</b> 의 <b>클래스</b> 를 구현 하는 <b>IDotNetActivity</b> 인터페이스입니다.</li>
	<li><b>PackageLinkedService</b> 는 zip 파일이 포함 된 blob을 참조 하는 연결 된 서비스입니다. </li>
	<li><b>PackageFile</b> Azure blob 저장소에 업로드 된 zip 파일의 이름과 위치를 지정 합니다.</li>
	<li><b>LinkedServiceName</b> HDInsight 클러스터를 연결 하는 연결 된 서비스의 이름입니다 (요청 시 또는 사용자 고유의) 데이터 팩터리입니다. 사용자 지정 작업은 지정된 HDInsight 클러스터에서 맵 전용 작업으로 실행됩니다.</li>
</ol>**부분적으로 JSON 예**

		"Name": "MyDotNetActivity",
    	"Type": "DotNetActivity",
    	"Inputs": [{"Name": "EmpTableFromBlob"}],
    	"Outputs": [{"Name": "OutputTableForCustom"}],
		"LinkedServiceName": "myhdinsightcluster",
    	"Transformation":
    	{
	    	"AssemblyName": "MyDotNetActivity.dll",
    	    "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
    	    "PackageLinkedService": "MyBlobStore",
    	    "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",

## 사용자 지정 활동을 업데이트합니다.
사용자 지정 활동에 대 한 코드를 업데이트 하는 경우 빌드하고 blob 저장소에 새 바이너리를 포함 하는 zip 파일을 업로드 합니다.

## <a name="walkthrough" /> 연습
이 연습에서는 사용자 지정 활동을 만들고는 Azure 데이터 팩터리 파이프라인에서 활동을 사용 하기 위한 단계별 지침을 제공 합니다. 이 연습에서는 확장에서 자습서는 [Azure 데이터 팩터리 시작][adfgetstarted]. 사용자 지정 작업의 작동을 확인하려면 먼저 시작 자습서를 살펴본 다음 이 연습을 수행해야 합니다.

**필수 조건:**


- 자습서에서 [Azure 데이터 팩터리 시작][adfgetstarted]. 이 연습을 수행 하기 전에이 문서에서 자습서를 완료 해야 합니다.
- Visual Studio 2012 또는 2013
- 다운로드 및 설치 [Azure.NET SDK][azure-developer-center]
- 최신 다운로드 [Azure 데이터 팩터리에 대 한 NuGet 패키지][nuget-package] 하 고 설치 합니다. 지침은 연습에 있습니다.
- Azure 저장소용 NuGet 패키지 다운로드 및 설치. 지침이 이 연습에 나오므로 이 단계는 건너뛰어도 됩니다.

## 1 단계: 사용자 지정 활동 만들기

1.	.NET 클래스 라이브러리 프로젝트를 만듭니다.
	<ol type="a">
	<li>시작 <b>Visual Studio 2012</b> 또는 <b>Visual Studio 2013</b>.</li>
	<li>클릭 하 여 <b>파일</b>, 가리킨 <b>새로</b>, 클릭 하 고 <b>프로젝트</b>.</li> 
	<li>확장 하 고 <b>템플릿</b>, 선택 및 <b>Visual C#</b>. 이 연습에서는 C#을 사용하지만 다른 .NET 언어를 사용하여 사용자 지정 작업을 개발할 수도 있습니다.</li> 
	<li>선택 <b>클래스 라이브러리</b> 오른쪽에 프로젝트 형식 목록에서.</li>
	<li>입력 <b>MyDotNetActivity</b> 에 대 한는 <b>이름</b>.</li> 
	<li>선택 <b>C:\ADFGetStarted</b> 에 대 한는 <b>위치</b>.</li>
	<li><b>확인</b>을 클릭하여 프로젝트를 만듭니다.</li>
</ol>
2.  클릭 하 여 <b>도구</b>, 가리킨 <b>NuGet 패키지 관리자</b>, 를 클릭 하 고 <b>패키지 관리자 콘솔</b>.
3.	에 <b>패키지 관리자 콘솔</b>, 을 가져오려면 다음 명령을 실행 <b>Microsoft.Azure.Management.DataFactories</b>. 

		Install-Package Microsoft.Azure.Management.DataFactories –Pre

3.	에 <b>패키지 관리자 콘솔</b>, 을 가져오려면 다음 명령을 실행 <b>Microsoft.DataFactories.Runtime</b>. 폴더는 다운로드한 데이터 팩터리 NuGet 패키지가 포함된 위치로 바꿉니다.

		Install-Package Microsoft.DataFactories.Runtime –Pre

4. 프로젝트에서 Azure 저장소 NuGet 패키지를 가져옵니다.

		Install-Package Azure.Storage

5. 다음 추가 **를 사용 하 여** 프로젝트에서 소스 파일에는 문입니다.

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 
	
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
  
6. 이름을 변경 하는 **네임 스페이스** 를 **MyDotNetActivityNS**.

		namespace MyDotNetActivityNS

7. 클래스의 이름을 변경 **MyDotNetActivity** 에서 파생 하 고는 **IDotNetActivity** 아래와 같이 인터페이스입니다.

		public class MyDotNetActivity : IDotNetActivity

8. 구현 (추가)는 **Execute** 의 메서드는 **IDotNetActivity** 인터페이스는 **MyDotNetActivity** 클래스 및 메서드에 다음 코드 예제를 복사 합니다.

	 **inputTables** 및 **outputTables** 매개 변수 이름에 활동에 대 한 입력 및 출력 테이블을 나타냅니다. 메시지를 사용 하 여 로그를 볼 수는 **로 거** Azure 포털 또는 cmdlet을 사용 하 여에서 다운로드할 수 있는 로그 파일에는 개체입니다.  **extendedProperties** 사전에는 활동 및 해당 값에 대 한 JSON 파일에 지정 된 확장된 속성의 목록을 포함 합니다.

	입력된 blob에 있는 줄 수를 계산 하 고 출력 blob에 다음 콘텐츠를 생성 하는 다음 샘플 코드: blob, 활동 실행 된 현재 날짜 시간 컴퓨터에 있는 줄 수 있는 blob 경로입니다.

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
                                        "{0},{1},{2},{3},{4}\n",
                                        folderPath,
                                        inputBlob.Name,
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

9. 다음과 같은 도우미 메서드를 추가합니다.  **Execute** 메서드는 이러한 도우미 메서드를 호출 합니다.  **GetConnectionString** Azure 저장소 연결 문자열을 검색 하는 메서드 및 **GetFolderPath** 메서드는 blob 위치를 검색 합니다.


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
   


10. 프로젝트를 컴파일합니다. 클릭 하 여 **빌드** 를클릭하고 메뉴에서 **솔루션 빌드**.
11. 시작 **Windows 탐색기**, 로 이동 하 고 **bin\debug** 또는 **bin\release** 빌드의 폴더 따라 유형입니다.
12. Zip 파일을 만들 **MyDotNetActivity.zip** 의 모든 이진 파일을 포함 하는 <project folder>\bin\Debug 폴더입니다.
13. 업로드 **MyDotNetActivity.zip** blob 컨테이너에 blob으로: **customactvitycontainer** Azure에서 blob 저장소 하는 **MyBlobStore** 서비스에 연결 된는 **ADFTutorialDataFactory** 사용 합니다. Blob 컨테이너 만들기 **blobcustomactivitycontainer** 이미 존재 하지 않는 경우. 


## 파이프라인에서 사용자 지정 활동을 사용 하는 2 단계:
이 단계에서 수행 하는 단계는 다음과 같습니다.

1. 사용자 지정 활동 지도 전용 작업으로 실행 될 HDInsight 클러스터에 대 한 연결 된 서비스를 만듭니다. 
2. 이 샘플에서 파이프라인 생성 하는 출력 테이블을 만듭니다.
3. 만들고 1 단계에서 만든 사용자 지정 활동을 사용 하는 파이프라인을 실행 합니다. 
 
### 사용자 지정 활동을 실행 하는데 사용할 수 있는 HDInsight 클러스터에 대 한 연결 된 서비스 만들기
Azure 데이터 팩터리 서비스는 주문형 클러스터 만들기를 지원하며 이 클러스터를 사용하여 입력을 처리하고 출력 데이터를 생성합니다. 또한 고유한 클러스터를 사용하여 같은 작업을 할 수도 있습니다. 주문형 HDInsight 클러스터를 사용하면 각 조각에 대해 클러스터가 생성됩니다. 반면 고유한 HDInsight 클러스터를 사용하는 경우에는 클러스터에서 조각을 즉시 처리할 수 있습니다. 따라서 주문형 클러스터를 사용하는 경우 출력 데이터가 고유한 클러스터를 사용할 때처럼 빠르게 표시되지 않을 수 있습니다.

> [AZURE.NOTE]런타임에.NET 활동의 인스턴스를 HDInsight 클러스터;의 작업자 노드 하나에 대해서만 실행 여러 노드에서 실행 하는 확장할 수 없습니다. .NET 활동의 여러 인스턴스는 HDInsight 클러스터의 다른 노드에서 병렬로 실행할 수 있습니다.

확장 한 경우는 [Azure 데이터 팩터리 시작][adfgetstarted] 자습서에서 연습을 [사용 하 여 Pig 및 Hive Azure 데이터 팩터리로][hivewalkthrough], 이 연결 된 서비스 만들기를 건너뛸 하 고는 ADFTutorialDataFactory에 이미 연결 된 서비스를 사용할 수 있습니다.


#### 주문형 HDInsight 클러스터를 사용하려면

1. 에 **Azure 포털**, 를 클릭 하 여 **작성자 및 배포** 데이터 팩터리 홈페이지에서.
2. 데이터 팩터리 편집기에서 클릭 **새 계산** 를선택하고 명령 모음에서 **주문형 HDInsight 클러스터** 메뉴에서.
2. JSON 스크립트에서 다음을 수행 합니다. 
	1. 에 대 한는 **clusterSize** 속성을 HDInsight 클러스터의 크기를 지정 합니다.
	2. 에 대 한는 **jobsContainer** 속성, 클러스터 로그를 저장할 기본 컨테이너의 이름을 지정 합니다. 이 자습서에서는 지정 **adfjobscontainer**.
	3. 에 대 한는 **timeToLive** 속성을 얼마나 오래 고객 유휴 상태일 수 있는 삭제 하기 전에 지정 합니다. 
	4. 에 대 한는 **버전** 속성을 사용 하려는 HDInsight 버전을 지정 합니다. 이 속성을 제외 하는 경우에 최신 버전이 사용 됩니다.  
	5. 에 대 한는 **linkedServiceName**, 지정 **StorageLinkedService** 가져오기에서 만들었을 자습서를 시작 합니다. 

			{
		    	"name": "HDInsightOnDemandLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "StorageLinkedService"
		    	}
			}

2. 클릭 하 여 **배포** 연결 된 서비스를 배포 하려면 명령 모음에 있습니다.
   
#### 고유한 HDInsight 클러스터를 사용하려면 

1. 에 **Azure 포털**, 를 클릭 하 여 **작성자 및 배포** 데이터 팩터리 홈페이지에서.
2. 에 **데이터 팩터리 편집기**, 를 클릭 하 여 **새 계산** 를선택하고 명령 모음에서 **HDInsight 클러스터** 메뉴에서.
2. JSON 스크립트에서 다음을 수행 합니다. 
	1. 에 대 한는 **clusterUri** 속성을 프로그램 HDInsight에 대 한 URL을 입력 합니다. 예: https://<clustername>.azurehdinsight.net/     
	2. 에 대 한는 **UserName** 속성을 HDInsight 클러스터에 대 한 액세스 권한이 있는 사용자 이름을 입력 합니다.
	3. 에 대 한는 **암호** 속성을 사용자에 대 한 암호를 입력 합니다. 
	4. 에 대 한는 **LinkedServiceName** 속성, 입력 **StorageLinkedService**. 시작된 자습서 다운로드에에서 만들 때 연결 된 서비스입니다. 

2. 클릭 하 여 **배포** 연결 된 서비스를 배포 하려면 명령 모음에 있습니다.

### 출력 테이블 만들기

1. 에 **데이터 팩터리 편집기**, 를 클릭 하 여 **새 데이터 집합**, 클릭 하 고 **Azure Blob 저장소** 명령 모음에서.
2. 오른쪽 창에서 JSON 스크립트를 다음 JSON 스크립트 바꿉니다.

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


 	출력 위치는 **adftutorial/customactivityoutput/YYYYMMDDHH/** YYYYMMDDHH 연도, 월, 날짜 및 시간 생성 되 고 조각의입니다. 참조 [개발자 참조][adf-developer-reference] 대 한 자세한 내용은 합니다.

2. 클릭 하 여 **배포** 테이블을 배포 하려면 명령 모음에 있습니다.


### 만들기 및 사용자 지정 활동을 사용 하는 파이프라인 실행
   
1. 데이터 팩터리 편집기에서 클릭 **새 파이프라인** 명령 모음에서. 명령이 표시 되지 않으면 클릭 **... (줄임표)** 이 확인 합니다. 
2. 오른쪽 창에서 JSON을 다음 JSON 스크립트 바꿉니다. 만드는 단계를 실행 및 사용자 고유의 클러스터를 사용 하려는 경우는 **HDInsightLinkedService** 서비스에 연결 된 대체 **HDInsightOnDemandLinkedService** 와 **HDInsightLinkedService** 다음 JSON에 있습니다. 

		{
    		"name": "ADFTutorialPipelineCustom",
    		"properties":
    		{
        		"description" : "Use custom activity",
        		"activities":
        		[
					{
                		"Name": "MyDotNetActivity",
                     	"Type": "DotNetActivity",
                     	"Inputs": [{"Name": "EmpTableFromBlob"}],
                     	"Outputs": [{"Name": "OutputTableForCustom"}],
						"LinkedServiceName": "HDInsightLinkedService",
                     	"Transformation":
                     	{
                        	"AssemblyName": "MyDotNetActivity.dll",
                            "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                            "PackageLinkedService": "MyBlobStore",
                            "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
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
        		],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
			}
		}

	> [AZURE.NOTE]대체 **시작 날짜 시간** 현재 날짜 전에 3 일을 사용 하 여 값 및 **EndDateTime** 은 현재 날짜를 사용 하 여 값입니다. 시작 날짜 시간 및 EndDateTime 모두에 있어야 [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601). 예: 2014-10-14T16:32:41Z 합니다. 출력 테이블이 매일 생성되도록 예약되었으므로 3개의 조각이 생성됩니다.

	다음 사항에 유의하세요.

	- 작업 섹션에는 하나의 활동 및 형식의: **DotNetActivity**.
	- 동일한 입력된 테이블을 사용 하 여 **EmpTableFromBlob** Get에 사용 되는 자습서를 시작 합니다.
	- 새 출력 테이블을 사용 하 여 **OutputTableForCustom** 다음 단계에서 만든 됩니다.
	- **AssemblyName** DLL의 이름으로 설정 됩니다: **MyActivities.dll**.
	- **EntryPoint** 로 설정 된 **MyDotNetActivityNS.MyDotNetActivity**.
	- **PackageLinkedService** 로 설정 된 **MyBlobStore** 에서 자습서의 일부로 만들어진 [Azure 데이터 팩터리 시작][adfgetstarted]. 사용자 지정 작업 Zip 파일을 포함합니다.
	- **PackageFile** 로 설정 된 **customactivitycontainer/MyDotNetActivity.zip**.
     
4. 클릭 하 여 **배포** 파이프라인을 배포 하려면 명령 모음에 있습니다.
8. Blob 저장소에 생성 된 출력 파일은 확인 된 **adftutorial** 컨테이너입니다.

	![사용자 지정 활동에서 출력][image-data-factory-ouput-from-custom-activity]

9. 출력 파일을 열면 다음과 유사한 출력이 표시됩니다.
	
	adftutorial/,emp.txt,2,WORKERNODE0,03/27/2015 19시 23분: 28

	(위치 blob) (blob의 이름), (blob에 있는 줄 수) (노드는 활동 실행 된) (날짜 타임 스탬프)

10.	사용 하 여는 [Azure 포털][azure-preview-portal] 또는 Azure PowerShell cmdlet에 데이터 팩터리, 파이프라인 및 데이터 집합을 모니터링할 수 있습니다. 메시지를 볼 수는 **ActivityLogger** 로그에 사용자 지정 활동에 대 한 코드는 포털 또는 cmdlet을 사용 하 여 다운로드할 수 있습니다.

	![사용자 지정 활동에서 로그를 다운로드 합니다.][image-data-factory-download-logs-from-custom-activity]
   
참조 [Azure 데이터 팩터리 시작][adfgetstarted] 모니터링 데이터 집합 및 파이프라인에 대 한 자세한 단계입니다.
    
## <a name="AzureBatch"></a> 서비스에 연결 된 Azure 일괄 처리를 사용 하 여 
> [AZURE.NOTE]참조 [Azure 일괄 처리에 대 한 기술 개요][batch-technical-overview] 서비스 Azure 일괄 처리에 대 한 개요 및 참조 [.NET 용 Azure 일괄 처리 라이브러리 시작][batch-get-started] Azure 일괄 처리 서비스와 빠르게 시작 하려면.

이전 섹션에서 설명한 연습에서 Azure 일괄 처리가 연결 된 서비스를 사용 하는 대략적인 단계는 다음과 같습니다.

1. Azure 관리 포털을 사용 하 여 일괄 처리 Azure 계정을 만듭니다. 참조 [Azure 일괄 처리에 대 한 기술 개요][batch-create-account] 지침에 대 한 문서. 일괄 처리 Azure 계정 이름 및 계정 키를 note 합니다. 

	사용할 수도 있습니다 [새로 AzureBatchAccount][new-azure-batch-account] cmdlet의 일괄 처리 Azure 계정을 만들려고 합니다. 참조 [Azure 일괄 처리 계정 관리를 사용 하 여 Azure PowerShell][azure-batch-blog] 에 대 한 자세한 지침은이 cmdlet을 사용 합니다. 
2. Azure 일괄 처리 풀을 만들어야 합니다. 다운로드 하 고 사용할 수는 [Azure 일괄 처리 탐색기 도구][batch-explorer] (또는)를 사용 [.NET 용 Azure 일괄 처리 라이브러리][batch-net-library] Azure 일괄 처리 풀을 만듭니다. 참조 [Azure 일괄 처리 탐색기 샘플 연습][batch-explorer-walkthrough] Azure 일괄 처리 탐색기를 사용 하기 위한 단계별 지침.
	
	사용할 수도 있습니다 [새로 AzureBatchPool][new-azure-batch-pool] Azure 일괄 처리 풀을 만들어야 하는 cmdlet입니다.

2. 다음 JSON 템플릿을 사용 하 여 Azure 일괄 처리가 연결 된 서비스를 만듭니다. 데이터 팩터리 편집기를 시작 하 드립니다 비슷한 템플릿을 표시합니다. JSON 조각에는 Azure 일괄 처리 계정 이름, 계정 키와 풀 이름을 지정 합니다.

		{
		    "name": "AzureBatchLinkedService",
		    "properties": {
		        "type": "AzureBatchLinkedService",
		        "accountName": "<Azure Batch account name>",
		        "accessKey": "<Azure Batch account key>",
		        "poolName": "<Azure Batch pool name>",
		        "linkedServiceName": "<Specify associated storage linked service reference here>"
		  }
		}

	참조 [Azure 일괄 처리가 연결 된 서비스 MSDN 항목](https://msdn.microsoft.com/library/mt163609.aspx) 이러한 속성에 대 한 설명입니다.

2.  데이터 팩터리 편집기에서 열고 바꾸고 연습에서 만든 파이프라인에 대 한 JSON 정의 **HDInsightLinkedService** 와 **AzureBatchLinkedService**.
3.  Azure 일괄 처리 서비스를 사용 하 여 시나리오를 테스트할 수 있도록 파이프라인에 대 한 시작 시간과 종료 시간을 변경 수 있습니다. 
4.  다음 다이어그램에 표시 된 것 처럼 Azure 일괄 처리 탐색기에서 조각 처리와 관련 된 Azure 일괄 처리 작업을 볼 수 있습니다.

	![Azure 일괄 처리 작업][image-data-factory-azure-batch-tasks]

## 참고 항목

[Azure 데이터 팩터리 업데이트: Azure 일괄 처리를 사용 하 여 실행 ADF 사용자 지정.NET 활동](http://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/).

[batch-net-library]: ./batch/batch-dotnet-get-started.md
[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch-create-account]: ./batch/batch-technical-overview.md/#batch-concepts
[batch-technical-overview]: ./batch/batch-technical-overview.md
[batch-get-started]: ./batch/batch-dotnet-get-started.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
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
[hivewalkthrough]: data-factory-pig-hive-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

[image-data-factory-azure-batch-tasks]: ./media/data-factory-use-custom-activities/AzureBatchTasks.png

<!---HONumber=GIT-SubDir-->