<properties 
	pageTitle="기계 학습 실험에서 게시된 기계 학습 웹 서비스를 사용하는 방법 | Azure" 
	description="기계 학습 서비스가 게시된 후에는 요청-응답 서비스 또는 일괄 처리 실행 서비스로 RESTFul 웹 서비스를 사용할 수 있습니다." 
	services="machine-learning" 
	solutions="big-data" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="tbd" 
	ms.date="02/20/2015" 
	ms.author="bradsev" />


# 게시된 기계 학습 웹 서비스를 사용하는 방법

## 소개

웹 서비스로 게시된 경우 Azure 기계 학습 실험에서는 광범위한 장치 및 플랫폼에서 사용할 수 있는 REST API를 제공합니다. 이는 단순 REST API는 JSON 형식의 메시지를 허용하고 응답하기 때문입니다. Azure 기계 학습 포털에서는 웹 서비스를 호출하는 데 사용할 수 있는 R, C# 및 Python 코드를 제공합니다. 그러나 다음 세 가지 기준을 충족하는 모든 장치에서 모든 프로그래밍 언어로 이러한 서비스를 호출할 수 있습니다.

* 네트워크에 연결되어야 합니다.
* HTTPS 요청을 수행할 SSL 기능이 있어야 합니다.
* JSON을 구문 분석할 수 있어야 합니다(직접 또는 지원 라이브러리를 통해).

따라서 웹 응용 프로그램, 모바일 응용 프로그램, 사용자 지정 데스크톱 응용 프로그램뿐만 아니라 Excel 내에서도 서비스를 사용할 수 있습니다.  

Azure 기계 학습 웹 서비스는 요청-응답 서비스 및 일괄 처리 실행 서비스의 두 가지 방법 중 하나로 사용될 수 있습니다. 각 시나리오에서 기능은 실험이 게시된 후 사용할 수 있는 RESTFul 웹 서비스를 통해 제공됩니다. Azure에서 Azure 웹 서비스 끝점을 사용하여 기계 학습 웹 서비스를 배포하면 사용량에 따라 서비스가 자동으로 확장되기 때문에 하드웨어 리소스의 사전 비용 및 지속적인 비용을 방지할 수 있습니다.

<!-- When this article gets published, fix the link and uncomment
REST API를 사용하여 Azure 기계 학습 웹 서비스 끝점을 관리하는 방법에 대한 자세한 내용은 **Azure 기계 학습 웹 서비스 끝점**을 참조하세요. 
-->

Azure 기계 학습 웹 서비스를 만들고 게시하는 방법에 대한 자세한 내용은 
[Azure 기계 학습 웹 서비스 게시][게시]를 참조하세요. 기계 학습 실험을 만들고 게시하는 단계별 연습은 [Azure 기계 학습을 사용하여 예측 솔루션 개발][연습]을 참조하세요.

[게시]: machine-learning-publish-a-machine-learning-web-service.md
[연습]: machine-learning-walkthrough-develop-predictive-solution.md


## RRS(요청-응답 서비스)

RRS(요청-응답 서비스)는 대기 시간이 짧고, 확장성이 높은 웹 서비스로, 기계 학습 스튜디오 실험에서 생성 및 게시된 상태 비저장 모델에 대한 인터페이스를 제공하는 데 사용됩니다.

RRS는 단일 입력 매개 변수 행을 수락하고 단일 행을 출력으로 생성합니다. 출력 행에는 여러 열이 포함될 수 있습니다.

RRS 예제에서는 응용 프로그램의 신뢰성을 검증합니다. 이 경우 수억 회의 응용 프로그램 설치를 예상할 수 있습니다. 응용 프로그램을 시작하면 관련 입력으로 RRS 서비스를 호출합니다. 그런 다음 서비스로부터 응용 프로그램 실행을 허용하거나 차단하는 유효성 검사 응답을 받습니다.


## BES(일괄 처리 실행 서비스)
 
BES(일괄 처리 실행 서비스)는 많은 데이터 레코드의 점수 매기기를 일괄적으로 처리하는 비동기 서비스입니다. BES의 입력에는 Blob, Azure의 테이블, SQL Azure, HDInsight(예: Hive 쿼리의 결과) 및 HTTP 소스 등 다양한 소스의 일괄 처리 레코드가 포함되어 있습니다. BES의 출력에는 점수 매기기 결과가 포함되어 있습니다. 결과는 Azure Blob 저장소의 파일 출력이며 저장소 끝점의 데이터는 응답으로 반환됩니다.

BES는 개인 또는 IOT(사물 인터넷) 장치에 대한 정기적으로 예약된 점수 매기기와 같이 응답이 즉시 필요하지 않은 경우에 유용합니다.

