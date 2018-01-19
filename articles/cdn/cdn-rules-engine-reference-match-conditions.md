---
title: "Azure CDN 규칙 엔진에 대한 일치 조건 | Microsoft Docs"
description: "Azure Content Delivery Network 규칙 엔진 일치 조건에 대한 참조 설명서"
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
ms.date: 12/21/2017
ms.author: rli
ms.openlocfilehash: 9986e654b076df099e3912f9da628728723b5c3d
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2018
---
# <a name="match-conditions-for-the-azure-cdn-rules-engine"></a>Azure CDN 규칙 엔진에 대한 일치 조건
이 문서에서는 Azure CDN(Content Delivery Network) [규칙 엔진](cdn-rules-engine.md)에 대해 제공되는 일치 조건에 대해 자세히 설명합니다.

규칙의 두 번째 부분이 일치 조건입니다. 일치 조건은 기능 집합에 대해 수행할 특정 요청 유형을 식별합니다.

예를 들어 다음을 수행하는 데 일치 조건을 사용할 수 있습니다.
- 특정 위치에서 콘텐츠에 대한 필터 요청
- 특정 IP 주소 또는 국가에서 생성된 필터 요청
- 헤더 정보에 의한 필터 요청

## <a name="always-match-condition"></a>항상 일치 조건

항상 일치 조건은 모든 요청에 기본 기능 집합을 적용합니다.

