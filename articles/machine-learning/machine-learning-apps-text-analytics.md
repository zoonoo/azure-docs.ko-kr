<properties
	pageTitle="기계 학습 API: 텍스트 분석 | Microsoft Azure"
	description="Azure 기계 학습에서 제공된 텍스트 분석 API입니다. 이 API를 사용하여 정서 분석, 핵심 구문 추출 및 언어 검색을 위해 구조화되지 않은 텍스트를 분석할 수 있습니다."
	services="machine-learning"
	documentationCenter=""
	authors="onewth"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/17/2015"
	ms.author="onewth"/>


# 기계 학습 API: 정서, 핵심 구문 추출 및 언어 검색을 위한 텍스트 분석

## 개요

텍스트 분석 API는 Azure 기계 학습을 사용하여 빌드한 텍스트 분석 [웹 서비스](https://datamarket.azure.com/dataset/amla/text-analytics) 제품군입니다. 이 API를 사용하여 정서 분석, 핵심 문구 추출 및 언어 검색과 같은 작업에 대한 구조화되지 않은 텍스트를 분석할 수 있습니다. 학습 데이터 없이 이 API를 사용할 수 있으며, 텍스트 데이터를 가져오기만 하면 됩니다. 이 API는 고급 자연어 처리 기술을 사용하여 클래스 예측을 가장 잘 전달합니다.

[데모 사이트](https://text-analytics-demo.azurewebsites.net/)에서 작업에 대한 텍스트 분석을 볼 수 있으며 이 사이트에는 C# 및 Python에서 텍스트 분석을 구현하는 방법에 대한 [샘플](https://text-analytics-demo.azurewebsites.net/Home/SampleCode)도 있습니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

---

## 정서 분석

이 API는 0에서 1 사이의 숫자 점수를 반환합니다. 1에 가까운 점수는 긍정적인 정서를 나타내고, 0에 가까운 점수는 부정적인 정서를 나타냅니다. 정서 점수는 분류 기술을 사용하여 생성됩니다. 분류자의 입력 기능에는 N-그램, 음성 부분 태그에서 생성된 기능 및 단어 포함이 포함됩니다. 현재 지원되는 언어는 영어뿐입니다.
 
## 핵심 문구 추출

이 API는 입력 텍스트의 핵심 요지를 나타내는 문자열 목록을 반환합니다. Microsoft Office의 정교한 자연어 처리 도구 키트에서 제공되는 기술을 사용합니다. 현재 지원되는 언어는 영어뿐입니다.

## 언어 검색

API는 검색된 언어 및 0에서 1 사이의 숫자 점수를 반환합니다. 점수가 1에 가까울수록 식별된 언어가 true라는 100% 확실성을 나타냅니다. 총 120개의 언어가 지원됩니다.

---

## API 정의

### 헤더

다음과 같이 요청에 올바른 헤더가 포함되었는지 확인합니다.

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

[Azure 데이터 마켓](https://datamarket.azure.com/account/keys)에서 계정의 계정 키를 찾을 수 있습니다.

---

## 단일 응답 API

### GetSentiment

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**예제 요청**

아래 호출에서는 "Hello World"라는 문구에 대한 정서 분석을 요청합니다.

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

그러면 다음과 같이 응답을 반환합니다.

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
		"Score":1.0
	}

---

### GetKeyPhrases

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**예제 요청**

아래 호출에서는 "It was a wonderful hotel to stay at, with unique decor and friendly staff" 텍스트에 있는 핵심 문구를 요청합니다.

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

그러면 다음과 같이 응답을 반환합니다.

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	  "KeyPhrases":[
	    "wonderful hotel",
	    "unique decor",
	    "friendly staff"
	  ]
	}
 
---

### GetLanguage

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguage

**예제 요청**

아래 호출에서는 *Hello World* 텍스트의 핵심 문구에 대한 정서를 요청합니다.

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguages?
	Text=Hello+World

그러면 다음과 같이 응답을 반환합니다.

	{
	  "UnknownLanguage": false,
	  "DetectedLanguages": [{
	    "Name": "English",
	    "Iso6391Name": "en",
	    "Score": 1.0
	  }]
	}

**선택적 매개 변수**

`NumberOfLanguagesToDetect`는 선택적 매개 변수입니다. 기본값은 1입니다.

---

## 배치 API

텍스트 분석 서비스를 통해 배치 모드에서 정서 및 키 구문 추출 작업을 수행할 수 있습니다. 점수가 매겨진 각 레코드는 하나의 트랜잭션으로 계산됩니다. 예를 들어 단일 호출에서 1000개의 레코드에 대한 정서를 요청하면 1000개의 트랜잭션이 공제됩니다.

시스템에 입력한 ID가 시스템에서 반환한 ID입니다. 웹 서비스는 이러한 ID가 고유한지 검사하지 않습니다. 호출자가 고유성을 확인해야 합니다.


### GetSentimentBatch

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**예제 요청**

아래 POST 호출에서는 요청 본문에 있는 "Hello World", "Hello Foo World", "Hello My World" 문구에 대한 정서를 요청합니다.

	POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

본문 요청:

	{"Inputs":
	[
	    {"Id":"1","Text":"hello world"},
    	    {"Id":"2","Text":"hello foo world"},
    	    {"Id":"3","Text":"hello my world"},
	]}

아래 응답에서 텍스트 ID와 연결된 점수 목록을 확인할 수 있습니다.

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata", 
	  "SentimentBatch":
	  [
		{"Score":0.9549767,"Id":"1"},
		{"Score":0.7767222,"Id":"2"},
		{"Score":0.8988889,"Id":"3"}
	  ],  
	  "Errors":[]
	}


---

### GetKeyPhrasesBatch

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**예제 요청**

이 예제에서는 다음 텍스트의 핵심 문구에 대한 정서 목록을 요청합니다.

* "It was a wonderful hotel to stay at, with unique decor and friendly staff"
* "It was an amazing build conference, with very interesting talks"
* "The traffic was terrible, I spent three hours going to the airport"

다음 요청은 끝점에 대한 POST 호출로 생성되었습니다.

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

본문 요청:

	{"Inputs":
	[
		{"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
		{"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
		{"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
	]}

아래 응답에서 텍스트 ID와 연결된 핵심 문구 목록을 확인할 수 있습니다.

	{ "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	 	"KeyPhrasesBatch":
		[
		   {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
		   {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
		   {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
		],
		"Errors":[]
	}

---

### GetLanguageBatch

아래의 POST 호출에서는 두 텍스트 입력에 대한 언어 검색을 요청하고 있습니다.

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguageBatch

본문 요청:

    {
      "Inputs": [
        {"Text": "hello world", "Id": "1"},
        {"Text": "c'est la vie", "Id": "2"}
      ]
    }

그러면 다음 응답이 반환되는데 첫 번째 입력에서 영어가 검색되고 두 번째 입력에서 프랑스어가 검색됩니다.

    {
       "LanguageBatch": [{
         "Id": "1",
         "DetectedLanguages": [{
            "Name": "English",
            "Iso6391Name": "en",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       },
       {
         "Id": "2",
         "DetectedLanguages": [{
            "Name": "French",
            "Iso6391Name": "fr",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       }],
       "Errors": []
    }

<!---HONumber=AcomDC_1125_2015-->