## 예제
RRS와 BES의 작동 방식을 모두 보여 주기 위해 여기에서는 Azure 웹 서비스 예제를 사용합니다. 이 서비스는 IOT(사물 인터넷) 시나리오에서 사용됩니다. 단순함을 유지하기 위해 장치에서는 하나의 값인 `cog_speed`만 전송하고 단일 응답을 받습니다. 

RRS 또는 BES 서비스를 호출하는 데 필요한 정보에는 네 가지가 있습니다. 이 정보는 실험이 게시된 후 [Azure 기계 학습 서비스 페이지](https://studio.azureml.net)의 서비스 페이지에서 즉시 사용할 수 있습니다. 화면 왼쪽의 웹 서비스 링크를 클릭하면 게시된 서비스가 표시됩니다. 특정 서비스에 대한 정보를 찾으려면 RRS와 BES 둘 다에 대한 API 도움말 페이지 링크를 클릭하면 됩니다.

1.	**서비스 API 키** - 서비스 기본 페이지에 있음
2.	**서비스 URI** - 선택한 서비스에 대한 API 도움말 페이지에 있음
3.	예상 **API 요청 본문** - 선택한 서비스에 대한 API 도움말 페이지에 있음
4.	예상 **API 응답 본문** - 선택한 서비스에 대한 API 도움말 페이지에 있음

아래의 두 예제에서는 C# 언어를 사용하여 필요한 코드를 설명하며, 대상 플랫폼은 Windows 8 데스크톱입니다. 

### RRS 예제
API 도움말 페이지에서 URI와 별도로 정의 및 코드 샘플을 입력하고 출력합니다. API 입력은 특별히 이 서비스에 대해 설명되며, API 호출의 페이로드입니다. 

**샘플 요청**

	{
	  "Inputs": {
	    "input1": {
	      "ColumnNames": [
	        "cog_speed"
	      ],
	      "Values": [
	        [
	          "0"
	        ],
	        [
	          "1"
	        ]
	      ]
	    }
	  },
	  "GlobalParameters": {}
	}


마찬가지로 API 응답도 특별히 이 서비스에 대해 설명됩니다.

**샘플 응답**

	{
	  "Results": {
	    "output1": {
	      "type": "DataTable",
	      "value": {
	        "ColumnNames": [
	          "cog_speed"
	        ],
	        "ColumnTypes": [
	          "Numeric"
	        ].
	      "Values": [
	        [
	          "0"
	        ],
	        [
	          "1"
	        ]
	      ]
	    }
	  },
	  "GlobalParameters": {}
	}

페이지의 아래쪽에 코드 예제가 있습니다. 다음은 C# 구현에 대한 코드 샘플입니다. 
                   
**샘플 코드**
	using System;
	using System.Collections.Generic;
	using System.IO;
	using System.Net.Http;
	using System.Net.Http.Formatting;
	using System.Net.Http.Headers;
	using System.Text;
	using System.Threading.Tasks;
	
	namespace CallRequestResponseService
	{
	    public class StringTable
	    {
	        public string[] ColumnNames { get; set; }
	        public string[,] Values { get; set; }
	    }
	
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            InvokeRequestResponseService().Wait();
	        }
	
	        static async Task InvokeRequestResponseService()
	        {
	            using (var client = new HttpClient())
	            {
	                var scoreRequest = new
	                {
	                    Inputs = new Dictionary<string, StringTable> () { 
	                        { 
	                            "input1", 
	                            new StringTable() 
	                            {
	                                ColumnNames = new string[] {"cog_speed"},
	                                Values = new string[,] {  { "0"},  { "1"}  }
	                            }
	                        },
	                    GlobalParameters = new Dictionary<string, string>() { }
	                };
	                
	                const string apiKey = "abc123"; // Replace this with the API key for the web service
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
	
	                client.BaseAddress = new Uri("https://ussouthcentral.services.azureml.net/workspaces/<workspace id>/services/<service id>/execute?api-version=2.0&details=true");
	                
	                // WARNING: The 'await' statement below can result in a deadlock if you are calling this code from the UI thread of an ASP.Net application.
	                // One way to address this would be to call ConfigureAwait(false) so that the execution does not attempt to resume on the original context.
	                // For instance, replace code such as:
	                //      result = await DoSomeTask()
	                // with the following:
	                //      result = await DoSomeTask().ConfigureAwait(false)

	                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);
	
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    Console.WriteLine("Result: {0}", result);
	                }
	                else
	                {
	                    Console.WriteLine("Failed with status code: {0}", response.StatusCode);
	                }
	            }
	        }
	    }
	}

