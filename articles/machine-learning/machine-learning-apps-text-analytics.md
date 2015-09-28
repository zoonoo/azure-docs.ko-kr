<properties
	pageTitle="기계 학습 앱: 정서 분석 | Microsoft Azure"
	description="텍스트 분석 API는 Azure 기계 학습을 사용하여 빌드한 텍스트 분석 제품군입니다. 이 API를 사용하여 정서 분석 및 핵심 문구 추출과 같은 작업에 대한 구조화되지 않은 텍스트를 분석할 수 있습니다."
	services="machine-learning"
	documentationCenter=""
	authors="LuisCabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/11/2015"
	ms.author="luisca"/>


# 기계 학습 앱: 정서 분석용 텍스트 분석 서비스#
##개요
텍스트 분석 API는 Azure 기계 학습을 사용하여 빌드한 텍스트 분석 [웹 서비스](https://datamarket.azure.com/dataset/amla/text-analytics) 제품군입니다. 이 API를 사용하여 정서 분석 및 핵심 문구 추출과 같은 작업에 대한 구조화되지 않은 텍스트를 분석할 수 있습니다. 학습 데이터 없이 이 API를 사용할 수 있으며, 텍스트 데이터를 가져오기만 하면 됩니다. 지금은 영어만 지원됩니다. 이 API는 내장된 고급 자연어 처리 기술을 사용합니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
 
## 정서 분석##
이 API는 0에서 1 사이의 숫자 점수를 반환합니다. 1에 가까운 점수는 긍정적인 정서를 나타내고, 0에 가까운 점수는 부정적인 정서를 나타냅니다. 정서 점수는 분류 기술을 사용하여 생성됩니다. 분류자의 입력 기능에는 N-그램, 음성 부분 태그에서 생성된 기능 및 단어 포함이 포함됩니다.
 
## 핵심 문구 추출##
이 API는 입력 텍스트의 핵심 요지를 나타내는 문자열 목록을 반환합니다. Microsoft Office의 정교한 자연어 처리 도구 키트에서 제공되는 기술을 사용합니다.

## API 정의##

###GetSentiment###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**예제 요청**

아래 GET 호출에서는 *Hello World*라는 문구에 대한 정서를 요청합니다.

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

헤더:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

[여기](https://datamarket.azure.com/account/keys)에서 계정 키를 가져옵니다.

**예제 응답**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
		"Score":1.0
	}

---

###GetKeyPhrases###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**예제 요청**

아래 GET 호출에서는 *It was a wonderful hotel to stay at, with unique decor and friendly staff*라는 텍스트의 핵심 문구에 대한 생각을 요청합니다.

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

헤더:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey)

[여기](https://datamarket.azure.com/account/keys)에서 계정 키를 가져옵니다.


**예제 응답**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata","KeyPhrases":[
	    "wonderful hotel","unique decor","friendly staff"]
	}
 
---

###GetSentimentBatch###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**예제 요청**

아래 POST 호출에서는 요청 본문에 있는 Hello World, Hello Foo World, Hello My World 문구에 대한 정서를 요청합니다.

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

본문:

	{"Inputs":
	[
	    {"Id":"1","Text":"hello world"},
    	{"Id":"2","Text":"hello foo world"},
    	{"Id":"3","Text":"hello my world"},
	]}


헤더:

	Authorization: Basic <creds>
	Accept: application/json

	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  


[여기](https://datamarket.azure.com/account/keys)에서 계정 키를 가져옵니다.

**예제 응답**

아래 응답에서 텍스트 ID와 연결된 점수 목록을 확인할 수 있습니다.

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata", "SentimentBatch":
		[{"Score":0.9549767,"Id":"1"},
		 {"Score":0.7767222,"Id":"2"},
		 {"Score":0.8988889,"Id":"3"}
		],  
		"Errors":[
		   {"Id": "4", Message:"Record cannot be null/empty"}
		]
	}


---

###GetKeyPhrasesBatch###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**예제 요청**

아래 POST 호출에서는 다음 텍스트의 핵심 문구에 대한 정서 목록을 요청합니다.

*It was a wonderful hotel to stay at, with unique decor and friendly staff*
 
*It was an amazing build conference, with very interesting talks*

*The traffic was terrible, I spent three hours going to the airport*



	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

본문:

	{"Inputs":
	[
		{"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
		{"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
		{"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
	]}

헤더:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey)

[여기](https://datamarket.azure.com/account/keys)에서 계정 키를 가져옵니다.


**예제 응답**

아래 응답에서 텍스트 ID와 연결된 핵심 문구 목록을 확인할 수 있습니다.

	{ "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	 	"KeyPhrasesBatch":
		[
		   {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
		   {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
		   {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
		],
		"Errors":[
		   {"Id": "4", Message:"Record cannot be null/empty"}
		]
	}

---

**일괄 처리와 관련된 참고 사항**

시스템에 입력한 ID가 시스템에서 반환됩니다. 웹 서비스는 ID가 고유한지 검사하지 않습니다. 호출자가 고유성을 확인해야 합니다.
 

<!---HONumber=Sept15_HO3-->