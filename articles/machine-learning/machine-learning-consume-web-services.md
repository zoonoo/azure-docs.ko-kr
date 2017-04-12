---
title: "Machine Learning 웹 서비스 사용 | Microsoft Docs"
description: "Machine Learning 서비스가 배포된 후에는 요청-응답 서비스 또는 배치 실행 서비스로 RESTFul 웹 서비스를 사용할 수 있습니다."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 804f8211-9437-4982-98e9-ca841b7edf56
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 01/05/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: f4f3caed8390ba3a80d6cf1282f4d2751c67ae6b
ms.lasthandoff: 03/29/2017


---
# <a name="how-to-consume-an-azure-machine-learning-web-service-that-has-been-deployed-from-a-machine-learning-experiment"></a>Azure Machine Learning 실험에서 배포된 Machine Learning 웹 서비스를 사용하는 방법
## <a name="introduction"></a>소개
웹 서비스로 배포된 경우 Azure Machine Learning 실험에서는 광범위한 장치 및 플랫폼에서 사용할 수 있는 REST API 및 JSON 형식 메시지를 제공합니다. Azure Machine Learning 포털에서는 웹 서비스를 호출하는 데 사용할 수 있는 R, C# 및 Python 코드를 제공합니다. 

다음 세 가지 기준을 충족하는 모든 장치에서 모든 프로그래밍 언어로 이러한 서비스를 호출할 수 있습니다.

* 네트워크에 연결되어야 합니다.
* HTTPS 요청을 수행할 SSL 기능이 있어야 합니다.
* JSON 구문 분석(직접 또는 지원 라이브러리 사용)

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Azure Machine Learning 웹 서비스는 요청-응답 서비스 및 배치 실행 서비스의 두 가지 방법 중 하나로 사용할 수 있습니다. 각 시나리오에서 기능은 실험이 배포된 후에 사용할 수 있는 RESTFul 웹 서비스를 통해 제공됩니다.

> [!TIP]
> 예측 웹 서비스에 액세스하는 웹앱을 손쉽게 만드는 방법은 [웹앱 템플릿을 사용한 Azure Machine Learning 웹 서비스 사용](machine-learning-consume-web-service-with-web-app-template.md)을 참조하세요.
> 
> 
> Azure Machine Learning 웹 서비스를 만들고 배포하는 방법에 대한 자세한 내용은 [Azure Machine Learning 웹 서비스 배포][publish]를 참조하세요. 기계 학습 실험을 만들고 배포하는 단계별 연습은 [Azure Machine Learning을 사용하여 예측 솔루션 개발][walkthrough]을 참조하세요.

## <a name="request-response-service-rrs"></a>RRS(요청-응답 서비스)
대기 시간이 짧고 확장성 있는 웹 서비스인 RRS(요청-응답 서비스)는 Azure Machine Learning Studio 실험에서 만들어지고 배포된 상태 비저장 모델에 대한 인터페이스를 제공하는 데 사용됩니다. 사용 중인 응용 프로그램에서 실시간 응답을 예상하는 시나리오를 가능하게 합니다.

RRS는 단일 행 또는 여러 행의 입력 매개 변수를 허용하며 단일 행 또는 여러 행을 출력으로 생성할 수 있습니다. 출력 행에는 여러 열이 포함될 수 있습니다.

RRS 예제에서는 응용 프로그램의 신뢰성을 검증합니다. 이 경우 수억 회의 응용 프로그램 설치를 예상할 수 있습니다. 응용 프로그램을 시작하면 관련 입력으로 RRS 서비스를 호출합니다. 그런 다음 서비스로부터 응용 프로그램 실행을 허용하거나 차단하는 유효성 검사 응답을 받습니다.

## <a name="batch-execution-service-bes"></a>BES(일괄 처리 실행 서비스)
BES(일괄 처리 실행 서비스)는 많은 데이터 레코드의 점수 매기기를 일괄적으로 처리하는 비동기 서비스입니다. BES 입력에는 Blob, Azure 테이블, SQL Azure, HDInsight(예: Hive 쿼리 결과) 및 HTTP 원본 등 다양한 원본의 레코드 배치가 포함되어 있습니다. BES의 출력에는 점수 매기기 결과가 포함되어 있습니다. 결과는 Azure Blob 저장소의 파일 출력이며 저장소 끝점의 데이터는 응답으로 반환됩니다.

