<properties 
	pageTitle="Azure 기계 학습 웹 서비스에 연결 | Azure" 
	description="C# 또는 Python을 사용하는 경우 권한 부여 키를 사용하여 Azure 기계 학습 웹 서비스에 연결합니다." 
	services="machine-learning" 
	documentationCenter="" 
	authors="derrickv" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="derrickv" />


# Azure 기계 학습 웹 서비스에 연결 
Azure 기계 학습 개발자 환경은 실시간 또는 일괄 처리 모드로 입력 데이터에서 예측하는 웹 서비스 API입니다. Azure 기계 학습 스튜디오를 사용하여 예측을 만들고 Azure ML 웹 서비스를 게시할 수 있습니다. 

기계 학습 스튜디오를 사용하여 Azure 기계 학습 웹 서비스를 만들고 게시하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [Azure 기계 학습 프로세스 개요](../machine-learning-overview-of-azure-ml-process.md)
- [기계 학습 스튜디오 시작](http://azure.microsoft.com/documentation/videos/getting-started-with-ml-studio/)
- [Azure 기계 학습 미리 보기](https://studio.azureml.net/)
- [기계 학습 설명서 센터](http://azure.microsoft.com/documentation/services/machine-learning/)

## Azure 기계 학습 웹 서비스 ##

Azure 기계 학습 웹 서비스를 통해 외부 응용 프로그램에서 기계 학습 워크플로 점수 매기기 모델과 실시간으로 통신할 수 있습니다. 기계 학습 웹 서비스 호출은 외부 응용 프로그램에 예측 결과를 반환합니다. 기계 학습 웹 서비스를 호출하려면 예측을 게시할 때 생성된 API 키를 전달합니다. 기계 학습 웹 서비스는 웹 프로그래밍 프로젝트에 일반적으로 사용되는 아키텍처인 REST를 기반으로 합니다.

Azure 기계 학습에는 다음 두 가지 유형의 서비스가 있습니다.

- RRS(요청-응답 서비스) - 대기 시간이 짧고, 확장성이 높은 서비스로, 기계 학습 스튜디오에서 생성 및 게시되는 상태 비저장 모델에 대한 인터페이스를 제공합니다.
- BES(일괄 처리 실행 서비스) - 데이터 레코드의 점수를 일괄적으로 매기는 비동기 서비스입니다.

Azure 기계 학습 웹 서비스에 대한 자세한 내용은 [Azure 기계 학습 프로세스 개요](../machine-learning-overview-of-azure-ml-process.md)를 참조하세요.

## Azure 기계 학습 권한 부여 키 가져오기 ##
기계 학습 웹 서비스에서 웹 서비스 API 키를 가져올 수 있습니다. Microsoft Azure 기계 학습 스튜디오 또는 Azure 관리 포털에서 가져올 수 있습니다.
### Microsoft Azure 기계 학습 스튜디오 ###
1. Microsoft Azure 기계 학습 스튜디오의 왼쪽에서 **웹 서비스**를 클릭합니다.
2. 웹 서비스를 클릭합니다. "API 키"는 **대시보드** 탭에 있습니다.

### Azure 관리 포털 ###

1. 왼쪽에서 **기계 학습**을 클릭합니다.
2. 작업 영역을 클릭합니다.
3. **웹 서비스**를 클릭합니다.
4. 웹 서비스를 클릭합니다.
5. 끝점을 클릭합니다. "API 키"는 오른쪽 아래에 있습니다.

## <a id="connect"></a>Azure 기계 학습 웹 서비스에 연결

HTTP 요청 및 응답을 지원하는 모든 프로그래밍 언어를 사용하여 Azure 기계 학습 웹 서비스에 연결할 수 있습니다. Azure 기계 학습 웹 서비스 도움말 페이지에서 C#, Python 및 R로 작성된 예제를 볼 수 있습니다.

### Azure 기계 학습 웹 서비스 API 도움말 페이지를 보려면 ###
웹 서비스를 게시하면 Azure 기계 학습 API 도움말 페이지가 생성됩니다. [Azure 기계 학습 연습 - 웹 서비스 게시](machine-learning-walkthrough-5-publish-web-service.md)를 참조하세요.


**Azure 기계 학습 API 도움말 페이지를 보려면**
Microsoft Azure 기계 학습 스튜디오에서 다음을 수행합니다.

1. **웹 서비스**를 선택합니다.
2. 웹 서비스를 선택합니다.
3. **API 도움말 페이지** - **요청/응답** 또는 **일괄 처리 실행**을 선택합니다.


**Azure 기계 학습 API 도움말 페이지**
Azure 기계 학습 API 도움말 페이지에는 다음을 비롯하여 예측 웹 서비스에 대한 자세한 정보가 포함되어 있습니다.


<table>
	<tr>
		<td>&nbsp;</td>
		<td>예제 </td>
	</tr>
	<tr>
		<td>요청 POST URI </td>

		<td>https://ussouthcentral.services.azureml.net/workspaces/{WorkspaceId}/services/{ServiceId}/score
		</td>
	</tr>
	<tr>
		<td>샘플 요청 </td>
		<td>{ <br/> 
			&nbsp;&nbsp; "Id": "score00001",   <br/>
			&nbsp;&nbsp; "Instance": <br/>
			&nbsp;&nbsp;&nbsp;&nbsp; {  <br/>  
 			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp; "FeatureVector": { <br/>
			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;  "Col1": "0", <br/>      
			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;  "Col2": "0", <br/>      
			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;  "Col3": "0", <br/>  
			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;  ...     },   <br/>
			&nbsp;&nbsp;&nbsp;&nbsp;   "GlobalParameters": {}   <br/>
			&nbsp;&nbsp;&nbsp;&nbsp; } <br/>
		}</td>
	</tr>
	<tr>
		<td>응답 본문 </td>
		<td>
		<table style="width: 100%">

			<tr>
				<td><B>이름</B></td>
				<td><B>데이터 형식</B></td>
			</tr>
	
			<tr>
				<td>기능</td>
				<td>문자열</td>
			</tr>
			<tr>
				<td>개수</td>
				<td>숫자</td>
			</tr>
			<tr>
				<td>고유 값 개수 </td>
				<td>숫자 </td>
			</tr>
			<tr>
				<td>... </td>
				<td>... </td>
			</tr>
		</table>
		</td>
	</tr>
	<tr>
		<td>샘플 응답 </td>
		<td>[&quot;Col1&quot;,&quot;1&quot;,&quot;1&quot;,...] </td>
	</tr>
	<tr>
		<td>샘플 코드 </td>
		<td>(Sample code in C#, Python, and R) </td>
	</tr>
</table>

**참고** 예제는 Azure 기계 학습 샘플 컬렉션의 Sample 1: Download dataset from UCI: Adult 2 class dataset 부분에서 가져온 것입니다.

### C# 샘플 ###

Azure 기계 학습 웹 서비스에 연결하려면 ScoreData를 전달하는 **HttpClient**를 사용합니다. ScoreData에는 ScoreData를 나타내는 숫자 기능의 n 차원  벡터인 FeatureVector가 포함되어 있습니다. API 키를 사용하여 Azure 기계 학습 서비스에 인증합니다.

기계 학습 웹 서비스에 연결하려면 **Microsoft.AspNet.WebApi.Client** Nuget 패키지를 설치해야 합니다.

**Visual Studio에서 Microsoft.AspNet.WebApi.Client Nuget 설치**

1. Download dataset from UCI: Adult 2 class dataset 웹 서비스를 게시합니다.
2. **도구** > **Nuget 패키지 관리자** > **패키지 관리자 콘솔**을 클릭합니다.
2. **Install-Package Microsoft.AspNet.WebApi.Client**를 선택합니다.

**코드 샘플을 실행하려면**

1. Azure 기계 학습 샘플 컬렉션의 일부인 "Sample 1: Download dataset from UCI: Adult 2 class dataset" 실험을 게시합니다.
2. 웹 서비스에서 가져온 키로 apiKey를 할당합니다. Azure 기계 학습 권한 부여 키를 가져오는 방법을 참조하세요.
3. 요청 URI로 serviceUri를 할당합니다. 요청 URI를 가져오는 방법을 참조하세요.

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
	    public class ScoreData
	    {
	        public Dictionary<string, string> FeatureVector { get; set; }
	        public Dictionary<string, string> GlobalParameters { get; set; }
	    }
	
	    public class ScoreRequest
	    {
	        public string Id { get; set; }
	        public ScoreData Instance { get; set; }
	    }
	
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            InvokeRequestResponseService().Wait();
	
	            Console.ReadLine();
	        }
	
	        static async Task InvokeRequestResponseService()
	        {
	            //Assign apiKey with the key from a web service.
	            const string apiKey = "{ApiKey}";
	
	            //Assign serviceUri with the Request URI. See How to get a Request URI.
	            const string serviceUri = "{ServiceUri}";
	            
	            using (var client = new HttpClient())
	            {
	                ScoreData scoreData = new ScoreData()
	                {
	                    //Input data
	                    FeatureVector = new Dictionary<string, string>() 
	                    {
	                        { "Col1", "0" },
	                        { "Col2", "0" },
	                        { "Col3", "0" },
	                        { "Col4", "0" },
	                        { "Col5", "0" },
	                        { "Col6", "0" },
	                        { "Col7", "0" },
	                        { "Col8", "0" },
	                        { "Col9", "0" },
	                        { "Col10", "0" },
	                        { "Col11", "0" },
	                        { "Col12", "0" },
	                        { "Col13", "0" },
	                        { "Col14", "0" },
	                        { "Col15", "0" },
	                    },
	                    GlobalParameters = 
	                        new Dictionary<string, string>() {}
	                };
	
	                ScoreRequest scoreRequest = new ScoreRequest()
	                {
	                    Id = "score00001",
	                    Instance = scoreData
	                };
	
	                //Set authorization header
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
	             
	                client.BaseAddress = new Uri(serviceUrl);
	
	                //Post HTTP response message
	                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);
	
	                if (response.IsSuccessStatusCode)
	                {
	                    //Read result string
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


### Python 샘플 ###

Azure 기계 학습 웹 서비스에 연결하려면 ScoreData를 전달하는 **urllib2**를 사용합니다. ScoreData에는 ScoreData를 나타내는 숫자 기능의 n 차원  벡터인 FeatureVector가 포함되어 있습니다. API 키를 사용하여 Azure 기계 학습 서비스에 인증합니다.


**코드 샘플을 실행하려면**

1. Azure 기계 학습 샘플 컬렉션의 일부인 "Sample 1: Download dataset from UCI: Adult 2 class dataset" 실험을 게시합니다.
2. 웹 서비스에서 가져온 키로 apiKey를 할당합니다. Azure 기계 학습 권한 부여 키를 가져오는 방법을 참조하세요.
3. 요청 URI로 serviceUri를 할당합니다. 요청 URI를 가져오는 방법을 참조하세요.

		import urllib2
		# If you are using Python 3+, import urllib instead of urllib2
	
		import json 
	
		data =  {
	            "Id": "score00001",
	            "Instance": {
	                "FeatureVector": {
	                    "Col1": "0",
	                    "Col2": "0",
	                    "Col3": "0",
	                    "Col4": "0",
	                    "Col5": "0",
	                    "Col6": "0",
	                    "Col7": "0",
	                    "Col8": "0",
	                    "Col9": "0",
	                    "Col10": "0",
	                    "Col11": "0",
	                    "Col12": "0",
	                    "Col13": "0",
	                    "Col14": "0",
	                    "Col15": "0",
	                },
	                "GlobalParameters": { }
	            }
	        }
	
		body = str.encode(json.dumps(data))
	
		#Assign serviceUrl with the Request URI. See How to get a Request URI.
		uri = '{ServiceUri}'
	
		#Assign apiKey with the key from a web service.
		api_key = '{ApiKey}'
		headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
	
		req = urllib2.Request(uri, body, headers) 
		response = urllib2.urlopen(req)
	
		#If you are using Python 3+, replace urllib2 with urllib.request in the above code:
		#req = urllib.request.Request(uri, body, headers) 
		#response = urllib.request.urlopen(req)
	
		result = response.read()
		print(result) 

<!--HONumber=49--> 