### BES 예제
API 도움말 페이지에서 URI 외에 사용 가능한 여러 호출에 대한 정보를 찾을 수 있습니다. RRS 서비스와 달리 BES는 비동기 서비스입니다. 즉, BES API는 실행할 작업을 큐에 넣기만 합니다. API 응답을 받기 전에 작업을 실제로 실행하지는 않습니다. 개발자가 BES 서비스를 사용하여 수행할 수 있는 작업에는 다음 세 가지가 있습니다.

1. 일괄 처리 실행 작업 제출
1. 일괄 처리 실행 작업의 상태 또는 결과 가져오기
1. 일괄 처리 실행 작업 삭제  

**1. 일괄 처리 실행 작업 제출**

일괄 처리 데이터가 저장된 위치에 대한 정보를 제공하여 일괄 처리 실행 작업을 제출합니다. 이 예제에서는 일괄적으로 점수를 매길 레코드가 저장소 계정의 Blob 파일에 있는 것으로 가정합니다.

일괄 처리 작업에 대한 응답은 작업 ID입니다. 이는 작업이 비동기적으로 실행되기 때문입니다. 나중에 작업 ID를 사용하여 작업 상태 및 결과를 가져옵니다.

**샘플 요청**

	{
	  "Input": {
	    "ConnectionString":     
	    "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=mystorageacctKey",
	    "RelativeLocation": "/mycontainer/mydatablob.csv",
	    "BaseLocation": null,
	    "SasBlobToken": null
	  },
	  "Output": null,
	  "GlobalParameters": {}
	}

**샘플 응답**

	"539d0bc2fde945b6ac986b851d0000f0" // The JOB_ID

**샘플 코드**

	// This code requires the Nuget package Microsoft.AspNet.WebApi.Client to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.AspNet.WebApi.Client
	
	using System;
	using System.Collections.Generic;
	using System.Net.Http;
	using System.Threading.Tasks;
	using System.Net.Http.Headers;
	
	namespace CallBatchExecutionService
	{
	    internal class Program
	    {
	        private static void Main(string[] args)
	        {
	            InvokeBatchExecutionService().Wait();
	        }
	
	        private static async Task InvokeBatchExecutionService()
	        {
	            // API Information
	            const string BESUrl = "[BES URI]";
	            const string ApiKey = "abc123"; 
	            // The storage account information
	            const string StorageAccountName = @"mystorageacct"; 
	            const string StorageAccountKey = @"Dx9WbMIThAvXRQWap/aLnxT9LV5txxw==";
	            // Storage file with the batch of records
	            const string StorageInputFile = @"/mycontainermydatablob.csv"; 
	
	
	            String connString = String.Format(
	                "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
	                StorageAccountName,
	                StorageAccountKey);
	
	            BatchRequest request = new BatchRequest();
	            request.Input.RelativeLocation = StorageInputFile;
	            request.Input.ConnectionString = connString;
	
	            using (var client = new HttpClient())
	            {
	                client.BaseAddress = new Uri(BESUrl);
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", ApiKey);
	
	                HttpResponseMessage response = await client.PostAsJsonAsync("", request);
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    Console.WriteLine("Job ID: {0}", result);
	                }
	                else
	                {
	                    Console.WriteLine("Failed with status code: {0}", response.StatusCode);
	                }
	            }
	        }
	    }
	
	    public class BatchInput
	    {
	        public String ConnectionString { get; set; }
	        public String RelativeLocation { get; set; }
	        public String BaseLocation { get; set; }
	        public String SasBlobToken { get; set; }
	
	        public BatchInput()
	        {
	            ConnectionString = null;
	            RelativeLocation = null;
	            BaseLocation = null;
	            SasBlobToken = null;
	        }
	    }
	
	    public class BatchRequest
	    {
	        public BatchInput Input { get; set; }
	
	        public Object Output { get; set; }
	
	        public Dictionary<string, string> GlobalParameters { get; set; }
	
	        public BatchRequest()
	        {
	            this.GlobalParameters = new Dictionary<string, string>();
	            Input = new BatchInput();
	            Output = null;
	        }
	    }
	}
	
**2. 일괄 처리 실행 작업의 상태 또는 결과 가져오기**

작업의 결과를 가져오려면 먼저 작업 제출에 대한 응답에 있던 작업 ID가 있어야 합니다. 이 API 호출에 대한 실제 입력은 없습니다. 약간 변경된 BES URI와 요청 메서드를 사용합니다. POST 요청 대신 API 도움말 페이지에 정의된 URI를 따르는 GET 요청을 사용합니다.
 
그러나 응답은 계층화됩니다.

**응답 페이로드**

	{
	    "StatusCode": STATUS_CODE,
	    "Result": RESULT,
	    "Details": DETAILS
	}

`StatusCode`는 다음과 같은 의미 체계의 0, 1, 2, 3 또는 4 값을 가질 수 있습니다.

