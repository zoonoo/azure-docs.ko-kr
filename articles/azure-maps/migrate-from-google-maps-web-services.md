---
title: Google Maps에서 웹 서비스 마이그레이션 | Microsoft Docs
description: Google Maps에서 Microsoft Azure Maps로 웹 서비스를 마이그레이션하는 방법에 대 한 자습서입니다.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 8fc3270d15f77576ff353a110d8a3a9a17c7144d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480022"
---
# <a name="migrate-web-service-from-google-maps"></a>Google Maps에서 웹 서비스 마이그레이션

Azure와 Google Maps는 REST 웹 서비스를 통해 공간 Api에 대 한 액세스를 제공 합니다. 이러한 플랫폼에 대 한 API 인터페이스는 비슷한 기능을 수행 하지만 다른 명명 규칙 및 응답 개체를 사용 합니다.

다음 표에서는 나열 된 Google Maps 서비스 Api와 유사한 기능을 제공 하는 Azure Maps 서비스 Api를 제공 합니다.

| Google Maps 서비스 API | Azure Maps service API                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Directions              | [Route](https://docs.microsoft.com/rest/api/maps/route)                               |
| 거리 매트릭스         | [경로 행렬](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) |
| 지오코딩               | [검색](https://docs.microsoft.com/rest/api/maps/search)                             |
| 배치 검색           | [검색](https://docs.microsoft.com/rest/api/maps/search)                             |
| 자동 완성 기능      | [검색](https://docs.microsoft.com/rest/api/maps/search)                             |
| 정적 맵              | [Render](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                 |
| 표준 시간대               | [표준 시간대](https://docs.microsoft.com/rest/api/maps/timezone)                        |

다음 서비스 Api는 현재 Azure Maps에서 사용할 수 없습니다.

- 상승
- 지리적 위치
- 세부 정보 및 사진을 배치 합니다. Azure Maps 검색 API에서 사용할 수 있는 전화 번호 및 웹 사이트 URL입니다.
- 맵 Url
- 도로 – 속도 제한 데이터는 Azure Maps의 경로 및 역방향 지 오 코딩 Api를 통해 사용할 수 있습니다.
- 정적 주소 보기

Azure Maps에는 관심을 가질 수 있는 몇 가지 추가 REST 웹 서비스가 있습니다.

- [공간 작업](https://docs.microsoft.com/rest/api/maps/spatial): 지 오 펜싱와 같은 복잡 한 공간 계산과 작업을 서비스에 오프 로드 합니다.
- [트래픽](https://docs.microsoft.com/rest/api/maps/traffic): 실시간 트래픽 흐름 및 인시던트 데이터에 액세스 합니다.

## <a name="geocoding-addresses"></a>지 오 코딩 주소

지 오 코딩 주소 (예: "1 Microsoft 방법, Redmond, WA")를 좌표로 변환 하는 프로세스입니다 (예:-122.1298, 위도: 47.64005). 그런 다음 좌표는 지도에 표식을 배치 하거나 지도를 가운데에 배치 하는 데 주로 사용 됩니다.

Azure Maps는 지 오 코딩 주소에 대해 몇 가지 메서드를 제공 합니다.

- [**자유 형식 주소 지 오 코딩**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): 단일 주소 문자열 (예: "1 Microsoft 방법, REDMOND, WA")을 지정 하 고 요청을 즉시 처리 합니다. 개별 주소를 신속 하 게 전환 해야 하는 경우에 권장 됩니다.
- [**구조적 주소 지 오 코딩**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): 주소, 구/군/시, 국가, 우편 번호 등의 단일 주소 부분을 지정 하 고 요청을 즉시 처리 합니다. 개별 주소를 신속 하 게 전환 하 고 해당 데이터를 개별 주소 파트로 구문 분석 해야 하는 경우에 권장 됩니다.
- [**Batch 주소 지 오 코딩**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): 최대 1만 개의 주소를 포함 하는 요청을 만들고 일정 시간 동안 처리 되도록 합니다. 모든 주소가 서버에서 병렬로 코딩 되 고 완료 되 면 전체 결과 집합을 다운로드할 수 있습니다. 지 오 코딩 large data sets에 권장 됩니다.
- [**유사 항목 검색**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy):이 API는 지 오 코딩 주소와 관련 된 검색 지점을 결합 합니다. 이 API는 주소, 장소, 랜드마크, 관심 지점 또는 관심 지점 범주 일 수 있는 자유 형식 문자열을 사용 하 여 즉시 요청을 처리 합니다. 이 API는 사용자가 같은 텍스트 상자에서 주소 또는 관심 지점을 검색할 수 있는 응용 프로그램에 권장 됩니다.
- [**유사 항목 일괄 검색**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): 최대 1만 개의 주소, 장소, 랜드마크 또는 취미를 포함 하는 요청을 만들고 일정 시간 동안 처리 합니다. 모든 데이터가 서버에서 병렬로 처리 되 고 완료 되 면 전체 결과 집합을 다운로드할 수 있습니다.

다음 표에서는 Google Maps API 매개 변수와 Azure Maps의 비교 가능한 API 매개 변수를 상호 참조 합니다.

| Google Maps API 매개 변수 | 비교 가능한 Azure Maps API 매개 변수  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` 및 `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality`-도시/타운<br/>`municipalitySubdivision`-환경, 하위/슈퍼 도시<br/>`countrySubdivision` 시/도<br/>`countrySecondarySubdivision`-관할지<br/>`countryTertiarySubdivision` 구역<br/>`countryCode`-두 문자 국가 코드 |
| `key`                       | `subscription-key` – [Azure Maps 설명서를 사용 하 여 인증](azure-maps-authentication.md) 을 참조 하세요. |
| `language`                  | `language` – [지원 되는 언어](supported-languages.md) 설명서를 참조 하세요.  |
| `region`                    | `countrySet`                       |

검색 서비스를 사용 하는 방법의 예는 [여기](how-to-search-for-address.md)에 설명 되어 있습니다. [검색 설명서에 대 한 모범 사례](how-to-use-best-practices-for-search.md) 를 검토 하세요.

> [!TIP]
> 지 오 코딩 및 유사 항목 검색 Api는 요청 URL에 `&amp;typeahead=true`을 추가 하 여 자동 완성 모드에서 사용할 수 있습니다. 이렇게 하면 입력 텍스트가 부분적인 것으로 예상 되 고 예측 모드로 전환 됩니다.

## <a name="reverse-geocode-a-coordinate"></a>역 geocode 좌표

역방향 지 오 코딩은 지리 좌표 (예:-122.1298, 위도: 47.64005)를 근사 주소 (예: "1 Microsoft 방식, Redmond, WA")로 변환 하는 프로세스입니다.

Azure Maps는 다음과 같은 몇 가지 역방향 지 오 코딩 메서드를 제공 합니다.

- [**주소 역방향 geocoder**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse): 대략적인 주소를 가져오고 요청을 즉시 처리 하는 단일 지리 좌표를 지정 합니다.
- [**교차 주소 역방향 geocoder**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): 인접 한 거리 정보 (예: 1 & 주)를 가져오는 단일 지리적 좌표를 지정 하 고 요청을 즉시 처리 합니다.
- [**일괄 처리 주소 역방향 geocoder**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): 최대 1만의 좌표를 포함 하는 요청을 만들고 일정 시간 동안 처리 되도록 합니다. 모든 데이터가 서버에서 병렬로 처리 되 고 완료 되 면 전체 결과 집합을 다운로드할 수 있습니다.

다음 표에서는 Google Maps API 매개 변수와 Azure Maps의 비교 가능한 API 매개 변수를 상호 참조 합니다.

| Google Maps API 매개 변수   | 비교 가능한 Azure Maps API 매개 변수   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` – [Azure Maps 설명서를 사용 하 여 인증](azure-maps-authentication.md) 을 참조 하세요. |
| `language`                  | `language` – [지원 되는 언어](supported-languages.md) 설명서를 참조 하세요.  |
| `latlng`                    | `query`  |
| `location_type`             | *해당 사항 없음*     |
| `result_type`               | `entityType`    |

[검색 설명서에 대 한 모범 사례](how-to-use-best-practices-for-search.md) 를 검토 하세요.

Azure Maps 역방향 지 오 코딩 API에는 앱을 마이그레이션할 때 통합 하는 데 유용할 수 있는 Google Maps에서 사용할 수 없는 몇 가지 추가 기능이 있습니다.

- 속도 제한 데이터를 검색 합니다.
- 로드맵 사용 정보: 로컬도로, arterial, 제한 된 액세스, 램프 등
- 좌표가 속하는 거리의 측면입니다.

## <a name="search-for-points-of-interest"></a>관심 지점 검색

장소 검색 API를 사용 하 여 Google Maps에서 관심 분야 데이터를 검색할 수 있습니다. 이 API는 관심 요소를 검색 하는 세 가지 방법을 제공 합니다.

- **텍스트에서 위치 찾기:** 이름, 주소 또는 전화 번호를 기준으로 관심 지점을 검색 합니다.
- **근접 한 검색**: 특정 위치의 특정 거리에 있는 관심사의 요소를 검색 합니다.
- **텍스트 검색:** 관심 지점 및 위치 정보를 포함 하는 자유 형식 텍스트를 사용 하 여 위치를 검색 합니다. 예: "뉴욕의 피자" 또는 "음식점 near main st".

Azure Maps는 관심 점에 대 한 몇 가지 검색 Api를 제공 합니다.

- [**Poi 검색**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): 이름으로 취미의 요소를 검색 합니다. 예를 들면 "Starbucks"입니다.
- [**Poi 범주 검색**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): 범주별 관심사 요소를 검색 합니다. 예를 들면 "식당"입니다.
- [**근접 한 검색**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): 특정 위치의 특정 거리에 있는 관심사의 요소를 검색 합니다.
- [**유사 항목 검색**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy):이 API는 지 오 코딩 주소와 관련 된 검색 지점을 결합 합니다. 이 API는 주소, 장소, 랜드마크, 관심 지점 또는 관심 지점 범주 일 수 있는 자유 형식 문자열을 사용 하 여 즉시 요청을 처리 합니다. 이 API는 사용자가 같은 텍스트 상자에서 주소 또는 관심 지점을 검색할 수 있는 응용 프로그램에 권장 됩니다.
- [**Geometry 내에서 검색**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): 지정 된 기 하 도형 (다각형) 내에 있는 관심사의 요소를 검색 합니다.
- [**경로를 따라 검색**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): 지정 된 경로 경로를 따라 하는 관심사의 요소를 검색 합니다.
- [**유사 항목 일괄 검색**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): 최대 1만 개의 주소, 장소, 랜드마크 또는 취미를 포함 하는 요청을 만들고 일정 시간 동안 처리 합니다. 모든 데이터가 서버에서 병렬로 처리 되 고 완료 되 면 전체 결과 집합을 다운로드할 수 있습니다.

현재 Azure Maps Google Maps 텍스트 검색 API와 비교할 수 있는 API가 없습니다.

> [!TIP]
> 요청 URL에 `&amp;typeahead=true`을 추가 하 여 자동 완성 모드로 POI 검색, POI 범주 검색 및 유사 항목 검색 Api를 사용할 수 있습니다. 이렇게 하면 입력 텍스트가 부분적인 것으로 예상 되 고 예측 모드로 전환 됩니다.

[검색 설명서에 대 한 모범 사례](how-to-use-best-practices-for-search.md) 를 검토 하세요.

### <a name="find-place-from-text"></a>텍스트에서 찾기

이름 또는 주소를 기준으로 관심사의 점에 대해 검색 하려면 Azure Maps [Poi 검색](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) 및 [유사 항목 검색](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) api를 사용할 수 있습니다.

다음 표에서는 Google Maps API 매개 변수와 Azure Maps의 비교 가능한 API 매개 변수를 상호 참조 합니다.

| Google Maps API 매개 변수 | 비교 가능한 Azure Maps API 매개 변수 |
|---------------------------|-------------------------------------|
| `fields`                  | *해당 사항 없음*                               |
| `input`                   | `query`                             |
| `inputtype`               | *해당 사항 없음*                               |
| `key`                     | `subscription-key` – [Azure Maps 설명서를 사용 하 여 인증](azure-maps-authentication.md) 을 참조 하세요. |
| `language`                | `language` – [지원 되는 언어](supported-languages.md) 설명서를 참조 하세요.  |
| `locationbias`            | `lat`, `lon`및 `radius`<br/>`topLeft` 및 `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>근접 검색

근처의 관심사는 [근접 한 검색](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) API를 사용 하 여 Azure Maps에서 검색할 수 있습니다.

다음 표에서는 Google Maps API 매개 변수와 Azure Maps의 비교 가능한 API 매개 변수를 상호 참조 합니다.

| Google Maps API 매개 변수 | 비교 가능한 Azure Maps API 매개 변수  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` – [Azure Maps 설명서를 사용 하 여 인증](azure-maps-authentication.md) 을 참조 하세요. |
| `keyword`                   | `categorySet` 및 `brandSet`        |
| `language`                  | `language` – [지원 되는 언어](supported-languages.md) 설명서를 참조 하세요.  |
| `location`                  | `lat` 및 `lon`                     |
| `maxprice`                  | *해당 사항 없음*                               |
| `minprice`                  | *해당 사항 없음*                               |
| `name`                      | `categorySet` 및 `brandSet`        |
| `opennow`                   | *해당 사항 없음*                               |
| `pagetoken`                 | `ofs` 및 `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *해당 사항 없음*                               |
| `type`                      | `categorySet –` [지원 되는 검색 범주](supported-search-categories.md) 설명서를 참조 하세요.   |

## <a name="calculate-routes-and-directions"></a>경로 및 방향 계산

Azure Maps를 사용 하 여 경로 및 방향을 계산할 수 있습니다. Azure Maps에는 Google Maps 라우팅 서비스와 같은 여러 기능이 있습니다.

- 도착 및 출발 시간
- 실시간 및 예측 기반 트래픽 경로를 사용 합니다.
- 다양 한 교통 모드 주행, 탐색, 자전거.

> [!NOTE]
> Azure Maps에는 모든 waypoints 좌표가 필요 합니다. 주소를 먼저 지 오 코딩 해야 합니다.

Azure Maps 라우팅 서비스는 경로 계산을 위해 다음과 같은 Api를 제공 합니다.

- [**경로 계산**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): 경로를 계산 하 고 요청을 즉시 처리 합니다. 이 API는 GET 및 POST 요청을 모두 지원 합니다. POST 요청은 많은 수의 waypoints를 지정 하거나 많은 경로 옵션을 사용 하 여 URL 요청이 너무 길어서 문제가 발생 하지 않도록 하는 경우에 권장 됩니다.
- [**일괄 처리 경로**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): 최대 1000 경로 요청을 포함 하는 요청을 만들고 일정 시간 동안 처리 되도록 합니다. 모든 데이터가 서버에서 병렬로 처리 되 고 완료 되 면 전체 결과 집합을 다운로드할 수 있습니다.
- [**모바일 서비스**](https://docs.microsoft.com/rest/api/maps/mobility): 공개 전송을 사용 하 여 경로 및 방향을 계산 합니다.

다음 표에서는 Google Maps API 매개 변수와 Azure Maps의 비교 가능한 API 매개 변수를 상호 참조 합니다.

| Google Maps API 매개 변수    | 비교 가능한 Azure Maps API 매개 변수  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` – 형식의 좌표 `"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key` – [Azure Maps 설명서를 사용 하 여 인증](azure-maps-authentication.md) 을 참조 하세요. |
| `language`                     | `language` – [지원 되는 언어](supported-languages.md) 설명서를 참조 하세요.   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | 해당 *없음* – 지 오 코딩 관련 기능입니다. Azure Maps 지 오 코딩 API를 사용 하는 경우 *countrySet* 매개 변수를 사용 합니다.  |
| `traffic_model`               | *해당 없음* – 트래픽 데이터를 *트래픽* 매개 변수와 함께 사용 해야 하는 경우에만 지정할 수 있습니다. |
| `transit_mode`                | [모바일 서비스 설명서](https://docs.microsoft.com/rest/api/maps/mobility) 를 참조 하세요. |
| `transit_routing_preference` | [모바일 서비스 설명서](https://docs.microsoft.com/rest/api/maps/mobility) 를 참조 하세요. |
| `units`                        | *N/A* – Azure Maps 메트릭 시스템만 사용 합니다.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> 기본적으로 Azure Maps 경로 API는 요약 (거리 및 시간)과 경로 경로에 대 한 좌표를 반환 합니다. `instructionsType` 매개 변수를 사용 하 여 턴 턴의 지침을 검색 합니다. `routeRepresentation` 매개 변수를 사용 하 여 요약 및 경로 경로를 필터링 할 수 있습니다.

Azure Maps 라우팅 API에는 앱을 마이그레이션할 때 통합 하는 데 도움이 될 수 있는 Google Maps에서 사용할 수 없는 여러 가지 추가 기능이 있습니다.

- 경로 유형 지원: 최단, 가장 빠름, trilling 및 대부분의 연료 효율성.
- 추가 여행 모드에 대 한 지원: bus, motorcycle, taxi, 트럭 및 van.
- 150 waypoints에 대 한 지원.
- 단일 요청에서 여러 이동 시간을 계산 합니다. 기록 트래픽, 라이브 트래픽, 트래픽 없음
- 추가 로드맵을 사용 하지 않도록 합니다. carpool, unpaved,도로는 이미 사용 하 고 있습니다.
- 방지할 사용자 지정 영역을 지정 합니다.
- 경로를 사용할 수 있는 수준 상승을 제한 합니다.
- 엔진 사양 기반 라우팅입니다. 나머지 연료/요금 및 엔진 사양을 기반으로 하 여 combustion 또는 전기 차량의 경로를 계산 합니다.
- 상업적 차량 경로 매개 변수 지원 차량 차원, 무게, axels의 수 및 화물 유형입니다.
- 최대 차량 속도를 지정 합니다.

이 외에도 Azure Maps의 경로 서비스는 지정 된 시간 또는 연료/요금이 있는 원본 지점에서 임의의 방향으로 이동할 수 있는 영역을 포함 하는 다각형을 생성 하는 등시성 라고도 하는 라우팅할 수 있는 [범위 계산](https://docs.microsoft.com/rest/api/maps/route/getrouterange)도 지원 합니다.

## <a name="retrieve-a-map-image"></a>지도 이미지 검색

Azure Maps는 데이터를 오버레이 하는 정적 지도 이미지를 렌더링 하기 위한 API를 제공 합니다. Azure Maps [맵 이미지 렌더링](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) Api는 Google Maps의 정적 맵 api와 비교할 수 있습니다.

> [!NOTE]
> Azure Maps에는 중심, 모든 표식 및 경로 위치가 "경도, 위도" 형식의 좌표가 되어야 하지만 Google Maps에는 "위도, 경도" 형식이 사용 됩니다. 주소를 먼저 지 오 코딩 해야 합니다.

다음 표에서는 Google Maps API 매개 변수와 Azure Maps의 비교 가능한 API 매개 변수를 상호 참조 합니다.

| Google Maps API 매개 변수 | 비교 가능한 Azure Maps API 매개 변수  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` – URL 경로의 일부로 지정 됩니다. 현재는 PNG만 지원 됩니다. |
| `key`                       | `subscription-key` – [Azure Maps 설명서를 사용 하 여 인증](azure-maps-authentication.md) 을 참조 하세요. |
| `language`                  | `language` – [지원 되는 언어](supported-languages.md) 설명서를 참조 하세요.  |
| `maptype`                   | `layer` 및 `style` – [지원 되는 맵 스타일](supported-map-styles.md) 설명서를 참조 하세요. |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | 해당 *없음* – 지 오 코딩 관련 기능입니다. Azure Maps 지 오 코딩 API를 사용 하는 경우 `countrySet` 매개 변수를 사용 합니다.  |
| `scale`                     | *해당 사항 없음*                              |
| `size`                      | `width` 및 `height` – 크기가 최대 8192x8192 일 수 있습니다. |
| `style`                     | *해당 사항 없음*                              |
| `visible`                   | *해당 사항 없음*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> Azure Maps는 Google Maps에서 사용 되는 맵 타일의 두 배 크기를 가진 타일 시스템을 사용 합니다. 따라서 Azure Maps의 확대/축소 수준 값은 Google Maps와 비교 하 여 Azure Maps에서 한 확대/축소 수준으로 표시 됩니다. 이를 보정 하기 위해 마이그레이션하는 요청에서 확대/축소 수준을 낮춥니다.

자세한 내용은 [지도 이미지 렌더링 API에 대 한 방법 가이드](how-to-render-custom-data.md)를 참조 하세요.

Azure Maps render service는 정적 지도 이미지를 생성 하는 것 외에도 래스터 (PNG) 및 벡터 형식의 지도 타일에 직접 액세스 하는 기능을 제공 합니다.

- [**지도 타일**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): 기본 지도 (도로, 경계, 배경)의 래스터 (PNG) 및 벡터 타일을 검색 합니다.
- [**지도 이미지 타일**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile): 항공 및 위성 이미지 타일을 검색 합니다.

> [!TIP]
> 대부분의 Google Maps 응용 프로그램은 비용 절약 방법으로 대화형 지도 환경에서 몇 년 전에 정적 지도 이미지로 전환 했습니다. Azure Maps 지도 타일 부하에 따라 요금이 청구 되므로 웹 SDK에서 대화형 지도 컨트롤을 사용 하는 것이 훨씬 더 비용 효율적입니다. Azure Maps 지도 타일은 크고, 일반적으로는 정적 맵과 동일한 지도 보기를 다시 만들고 맵 타일이 브라우저에 의해 자동으로 캐시 됩니다. 따라서 대화형 맵 컨트롤은 종종 정적 지도 뷰를 재현할 때 트랜잭션의 일부만 생성 합니다. 패닝 및 확대/축소는 더 많은 타일을 로드 하지만 원하는 경우이 동작을 사용 하지 않도록 설정 하는 옵션을 지도 컨트롤에 있습니다. 또한 대화형 지도 컨트롤은 정적 map 서비스에서 수행 하는 것 보다 훨씬 더 많은 시각화 옵션을 제공 합니다.

### <a name="marker-url-parameter-format-comparison"></a>표식 URL 매개 변수 형식 비교

**이전: Google Maps**

Google Maps에서 URL의 `markers` 매개 변수를 사용 하 여 정적 지도 이미지에 추가할 수 있습니다. `markers` 매개 변수는 아래와 같이 해당 스타일을 사용 하 여 맵에서 렌더링할 스타일 및 위치 목록을 가져옵니다.

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

다른 스타일 및 위치 집합을 사용 하 여 URL에 `markers` 매개 변수를 추가 하 여 추가 스타일을 사용할 수 있습니다.

표식 위치는 "위도, 경도" 형식으로 지정 됩니다.

Google Maps의 마커 스타일은 `optionName:value`형식으로 추가 됩니다. 여기에는 "optionName1: value1\|optionName2: value2"와 같이 파이프 (\|) 문자로 구분 된 여러 스타일이 있습니다. 옵션 이름 및 값은 콜론 (:)으로 구분 됩니다. 다음 스타일 옵션 이름을 사용 하 여 Google Maps에서 표식 스타일을 지정할 수 있습니다.

- `color` – 기본 표식 아이콘의 색입니다. 24 비트 16 진수 색 (`0xrrggbb`) 이거나 다음 값 중 하나일 수 있습니다. `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label` – 아이콘 위에 표시할 단일 대문자 영숫자입니다.
- `size`-표식의 크기입니다. `tiny`, `mid` 또는 `small`일 수 있습니다.

사용자 지정 아이콘은 다음 스타일 옵션 이름을 사용 하 여 Google Maps에서 사용할 수도 있습니다.

- `anchor` – 좌표에 아이콘 이미지를 맞추는 방법을 지정 합니다. 는 픽셀 (x, y) 값 또는 다음 값 중 하나일 수 있습니다. `top`, `bottom`, `left`, `right`, `center`, `topleft`, `topright`, `bottomleft`또는 `bottomright`입니다.
- `icon` – 아이콘 이미지를 가리키는 URL입니다.

예를 들어 Google Maps에서 다음 URL 매개 변수를 사용 하 여 좌표 (경도:-110, 위도: 45)에서 지도에 빨강 크기의 마커를 추가할 수 있습니다.

```
&markers=color:red|size:mid|45,-110
```

<center>

Google Maps 표식 ![](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**이후: Azure Maps**

URL에 `pins` 매개 변수를 지정 하 여 Azure Maps 표식을 정적 지도 이미지에 추가할 수도 있습니다. Google Maps와 마찬가지로이 매개 변수에는 스타일 및 위치 목록을 지정할 수 있으며, 다른 스타일의 표식을 지원 하기 위해 `pins` 매개 변수를 여러 번 지정할 수 있습니다.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

다른 스타일 및 위치 집합을 사용 하 여 URL에 `pins` 매개 변수를 추가 하 여 추가 스타일을 사용할 수 있습니다.

고정 위치를 사용 하는 경우에는 Azure Maps 좌표를 "경도 위도" 형식으로 지정 하는 반면 Google Maps는 "위도, 경도" 형식을 사용 합니다. 또한 Azure Maps에서 경도와 위도를 구분 하는 공백은 아닌 공백이 있습니다.

`iconType` 값은 만들 pin의 유형을 지정 하 고 다음 값을 가질 수 있습니다.

- `default` – 기본 pin 아이콘입니다.
- `none` – 아이콘이 표시 되지 않고 레이블만 렌더링 됩니다.
- `custom` – 사용자 지정 아이콘을 사용할 수 있도록 지정 합니다. 아이콘 이미지를 가리키는 URL은 고정 위치 정보 뒤에 `pins` 매개 변수의 끝에 추가할 수 있습니다.
- `{udid}` – Azure Maps 데이터 저장소 플랫폼에 저장 된 아이콘의 UDID (고유 데이터 ID)입니다.

Azure Maps의 Pin 스타일은이 `iconType|optionName1Value1|optionName2Value2`같이 파이프 (\|) 문자로 구분 된 여러 스타일의 `optionNameValue`형식으로 추가 됩니다. 옵션 이름 및 값은 구분 되지 않습니다. Azure Maps에서 표식 스타일을 지정할 때 사용할 수 있는 스타일 옵션 이름은 다음과 같습니다.

- `al` – 표식의 불투명도 (알파)를 지정 합니다. 0에서 1 사이의 숫자가 될 수 있습니다.
- `an` – 고정 앵커를 지정 합니다. "X y" 형식으로 지정 된 x 및 y 픽셀 값입니다.
- `co` – pin의 색입니다. `FFFFFF``000000` 24 비트 16 진수 색 이어야 합니다.
- `la` – 레이블 앵커를 지정 합니다. "X y" 형식으로 지정 된 x 및 y 픽셀 값입니다.
- `lc` – 레이블의 색입니다. 24-16 진수 색 이어야 합니다. `FFFFFF``000000` 합니다.
- `ls` – 레이블의 크기 (픽셀)입니다. 0 보다 큰 숫자가 될 수 있습니다.
- `ro` – 아이콘을 회전 하는 값 (도)입니다. -360과 360 사이의 숫자가 될 수 있습니다.
- `sc` – 고정 아이콘의 배율 값입니다. 0 보다 큰 숫자가 될 수 있습니다.

레이블 값은 위치 목록의 모든 표식에 적용 되는 단일 레이블 값을 포함 하지 않고 각 pin 위치에 대해 지정 됩니다. 레이블 값은 여러 문자로 된 문자열일 수 있으며, 스타일 또는 위치 값으로 잘못 된 문자로 오인 되지 않도록 작은따옴표로 래핑됩니다.

예를 들어 Azure Maps에서 다음 URL 매개 변수를 사용 하 여 좌표 (경도:-122.349300, 위도: 47.620180)에 있는 아이콘을 "공간 니 들" 레이블 (15 50)로`FF0000`추가 하는 것이 가능 합니다.

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![Azure Maps 표식](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

다음 예제에서는 레이블 값 ' 1 ', ' 2 ', ' 3 '이 포함 된 pin 3 개를 추가 합니다.

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

여러 마커를 Azure Maps ![](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>경로 URL 매개 변수 형식 비교

**이전: Google Maps**

Google 지도에서 URL의 `path` 매개 변수를 사용 하 여 정적 지도 이미지에 선 및 다각형을 추가할 수 있습니다. `path` 매개 변수는 아래와 같이 해당 스타일을 사용 하 여 맵에서 렌더링할 스타일 및 위치 목록을 가져옵니다.

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

다른 스타일 및 위치 집합을 사용 하 여 URL에 `path` 매개 변수를 추가 하 여 추가 스타일을 사용할 수 있습니다.

Google Maps의 경로 위치는 `latitude1,longitude1|latitude2,longitude2|…`형식으로 지정 됩니다. 경로는 인코딩할 수도 있고, 요소에 대 한 주소를 포함할 수도 있습니다.

Google Maps의 경로 스타일은 `optionName:value`형식으로 추가 되며,이 `optionName1:value1|optionName2:value2`와 같이 파이프 (\|) 문자로 구분 된 여러 스타일을 사용 합니다. 옵션 이름 및 값은 콜론 (:)으로 구분 됩니다. 다음 스타일 옵션 이름은 Google Maps의 스타일 경로에 사용할 수 있습니다.

- `color` – 경로 또는 다각형 윤곽선의 색입니다. 은 24 비트 16 진수 색 (`0xrrggbb`), 32 비트 16 진수 색 (`0xrrggbbbaa`) 또는 다음 값 중 하나일 수 있습니다. 검정, 밤색, 녹색, 자주색, 노랑, 파랑, 회색, 주황, 빨강, 흰색입니다.
- `fillColor` – 경로 영역을 채울 색 (다각형)입니다. 은 24 비트 16 진수 색 (`0xrrggbb`), 32 비트 16 진수 색 (`0xrrggbbbaa`) 또는 다음 값 중 하나일 수 있습니다. 검정, 밤색, 녹색, 자주색, 노랑, 파랑, 회색, 주황, 빨강, 흰색입니다.
- `geodesic` – 경로가 지구 뒤에 오는 선 이어야 하는지 여부를 나타냅니다.
- `weight` – 경로 선의 두께 (픽셀)입니다.

예를 들어 Google Maps에서 다음 URL 매개 변수를 사용 하 여 좌표 (경도:-110, 위도: 45 및 경도:-100, 위도: 50) 사이에 50% 불투명도와 두께가 4 픽셀인 빨강 선을 지도에 추가할 수 있습니다.

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![Google Maps 폴리라인](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**이후: Azure Maps**

Azure Maps에서는 URL에 `path` 매개 변수를 지정 하 여 정적 지도 이미지에 다각형을 추가할 수도 있습니다. Google Maps와 마찬가지로이 매개 변수에는 스타일 및 위치 목록을 지정할 수 있으며, 여러 개의 원, 선 및 다른 스타일의 다각형을 렌더링 하기 위해 `path` 매개 변수를 여러 번 지정할 수 있습니다.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

경로 위치에 도달 하면 Azure Maps 좌표가 "경도 위도" 형식 이어야 하며, Google Maps는 "위도, 경도" 형식을 사용 합니다. 또한 Azure Maps에서 경도와 위도를 구분 하는 공백은 아닌 공백이 있습니다. Azure Maps는 요소에 대해 인코딩된 경로 또는 주소를 지원 하지 않습니다. [여기](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage)에 설명 된 대로 Azure Maps 데이터 저장소 API에 GeoJSON 채우기로 더 큰 데이터 집합을 업로드할 수 있습니다.

Azure Maps의 경로 스타일은이 `optionName1Value1|optionName2Value2`같이 파이프 (\|) 문자로 구분 된 여러 스타일의 `optionNameValue`형식으로 추가 됩니다. 옵션 이름 및 값은 구분 되지 않습니다. Azure Maps에서 경로 스타일을 지정할 때 사용할 수 있는 스타일 옵션 이름은 다음과 같습니다.

- `fa`-다각형을 렌더링할 때 사용 되는 채우기 색 불투명도 (알파)입니다. 0에서 1 사이의 숫자가 될 수 있습니다.
- `fc`-다각형 영역을 렌더링 하는 데 사용 되는 채우기 색입니다.
- `la` – 선 및 다각형 윤곽선을 렌더링할 때 사용 되는 선 색 불투명도 (알파)입니다. 0에서 1 사이의 숫자가 될 수 있습니다.
- `lc` – 선 및 다각형 윤곽선을 렌더링 하는 데 사용 되는 선 색입니다.
- `lw` – 줄의 너비 (픽셀)입니다.
- `ra` – 원 반지름을 미터 단위로 지정 합니다.

예를 들어 Azure Maps에서 다음 URL 매개 변수를 사용 하 여 좌표 (경도:-110, 위도: 45 및 경도:-100, 위도: 50) 사이의 맵에 50% 불투명도 및 두께 4 픽셀의 빨간색 선을 추가할 수 있습니다.

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Azure Maps 폴리라인](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>거리 행렬 계산

Azure Maps는 이동 시간 및 위치 집합 간의 거리를 거리 행렬로 계산 하기 위한 API를 제공 합니다. Azure Maps distance matrix API는 Google Maps의 거리 매트릭스 API와 유사 합니다.

- [**경로 행렬**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): 원본 및 대상 집합의 이동 시간과 거리를 비동기적으로 계산 합니다. 요청당 최대 700 셀 (원본 수와 대상 수를 곱한 수)이 지원 됩니다. 이러한 제약 조건을 염두에 두면 가능한 행렬 차원의 예는 700x1, 50x10, 10x10, 28x25, 10x70입니다.

> [!NOTE]
> 거리 행렬 API에 대 한 요청은 요청 본문에서 원본 및 대상 정보를 포함 하는 POST 요청을 통해서만 수행할 수 있습니다. 또한 Azure Maps 모든 원본 및 대상이 좌표가 되어야 합니다. 주소를 먼저 지 오 코딩 해야 합니다.

다음 표에서는 Google Maps API 매개 변수와 Azure Maps의 비교 가능한 API 매개 변수를 상호 참조 합니다.

| Google Maps API 매개 변수      | 비교 가능한 Azure Maps API 매개 변수  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` – POST 요청 본문에서 GeoJSON로 지정 합니다. |
| `key`                          | `subscription-key` – [Azure Maps 설명서를 사용 하 여 인증](azure-maps-authentication.md) 을 참조 하세요. |
| `language`                     | `language` – [지원 되는 언어](supported-languages.md) 설명서를 참조 하세요.  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` – POST 요청 본문에서 GeoJSON로 지정 합니다.  |
| `region`                       | 해당 *없음* – 지 오 코딩 관련 기능입니다. Azure Maps 지 오 코딩 API를 사용 하는 경우 `countrySet` 매개 변수를 사용 합니다. |
| `traffic_model`                | *N/A* – 트래픽 데이터를 `traffic` 매개 변수와 함께 사용 해야 하는 경우에만 지정할 수 있습니다. |
| `transit_mode`                 | *해당 없음* -전송 기반 거리 매트릭스가 현재 지원 되지 않습니다.  |
| `transit_routing_preference`   | *해당 없음* -전송 기반 거리 매트릭스가 현재 지원 되지 않습니다.  |
| `units`                        | *N/A* – Azure Maps 메트릭 시스템만 사용 합니다. |

> [!TIP]
> Azure Maps routing API (트럭 라우팅, 엔진 사양, 방지 ...)에서 사용할 수 있는 모든 고급 라우팅 옵션은 Azure Maps distance matrix API에서 지원 됩니다.

## <a name="get-a-time-zone"></a>표준 시간대 가져오기

Azure Maps는 좌표가 있는 표준 시간대를 검색 하기 위한 API를 제공 합니다. Azure Maps 표준 시간대 API는 Google Maps의 표준 시간대 API와 비교할 수 있습니다.

- [**좌표의 표준**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates)시간대: 좌표를 지정 하 고 해당 표준 시간대에 대 한 세부 정보를 가져옵니다.

다음 표에서는 Google Maps API 매개 변수와 Azure Maps의 비교 가능한 API 매개 변수를 상호 참조 합니다.

| Google Maps API 매개 변수 | 비교 가능한 Azure Maps API 매개 변수   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` – [Azure Maps 설명서를 사용 하 여 인증](azure-maps-authentication.md) 을 참조 하세요.       |
| `language`                  | `language` – [지원 되는 언어](supported-languages.md) 설명서를 참조 하세요.    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

이 외에도 Azure Maps 플랫폼은 표준 시간대 이름 및 Id로 변환 하는 데 도움이 되는 다양 한 추가 표준 시간대 Api를 제공 합니다.

- [**ID 별 표준**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid)시간대: 지정 된 IANA 표준 시간대 ID에 대 한 현재, 과거 및 미래의 표준 시간대 정보를 반환 합니다.
- [**표준 시간대 열거형 iana**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): iana 표준 시간대 id의 전체 목록을 반환 합니다. IANA 서비스에 대 한 업데이트는 1 일 이내에 시스템에 반영 됩니다.
- [**표준 시간대 열거형 windows**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): Windows 표준 시간대 id의 전체 목록을 반환 합니다.
- [**표준 시간대 IANA 버전**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): Azure Maps에서 사용 하는 현재 IANA 버전 번호를 반환 합니다.
- [**표준 시간대 windows-iana**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): 유효한 Windows 표준 시간대 id를 지정 하 여 해당 IANA id를 반환 합니다. 단일 Windows ID에 대해 여러 IANA Id를 반환할 수 있습니다.

## <a name="client-libraries"></a>클라이언트 라이브러리

Azure Maps는 다음과 같은 프로그래밍 언어에 대 한 클라이언트 라이브러리를 제공 합니다.

- JavaScript, TypeScript, node.js – [설명서](how-to-use-services-module.md) \| [NPM 패키지](https://www.npmjs.com/package/azure-maps-rest)

다른 프로그래밍 언어에 대 한 오픈 소스 클라이언트 라이브러리:

- .NET Standard 2.0 – [GitHub 프로젝트](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet 패키지](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>추가 리소스

다음은 Azure Maps REST 서비스에 대 한 몇 가지 추가 설명서 및 리소스입니다.

- [검색에 대 한 모범 사례](how-to-use-best-practices-for-search.md)
- [주소 검색](how-to-search-for-address.md)
- [Azure Maps REST 서비스 API 참조 설명서](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>다음 단계

Azure Maps REST 서비스에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [검색 서비스 사용에 대 한 모범 사례](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [서비스 모듈을 사용 하는 방법 (웹 SDK)](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [코드 샘플](https://docs.microsoft.com/samples/browse/?products=azure-maps)