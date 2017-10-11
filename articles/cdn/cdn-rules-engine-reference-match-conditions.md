---
title: "Azure CDN 규칙 엔진 일치 조건 | Microsoft Docs"
description: "Azure CDN 규칙 엔진 일치 조건 및 기능에 대한 참조 설명서"
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: f4886b1d78dfa87cf25737fb46c12b5963034f27
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="azure-cdn-rules-engine-match-conditions"></a>Azure CDN 규칙 엔진 일치 조건
이 항목에서는 Azure CDN(Content Delivery Network) [규칙 엔진](cdn-rules-engine.md)에 대해 제공되는 일치 조건에 대해 자세히 설명합니다.

규칙의 두 번째 부분이 일치 조건입니다. 일치 조건은 기능 집합에 대해 수행할 특정 요청 유형을 식별합니다.

예를 들어 특정 위치에서 콘텐츠에 대한 요청, 특정 IP 주소 또는 국가에서 생성된 요청을 헤더 정보로 필터링하는 데 사용할 수 있습니다.

## <a name="always"></a>항상

항상 일치 조건은 모든 요청에 기본 기능 집합을 적용하도록 설계되었습니다.

## <a name="device"></a>장치

장치 일치 조건은 속성에 따라 모바일 장치에서 생성되는 요청을 식별합니다.  모바일 장치 검색은 [WURFL](http://wurfl.sourceforge.net/)을 통해 이루어집니다.  WURFL 기능 및 해당 CDN 규칙 엔진 변수는 아래에 나열되어 있습니다.

> [!NOTE] 
> 아래 변수는 **클라이언트 요청 헤더 수정** 및 **클라이언트 응답 헤더 수정** 기능에서 지원됩니다.

기능 | 변수 | 설명 | 샘플 값
-----------|----------|-------------|----------------
브랜드 이름 | %{wurfl_cap_brand_name} | 장치의 브랜드 이름을 나타내는 문자열입니다. | Samsung
장치 OS | %{wurfl_cap_device_os} | 장치에 설치된 운영 체제를 나타내는 문자열입니다. | IOS
장치 OS 버전 | %{wurfl_cap_device_os_version} | 장치에 설치된 OS의 버전 번호를 나타내는 문자열입니다. | 1.0.1
이중 방향 | %{wurfl_cap_dual_orientation} | 장치가 이중 방향을 지원하는지 여부를 나타내는 부울입니다. | true
HTML 기본 DTD | %{wurfl_cap_html_preferred_dtd} | HTML 콘텐츠에 대한 모바일 장치의 기본 DTD(문서 종류 정의)를 나타내는 문자열입니다. | 없음<br/>xhtml_basic<br/>html5
이미지 인라인 처리 | %{wurfl_cap_image_inlining} | 장치가 Base64로 인코딩된 이미지를 지원하는지 여부를 나타내는 부울입니다. | false
Android 여부 | %{wurfl_vcap_is_android} | 장치가 Android OS를 사용하는지 여부를 나타내는 부울입니다. | true
IOS 여부 | %{wurfl_vcap_is_ios} | 장치에서 iOS를 사용하는지 여부를 나타내는 부울입니다. | false
스마트 TV 여부 | %{wurfl_cap_is_smarttv} | 장치가 스마트 TV인지 여부를 나타내는 부울입니다. | false
스마트폰 여부 | %{wurfl_vcap_is_smartphone} | 장치가 스마트폰인지 여부를 나타내는 부울입니다. | true
태블릿 여부 | %{wurfl_cap_is_tablet} | 장치가 태블릿인지 여부를 나타내는 부울입니다. 이는 OS와 별개의 설명입니다. | true
무선 장치 여부 | %{wurfl_cap_is_wireless_device} | 장치가 무선 장치로 간주되는지 여부를 나타내는 부울입니다. | true
마케팅 이름 | %{wurfl_cap_marketing_name} | 장치의 마케팅 이름을 나타내는 문자열입니다. | BlackBerry 8100 Pearl
모바일 브라우저 | %{wurfl_cap_mobile_browser} | 장치에서 콘텐츠를 요청하는 데 사용되는 브라우저를 나타내는 문자열입니다. | Chrome
모바일 브라우저 버전 | %{wurfl_cap_mobile_browser_version} | 장치에서 콘텐츠를 요청하는 데 사용되는 브라우저의 버전을 나타내는 문자열입니다. | 31
모델 이름 | %{wurfl_cap_model_name} | 장치의 모델 이름을 나타내는 문자열입니다. | s3
점진적 다운로드 | %{wurfl_cap_progressive_download} | 장치가 오디오/비디오를 다운로드하는 중에 오디오/비디오 재생을 지원하는지 여부를 나타내는 부울입니다. | true
릴리스 날짜 | %{wurfl_cap_release_date} | 장치가 WURFL 데이터베이스에 추가된 연도와 월을 나타내는 문자열입니다.<br/><br/>형식: `yyyy_mm` | 2013_december
해상도 높이 | %{wurfl_cap_resolution_height} | 장치의 높이(픽셀)를 나타내는 정수입니다. | 768
해상도 너비 | %{wurfl_cap_resolution_width} | 장치의 너비(픽셀)를 나타내는 정수입니다. | 1024


## <a name="location"></a>위치

이 일치 조건은 요청자의 위치에 따라 요청을 식별하도록 설계되었습니다.

이름 | 목적
-----|--------
AS 숫자 | 특정 네트워크에서 발생하는 요청을 식별합니다.
국가 | 지정된 국가에서 발생하는 요청을 식별합니다.


## <a name="origin"></a>원본

이 일치 조건은 CDN 저장소 또는 고객 원본 서버를 가리키는 요청을 식별하도록 설계되었습니다.

이름 | 목적
-----|--------
CDN 원본 | CDN 저장소에 저장된 콘텐츠에 대한 요청을 식별합니다.
고객 원본 | 특정 고객 원본 서버에 저장된 콘텐츠에 대한 요청을 식별합니다.


## <a name="request"></a>요청

이 일치 조건은 해당 속성에 따라 요청을 식별하도록 설계되었습니다.

이름 | 목적
-----|--------
클라이언트 IP 주소 | 특정 IP 주소에서 발생하는 요청을 식별합니다.
쿠키 매개 변수 | 지정된 값에 대한 각 요청과 관련된 쿠키를 확인합니다.
쿠키 매개 변수 Regex | 지정된 정규식에 대한 각 요청과 관련된 쿠키를 확인합니다.
에지 Cname | 특정 에지 CNAME을 가리키는 요청을 식별합니다.
참조 도메인 | 지정된 호스트 이름에서 참조된 요청을 식별합니다.
요청 헤더 리터럴 | 지정된 값으로 설정된 지정된 헤더를 포함하는 요청을 식별합니다.
요청 헤더 Regex | 지정된 정규식과 일치하는 값으로 설정된 지정된 헤더를 포함하는 요청을 식별합니다.
요청 헤더 와일드카드 | 지정된 패턴과 일치하는 값으로 설정된 지정된 헤더를 포함하는 요청을 식별합니다.
요청 메서드 | 해당 HTTP 메서드로 요청을 식별합니다.
요청 스키마 | 해당 HTTP 프로토콜로 요청을 식별합니다.

## <a name="url"></a>URL

이 일치 조건은 해당 URL에 따라 요청을 식별하도록 설계되었습니다.

이름 | 목적
-----|--------
URL 경로 디렉터리 | 해당 상대 경로로 요청을 식별합니다.
URL 경로 확장 | 해당 파일 확장명으로 요청을 식별합니다.
URL 경로 파일 이름 | 해당 파일 이름으로 요청을 식별합니다.
URL 경로 리터럴 | 요청의 상대 경로를 지정된 값과 비교합니다.
URL 경로 Regex | 요청의 상대 경로를 지정된 정규식과 비교합니다.
URL 경로 와일드카드 | 요청의 상대 경로를 지정된 패턴과 비교합니다.
URL 쿼리 리터럴 | 요청의 쿼리 문자열을 지정된 값과 비교합니다.
URL 쿼리 매개 변수 | 지정된 패턴과 일치하는 값으로 설정된 지정된 쿼리 문자열 매개 변수를 포함하는 요청을 식별합니다.
URL 쿼리 Regex | 지정된 정규식과 일치하는 값으로 설정된 지정된 쿼리 문자열 매개 변수를 포함하는 요청을 식별합니다.
URL 쿼리 와일드카드 | 지정된 값을 요청의 쿼리 문자열에 대해 비교합니다.


## <a name="next-steps"></a>다음 단계
* [Azure CDN 개요](cdn-overview.md)
* [규칙 엔진 참조](cdn-rules-engine-reference.md)
* [규칙 엔진 조건식](cdn-rules-engine-reference-conditional-expressions.md)
* [규칙 엔진 기능](cdn-rules-engine-reference-features.md)
* [규칙 엔진을 사용하여 기본 HTTP 동작 재정의](cdn-rules-engine.md)