BES는 개인 또는 IOT(사물 인터넷) 장치에 대한 정기적으로 예약된 점수 매기기와 같이 응답이 즉시 필요하지 않은 경우에 유용합니다.

## <a name="examples"></a>예
여기서는 Azure 웹 서비스 예제를 사용하여 RRS 및 BES의 작동 방식을 모두 보여 줍니다. 이 서비스는 IOT(사물 인터넷) 시나리오에서 사용됩니다. 단순함을 유지하기 위해 장치에서는 하나의 값인 `cog_speed`만 전송하고 단일 응답을 받습니다.

실험이 배포된 후에 RRS 또는 BES 서비스를 호출하는 데 필요한 네 가지 정보는 다음과 같습니다.

* 서비스 **API 키** 또는 **기본 키**
* 서비스 **요청 URI**
* 예상 API **요청 헤더** 및 **본문**
* 예상 API **응답 헤더** 및 **본문**

이 정보를 찾는 방법은 배포된 서비스 유형(새 웹 서비스 또는 기존 웹 서비스)에 따라 다릅니다.

### <a name="information-location-in-the-azure-machine-learning-web-services-portal"></a>Azure Machine Learning 웹 서비스 포털에서의 정보 위치
필요한 정보를 찾으려면

1. [Azure Machine Learning 웹 서비스][webservicesportal] 포털에 로그인합니다.
2. **웹 서비스** 또는 **기존 웹 서비스**를 클릭합니다.
3. 사용하고 있는 웹 서비스를 클릭합니다. 
4. 기존 웹 서비스로 작업하는 경우 사용하고 있는 끝점을 클릭합니다.

페이지에 있는 정보는 다음과 같습니다.

* **사용** 페이지에서 **기본 키**를 사용할 수 있습니다.
* **사용** 페이지에서 **요청 URI**를 사용할 수 있습니다. 
* **Swagger API** 페이지에서 예상 API **요청 헤더**, **응답 헤더** 및 **본문**을 사용할 수 있습니다.

### <a name="information-locations-in-machine-learning-studio-classic-web-service-only"></a>Machine Learning Studio에서의 정보 위치(기존 웹 서비스에만 해당)
두 위치, 즉 Machine Learning Studio 또는 Azure Machine Learning 웹 서비스 포털에서 필요한 정보를 찾을 수 있습니다.

Machine Learning Studio에서 필요한 정보를 찾으려면

1. [Machine Learning Studio][mlstudio]에 로그인합니다.
2. 화면 왼쪽에서 **웹 서비스**를 클릭합니다.
3. 사용하고 있는 웹 서비스를 클릭합니다. 

페이지에 있는 정보는 다음과 같습니다.

* 서비스 **대시보드**에서 **API 키**를 사용할 수 있습니다. 
* API 도움말 페이지에서 **요청 URI**를 사용할 수 있습니다.
* API 도움말 페이지에서 예상 API **요청 헤더**, **응답 헤더** 및 **본문**을 사용할 수 있습니다.

API 도움말 페이지에 액세스하려면 작업에 필요한 **요청/응답** 또는 **배치 실행** 링크를 클릭합니다.

Azure Machine Learning 웹 서비스 포털에서 필요한 정보를 찾으려면

1. [Azure Machine Learning 웹 서비스][webservicesportal] 포털에 로그인합니다.
2. **기존 웹 서비스**를 클릭합니다.
3. 사용하고 있는 웹 서비스를 클릭합니다. 
4. 사용하고 있는 끝점을 클릭합니다.

페이지에 있는 정보는 다음과 같습니다.

* **사용** 페이지에서 **기본 키**를 사용할 수 있습니다.
* **사용** 페이지에서 **요청 URI**를 사용할 수 있습니다. 
* **Swagger API** 페이지에서 예상 API **요청 헤더**, **응답 헤더** 및 **본문**을 사용할 수 있습니다.

아래의 두 예제에서는 필요한 코드에 대해 C# 언어로 설명하고 있습니다.

