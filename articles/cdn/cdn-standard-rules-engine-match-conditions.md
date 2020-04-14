---
title: Azure CDN에 대한 표준 규칙 엔진의 조건 일치 | 마이크로 소프트 문서
description: Azure CDN(Azure CDN)에 대한 표준 규칙 엔진의 일치 조건에 대한 참조 설명서입니다.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: b8050b973027ac91ede0ba98f4d1c76831da9828
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259932"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Azure CDN에 대한 표준 규칙 엔진의 조건 일치

Azure CDN(Azure CDN)에 대한 [표준 규칙 엔진에서](cdn-standard-rules-engine.md) 규칙은 하나 이상의 일치 조건과 작업으로 구성됩니다. 이 문서에서는 Azure CDN의 표준 규칙 엔진에서 사용할 수 있는 일치 조건에 대한 자세한 설명을 제공합니다.

규칙의 첫 번째 부분은 일치 조건 또는 일치 조건 집합입니다. Azure CDN에 대한 표준 규칙 엔진에서 각 규칙에는 최대 4개의 일치 조건이 있을 수 있습니다. 일치 조건은 정의된 작업이 수행되는 특정 유형의 요청을 식별합니다. 여러 일치 조건을 사용하는 경우 일치 조건은 AND 논리를 사용하여 함께 그룹화됩니다.

예를 들어 다음을 수행하는 데 일치 조건을 사용할 수 있습니다.

- 특정 IP 주소, 국가 또는 지역을 기반으로 요청을 필터링합니다.
- 헤더 정보에 의한 필터 요청
- 모바일 장치 또는 데스크톱 장치에서 요청을 필터링합니다.

## <a name="match-conditions"></a>일치 조건

다음 일치 조건은 Azure CDN의 표준 규칙 엔진에서 사용할 수 있습니다. 

### <a name="device-type"></a>디바이스 유형 

모바일 장치 또는 데스크톱 장치에서 만든 요청을 식별합니다.  

#### <a name="required-fields"></a>Required fields

연산자 | 지원되는 값
---------|----------------
같음, 같지 않음 | 모바일, 데스크톱

### <a name="http-version"></a>HTTP 버전

요청의 HTTP 버전을 기반으로 요청을 식별합니다.

#### <a name="required-fields"></a>Required fields

연산자 | 지원되는 값
---------|----------------
같음, 같지 않음 | 2.0, 1.1, 1.0, 0.9, 모두

### <a name="request-cookies"></a>요청 쿠키입니다.

들어오는 요청의 쿠키 정보를 기반으로 요청을 식별합니다.

#### <a name="required-fields"></a>Required fields

