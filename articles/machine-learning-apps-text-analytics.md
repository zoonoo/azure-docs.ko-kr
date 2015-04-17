<properties
	pageTitle="기계 학습 앱: 정서 분석용 텍스트 분석 서비스 | Azure "
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
	ms.date="01/27/2015"
	ms.author="luisca"/>


# 기계 학습 텍스트 분석 서비스#
##개요
텍스트 분석 API는 Azure 기계 학습을 사용하여 빌드한 텍스트 분석 [웹 서비스]( https://datamarket.azure.com/dataset/amla/text-analytics) 제품군입니다. 이 API를 사용하여 정서 분석 및 핵심 문구 추출과 같은 작업에 대한 구조화되지 않은 텍스트를 분석할 수 있습니다. 학습 데이터 없이 이 API를 사용할 수 있으며, 텍스트 데이터를 가져오기만 하면 됩니다. 지금은 영어만 지원됩니다. 이 API는 내장된 고급 자연어 처리 기술을 사용합니다.

[AZURE.INCLUDE [machine-learning-free-trial](../includes/machine-learning-free-trial.md)] 
 
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
               
	Where <creds> = ConvertToBase64("AccountKey:" + yourActualAccountKey);  

[여기]( https://datamarket.azure.com/account/keys)에서 계정 키를 가져옵니다. 

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

아래 GET 호출에서는 *독특한 장식과 친절한 직원들이 맞아주던 머무르기에 만족스러웠던 호텔이었습니다.* 라는 텍스트의 핵심 문구에 대한 정서를 요청합니다.

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

헤더:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64("AccountKey:" + yourActualAccountKey)

[여기]( https://datamarket.azure.com/account/keys)에서 계정 키를 가져옵니다. 


**예제 응답**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata","KeyPhrases":[
	    "wonderful hotel","unique decor","friendly staff"]
	}
 

<!--HONumber=49-->