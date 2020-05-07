---
title: Azure Front Door | Microsoft Docs
description: 이 문서는 Azure Front Door의 개요를 제공합니다. 애플리케이션에 대한 사용자 트래픽을 부하 분산하기에 적합한 선택인지 알아보세요.
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: mebeatty
ms.openlocfilehash: 77c0d68f507e09b315c912d1d91fdf9cf63db6fa
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515768"
---
# <a name="azure-front-door-rules-engine-match-conditions"></a>Azure Front Door 규칙 엔진 일치 조건

[AFD 규칙 엔진](front-door-rules-engine.md)에서 규칙은 0개 이상의 일치 조건과 작업으로 구성됩니다. 이 문서에서는 AFD 규칙 엔진에서 사용할 수 있는 일치 조건에 대해 자세히 설명합니다. 

규칙의 첫 번째 파트는 일치 조건 또는 일치 조건 세트입니다. 최대 10개의 일치 조건으로 규칙을 구성할 수 있습니다. 일치 조건은 정의된 작업을 수행할 특정 유형의 요청을 식별합니다. 여러 일치 조건을 사용하면 AND 논리를 사용하여 일치 조건이 그룹화됩니다. 여러 값을 지원하는 모든 일치 조건(아래의 "공백으로 구분됨")에서는 "OR" 연산자를 가정합니다. 

예를 들어 다음을 수행하는 데 일치 조건을 사용할 수 있습니다.

- 특정 IP 주소, 국가 또는 지역을 기준으로 요청을 필터링합니다.
- 헤더 정보에 의한 필터 요청
- 모바일 디바이스 또는 데스크톱 디바이스에서 요청을 필터링합니다.

다음은 Azure Front Door 규칙 엔진에서 사용할 수 있는 일치 조건입니다.  

## <a name="device-type"></a>디바이스 유형 

모바일 디바이스 또는 데스크톱 디바이스에서 생성되는 요청을 식별합니다.  

#### <a name="required-fields"></a>Required fields

연산자 | 지원되는 값
---------|----------------
같음, 같지 않음 | 모바일, 데스크톱

## <a name="post-argument"></a>Post 인수

요청에 사용되는 POST 요청 메서드에 대해 정의된 인수를 기반으로 요청을 식별합니다. 

#### <a name="required-fields"></a>Required fields

