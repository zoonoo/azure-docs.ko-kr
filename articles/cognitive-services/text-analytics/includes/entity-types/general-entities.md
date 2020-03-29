---
title: 일반 명명된 엔터티
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: ba516a548fc8385ca86526a7f2dd082b27e53208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77211400"
---
## <a name="general-entity-types"></a>일반 엔터티 유형:

### <a name="person"></a>Person

텍스트에서 사람 이름을 인식합니다.

언어:
* 공개 미리 `Arabic` `Czech`보기 `Chinese-Simplified` `Danish`: `Dutch` `English`" `Finnish` `French`, `German` `Hungarian`" `Italian` `Japanese`, `Korean` `Norwegian (Bokmål)`" `Polish` `Portuguese (Portugal)`, `Portuguese (Brazil)` `Russian`, `Spanish` `Swedish` " , , , , , , ,`Turkish`

| 하위 유형 이름 | 설명                                                      | 모델 버전으로 시작 가능 |
|--------------|------------------------------------------------------------------|---------------------------------------|
| 해당 없음          | 인식된 사람 이름(예: `Bill Gates``Marie Curie` | `2019-10-01`                          | 

### <a name="persontype"></a>인물 유형
사람이 보유한 작업 유형 또는 역할입니다.

언어:
* 공개 미리 보기:`English`

| 하위 유형 이름 | 설명                                                                                | 모델 버전으로 시작 가능 |
|--------------|--------------------------------------------------------------------------------------------|----------------------------------------|
| 해당 없음          | `civil engineer`작업 유형(예: `librarian`" `salesperson` `chef``nursing aide` | `2020-02-01`                           |

### <a name="location"></a>위치

자연및인간이만든랜드마크,구조물,지리적특징및지정학적적실체.

언어:

* 공개 미리 `Arabic` `Czech`보기 `Chinese-Simplified` `Danish`: `Dutch` `English`" `Finnish` `French`, `German` `Hungarian`" `Italian` `Japanese`, `Korean` `Norwegian (Bokmål)`" `Polish` `Portuguese (Portugal)`, `Portuguese (Brazil)` `Russian`, `Spanish` `Swedish` " , , , , , , ,`Turkish`

| 하위 유형 이름              | 설명                                                                              | 모델 버전으로 시작 가능 |
|---------------------------|------------------------------------------------------------------------------------------|----------------------------------------|
| 해당 없음                       | 위치(예: `Atlantic Ocean` `library`" `Eiffel Tower``Statue of Liberty`  | `2019-10-01`                           |
| 지정학적 법인(GPE) - 영어만| 도시, 국가, 주( `Pennsylvania` `South Africa`예: `Seattle`, )`Tokyo` | `2020-02-01`                           |

### <a name="organization"></a>조직  

인정된 조직, 기업, 기관 및 기타 그룹. 예: 기업, 정치 단체, 음악 밴드, 스포츠 클럽, 정부 기관 및 공공 기관. 국적과 종교는 이 엔터티 유형에 포함되지 않습니다. 

언어: 

* 공개 미리 `Arabic` `Czech`보기 `Chinese-Simplified` `Danish`: `Dutch` `English`" `Finnish` `French`, `German` `Hungarian`" `Italian` `Japanese`, `Korean` `Norwegian (Bokmål)`" `Polish` `Portuguese (Portugal)`, `Portuguese (Brazil)` `Russian`, `Spanish` `Swedish` " , , , , , , ,`Turkish`

| 하위 유형 이름 | 설명                                                                                             | 모델 버전으로 시작 가능 |
|--------------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| 해당 없음          | 예를 들어 `Microsoft`, `NASA` `National Oceanic and Atmospheric Administration``VOA` | `2019-10-01`                           |

### <a name="event"></a>행사  

역사적, 사회적, 자연적 사건.  

언어: 

* 공개 미리 보기:`English`

| 하위 유형 이름 | 설명                                                            | 모델 버전으로 시작 가능 |
|--------------|------------------------------------------------------------------------|----------------------------------------|
| 해당 없음          | 의 `hurricane`이벤트 와 `car accident` `solar eclipse`같은 `wedding`.`American Revolution` | `2020-02-01`                           |

### <a name="product"></a>Product  

다양한 범주의 물리적 개체입니다.  

언어: 

* 공개 미리 보기:`English`

| 하위 유형 이름 | 설명                                                                        | 모델 버전으로 시작 가능 |
|--------------|------------------------------------------------------------------------------------|----------------------------------------|
| 해당 없음          | 예를 들어 `Microsoft Surface laptop` `sunglasses`, `motorcycle` `bag`"`Xbox` | `2020-02-01`                           |
| 컴퓨팅    | `Azure Cosmos DB`, `Azure Kubernetes Service`                                     | `2020-02-01`                           |

### <a name="skill"></a>기술  

역량 또는 전문 지식을 설명하는 엔터티입니다.  

언어: 

* 공개 미리 보기:`English`

| 하위 유형 이름 | 설명                                                                 | 모델 버전으로 시작 가능 |
|--------------|-----------------------------------------------------------------------------|----------------------------------------|
| 해당 없음          | `nursing`, `data mining`, `linguistics`, `critical thinking`, `photography` | `2020-02-01`                           |

### <a name="phone-number"></a>전화 번호

전화 번호(미국 전화 번호만) 

언어:

* 공개 미리 보기:`English`

| 하위 유형 이름 | 설명                                    | 모델 버전으로 시작 가능 |
|--------------|------------------------------------------------|----------------------------------------|
| 해당 없음          | 예를 들어 미국 전화 번호`(312) 555-0176` | `2019-10-01`                           |

### <a name="email"></a>Email

메일 주소입니다. 

언어:

* 공개 미리 보기:`English`

| 하위 유형 이름 | 설명                                      | 모델 버전으로 시작 가능 |
|--------------|--------------------------------------------------|----------------------------------------|
| 해당 없음          | 전자 메일 주소(예:`support@contoso.com` | `2019-10-01`                           |

### <a name="url"></a>URL

인터넷 URL.

언어:

* 공개 미리 보기:`English`

| 하위 유형 이름 | 설명                                          | 모델 버전으로 시작 가능 |
|--------------|------------------------------------------------------|----------------------------------------|
| 해당 없음          | 예를 들어 웹 사이트에 대한 URL`https://www.bing.com` | `2019-10-01`                           |

### <a name="ip-address"></a>IP 주소

인터넷 프로토콜 주소

언어:

* 공개 미리 보기:`English`

| 하위 유형 이름 | 설명                              | 모델 버전으로 시작 가능 |
|--------------|------------------------------------------|----------------------------------------|
| 해당 없음          | 예를 들어 네트워크 주소`10.0.0.101` | `2019-10-01`                           |

###  <a name="datetime"></a>DateTime

날짜 및 시간 엔터티입니다. 

* 모델 버전으로 시작 가능`2019-10-01`

언어:

* 공개 미리 `Chinese-Simplified` `English`보기: `German` , 및 `French``Spanish`

| 하위 유형 이름    | 예                     |
|-------------|------------------------------|
| 해당 없음         | `6:30PM February 4, 2012`, `4/1/2011 2:45`                   |
| Date  | `May 2nd, 2017`, `05/02/2017`       |
| Time     | `8:15`, `6AM`              |
| DateRange    | `August 2nd to August 5th`         |
| TimeRange   | `4-6PM`, `10:00AM to Noon`          |
| Duration | `2.5 minutes`, `one and a half hours`         |
| 설정 | `every Saturday`         |

###  <a name="quantity"></a>수량

숫자 및 숫자 수량입니다. 

* 모델 버전으로 시작 가능`2019-10-01`

언어:

* 공개 미리 `Chinese-Simplified` `English`보기: `German` , 및 `French``Spanish`

| 하위 유형 이름    | 예                     |
|-------------|------------------------------|
| Number         | `6`, `six`                   |
| 백분율  | `50%`, `fifty percent`       |
| Ordinal     | `2nd`, `second`              |
| Age         | `90 day old`, `30 years old` |
| 통화    | `$10.99`, `€30.00`           |
| 차원   | `10 miles`, `40 cm`          |
| 온도 | `32 degrees`, `10°C`         |