* 0	시작 안 함
* 1	실행 중
* 2	실패함
* 3	취소됨
* 4	마침

작업이 완료되지 않은 경우 `Result`는 **null**입니다. 작업이 완료된 경우 `Result`는 다음과 같은 형식입니다. 

	{
	  "ConnectionString": null,
	  "RelativeLocation": "RELATIVE_LOCATION",
	  "BaseLocation": "BASE_LOCATION",
	  "SasBlobToken": "SAS_BLOB_TOKEN"
	}

세부 정보에는 오류 정보(있는 경우)가 표시됩니다.

**샘플 코드**

	// This code requires the Nuget package Microsoft.AspNet.WebApi.Client to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.AspNet.WebApi.Client
	//
	// Also, add a reference to Microsoft.WindowsAzure.Storage.dll for reading from and writing to the Azure blob storage
	
	using System;
	using System.IO;
	using System.Net.Http;
	using System.Net.Http.Headers;
	using System.Threading.Tasks;
	using Newtonsoft.Json;
	
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;
	
	namespace CallBatchExecutionService
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            String jobId = "123";
	            InvokeBatchExecutionService(jobId).Wait();
	        }
	
	        static async Task InvokeBatchExecutionService(String JobId)
	        {
	            Console.WriteLine(String.Format("Getting job status for job {0}", JobId));
	
	            // BES Information
	            const string BaseUrl = @"[BES Job Id]/{0}";
	            const string ApiKey = "abc123"; 
	            // Replace this with the location you would like to use for your output file
	            const string OutputFileLocation = @"myresults.csv"; 
	
	            using (var client = new HttpClient())
	            {
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", ApiKey);
	
	                HttpResponseMessage response = await client.GetAsync(String.Format(BaseUrl, JobId));
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    BatchResponseStructure responseStruct = JsonConvert.DeserializeObject<BatchResponseStructure>(result);
	
	                    switch (responseStruct.StatusCode)
	                    {
	                        case (int)BatchScoreStatusCode.NotStarted:
	                            Console.WriteLine("Not started...");
	                            break;
	                        case (int)BatchScoreStatusCode.Running:
	                            Console.WriteLine("Running...");
	                            break;
	                        case (int)BatchScoreStatusCode.Failed:
	                            Console.WriteLine("Failed!");
	                            Console.WriteLine(string.Format(@"Error details: {0}", status.Details));
	                            break;
	                        case (int)BatchScoreStatusCode.Cancelled:
	                            Console.WriteLine("Cancelled!");
	                            break;
	                        case (int)BatchScoreStatusCode.Finished:
	                            Console.WriteLine("Finished!");
	                            var credentials = new StorageCredentials(status.Result.SasBlobToken);
	                            var cloudBlob = new CloudBlockBlob(new Uri(new Uri(responseStruct.Result.BaseLocation), 
	                                                                                               responseStruct.Result.RelativeLocation), credentials);
	                            cloudBlob.DownloadToFile(OutputFileLocation, FileMode.Create);
	                            Console.WriteLine(string.Format(@"The results have been written to the file {0}", OutputFileLocation));
	                            break;
	                    }
	                }
	                else
	                {
	                    Console.WriteLine(String.Format("Batch Result : Failed with status code: {0}", response.StatusCode));
	                }
	            }
	        }
	    }
	
	    public enum BatchScoreStatusCode : int
	    {
	        NotStarted = 0,
	        Running = 1,
	        Failed = 2,
	        Cancelled = 3,
	        Finished = 4
	    }
	
	    public class BatchResult
	    {
	        public String ConnectionString { get; set; }
	        public String RelativeLocation { get; set; }
	        public String BaseLocation { get; set; }
	        public String SasBlobToken { get; set; }
	    }
	
	    public class BatchResponseStructure
	    {
	        public int StatusCode { get; set; }
	        public BatchResult Result { get; set; }
	        public String Details { get; set; }
	        public BatchResponseStructure()
	        {
	            this.Result = new BatchResult();
	        }
	    }
	}

**3. 일괄 처리 실행 작업 삭제**              
작업을 시작한 후 해당 작업을 삭제할 수도 있습니다. 작업을 완료하는 데 시간이 너무 오래 걸리는 등 여러 가지 이유로 작업을 삭제할 수 있습니다. 작업을 삭제하려면 먼저 작업 제출에 대한 응답에 포함된 작업 ID가 있어야 합니다.

이 API 호출에 대한 실제 입력은 없습니다. BES URI와 요청 메서드가 약간 변경되었습니다. POST 요청 대신 API 도움말 페이지에 정의된 URI를 따르는 DELETE 요청을 사용합니다. 코드 샘플은 매우 간단합니다.


<!--HONumber=49--> 