인수 이름 | 연산자 | 인수 값 | 대/소문자 변환
--------------|----------|----------------|---------------
String | [표준 연산자 목록](#standard-operator-list) | 문자열, Int | 소문자, 대문자, 자르기, 공백 제거, URL 인코딩, URL 디코딩

## <a name="query-string"></a>쿼리 문자열

특정 쿼리 문자열 매개 변수가 있는 요청을 식별합니다. 이 매개 변수는 특정 패턴과 일치하는 값으로 설정됩니다. 요청 URL의 쿼리 문자열 매개 변수(예: **parameter=value**)에 따라 이 조건의 충족 여부가 결정됩니다. 이 일치 조건은 쿼리 문자열 매개 변수를 이름으로 식별하고 매개 변수 값에 하나 이상의 값을 허용합니다.

#### <a name="required-fields"></a>Required fields

연산자 | 쿼리 문자열 | 대/소문자 변환
---------|--------------|---------------
[표준 연산자 목록](#standard-operator-list) | 문자열, Int | 소문자, 대문자, 자르기, 공백 제거, URL 인코딩, URL 디코딩

## <a name="remote-address"></a>원격 주소

요청자의 위치 또는 IP 주소를 기준으로 요청을 식별합니다.

#### <a name="required-fields"></a>Required fields

연산자 | 지원되는 값
---------|-----------------
지역 일치 | 국가 코드
IP 일치 | IP 주소(공백으로 구분됨)
지역이 일치하지 않음 | 국가 코드
IP가 일치하지 않음 | IP 주소(공백으로 구분됨)

#### <a name="key-information"></a>주요 정보

- CIDR 표기법을 사용합니다.
- 여러 IP 주소와 IP 주소 블록을 지정하려면 값 사이에 공백 하나를 사용합니다.
  - **IPv4 예제**: *1.2.3.4 10.20.30.40*은 1.2.3.4 또는 10.20.30.40 주소에서 도착하는 요청과 일치합니다.
  - **IPv6 예제**: *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80*은 1:2:3:4:5:6:7:8 또는 10:20:30:40:50:60:70:80 주소에서 도착하는 요청과 일치합니다.
- IP 주소 블록에 대한 구문은 뒤에 슬래시와 접두사 크기가 오는 기본 IP 주소입니다. 다음은 그 예입니다. 
  - **IPv4 예제**: *5.5.5.64/26*은 5.5.5.64~5.5.5.127 주소에서 도착하는 요청과 일치합니다.
  - **IPv6 예제**: *1:2:3:/48*은 1:2:3:0:0:0:0:0~1:2:3:ffff:ffff:ffff:ffff:ffff 주소에서 도착하는 요청과 일치합니다.

## <a name="request-body"></a>요청 본문

요청 본문에 표시되는 특정 텍스트를 기준으로 요청을 식별합니다.

#### <a name="required-fields"></a>Required fields

연산자 | 요청 본문 | 대/소문자 변환
---------|--------------|---------------
[표준 연산자 목록](#standard-operator-list) | 문자열, Int | 소문자, 대문자, 자르기, 공백 제거, URL 인코딩, URL 디코딩

## <a name="request-header"></a>요청 헤더

요청에 특정 헤더를 사용하는 요청을 식별합니다.

#### <a name="required-fields"></a>Required fields

헤더 이름 | 연산자 | 헤더 값 | 대/소문자 변환
------------|----------|--------------|---------------
String | [표준 연산자 목록](#standard-operator-list) | 문자열, Int | 소문자, 대문자, 자르기, 공백 제거, URL 인코딩, URL 디코딩

## <a name="request-method"></a>요청 메서드

지정된 요청 메서드를 사용하는 요청을 식별합니다.

#### <a name="required-fields"></a>Required fields

연산자 | 지원되는 값
---------|----------------
같음, 같지 않음 | GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE

#### <a name="key-information"></a>주요 정보

- 오직 GET 요청 메서드만이 Azure Front Door에서 캐시된 콘텐츠를 생성할 수 있습니다. 다른 모든 요청 메서드는 네트워크를 통해 프록시 설정됩니다. 

## <a name="request-protocol"></a>요청 프로토콜

지정된 프로토콜을 사용하는 요청을 식별합니다.

#### <a name="required-fields"></a>Required fields

연산자 | 지원되는 값
---------|----------------
같음, 같지 않음 | HTTP, HTTPS

## <a name="request-url"></a>요청 URL

지정된 URL과 일치하는 요청을 식별합니다.

#### <a name="required-fields"></a>Required fields

연산자 | 요청 URL | 대/소문자 변환
---------|-------------|---------------
[표준 연산자 목록](#standard-operator-list) | 문자열, Int | 소문자, 대문자, 자르기, 공백 제거, URL 인코딩, URL 디코딩

#### <a name="key-information"></a>주요 정보

- 이 규칙 조건을 사용하는 경우 프로토콜 정보를 포함해야 합니다. 예를 들어 *https://www.\<yourdomain\>.com* 으로 해야 합니다.

## <a name="request-file-extension"></a>요청 파일 확장명

요청 URL의 파일 이름에 지정된 파일 확장명이 있는 요청을 식별합니다.

#### <a name="required-fields"></a>Required fields

연산자 | 내선 번호 | 대/소문자 변환
---------|-----------|---------------
[표준 연산자 목록](#standard-operator-list) | 문자열, Int | 소문자, 대문자, 자르기, 공백 제거, URL 인코딩, URL 디코딩

#### <a name="key-information"></a>주요 정보

- 확장명에는 선행 마침표를 사용하지 않습니다. 예를 들어 *.html*이 아닌 *html*을 사용합니다.

## <a name="request-file-name"></a>요청 파일 이름

요청 URL에 지정된 파일 이름이 있는 요청을 식별합니다.

#### <a name="required-fields"></a>Required fields

연산자 | 파일 이름  | 대/소문자 변환
---------|-----------|---------------
[표준 연산자 목록](#standard-operator-list) | 문자열, Int | 소문자, 대문자, 자르기, 공백 제거, URL 인코딩, URL 디코딩

#### <a name="key-information"></a>주요 정보

- 여러 파일 이름을 지정하려면 ENTER 키를 눌러 각 파일 이름을 구분합니다. 

## <a name="request-path"></a>요청 경로

요청 URL에 지정된 경로가 있는 요청을 식별합니다.

#### <a name="required-fields"></a>Required fields

연산자 | 값 | 대/소문자 변환
---------|-------|---------------
[표준 연산자 목록](#standard-operator-list) | 문자열, Int | 소문자, 대문자, 자르기, 공백 제거, URL 인코딩, URL 디코딩

## <a name="standard-operator-list"></a>표준 연산자 목록

표준 연산자 목록의 값을 허용하는 규칙에는 다음 연산자가 유효합니다.

- 모두
- 같음 
- 포함 
- 시작 문자 
- 끝 문자 
- 보다 작음
- 작거나 같음
- 초과
- 크거나 같음
- 없음
- 포함하지 않음
- 다음으로 시작하지 않는 경우 
- 다음으로 끝나지 않는 경우 
- 보다 작지 않음
- 보다 크거나 같음
- 보다 크지 않음
- 보다 작거나 같음

*작거나 같음*이나 *크거나 같음*처럼 숫자 연산자의 경우 길이를 기준으로 비교합니다. 여기서 일치 조건의 값은 비교하려는 길이와 일치하는 정수여야 합니다. 


## <a name="next-steps"></a>다음 단계

- 첫 번째 [규칙 엔진 구성](front-door-tutorial-rules-engine.md)을 설정하는 방법을 알아봅니다. 
- [규칙 엔진 작업](front-door-rules-engine-actions.md)에 대해 자세히 알아봅니다.
- [Azure Front Door 규칙 엔진](front-door-rules-engine.md)에 대해 자세히 알아봅니다.