이름 | 목적
-----|--------
[항상](#always) | 모든 요청에 기본 기능 집합을 적용합니다.

## <a name="device-match-condition"></a>장치 일치 조건

장치 일치 조건은 속성에 따라 모바일 장치에서 생성되는 요청을 식별합니다.  

이름 | 목적
-----|--------
[장치](#device) | 속성에 따라 모바일 장치에서 생성되는 요청을 식별합니다.

## <a name="location-match-conditions"></a>위치 일치 조건

위치 일치 조건은 요청자의 위치에 따라 요청을 식별합니다.

이름 | 목적
-----|--------
[AS 숫자](#as-number) | 특정 네트워크에서 발생하는 요청을 식별합니다.
[국가](#country) | 지정된 국가에서 발생하는 요청을 식별합니다.

## <a name="origin-match-conditions"></a>원본 일치 조건

원본 일치 조건은 Content Delivery Network 저장소 또는 고객 원본 서버를 가리키는 요청을 식별합니다.

이름 | 목적
-----|--------
[CDN 원본](#cdn-origin) | Content Delivery Network 저장소에 저장된 콘텐츠에 대한 요청을 식별합니다.
[고객 원본](#customer-origin) | 특정 고객 원본 서버에 저장된 콘텐츠에 대한 요청을 식별합니다.

## <a name="request-match-conditions"></a>요청 일치 조건

요청 일치 조건은 해당 속성에 따라 요청을 식별합니다.

이름 | 목적
-----|--------
[클라이언트 IP 주소](#client-ip-address) | 특정 IP 주소에서 발생하는 요청을 식별합니다.
[쿠키 매개 변수](#cookie-parameter) | 지정된 값에 대한 각 요청과 관련된 쿠키를 확인합니다.
[쿠키 매개 변수 Regex](#cookie-parameter-regex) | 지정된 정규식에 대한 각 요청과 관련된 쿠키를 확인합니다.
[에지 Cname](#edge-cname) | 특정 에지 CNAME을 가리키는 요청을 식별합니다.
[참조 도메인](#referring-domain) | 지정된 호스트 이름에서 참조된 요청을 식별합니다.
[요청 헤더 리터럴](#request-header-literal) | 지정된 값으로 설정된 지정된 헤더를 포함하는 요청을 식별합니다.
[요청 헤더 Regex](#request-header-regex) | 지정된 정규식과 일치하는 값으로 설정된 지정된 헤더를 포함하는 요청을 식별합니다.
[요청 헤더 와일드카드](#request-header-wildcard) | 지정된 패턴과 일치하는 값으로 설정된 지정된 헤더를 포함하는 요청을 식별합니다.
[요청 메서드](#request-method) | 해당 HTTP 메서드로 요청을 식별합니다.
[요청 스키마](#request-scheme) | 해당 HTTP 프로토콜로 요청을 식별합니다.

## <a name="url-match-conditions"></a>URL 일치 조건

URL 일치 조건은 해당 URL에 따라 요청을 식별합니다.

이름 | 목적
-----|--------
URL 경로 디렉터리 | 해당 상대 경로로 요청을 식별합니다.
URL 경로 확장 | 해당 파일 이름 확장명으로 요청을 식별합니다.
URL 경로 파일 이름 | 해당 파일 이름으로 요청을 식별합니다.
URL 경로 리터럴 | 요청의 상대 경로를 지정된 값과 비교합니다.
URL 경로 Regex | 요청의 상대 경로를 지정된 정규식과 비교합니다.
URL 경로 와일드카드 | 요청의 상대 경로를 지정된 패턴과 비교합니다.
URL 쿼리 리터럴 | 요청의 쿼리 문자열을 지정된 값과 비교합니다.
URL 쿼리 매개 변수 | 지정된 패턴과 일치하는 값으로 설정된 지정된 쿼리 문자열 매개 변수를 포함하는 요청을 식별합니다.
URL 쿼리 Regex | 지정된 정규식과 일치하는 값으로 설정된 지정된 쿼리 문자열 매개 변수를 포함하는 요청을 식별합니다.
URL 쿼리 와일드카드 | 지정된 값을 요청의 쿼리 문자열에 대해 비교합니다.


## <a name="reference-for-rules-engine-match-conditions"></a>규칙 엔진 일치 조건에 대한 참조

---
### <a name="always"></a>항상

항상 일치 조건은 모든 요청에 기본 기능 집합을 적용합니다.

[맨 위로 이동](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="as-number"></a>AS 숫자 
AS 숫자 네트워크는 해당 ASN(자치 시스템 번호)으로 정의됩니다. 클라이언트의 네트워크가 지정된 ASN과 "일치" 또는 "일치하지 않음"의 경우 이 조건이 충족될지 여부를 나타내는 옵션이 제공됩니다.

주요 정보:
- 각각을 하나의 공백으로 구분하여 여러 ASN을 지정합니다. 예를 들어 64514 64515는 64514 또는 64515에서 도착하는 요청과 일치합니다.
- 특정 요청은 유효한 ASN을 반환하지 않을 수도 있습니다. 물음표(?)는 확인할 수 없는 유효한 ASN에 대한 요청과 일치합니다.
- 원하는 네트워크에 대한 전체 ASN을 지정해야 합니다. 부분 값은 일치하지 않습니다.
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale

[맨 위로 이동](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cdn-origin"></a>CDN 원본
다음 조건이 모두 충족될 때 CDN 원본 일치 조건이 충족됩니다.
- Content Delivery Network 저장소에서 콘텐츠가 요청되었습니다.
- 요청 URI는 이 일치 조건에서 정의된 콘텐츠 액세스 지점(예: /000001)을 사용합니다.
  - Content Delivery Network URL: 요청 URI는 선택한 콘텐츠 액세스 지점을 포함해야 합니다.
  - 에지 CNAME URL: 해당 에지 CNAME 구성은 선택한 콘텐츠 액세스 포인트를 가리켜야 합니다.
  
주요 정보:
 - 콘텐츠 액세스 포인트는 요청한 콘텐츠를 제공해야 하는 서비스를 식별합니다.
 - 특정 일치 조건을 결합하는 데 AND IF문을 사용하지 마십시오. 예를 들어 고객 원본 일치 조건과 CDN 원본 일치 조건을 결합하면 일치될 수 없는 일치 패턴을 만듭니다. 이러한 이유로 두 개의 CDN 원본 일치 조건은 AND IF문을 통해 결합될 수 없습니다.

[맨 위로 이동](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="client-ip-address"></a>클라이언트 IP 주소
클라이언트의 IP 주소가 지정한 IP 주소와 "일치" 또는 "일치하지 않음"의 경우 클라이언트 IP 주소 조건이 충족될지 여부를 나타내는 옵션이 제공됩니다.

주요 정보:
- CIDR 표기법을 사용해야 합니다.
- 각각을 단일 공백으로 구분하여 여러 IP 주소 및/또는 IP 주소 블록을 지정합니다.
  - **IPv4 예제**: 1.2.3.4 10.20.30.40은 1.2.3.4 또는 10.20.30.40에서 도착하는 요청과 일치합니다.
  - **IPv6 예제**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80은 1:2:3:4:5:6:7:8 또는 10:20:30:40:50:60:70:80에서 도착하는 요청과 일치합니다.
- IP 주소 블록에 대한 구문은 뒤에 슬래시와 접두사 크기가 오는 기본 IP 주소입니다.
  - **IPv4 예제**: 5.5.5.64/26은 5.5.5.127을 통해 5.5.5.64에서 도착하는 요청과 일치합니다.
  - **IPv6 예제**: 1:2:3:/48은 1:2:3:ffff:ffff:ffff:ffff:ffff를 통해 1:2:3:0:0:0:0:0에서 도착하는 요청과 일치합니다.
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale

[맨 위로 이동](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cookie-parameter"></a>쿠키 매개 변수
**일치**/**일치하지 않음** 옵션은 쿠키 매개 변수 일치 조건이 충족될지에 대한 조건을 결정합니다.
- **일치**: 이 일치 조건에 정의된 값 중 하나 이상과 일치하는 값으로 지정된 쿠키를 포함하는 요청이 필요합니다.
- **일치하지 않음**: 요청이 다음 조건 중 하나를 충족해야 합니다.
  - 지정된 쿠키를 포함하지 않습니다.
  - 지정된 쿠키를 포함하지만 해당 값은 이 일치 조건에 정의된 값과 일치하지 않습니다.
  
주요 정보:
- 쿠키 이름: 
  - 별표를 포함한 특수 문자는 쿠키 이름을 지정하는 경우에 지원되지 않습니다. 즉, 정확한 쿠키 이름 일치 항목만 비교할 수 있습니다.
  - 이 일치 조건의 인스턴스당 단일 쿠키 이름만 지정할 수 있습니다.
  - 쿠키 이름 비교는 대/소문자를 구분하지 않습니다.
- 쿠키 값: 
  - 각각을 하나의 공백으로 구분하여 여러 쿠키 값을 지정합니다.
  - 쿠키 값은 특수 문자를 활용할 수 있습니다. 
  - 와일드카드 문자를 지정하지 않은 경우 정확한 일치만 이 일치 조건을 충족합니다. 예를 들어 "Value"를 지정하면 "Value"와 일치하지만 "Value1" 또는 "Value2"와 일치하지 않습니다.
  - **대/소문자 무시** 옵션은 요청의 쿠키 값에 대해 대/소문자 구분 비교를 만들지 여부를 결정합니다.
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale

[맨 위로 이동](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cookie-parameter-regex"></a>쿠키 매개 변수 Regex
쿠키 매개 변수 Regex 일치 조건은 쿠키 이름과 값을 정의합니다. 정규식을 사용하여 원하는 쿠키 값을 정의할 수 있습니다. 

**일치**/**일치하지 않음** 옵션은 이 일치 조건이 충족될지에 대한 조건을 결정합니다.
- **일치**: 지정된 정규식과 일치하는 값으로 지정된 쿠키를 포함하는 요청이 필요합니다.
- **일치하지 않음**: 요청이 다음 조건 중 하나를 충족해야 합니다.
  - 지정된 쿠키를 포함하지 않습니다.
  - 지정된 쿠키를 포함하지만 해당 값은 지정된 정규식과 일치하지 않습니다.
  
주요 정보:
- 쿠키 이름: 
  - 별표를 포함한 정규식 및 특수 문자는 쿠키 이름을 지정하는 경우에 지원되지 않습니다. 즉, 정확한 쿠키 이름 일치 항목만 비교할 수 있습니다.
  - 이 일치 조건의 인스턴스당 단일 쿠키 이름만 지정할 수 있습니다.
  - 쿠키 이름 비교는 대/소문자를 구분하지 않습니다.
- 쿠키 값: 
  - 쿠키 값은 정규식을 활용할 수 있습니다.
  - **대/소문자 무시** 옵션은 요청의 쿠키 값에 대해 대/소문자 구분 비교를 만들지 여부를 결정합니다.
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale

[맨 위로 이동](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

--- 
### <a name="country"></a>국가
해당 국가 번호를 통해 국가를 지정할 수 있습니다. 요청이 발생하는 지역이 지정된 값과 "일치" 또는 "일치하지 않음"의 경우 이 조건이 충족될지 여부를 나타내는 옵션이 제공됩니다.

주요 정보:
- 각각을 하나의 공백으로 구분하여 여러 국가 번호를 지정합니다.
- 국가 번호를 지정하는 경우 와일드카드는 지원되지 않습니다.
- "EU"와 "AP" 국가 번호는 해당 영역에서 모든 IP 주소를 포함하지 않습니다.
- 특정 요청은 유효한 국가 번호를 반환하지 않을 수도 있습니다. 물음표(?)는 확인할 수 없는 유효한 국가 번호에 대한 요청과 일치합니다.
- 국가 번호는 대/소문자를 구분합니다.
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale

[맨 위로 이동](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="customer-origin"></a>고객 원본

주요 정보: 
- 콘텐츠가 Content Delivery Network URL 또는 선택한 고객 원본을 가리키는 에지 CNAME URL을 통해 요청되는지 여부에 관계 없이 고객 원본 일치 조건이 충족됩니다.
- 규칙에서 참조하는 고객 원본 구성은 고객 원본 페이지에서 삭제할 수 없습니다. 고객 원본 구성을 삭제하려고 하기 전에 다음 구성이 이를 참조하지 않는지 확인합니다.
  - 고객 원본 일치 조건
  - 에지 CNAME 구성
- 특정 일치 조건을 결합하는 데 AND IF문을 사용하지 마십시오. 예를 들어 CDN 원본 일치 조건과 고객 원본 일치 조건을 결합하면 일치될 수 없는 일치 패턴을 만듭니다. 이러한 이유로 두 개의 고객 원본 일치 조건은 AND IF문을 통해 결합될 수 없습니다.

[맨 위로 이동](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="device"></a>장치

장치 일치 조건은 속성에 따라 모바일 장치에서 생성되는 요청을 식별합니다. 모바일 장치 검색은 [WURFL](http://wurfl.sourceforge.net/)을 통해 이루어집니다. 다음 표는 WURFL 기능 및 Content Delivery Network 규칙 엔진에 대한 해당 변수를 나열합니다.
<br>
> [!NOTE] 
> 다음 변수는 **클라이언트 요청 헤더 수정** 및 **클라이언트 응답 헤더 수정** 기능에서 지원됩니다.

기능 | 변수 | 설명 | 샘플 값
-----------|----------|-------------|----------------
브랜드 이름 | %{wurfl_cap_brand_name} | 장치의 브랜드 이름을 나타내는 문자열입니다. | Samsung
장치 OS | %{wurfl_cap_device_os} | 장치에 설치된 운영 체제를 나타내는 문자열입니다. | IOS
장치 OS 버전 | %{wurfl_cap_device_os_version} | 장치에 설치된 운영 체제의 버전 번호를 나타내는 문자열입니다. | 1.0.1
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
점진적 다운로드 | %{wurfl_cap_progressive_download} | 장치가 오디오 및 비디오를 다운로드하는 중에 오디오 및 비디오 재생을 지원하는지 여부를 나타내는 부울입니다. | true
릴리스 날짜 | %{wurfl_cap_release_date} | 장치가 WURFL 데이터베이스에 추가된 연도와 월을 나타내는 문자열입니다.<br/><br/>형식: `yyyy_mm` | 2013_december
해상도 높이 | %{wurfl_cap_resolution_height} | 장치의 높이(픽셀)를 나타내는 정수입니다. | 768
해상도 너비 | %{wurfl_cap_resolution_width} | 장치의 너비(픽셀)를 나타내는 정수입니다. | 1024

[맨 위로 이동](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="edge-cname"></a>에지 Cname
주요 정보: 
- 사용 가능한 에지 CNAME의 목록은 HTTP 규칙 엔진이 구성되는 플랫폼에 해당하는 에지 CNAME 페이지에 구성되어 있는 항목으로 제한됩니다.
- 에지 CNAME 구성을 삭제하려고 하기 전에 에지 Cname 일치 조건이 이를 참조하지 않는지 확인합니다. 규칙에 정의된 에지 CNAME 구성은 에지 CNAME 페이지에서 삭제할 수 없습니다. 
- 특정 일치 조건을 결합하는 데 AND IF문을 사용하지 마십시오. 예를 들어 고객 원본 일치 조건과 에지 Cname 일치 조건을 결합하면 일치될 수 없는 일치 패턴을 만듭니다. 이러한 이유로 두 개의 에지 Cname 일치 조건은 AND IF문을 통해 결합될 수 없습니다.
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale

[맨 위로 이동](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="referring-domain"></a>참조 도메인
콘텐츠가 요청되었던 참조 페이지와 연결된 호스트 이름은 참조 도메인 조건이 충족되는지 여부를 결정합니다. 참조 호스트 이름이 지정된 값과 "일치" 또는 "일치하지 않음"의 경우 이 조건이 충족될지 여부를 나타내는 옵션이 제공됩니다.

주요 정보:
- 각각을 하나의 공백으로 구분하여 여러 호스트 이름을 지정합니다.
- 이 일치 조건은 특수 문자를 지원합니다.
- 지정된 값이 별표를 포함하지 않는 경우 참조 페이지의 호스트 이름에 대해 정확히 일치해야 합니다. 예를 들어 "mydomain.com"을 지정하면 "www.mydomain.com"과 일치하지 않습니다.
- **대/소문자 무시** 옵션은 대/소문자 구분 비교를 수행할지 여부를 결정합니다.
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale

[맨 위로 이동](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---  
### <a name="request-header-literal"></a>요청 헤더 리터럴
**일치**/**일치하지 않음** 옵션은 이 일치 조건이 충족될지에 대한 조건을 결정합니다.
- **일치**: 지정된 헤더를 포함하는 요청이 필요합니다. 해당 값은 이 일치 조건에 정의된 것과 일치해야 합니다.
- **일치하지 않음**: 요청이 다음 조건 중 하나를 충족해야 합니다.
  - 지정된 헤더를 포함하지 않습니다.
  - 지정된 헤더를 포함하지만 해당 값은 이 일치 조건에 정의된 값과 일치하지 않습니다.
  
주요 정보:
- 헤더 이름 비교는 항상 대/소문자를 구분하지 않습니다. **대/소문자 무시** 옵션은 헤더 값 비교의 대/소문자 구분을 결정합니다.
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale

[맨 위로 이동](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---  
### <a name="request-header-regex"></a>요청 헤더 Regex
**일치**/**일치하지 않음** 옵션은 요청 헤더 Regex 일치 조건이 충족될지에 대한 조건을 결정합니다.
- **일치**: 지정된 헤더를 포함하는 요청이 필요합니다. 해당 값은 지정된 정규식에 정의된 패턴과 일치해야 합니다.
- **일치하지 않음**: 요청이 다음 조건 중 하나를 충족해야 합니다.
  - 지정된 헤더를 포함하지 않습니다.
  - 지정된 헤더를 포함하지만 해당 값은 지정된 정규식과 일치하지 않습니다.

주요 정보:
- 헤더 이름: 
  - 헤더 이름 비교는 대/소문자를 구분하지 않습니다.
  - 헤더 이름의 공백은 "%20"으로 대체되어야 합니다. 
- 헤더 값: 
  - 헤더 값은 정규식을 활용할 수 있습니다.
  - **대/소문자 무시** 옵션은 헤더 값 비교의 대/소문자 구분을 결정합니다.
  - 하나 이상의 지정된 패턴과 일치하는 정확한 헤더 값만 이 조건을 만족합니다.
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale 

[맨 위로 이동](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-header-wildcard"></a>요청 헤더 와일드카드
**일치**/**일치하지 않음** 옵션은 요청 헤더 와일드카드 일치 조건이 충족될지에 대한 조건을 결정합니다.
- **일치**: 지정된 헤더를 포함하는 요청이 필요합니다. 해당 값은 이 일치 조건에 정의된 값 중 하나 이상과 일치해야 합니다.
- **일치하지 않음**: 요청이 다음 조건 중 하나를 충족해야 합니다.
  - 지정된 헤더를 포함하지 않습니다.
  - 지정된 헤더를 포함하지만 해당 값은 지정된 값과 일치하지 않습니다.
  
주요 정보:
- 헤더 이름: 
  - 헤더 이름 비교는 대/소문자를 구분하지 않습니다.
  - 헤더 이름의 공백은 "%20"으로 대체되어야 합니다. 또한 이 값을 사용하여 헤더 값에 공백을 지정할 수도 있습니다.
- 헤더 값: 
  - 헤더 값은 특수 문자를 활용할 수 있습니다.
  - **대/소문자 무시** 옵션은 헤더 값 비교의 대/소문자 구분을 결정합니다.
  - 하나 이상의 지정된 패턴과 일치하는 정확한 헤더 값만 이 조건을 만족합니다.
  - 각각을 하나의 공백으로 구분하여 여러 값을 지정합니다.
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale

[맨 위로 이동](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-method"></a>요청 메서드
선택한 요청 메서드를 통해 요청된 자산만 요청 메서드 조건을 충족합니다. 사용 가능한 요청 메서드는 다음과 같습니다.
- GET
- HEAD 
- POST 
- OPTIONS 
- PUT 
- 삭제 
- TRACE 
- CONNECT 

주요 정보:
- 기본적으로 GET 요청 메서드만 네트워크에서 캐시된 콘텐츠를 생성할 수 있습니다. 다른 모든 요청 메서드는 네트워크를 통해 프록시 설정됩니다.
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale

[맨 위로 이동](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-scheme"></a>요청 스키마
선택한 프로토콜을 통해 요청된 자산만 요청 스키마 조건을 충족합니다. 사용 가능한 프로토콜은 HTTP 및 HTTPS입니다.

주요 정보:
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale

[맨 위로 이동](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

## <a name="next-steps"></a>다음 단계
* [Azure Content Delivery Network 개요](cdn-overview.md)
* [규칙 엔진 참조](cdn-rules-engine-reference.md)
* [규칙 엔진 조건식](cdn-rules-engine-reference-conditional-expressions.md)
* [규칙 엔진 기능](cdn-rules-engine-reference-features.md)
* [규칙 엔진을 사용하여 기본 HTTP 동작 재정의](cdn-rules-engine.md)