쿠키 이름 | 연산자 | 쿠키 값 | 케이스 변환
------------|----------|--------------|---------------
String | [표준 연산자 목록](#standard-operator-list) | 문자열, Int | 대문자로 변환, 소문자로 변환 없음

#### <a name="key-information"></a>주요 정보

- 쿠키 이름을 지정할 때 와일드카드 값(별표()을\*포함)을 사용할 수 없습니다. 정확한 쿠키 이름을 사용해야 합니다.
- 이 일치 조건의 인스턴스당 단일 쿠키 이름만 지정할 수 있습니다.
- 쿠키 이름 비교는 대/소문자를 구분하지 않습니다.
- 여러 쿠키 값을 지정하려면 각 쿠키 값 사이에 단일 공간을 사용합니다. 
- 쿠키 값은 와일드카드 값을 활용할 수 있습니다.
- 와일드카드 값을 지정하지 않은 경우 정확한 일치만 이 일치 조건을 충족합니다. 예를 들어 "값"은 "값"과 일치하지만 "Value1"은 일치하지 않습니다. 

### <a name="post-argument"></a>포스트 인수

요청에 사용되는 POST 요청 메서드에 대해 정의된 인수를 기반으로 요청을 식별합니다. 

#### <a name="required-fields"></a>Required fields

인수 이름 | 연산자 | 인수 값 | 케이스 변환
--------------|----------|----------------|---------------
String | [표준 연산자 목록](#standard-operator-list) | 문자열, Int | 대문자로 변환, 소문자로 변환 없음

### <a name="query-string"></a>쿼리 문자열

특정 쿼리 문자열 매개 변수를 포함하는 요청을 식별합니다. 이 매개 변수는 특정 패턴과 일치하는 값으로 설정됩니다. 요청 URL의 쿼리 문자열 매개 변수(예: **parameter=value)는**이 조건이 충족되는지 여부를 결정합니다. 이 일치 조건은 쿼리 문자열 매개 변수를 이름으로 식별하고 매개 변수 값에 하나 이상의 값을 허용합니다.

#### <a name="required-fields"></a>Required fields

연산자 | 쿼리 문자열 | 케이스 변환
---------|--------------|---------------
[표준 연산자 목록](#standard-operator-list) | 문자열, Int | 대문자로 변환, 소문자로 변환 없음

### <a name="remote-address"></a>원격 주소

요청자의 위치 또는 IP 주소를 기반으로 요청을 식별합니다.

#### <a name="required-fields"></a>Required fields

연산자 | 지원되는 값
---------|-----------------
모두 | N/A
지오 매치 | 국가 코드
IP 매치 | IP 주소(공간 구분)
아니 | N/A
지오 매치 아님 | 국가 코드
IP 일치하지 않음 | IP 주소(공간 구분)

#### <a name="key-information"></a>주요 정보

- CIDR 표기법을 사용합니다.
- 여러 IP 주소와 IP 주소 블록을 지정하려면 값 사이에 단일 공간을 사용합니다.
  - **IPv4 예제**: *1.2.3.4 10.20.30.40주소* 1.2.3.4 또는 10.20.30.40에서 도착하는 모든 요청과 일치합니다.
  - **IPv6 예**: *1:2:3:5:5:7:8 10:30:50:50:60:70:80* 은 주소 1:2:3:5:5:6:7 또는 10:20:30:40:50:70:80에서 도착하는 모든 요청과 일치합니다.
- IP 주소 블록에 대한 구문은 뒤에 슬래시와 접두사 크기가 오는 기본 IP 주소입니다. 다음은 그 예입니다.
  - **IPv4 예제**: *5.5.5.64/26은* 주소 5.5.5.64부터 5.5.5.127까지 도착하는 모든 요청과 일치합니다.
  - **IPv6 예제**: 1:2:3:/48주소1:2:3:0:0:0:0 ~ 1:2:3:ffff:ffff:ffff:ffff:ffff:ffff에서 도착하는 모든 요청과 일치합니다. *1:2:3:/48*

### <a name="request-body"></a>요청 본문

요청 본문에 나타나는 특정 텍스트를 기반으로 요청을 식별합니다.

#### <a name="required-fields"></a>Required fields

연산자 | 요청 본문 | 케이스 변환
---------|--------------|---------------
[표준 연산자 목록](#standard-operator-list) | 문자열, Int | 대문자로 변환, 소문자로 변환 없음

### <a name="request-header"></a>요청 헤더

요청에서 특정 헤더를 사용하는 요청을 식별합니다.

#### <a name="required-fields"></a>Required fields

헤더 이름 | 연산자 | 헤더 값 | 케이스 변환
------------|----------|--------------|---------------
String | [표준 연산자 목록](#standard-operator-list) | 문자열, Int | 대문자로 변환, 소문자로 변환 없음

### <a name="request-method"></a>요청 메서드

지정된 요청 메서드를 사용하는 요청을 식별합니다.

#### <a name="required-fields"></a>Required fields

연산자 | 지원되는 값
---------|----------------
같음, 같지 않음 | 얻기, 게시, 넣기, 삭제, 머리, 옵션, 추적

#### <a name="key-information"></a>주요 정보

- GET 요청 메서드만 Azure CDN에서 캐시된 콘텐츠를 생성할 수 있습니다. 다른 모든 요청 메서드는 네트워크를 통해 프록시 설정됩니다. 

### <a name="request-protocol"></a>요청 프로토콜

사용된 지정된 프로토콜을 사용하는 요청을 식별합니다.

#### <a name="required-fields"></a>Required fields

연산자 | 지원되는 값
---------|----------------
같음, 같지 않음 | HTTP, HTTPS

### <a name="request-url"></a>요청 URL

지정된 URL과 일치하는 요청을 식별합니다.

#### <a name="required-fields"></a>Required fields

연산자 | 요청 URL | 케이스 변환
---------|-------------|---------------
[표준 연산자 목록](#standard-operator-list) | 문자열, Int | 대문자로 변환, 소문자로 변환 없음

#### <a name="key-information"></a>주요 정보

- 이 규칙 조건을 사용하는 경우 프로토콜 정보를 포함해야 합니다. 예: *https://www.\<yourdomain\>.com*.

### <a name="url-file-extension"></a>URL 파일 확장자

요청 URL의 파일 이름에 지정된 파일 확장명을 포함하는 요청을 식별합니다.

#### <a name="required-fields"></a>Required fields

연산자 | 내선 번호 | 케이스 변환
---------|-----------|---------------
[표준 연산자 목록](#standard-operator-list) | 문자열, Int | 대문자로 변환, 소문자로 변환 없음

#### <a name="key-information"></a>주요 정보

- 확장의 경우 선행 기간을 포함하지 마십시오. 예를 들어 *.html*대신 *html을* 사용합니다.

### <a name="url-file-name"></a>URL 파일 이름

요청 URL에 지정된 파일 이름을 포함하는 요청을 식별합니다.

#### <a name="required-fields"></a>Required fields

연산자 | 파일 이름 | 케이스 변환
---------|-----------|---------------
[표준 연산자 목록](#standard-operator-list) | 문자열, Int | 대문자로 변환, 소문자로 변환 없음

#### <a name="key-information"></a>주요 정보

- 여러 파일 이름을 지정하려면 각 파일 이름을 단일 공간으로 구분합니다. 

### <a name="url-path"></a>URL 경로

요청 URL에 지정된 경로를 포함하는 요청을 식별합니다.

#### <a name="required-fields"></a>Required fields

연산자 | 값 | 케이스 변환
---------|-------|---------------
[표준 연산자 목록](#standard-operator-list) | 문자열, Int | 대문자로 변환, 소문자로 변환 없음

#### <a name="key-information"></a>주요 정보

- 파일 이름 값은 와일드카드 값을 활용할 수 있습니다. 예를 들어 각각의 파일 이름 패턴은 하나 이상의 별표(*)로 구성될 수 있으며 각 별표는 하나 이상의 문자 시퀀스와 일치합니다.

## <a name="reference-for-rules-engine-match-conditions"></a>규칙 엔진 일치 조건에 대한 참조

### <a name="standard-operator-list"></a>표준 연산자 목록

표준 연산자 목록에서 값을 허용하는 규칙의 경우 다음 연산자는 유효합니다.

- 모두
- 같음 
- 포함 
- 시작 문자 
- 끝 문자 
- 보다 작음
- 이하 또는 같음
- 초과
- 보다 크거나 같음
- 아니, 아니
- 포함하지 않음
- 시작되지 않음 
- 끝나지 않음 
- 보다 작지 않음
- 이하 또는 같음
- 보다 크지 않음
- 크거나 같지 않음

*이하* 및 *보다 크거나 큰*숫자와 같은 숫자 연산자의 경우 사용되는 비교는 길이를 기준으로 합니다. 이 경우 일치 조건의 값은 비교할 길이와 동일한 정수여야 합니다. 

## <a name="next-steps"></a>다음 단계

- [Azure CDN 개요](cdn-overview.md)
- [표준 규칙 엔진 참조](cdn-standard-rules-engine-reference.md)
- [표준 규칙 엔진의 작업](cdn-standard-rules-engine-actions.md)
- [표준 규칙 엔진을 사용하여 HTTPS 적용](cdn-standard-rules-engine.md)