### <a name="rrs-example"></a>RRS 예제
다음 샘플 요청에서는 샘플 서비스의 API 호출에 대한 페이로드를 API 입력에 보여 줍니다. 기존 웹 서비스의 경우 **API 도움말 페이지** 또는 Machine Learning 웹 서비스 포털의 **Swagger API** 페이지에서 페이로드 샘플을 찾을 수 있습니다. 새 웹 서비스의 경우 Machine Learning 웹 서비스 포털의 **Swagger API** 페이지에서 페이로드 샘플을 찾을 수 있습니다.

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


마찬가지로 다음 샘플에서는 서비스의 API 응답을 보여 줍니다.

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

다음은 C# 구현을 위한 코드 샘플입니다. 기존 웹 서비스의 경우 **API 도움말 페이지** 또는 **사용** 페이지 아래에서 코드 샘플을 찾을 수 있습니다. 새 웹 서비스의 경우 **사용** 페이지 아래에서 코드 샘플을 찾을 수 있습니다.

**C#의 샘플 코드**

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

                    const string apiKey = "abc123"; // Replace this with the API key for the Web service
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

**Java의 샘플 코드**

다음 샘플 코드에서는 Java에서 REST API 요청을 생성하는 방법을 보여 줍니다. 여기서는 apikey 및 apiurl 변수에 필요한 API 세부 정보가 있고, jsonBody 변수에는 REST API에서 예상하는 대로 올바른 JSON 개체가 있다고 간주합니다. 전체 코드는 Github([https://github.com/nk773/AzureML_RRSApp](https://github.com/nk773/AzureML_RRSApp))에서 다운로드할 수 있습니다. 이 Java 샘플을 사용하려면 [apache http 클라이언트 라이브러리](https://hc.apache.org/downloads.cgi)가 필요합니다.

    /**
     * Download full code from github - [https://github.com/nk773/AzureML_RRSApp](https://github.com/nk773/AzureML_RRSApp)
      */
        /**
           * Call REST API for retrieving prediction from Azure ML 
           * @return response from the REST API
           */    
        public static String rrsHttpPost() {

            HttpPost post;
            HttpClient client;
            StringEntity entity;

            try {
                    // create HttpPost and HttpClient object
                    post = new HttpPost(apiurl);
                    client = HttpClientBuilder.create().build();

                    // setup output message by copying JSON body into 
                    // apache StringEntity object along with content type
                    entity = new StringEntity(jsonBody, HTTP.UTF_8);
                    entity.setContentEncoding(HTTP.UTF_8);
                    entity.setContentType("text/json");

                    // add HTTP headers
                    post.setHeader("Accept", "text/json");
                    post.setHeader("Accept-Charset", "UTF-8");

                    // set Authorization header based on the API key
                    post.setHeader("Authorization", ("Bearer "+apikey));
                    post.setEntity(entity);

                    // Call REST API and retrieve response content
                    HttpResponse authResponse = client.execute(post);

                    return EntityUtils.toString(authResponse.getEntity());

            }
            catch (Exception e) {

                    return e.toString();
            }

        }




### <a name="bes-example"></a>BES 예제
RRS 서비스와 달리 BES는 비동기 서비스입니다. 즉, BES API는 실행할 작업을 큐에 넣기만 하고 호출자는 작업이 완료되었는지 확인하기 위해 작업 상태를 폴링합니다. 다음은 일괄 처리 작업에 대해 현재 지원되는 작업입니다.

1. 일괄 처리 작업 만들기(제출)
2. 이 일괄 처리 작업 시작
3. 일괄 처리 작업의 상태 또는 결과 가져오기
4. 실행 중인 일괄 처리 작업 취소

**1. 일괄 처리 실행 작업 만들기**

Azure Machine Learning 서비스의 배치 작업을 만들 때 이 배치 실행을 정의하는 매개 변수를 여러 개 지정할 수 있습니다.

* **Input**: 배치 작업의 입력이 저장되는 Blob 참조를 나타냅니다.
* **GlobalParameters**: 실험에 정의할 수 있는 전역 매개 변수 집합을 나타냅니다. Azure 기계 학습 실험에는 서비스의 실행을 사용자 지정하는 필수 및 선택적 매개 변수가 모두 있을 수 있으며, 호출자는 해당되는 경우 필수 매개 변수를 모두 제공해야 합니다. 이러한 매개 변수는 키-값 쌍의 컬렉션으로 지정됩니다.
* **Outputs**: 서비스에서 출력을 하나 이상 정의한 경우 호출자에서 그 중 하나를 Azure Blob 위치로 리디렉션할 수 있습니다. 이 매개 변수를 설정하면 서비스의 출력을 예측 가능한 이름으로 기본 설정 위치에 저장할 수 있습니다. 그렇지 않으면 출력 Blob 이름이 임의로 생성됩니다. 
  
    서비스에서는 해당 형식에 따라 출력 내용을 예상하여 다음과 같이 지원되는 형식으로 저장해야 합니다.
  
  * 데이터 집합 출력: **.csv,.tsv,.arff**
  * 학습한 모델 출력: **.ilearner**
    
    출력 위치를 출력 이름 컬렉션 또는 Blob 참조 쌍으로 재정의하도록 지정합니다. *출력 이름*은 특정 출력 노드의 사용자 정의 이름이며, *Blob 참조*는 출력을 리디렉션할 Azure blob 위치에 대한 참조입니다. *출력 이름*은 서비스의 API 도움말 페이지에서 보여 줍니다.

작업 만들기 매개 변수는 모두 서비스의 속성에 따라 선택할 수 있습니다. 예를 들어 정의된 입력 노드가 없는 서비스에서는 *Input* 매개 변수를 제출할 필요가 없습니다. 마찬가지로 출력 위치 재정의 기능은 선택적입니다. 그렇지 않으면 Azure Machine Learning 작업 영역에 설정된 기본 저장소 계정에 출력이 저장됩니다. 다음은 입력 정보만 전달되는 서비스에 대한 샘플 요청 페이로드입니다.

**샘플 요청**

    {
      "Input": {
        "ConnectionString":     
        "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=mystorageacctKey",
        "RelativeLocation": "/mycontainer/mydatablob.csv",
        "BaseLocation": null,
        "SasBlobToken": null
      },
      "Outputs": null,
      "GlobalParameters": null
    }

배치 작업 만들기 API에 대한 응답은 작업에 연결된 고유한 작업 ID입니다. 이 ID는 다른 작업의 시스템에서 이 작업을 참조할 수 있는 유일한 수단을 제공하므로 중요합니다.  

**샘플 응답**

    "539d0bc2fde945b6ac986b851d0000f0" // The JOB_ID

**2. 일괄 처리 실행 작업 시작**

배치 작업을 만들면 해당 작업은 시스템에 등록되고 *Not started* 상태로 표시됩니다. 실제로 작업 실행을 예약하려면 서비스 끝점의 API 도움말 페이지 또는 웹 서비스의 Swagger API 페이지에 설명된 **start** API를 호출하고 작업을 만들 때 가져온 작업 ID를 제공해야 합니다.

**3. 일괄 처리 실행 작업의 상태 가져오기**

언제든지 작업 ID를 *GetJobStatus* API로 전달하여 비동기 배치 작업 상태를 폴링할 수 있습니다. API 응답에는 현재 작업 상태의 표시기 및 배치 작업이 성공적으로 완료되었는지를 나타내는 결과가 포함됩니다. 오류가 발생하는 경우 다음과 같이 실제 원인에 대한 자세한 정보가 *Details* 속성에 반환됩니다.

**응답 페이로드**

    {
        "StatusCode": STATUS_CODE,
        "Results": RESULTS,
        "Details": DETAILS
    }

*StatusCode* 는 다음 중 하나일 수 있습니다.

* Not started
* 실행 중
* Failed
* Canceled
* Finished

작업이 성공적으로 완료된 경우에 *Results* 속성이 채워집니다(그렇지 않은 경우 **null**임). 작업이 완료되고 서비스에 출력 노드가 하나 이상 정의된 경우 결과는 *[output name, blob reference]* 쌍의 컬렉션으로 반환됩니다. 여기서 Blob 참조는 결과를 포함하고 있는 Blob에 대한 SAS 읽기 전용 참조입니다.

**샘플 응답**

    {
        "Status Code": "Finished",
        "Results":
        {
            "dataOutput":
            {              
                "ConnectionString": null,
                "RelativeLocation": "outputs/dataOutput.csv",
                "BaseLocation": "https://mystorageaccount.blob.core.windows.net/",
                "SasBlobToken": "?sv=2013-08-15&sr=b&sig=ABCD&st=2015-04-04T05%3A39%3A55Z&se=2015-04-05T05%3A44%3A55Z&sp=r"              
            },
            "trainedModelOutput":
            {              
                "ConnectionString": null,
                "RelativeLocation": "models/trainedModel.ilearner",
                "BaseLocation": "https://mystorageaccount.blob.core.windows.net/",
                "SasBlobToken": "?sv=2013-08-15&sr=b&sig=EFGH%3D&st=2015-04-04T05%3A39%3A55Z&se=2015-04-05T05%3A44%3A55Z&sp=r"              
            },           
        },
        "Details": null
    }

**4. 일괄 처리 실행 작업 취소**

언제든지 지정된 *CancelJob* API를 호출하고 작업 ID를 제출하여 실행 중인 배치 작업을 취소할 수 있습니다. 작업을 완료하는 데 시간이 너무 오래 걸리는 것과 같이 여러 가지 이유로 작업을 삭제할 수 있습니다.

#### <a name="using-the-bes-sdk"></a>BES SDK 사용
[BES SDK Nuget package](http://www.nuget.org/packages/Microsoft.Azure.MachineLearning/)는 BES 호출을 간소화하여 배치 모드에서 점수를 매기는 함수를 제공합니다. Nuget 패키지를 설치하려면 Visual Studio의 **도구** 메뉴에서 **Nuget 패키지 관리자**를 선택하고 **패키지 관리자 콘솔**을 클릭합니다.

웹 서비스로 배포되는 Azure Machine Learning 실험에 웹 서비스 입력 모듈을 포함할 수 있습니다. 이는 웹 서비스에 대한 입력을 Blob 위치에 대한 참조 형식으로 웹 서비스 호출을 통해 제공해야 한다는 것을 의미합니다. 또한 웹 서비스 입력 모듈을 사용하지 않고 대신 **데이터 가져오기** 모듈을 사용하는 옵션도 있습니다. 이 경우 **데이터 가져오기** 모듈에서는 런타임에 쿼리를 사용하여 SQL DB와 같은 데이터 원본에서 데이터를 읽어들입니다. 웹 서비스 매개 변수를 사용하여 다른 서버 또는 테이블 등을 동적으로 가리킬 수 있습니다. SDK는 이 두 패턴을 모두 지원합니다.

다음 코드 샘플에서는 BES SDK를 사용하여 Azure Machine Learning 서비스에 대해 배치 작업을 제출하고 모니터링하는 방법을 보여 줍니다. 주석에는 설정 및 호출에 대한 세부 정보가 포함됩니다.

#### <a name="sample-code"></a>**샘플 코드**
    // This code requires the Nuget package Microsoft.Azure.MachineLearning to be installed.
    // Instructions for doing this in Visual Studio:
    // Tools -> Nuget Package Manager -> Package Manager Console
    // Install-Package Microsoft.Azure.MachineLearning

      using System;
      using System.Collections.Generic;
      using System.Threading.Tasks;

      using Microsoft.Azure.MachineLearning;
      using Microsoft.Azure.MachineLearning.Contracts;
      using Microsoft.Azure.MachineLearning.Exceptions;

    namespace CallBatchExecutionService
    {
        class Program
        {
            static void Main(string[] args)
            {                
                InvokeBatchExecutionService().Wait();
            }

            static async Task InvokeBatchExecutionService()
            {
                // First collect and fill in the URI and access key for your Web service endpoint.
                // These are available on your service's API help page.
                var endpointUri = "https://ussouthcentral.services.azureml.net/workspaces/YOUR_WORKSPACE_ID/services/YOUR_SERVICE_ENDPOINT_ID/";
                string accessKey = "YOUR_SERVICE_ENDPOINT_ACCESS_KEY";

                // Create an Azure Machine Learning runtime client for this endpoint
                var runtimeClient = new RuntimeClient(endpointUri, accessKey);

                // Define the request information for your batch job. This information can contain:
                // -- A reference to the AzureBlob containing the input for your job run
                // -- A set of values for global parameters defined as part of your experiment and service
                // -- A set of output blob locations that allow you to redirect the job's results

                // NOTE: This sample is applicable, as is, for a service with explicit input port and
                // potential global parameters. Also, we choose to also demo how you could override the
                // location of one of the output blobs that could be generated by your service. You might
                // need to tweak these features to adjust the sample to your service.
                //
                // All of these properties of a BatchJobRequest shown below can be optional, depending on
                // your service, so it is not required to specify all with any request.  If you do not want to
                // use any of the parameters, a null value should be passed in its place.

                // Define the reference to the blob containing your input data. You can refer to this blob by its
                    // connection string / container / blob name values; alternatively, we also support references
                    // based on a blob SAS URI

                    BlobReference inputBlob = BlobReference.CreateFromConnectionStringData(connectionString:                                         "DefaultEndpointsProtocol=https;AccountName=YOUR_ACCOUNT_NAME;AccountKey=YOUR_ACCOUNT_KEY",
                        containerName: "YOUR_CONTAINER_NAME",
                        blobName: "YOUR_INPUT_BLOB_NAME");

                    // If desired, one can override the location where the job outputs are to be stored, by passing in
                    // the storage account details and name of the blob where we want the output to be redirected to.

                    var outputLocations = new Dictionary<string, BlobReference>
                        {
                          {
                           "YOUR_OUTPUT_NODE_NAME",
                           BlobReference.CreateFromConnectionStringData(                                     connectionString: "DefaultEndpointsProtocol=https;AccountName=YOUR_ACCOUNT_NAME;AccountKey=YOUR_ACCOUNT_KEY",
                                containerName: "YOUR_CONTAINER_NAME",
                                blobName: "YOUR_DESIRED_OUTPUT_BLOB_NAME")
                           }
                        };

                // If applicable, you can also set the global parameters for your service
                var globalParameters = new Dictionary<string, string>
                {
                    { "YOUR_GLOBAL_PARAMETER", "PARAMETER_VALUE" }
                };

                var jobRequest = new BatchJobRequest
                {
                    Input = inputBlob,
                    GlobalParameters = globalParameters,
                    Outputs = outputLocations
                };

                try
                {
                    // Register the batch job with the system, which will grant you access to a job object
                    BatchJob job = await runtimeClient.RegisterBatchJobAsync(jobRequest);

                    // Start the job to allow it to be scheduled in the running queue
                    await job.StartAsync();

                    // Wait for the job's completion and handle the output
                    BatchJobStatus jobStatus = await job.WaitForCompletionAsync();
                    if (jobStatus.JobState == JobState.Finished)
                    {
                        // Process job outputs
                        Console.WriteLine(@"Job {0} has completed successfully and returned {1} outputs", job.Id, jobStatus.Results.Count);
                        foreach (var output in jobStatus.Results)
                        {
                            Console.WriteLine(@"\t{0}: {1}", output.Key, output.Value.AbsoluteUri);
                        }
                    }
                    else if (jobStatus.JobState == JobState.Failed)
                    {
                        // Handle job failure
                        Console.WriteLine(@"Job {0} has failed with this error: {1}", job.Id, jobStatus.Details);
                    }
                }
                catch (ArgumentException aex)
                {
                    Console.WriteLine("Argument {0} is invalid: {1}", aex.ParamName, aex.Message);
                }
                catch (RuntimeException runtimeError)
                {
                    Console.WriteLine("Runtime error occurred: {0} - {1}", runtimeError.ErrorCode, runtimeError.Message);
                    Console.WriteLine("Error details:");
                    foreach (var errorDetails in runtimeError.Details)
                    {
                        Console.WriteLine("\t{0} - {1}", errorDetails.Code, errorDetails.Message);
                    }
                }
                catch (Exception ex)
                {
                    Console.WriteLine("Unexpected error occurred: {0} - {1}", ex.GetType().Name, ex.Message);
                }
            }
        }
    }

#### <a name="sample-code-in-java-for-bes"></a>BES용 Java의 샘플 코드
다음 샘플에서 보여 주듯이 배치 실행 서비스 REST API는 입력 샘플 csv 및 출력 샘플 csv에 대한 참조로 구성된 JSON을 사용하고 Azure ML에서 작업을 만들어 배치 예측을 수행합니다. [GitHub](https://github.com/nk773/AzureML_BESApp/tree/master/src/azureml_besapp)에서 전체 코드를 볼 수 있습니다. 이 Java 샘플을 사용하려면 [apache http 클라이언트 라이브러리](https://hc.apache.org/downloads.cgi)가 필요합니다. 

    { "GlobalParameters": {}, 
        "Inputs": { "input1": { "ConnectionString":     "DefaultEndpointsProtocol=https;
            AccountName=myAcctName; AccountKey=Q8kkieg==", 
            "RelativeLocation": "myContainer/sampleinput.csv" } }, 
        "Outputs": { "output1": { "ConnectionString":     "DefaultEndpointsProtocol=https;
            AccountName=myAcctName; AccountKey=kjC12xQ8kkieg==", 
            "RelativeLocation": "myContainer/sampleoutput.csv" } } 
    } 


##### <a name="create-a-bes-job"></a>BES 작업 만들기
        /**
         * Call REST API to create a job to Azure ML 
         * for batch predictions
         * @return response from the REST API
         */    
        public static String besCreateJob() {

            HttpPost post;
            HttpClient client;
            StringEntity entity;

            try {
                // create HttpPost and HttpClient object
                post = new HttpPost(apiurl);
                client = HttpClientBuilder.create().build();

                // setup output message by copying JSON body into 
                // apache StringEntity object along with content type
                entity = new StringEntity(jsonBody, HTTP.UTF_8);
                entity.setContentEncoding(HTTP.UTF_8);
                entity.setContentType("text/json");

                // add HTTP headers
                post.setHeader("Accept", "text/json");
                post.setHeader("Accept-Charset", "UTF-8");

                // set Authorization header based on the API key
                // note a space after the word "Bearer " - don't miss that
                post.setHeader("Authorization", ("Bearer "+apikey));
                post.setEntity(entity);

                // Call REST API and retrieve response content
                HttpResponse authResponse = client.execute(post);

                jobId = EntityUtils.toString(authResponse.getEntity()).replaceAll("\"", "");


                return jobId;

            }
            catch (Exception e) {

                return e.toString();
            }

        }

##### <a name="start-a-previously-created-bes-job"></a>이전에 만든 BES 작업 시작
        /**
         * Call REST API for starting prediction job previously submitted 
         * 
         * @param job job to be started 
         * @return response from the REST API
         */    
        public static String besStartJob(String job){
            HttpPost post;
            HttpClient client;
            StringEntity entity;

            try {
                // create HttpPost and HttpClient object
                post = new HttpPost(startJobUrl+"/"+job+"/start?api-version=2.0");
                client = HttpClientBuilder.create().build();

                // add HTTP headers
                post.setHeader("Accept", "text/json");
                post.setHeader("Accept-Charset", "UTF-8");

                // set Authorization header based on the API key
                post.setHeader("Authorization", ("Bearer "+apikey));

                // Call REST API and retrieve response content
                HttpResponse authResponse = client.execute(post);

                if (authResponse.getEntity()==null)
                {
                    return authResponse.getStatusLine().toString();
                }

                return EntityUtils.toString(authResponse.getEntity());

            }
            catch (Exception e) {

                return e.toString();
            }
        }
##### <a name="cancel-a-previously-created-bes-job"></a>이전에 만든 BES 작업 취소
        /**
         * Call REST API for canceling the batch job 
         * 
         * @param job job to be started 
         * @return response from the REST API
         */    
        public static String besCancelJob(String job) {
            HttpDelete post;
            HttpClient client;
            StringEntity entity;

            try {
                // create HttpPost and HttpClient object
                post = new HttpDelete(startJobUrl+job);
                client = HttpClientBuilder.create().build();

                // add HTTP headers
                post.setHeader("Accept", "text/json");
                post.setHeader("Accept-Charset", "UTF-8");

                // set Authorization header based on the API key
                post.setHeader("Authorization", ("Bearer "+apikey));

                // Call REST API and retrieve response content
                HttpResponse authResponse = client.execute(post);

                if (authResponse.getEntity()==null)
                {
                    return authResponse.getStatusLine().toString();
                }
                return EntityUtils.toString(authResponse.getEntity());

            }
            catch (Exception e) {

                return e.toString();
            }
        }

### <a name="other-programming-environments"></a>다른 프로그래밍 환경
[swagger.io](http://swagger.io/) 사이트에 제공된 지침에 따라 여러 가지 다른 언어로 코드를 생성할 수도 있습니다. 기존 웹 서비스의 경우 다음과 같이 수행하여 swagger 문서를 가져올 수 있습니다.

* API 도움말 페이지에서 직접 
* Machine Learning 웹 서비스 포털의 Swagger API 페이지에 있는 끝점용 API 문서 가져오기를 호출 

[swagger.io](http://swagger.io/swagger-codegen/)로 이동하여 지침에 따라 swagger 코드, java 및 apache mvn을 다운로드합니다. 

여기서는 다른 프로그래밍 환경의 swagger를 설정하는 지침을 요약하고 있습니다.

* Java 7 이상이 설치되어 있는지 확인
* apache mvn 설치(ubuntu에서는 *apt-get install mvn*사용 가능)
* swagger에 대한 Github로 이동하여 swagger 프로젝트를 zip 파일로 다운로드
* swagger 압축 풀기
* swagger의 원본 디렉터리에서 *mvn package* 를 실행하여 swagger 도구 빌드

이제 swagger 도구 중 하나를 사용할 수 있습니다. 다음은 Java 클라이언트 코드를 생성하는 지침입니다. 

* Azure ML API 도움말 페이지(예: [여기](https://studio.azureml.net/apihelp/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/jobs))로 이동
* Azure ML REST API에 대한 swagger.json의 URL 찾기(API 도움말 페이지 맨 위에서 마지막 두 번째 글머리 기호)
* swagger 문서 링크(예: [여기](https://management.azureml.net/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/apidocument)) 클릭
* [swagger의 추가 정보 파일](https://github.com/swagger-api/swagger-codegen/blob/master/README.md) 에 표시된 대로 다음 명령을 사용하여 클라이언트 코드 생성

**클라이언트 코드를 생성하는 샘플 명령줄**

    java -jar swagger-codegen-cli.jar generate\
     -i https://ussouthcentral.services.azureml.net:443/workspaces/\
    fb62b56f29fc4ba4b8a8f900c9b89584/services/26a3afce1767461ab6e73d5a206fbd62/swagger.json\
     -l java -o /home/username/sample

* 아래에서 보여 주는 host 및 basePath 필드의 값과 swagger [API 도움말 페이지](https://management.azureml.net/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/apidocument)의 샘플에 있는 "/swagger.json" 값을 결합하여 위의 명령줄에서 사용된 swagger URL을 생성합니다.

**샘플 API 도움말 페이지**

    {
      "swagger": "2.0",
      "info": {
        "version": "2.0",
        "title": "Sample 5: Binary Classification with Web service: Adult Dataset [Predictive Exp.]",
        "description": "No description provided for this Web service.",
        "x-endpoint-name": "default"
      },
      "host": "ussouthcentral.services.azureml.net:443",
      "basePath": "/workspaces/afbd553b9bac4c95be3d040998943a4f/services/26a3afce1767461ab6e73d5a206fbd62",
      "schemes": [
        "https"
      ],
      "consumes": [
        "application/json"
      ],
      "produces": [
        "application/json"
      ],
      "paths": {
        "/swagger.json": {
          "get": {
            "summary": "Get swagger API document for the Web service",
            "operationId": "getSwaggerDocument",

<!-- Relative Links -->

[publish]: machine-learning-publish-a-machine-learning-web-service.md
[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- External Links -->
[webservicesportal]: https://services.azureml.net/
[mlstudio]: https://studio.azureml.net

