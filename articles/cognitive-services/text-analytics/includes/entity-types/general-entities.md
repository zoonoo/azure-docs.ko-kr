---
title: 일반적인 명명 된 엔터티
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: aahi
ms.openlocfilehash: 693a81cfb15407541311d7ab053bb2ab6a267b29
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73800000"
---
## <a name="general-entity-types"></a>일반 엔터티 형식:

### <a name="person"></a>사람
텍스트의 인식 된 이름 및 기타 사람
언어:
* 공개 미리 보기: `English`

| 하위 형식 이름 | 설명             |
|--------------|-------------------------|
| 해당 없음          | 인식할 수 있는 이름 (예: `Bill Gates``Marie Curie` |

### <a name="location"></a>위치

자연 스러운 랜드마크, 구조 및 지리적 기능.

언어:


* 공개 미리 보기: `English`

| 하위 형식 이름 | 설명                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| 해당 없음          | 위치 (예: `Atlantic Ocean`, `library`, `Eiffel Tower`, `Statue of Liberty` |

### <a name="organization"></a>조직  

인식 된 조직, 회사, 기관 및 기타 사용자 그룹. 예를 들어 회사, 정치적 그룹, 음악 밴드, 스포츠 클럽, 정부 기관, 공공 단체 등이 있습니다. Nationalities 및 religions는이 엔터티 형식에 포함 되지 않습니다. 언어: 

* 공개 미리 보기: `English`

| 하위 형식 이름 | 설명                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| 해당 없음          | 조직 (예: `Microsoft``NASA` `National Oceanic and Atmospheric Administration` |

### <a name="phone-number"></a>전화 번호

전화 번호. 

언어:


* 공개 미리 보기: `English`

| 하위 형식 이름 | 설명                                  |
|----------|----------------------------------------------|
| 해당 없음         | 전화 번호 (예: `+1 123-123-123`). |

### <a name="email"></a>Email

메일 주소입니다. 

언어:


* 공개 미리 보기: `English`

| 하위 형식 이름 | 설명                                  |
|----------|----------------------------------------------|
| 해당 없음         | 전자 메일 주소 (예: `support@contoso.com` |

### <a name="url"></a>URL

인터넷 Url.

언어:


* 공개 미리 보기: `English`

| 하위 형식 이름 | 설명                                           |
|----------|-------------------------------------------------------|
| 해당 없음         | 웹 사이트 Url (예: `https://www.bing.com`). |

###  <a name="number"></a>Number

숫자 및 숫자 수량. 

언어:


* 공개 미리 보기: `English`

| 하위 형식 이름    | 예                     |
|-------------|------------------------------|
| 해당 없음         | `6`, `six`                   |
| 백분율  | `50%`, `fifty percent`       |
| Ordinal     | `2nd`, `second`              |
| 통화    | `$10.99`, `€30.00`           |
| 차원   | `10 miles`, `40 cm`          |
| 온도 | `32 degrees`, `10°C`         |
