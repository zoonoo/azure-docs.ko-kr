<properties
    pageTitle="논리 앱에 Microsoft Translator 추가 | Microsoft Azure"
    description="REST API 매개 변수를 사용하는 Microsoft Translator 커넥터 개요"
    services=""
    suite=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="08/18/2016"
   ms.author="mandia"/>

# Microsoft Translator 커넥터 시작
Microsoft Translator에 연결하여 텍스트 번역, 언어 검색 등의 작업을 수행합니다. Microsoft Translator를 사용하여 다음을 수행할 수 있습니다.

- Microsoft Translator에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다.
- 텍스트 번역, 언어 검색 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 Dropbox에서 새 파일을 만든 경우 Microsoft Translator를 사용하여 파일의 텍스트를 다른 언어로 번역할 수 있습니다.

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업
Microsoft Translator에는 다음 작업이 포함됩니다. 트리거는 없습니다.

트리거 | 작업
--- | ---
없음 | <ul><li>언어 검색</li><li>텍스트 음성 변환</li><li>텍스트 번역</li><li>언어 가져오기</li><li>음성 언어 가져오기</li></ul>

모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다.


## Microsoft Translator에 대한 연결 만들기

>[AZURE.INCLUDE [Microsoft Translator에 대한 연결을 만드는 단계](../../includes/connectors-create-api-microsofttranslator.md)]


## Swagger REST API 참조
적용 버전: 1.0

### 언어 검색    
지정된 텍스트의 소스 언어를 검색합니다. ```GET: /Detect```

| Name| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|쿼리|string|yes|쿼리|없음 |해당 언어를 식별할 텍스트|

#### 응답
|Name|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 텍스트 음성 변환    
지정된 텍스트를 wave 형식의 오디오 스트림으로 음성 변환합니다. ```GET: /Speak```

| Name| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|쿼리|string|yes|쿼리|없음 |변환할 텍스트|
|language|string|yes|쿼리|없음 |음성을 생성할 언어 코드(예: ‘ko-kr’)|

#### 응답
|Name|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 텍스트 번역    
Microsoft Translator를 사용하여 텍스트를 지정된 언어로 번역합니다. ```GET: /Translate```

| Name| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|쿼리|string|yes|쿼리|없음 |번역할 텍스트|
|languageTo|string|yes|쿼리| 없음|대상 언어 코드(예: 'fr')|
|languageFrom|string|no|쿼리|없음 |원본 언어입니다. 제공되지 않은 경우 Microsoft Translator는 자동 검색을 시도합니다(예: en).|
|카테고리|string|no|쿼리|일반 |번역 범주(기본값: '일반')|

#### 응답
|Name|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 언어 가져오기    
Microsoft Translator에서 지원되는 모든 언어를 검색합니다. ```GET: /TranslatableLanguages```

이 호출에 대한 매개 변수는 없습니다.

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 음성 언어 가져오기    
음성 합성에 사용할 수 있는 언어를 검색합니다. ```GET: /SpeakLanguages```

이 호출에 대한 매개 변수는 없습니다.

#### 응답
|Name|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|

## 개체 정의

#### 언어: Microsoft Translator 번역 가능 언어에 대한 언어 모델

|속성 이름 | 데이터 형식 | 필수|
|---|---|---|
|코드|string|no|
|Name|string|no|


## 다음 단계

[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

[API 목록](apis-list.md)으로 돌아갑니다.


<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/connectors-create-api-microsofttranslator/register-your-application.png

<!---HONumber=AcomDC_0824_2016-->