---
title: 지원 되는 데이터 형식 세부 정보 | Microsoft Azure 맵
description: 공간 IO 모듈에서 구분 된 공간 데이터를 구문 분석 하는 방법에 대해 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3353620f1751e939a04543115fe704555fb3bc21
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334094"
---
# <a name="supported-data-format-details"></a>지원 되는 데이터 형식 세부 정보

이 문서에서는 모든 XML 태그 및 잘 알려진 텍스트 기 하 도형 형식에 대 한 읽기 및 쓰기 지원에 대 한 세부 정보를 제공 합니다. 또한 공간 IO 모듈에서 구분 된 공간 데이터를 구문 분석 하는 방법도 자세히 설명 합니다.

## <a name="supported-xml-namespaces"></a>지원 되는 XML 네임 스페이스

공간 IO 모듈은 다음 네임 스페이스의 XML 태그를 지원 합니다.

| 네임 스페이스 접두사 | 네임스페이스 URI   | 메모                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | GeoRSS 파일에서 읽기 전용 지원.           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | GeoRSS 파일에서 읽기 전용 지원.                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | .GPX) 파일에서 읽기 전용 지원. DisplayColor를 구문 분석 하 고 사용 합니다. 셰이프 메타 데이터에 추가 된 다른 모든 속성입니다. |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | .GPX) 파일에서 지원 됩니다. 선 색을 사용 합니다. |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | 읽기 전용입니다. GeoRSS는 Atom 형식을 사용 하 여 작성 합니다.              |

## <a name="supported-xml-elements"></a>지원 되는 XML 요소

공간 IO 모듈은 다음과 같은 XML 요소를 지원 합니다. 지원 되지 않는 모든 XML 태그는 JSON 개체로 변환 됩니다. 그런 다음 각 태그는 부모 셰이프 또는 계층의 `properties` 필드에 속성으로 추가 됩니다.

### <a name="kml-elements"></a>KML 요소

공간 IO 모듈은 다음과 같은 KML 요소를 지원 합니다.

| 요소 이름         | 읽기    | 쓰기   | 메모                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | 부분 | 예     | 개체가 구문 분석 되었지만 셰이프 위치를 지정 하는 데 사용 되지 않습니다.                                                                    |
| `AddressDetails`     | 부분 | 아니요      | 개체가 구문 분석 되었지만 셰이프 위치를 지정 하는 데 사용 되지 않습니다.                                                                    |
| `atom:author`        | 예     | 예     |                                                                                                                            |
| `atom:link`          | 예     | 예     |                                                                                                                            |
| `atom:name`          | 예     | 예     |                                                                                                                            |
| `BalloonStyle`       | 부분 | 부분 | `displayMode`는 지원되지 않습니다. 로 변환 `PopupTemplate`됩니다. 쓰려면 `popupTemplate` 속성을 작성 하려는 기능의 속성으로 추가 합니다. |
| `begin`              | 예     | 예     |                                                                                                                            |
| `color`              | 예     | 예     | 및 `#AABBGGRR` 를 `#BBGGRR`포함 합니다. CSS 색 문자열로 구문 분석 됨                                                           |
| `colorMode`          | 예     | 아니요      |                                                                                                                            |
| `coordinates`        | 예     | 예     |                                                                                                                            |
| `Data`               | 예     | 예     |                                                                                                                            |
| `description`        | 예     | 예     |                                                                                                                            |
| `displayName`        | 예     | 예     |                                                                                                                            |
| `Document`           | 예     | 예     |                                                                                                                            |
| `drawOrder`          | 부분 | 아니요      | 접지 오버레이를 읽고이를 정렬 하는 데 사용 합니다. 
| `east`               | 예     | 예     |                                                                                                                            |
| `end`                | 예     | 예     |                                                                                                                            |
| `ExtendedData`       | 예     | 예     | 형식의 `$[dataName]`형식화 `Data`되지 `SimpleData` 않은 `Schema`, 또는 및 엔터티 대체를 지원 합니다.                      |
| `extrude`            | 부분 | 부분 | 다각형에만 지원 됩니다. 다른 높이의 다각형이 있는 다중 기 하 도형은 개별 기능으로 구분 됩니다. 선 스타일이 지원 되지 않습니다. 가 중에서 0 인 다각형이 플랫 다각형으로 렌더링 됩니다. 읽을 때 외부 링의 첫 번째 좌표에 대 한 고가는 다각형의 height 속성으로 추가 됩니다. 그런 다음 첫 번째 좌표의 고도는 지도에서 다각형을 렌더링 하는 데 사용 됩니다. |
| `fill`               | 예     | 예     |                                                                                                                            |
| `Folder`             | 예     | 예     |                                                                                                                            |
| `GroundOverlay`      | 예     | 예     | `color`지원 되지 않음                                                                                                   |
| `heading`            | 부분 | 아니요      | 구문 분석 되었지만에서 `SimpleDataLayer`렌더링 되지 않습니다. 셰이프의 속성에 데이터를 저장 하는 경우에만 씁니다.                 |
| `hotSpot`            | 예     | 부분 | 셰이프의 속성에 데이터를 저장 하는 경우에만 씁니다. 단위는 "픽셀"로만 출력 됩니다.                         |
| `href`               | 예     | 예     |                                                                                                                            |
| `Icon`               | 부분 | 부분 | 구문 분석 되었지만에서 `SimpleDataLayer`렌더링 되지 않습니다. 는 URI 데이터를 포함 하는 경우에만 셰이프의 icon 속성을 씁니다. `href`만 지원됩니다. |
| `IconStyle`          | 부분 | 부분 | `icon``colorMode` `hotspots` , `heading`, 및 값은 구문 분석 되지만에서 렌더링 되지 않습니다.`SimpleDataLayer`         |
| `innerBoundaryIs`    | 예     | 예     |                                                                                                                            |
| `kml`                | 예     | 예     |                                                                                                                            |
| `LabelStyle`         | 아니요      | 아니요      |                                                                                                                            |
| `LatLonBox`          | 예     | 예     |                                                                                                                            |
| `gx:LatLonQuad`      | 예     | 예     |                                                                                                                            |
| `LinearRing`         | 예     | 예     |                                                                                                                            |
| `LineString`         | 예     | 예     |                                                                                                                            |
| `LineStyle`          | 예     | 예     | `colorMode`는 지원되지 않습니다.                                                                                         |
| `Link`               | 예     | 아니요      | `href` 속성만 네트워크 링크에 대해 지원 됩니다.                                                                   |
| `MultiGeometry`      | 부분 | 부분 | 읽을 때 개별 기능으로 구분할 수 있습니다.                                                                     |
| `name`               | 예     | 예     |                                                                                                                            |
| `NetworkLink`        | 예     | 아니요      | 링크는 문서와 동일한 도메인에 있어야 합니다.                                                                  |
| `NetworkLinkControl` | 아니요      | 아니요      |                                                                                                                            |
| `north`              | 예     | 예     |                                                                                                                            |
| `open`               | 예     | 예     |                                                                                                                            |
| `outerBoundaryIs`    | 예     | 예     |                                                                                                                            |
| `outline`            | 예     | 예     |                                                                                                                            |
| `overlayXY`          | 아니요      | 아니요      |                                                                                                                            |
| `Pair`               | 부분 | 아니요      | 의 `normal` 스타일만 지원 `StyleMap` 됩니다. `highlight`는 지원되지 않습니다.                                   |
| `phoneNumber`        | 예     | 예     |                                                                                                                            |
| `PhotoOverlay`       | 아니요      | 아니요      |                                                                                                                            |
| `Placemark`          | 예     | 예     |                                                                                                                            |
| `Point`              | 예     | 예     |                                                                                                                            |
| `Polygon`            | 예     | 예     |                                                                                                                            |
| `PolyStyle`          | 예     | 예     |                                                                                                                            |
| `Region`             | 부분 | 부분 | `LatLongBox`는 문서 수준에서 지원 됩니다.                                                                      |
| `rotation`           | 아니요      | 아니요      |                                                                                                                            |
| `rotationXY`         | 아니요      | 아니요      |                                                                                                                            |
| `scale`              | 아니요      | 아니요      |                                                                                                                            |
| `Schema`             | 예     | 예     |                                                                                                                            |
| `SchemaData`         | 예     | 예     |                                                                                                                            |
| `schemaUrl`          | 부분 | 예     | 은 KMZ에 포함 되지 않은 외부 문서에서 스타일을 로드 하는 것을 지원 하지 않습니다.                             |
| `ScreenOverlay`      | 아니요      | 아니요      |                                                                                                                            |
| `screenXY`           | 아니요      | 아니요      |                                                                                                                            |
| `SimpleData`         | 예     | 예     |                                                                                                                            |
| `SimpleField`        | 예     | 예     |                                                                                                                            |
| `size`               | 아니요      | 아니요      |                                                                                                                            |
| `Snippet`            | 부분 | 부분 | `maxLines`특성이 무시 됩니다.                                                                                  |
| `south`              | 예     | 예     |                                                                                                                            |
| `Style`              | 예     | 예     |                                                                                                                            |
| `StyleMap`           | 부분 | 아니요      | 의 일반 스타일만 지원 `StyleMap` 됩니다.                                                                        |
| `styleUrl`           | 부분 | 예     | 외부 스타일 Url은 지원 되지 않습니다.                                                                         |
| `text`               | 예     | 예     | 의 `$[geDirections]` 대체가 지원 되지 않음                                                                          |
| `textColor`          | 예     | 예     |                                                                                                                            |
| `TimeSpan`           | 예     | 예     |                                                                                                                            |
| `TimeStamp`          | 예     | 예     |                                                                                                                            |
| `value`              | 예     | 예     |                                                                                                                            |
| `viewRefreshMode`    | 부분 | 아니요      |  WMS 서비스를 가리키면 접지 오버레이에 대해서만 `onStop` 지원 됩니다. 는 맵이 `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` 이동 될 때 URL에 추가 하 고 업데이트 합니다.  |
| `visibility`         | 예     | 예     |                                                                                                                            |
| `west`               | 예     | 예     |                                                                                                                            |
| `when`               | 예     | 예     |                                                                                                                            |
| `width`              | 예     | 예     |                                                                                                                            |

### <a name="georss-elements"></a>GeoRSS 요소

공간 IO 모듈은 다음과 같은 GeoRSS 요소를 지원 합니다.

| 요소 이름             | 읽기    | 쓰기 | 메모                                                                                          |
|--------------------------|---------|-------|------------------------------------------------------------------------------------------------|
| `atom:author`            | 예     | 예   |                                                                                                |
| `atom:category`          | 예     | 예   |                                                                                                |
| `atom:content`           | 예     | 예   |                                                                                                |
| `atom:contributor`       | 예     | 예   |                                                                                                |
| `atom:email`             | 예     | 예   |                                                                                                |
| `atom:entry`             | 예     | 예   |                                                                                                |
| `atom:feed`              | 예     | 예   |                                                                                                |
| `atom:icon`              | 예     | 예   |                                                                                                |
| `atom:id`                | 예     | 예   |                                                                                                |
| `atom:link`              | 예     | 예   |                                                                                                |
| `atom:logo`              | 예     | 예   |                                                                                                |
| `atom:name`              | 예     | 예   |                                                                                                |
| `atom:published`         | 예     | 예   |                                                                                                |
| `atom:rights`            | 예     | 예   |                                                                                                |
| `atom:source`            | 예     | 예   |                                                                                                |
| `atom:subtitle`          | 예     | 예   |                                                                                                |
| `atom:summary`           | 예     | 예   |                                                                                                |
| `atom:title`             | 예     | 예   |                                                                                                |
| `atom:updated`           | 예     | 예   |                                                                                                |
| `atom:uri`               | 예     | 예   |                                                                                                |
| `geo:lat`                | 예     | 아니요    | 로 작성 `georss:point`됩니다.                                                                   |
| `geo:lon`                | 예     | 아니요    | 로 작성 `georss:point`됩니다.                                                                   |
| `geo:long`               | 예     | 아니요    | 로 작성 `georss:point`됩니다.                                                                   |
| `georss:box`             | 예     | 아니요    | 다각형으로 읽고 "Rectangle"의 `subType` 속성을 지정 합니다.                                |
| `georss:circle`          | 예     | 예   |                                                                                                |
| `georss:elev`            | 예     | 예   |                                                                                                |
| `georss:featurename`     | 예     | 예   |                                                                                                |
| `georss:featuretypetag`  | 예     | 예   |                                                                                                |
| `georss:floor`           | 예     | 예   |                                                                                                |
| `georss:line`            | 예     | 예   |                                                                                                |
| `georss:point`           | 예     | 예   |                                                                                                |
| `georss:polygon`         | 예     | 예   |                                                                                                |
| `georss:radius`          | 예     | 예   |                                                                                                |
| `georss:relationshiptag` | 예     | 예   |                                                                                                |
| `georss:where`           | 예     | 예   |                                                                                                |
| `geourl:latitude`        | 예     | 아니요    | 로 작성 `georss:point`됩니다.                                                                   |
| `geourl:longitude`       | 예     | 아니요    | 로 작성 `georss:point`됩니다.                                                                   |
| `position`               | 예     | 아니요    | 일부 XML 피드는 `georss:where` 태그를 사용 하 여 래핑하는 대신 위치 태그를 사용 하 여 GML을 래핑합니다. 는이 태그를 읽었지만 `georss:where` 태그를 사용 하 여 작성 합니다. |
| `rss`                    | 예     | 아니요    | GeoRSS는 ATOM 형식으로 작성 되었습니다.                                                                 |
| `rss:author`             | 예     | 부분 | 로 작성 `atom:author`됩니다.                                                                 |
| `rss:category`           | 예     | 부분 | 로 작성 `atom:category`됩니다.                                                               |
| `rss:channel`            | 예     | 아니요    |                                                                                                |
| `rss:cloud`              | 예     | 아니요    |                                                                                                |
| `rss:comments`           | 예     | 아니요    |                                                                                                |
| `rss:copyright`          | 예     | 부분 | `atom:rights` If 셰이프에 `rights` `properties` 속성이 이미 없는 경우로 작성 됩니다.       |
| `rss:description`        | 예     | 부분 | `atom:content` If 셰이프에 `content` `properties` 속성이 이미 없는 경우로 작성 됩니다.      |
| `rss:docs`               | 예     | 아니요    |                                                                                                |
| `rss:enclosure`          | 예     | 아니요    |                                                                                                |
| `rss:generator`          | 예     | 아니요    |                                                                                                |
| `rss:guid`               | 예     | 부분 | `atom:id` If 셰이프에 `id` `properties` 속성이 이미 없는 경우로 작성 됩니다.         |
| `rss:image`              | 예     | 부분 | `atom:logo` If 셰이프에 `logo` `properties` 속성이 이미 없는 경우로 작성 됩니다.      |
| `rss:item`               | 예     | 부분 | 로 작성 `atom:entry`됩니다.                                                                  |
| `rss:language`           | 예     | 아니요    |                                                                                                |
| `rss:lastBuildDate`      | 예     | 부분 | `atom:updated` If 셰이프에 `updated` `properties` 속성이 이미 없는 경우로 작성 됩니다.     |
| `rss:link`               | 예     | 부분 | 로 작성 `atom:link`됩니다.                                                                   |
| `rss:managingEditor`     | 예     | 부분 | 로 작성 `atom:contributor`됩니다.                                                            |
| `rss:pubDate`            | 예     | 부분 | `atom:published` If 셰이프에 `published` `properties` 속성이 이미 없는 경우로 작성 됩니다.  |
| `rss:rating`             | 예     | 아니요    |                                                                                                |
| `rss:skipDays`           | 예     | 아니요    |                                                                                                |
| `rss:skipHours`          | 예     | 아니요    |                                                                                                |
| `rss:source`             | 예     | 부분 | 을 `atom:source` 포함 하는로 `atom:link`작성 됩니다.                                       |
| `rss:textInput`          | 예     | 아니요    |                                                                                                |
| `rss:title`              | 예     | 부분 | 로 작성 `atom:title`됩니다.                                                                  |
| `rss:ttl`                | 예     | 아니요    |                                                                                                |
| `rss:webMaster`          | 예     | 아니요    |                                                                                                |

### <a name="gml-elements"></a>GML 요소

공간 IO 모듈은 다음과 같은 GML 요소를 지원 합니다. 

| 요소 이름            | 읽기 | 쓰기 | 메모                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | 예  | 아니요    | 로 `gml:posList`작성 됩니다.                                                              |
| `gml:curveMember`       | 예  | 아니요    |                                                                                        |
| `gml:curveMembers`      | 예  | 아니요    |                                                                                        |
| `gml:Box`               | 예  | 아니요    | 로 `gml:Envelope`작성 됩니다.                                                             |
| `gml:description`       | 예  | 예   |                                                                                        |
| `gml:Envelope`          | 예  | 예   |                                                                                        |
| `gml:exterior`          | 예  | 예   |                                                                                        |
| `gml:Feature`           | 예  | 아니요    | 도형으로 작성 됩니다.                                                                    |
| `gml:FeatureCollection` | 예  | 아니요    | 기 하 도형 컬렉션으로 작성 됩니다.                                                      |
| `gml:featureMember`     | 예  | 아니요    | 기 하 도형 컬렉션으로 작성 됩니다.                                                      |
| `gml:geometry`          | 예  | 아니요    | 도형으로 작성 됩니다.                                                                    |
| `gml:geometryMember`    | 예  | 예   |                                                                                        |
| `gml:geometryMembers`   | 예  | 예   |                                                                                        |
| `gml:identifier`        | 예  | 예   |                                                                                        |
| `gml:innerBoundaryIs`   | 예  | 아니요    | 을 사용 `gml.interior`하 여 작성 되었습니다.                                                          |
| `gml:interior`          | 예  | 예   |                                                                                        |
| `gml:LinearRing`        | 예  | 예   |                                                                                        |
| `gml:LineString`        | 예  | 예   |                                                                                        |
| `gml:lineStringMember`  | 예  | 예   |                                                                                        |
| `gml:lineStringMembers` | 예  | 아니요    |                                                                                        |
| `gml:MultiCurve`        | 예  | 아니요    | 멤버만 읽습니다 `gml:LineString` . 다음으로 작성`gml.MultiLineString`                  |
| `gml:MultiGeometry`     | 부분  | 부분   | FeatureCollection 으로만 읽습니다.                                              |
| `gml:MultiLineString`   | 예  | 예   |                                                                                        |
| `gml:MultiPoint`        | 예  | 예   |                                                                                        |
| `gml:MultiPolygon`      | 예  | 예   |                                                                                        |
| `gml:MultiSurface`      | 예  | 아니요    | 멤버만 읽습니다 `gml:Polygon` . 다음으로 작성`gml.MultiPolygon`                        |
| `gml:name`              | 예  | 예   |                                                                                        |
| `gml:outerBoundaryIs`   | 예  | 아니요    | 을 사용 `gml.exterior`하 여 작성 되었습니다.                                                          |
| `gml:Point`             | 예  | 예   |                                                                                        |
| `gml:pointMember`       | 예  | 예   |                                                                                        |
| `gml:pointMembers`      | 예  | 아니요    |                                                                                        |
| `gml:Polygon`           | 예  | 예   |                                                                                        |
| `gml:polygonMember`     | 예  | 예   |                                                                                        |
| `gml:polygonMembers`    | 예  | 아니요    |                                                                                        |
| `gml:pos`               | 예  | 예   |                                                                                        |
| `gml:posList`           | 예  | 예   |                                                                                        |
| `gml:surfaceMember`     | 예  | 예   |                                                                                        |

#### <a name="additional-notes"></a>추가 참고 사항

- 자식 요소 내에 포함 될 수 있는 기 하 도형에 대해 멤버 요소를 검색 합니다. 이 검색 작업은 GML에서 확장 되는 많은 XML 형식이 멤버 요소의 직계 자식으로 geometry를 삽입 하지 않을 수 있기 때문에 필요 합니다.
- `srsName`은 WGS84 좌표 및 다음 코드에 대해 부분적으로 지원 됩니다.[Epsg: 4326](https://epsg.io/4326)) 및 웹 Mercator ([epsg: 3857](https://epsg.io/3857) 또는 해당 대체 코드 중 하나) 다른 모든 좌표계는 WGS84으로 있는 그대로 구문 분석 됩니다.
- XML 피드를 읽을 때 지정 하지 않는 한 축 순서는 XML 피드의 힌트에 따라 결정 됩니다. "위도, 경도" 축 순서에 대 한 기본 설정이 제공 됩니다.
- GML 파일에 쓸 때 속성에 대해 사용자 지정 GML 네임 스페이스를 지정 하지 않으면 추가 속성 정보가 추가 되지 않습니다.

### <a name="gpx-elements"></a>.GPX) 요소

공간 IO 모듈은 다음과 같은 .GPX) 요소를 지원 합니다.

| 요소 이름             | 읽기    | 쓰기   | 메모                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | 예     | 예     |                                                                                             |
| `gpx:author`             | 예     | 예     |                                                                                             |
| `gpx:bounds`             | 예     | 예     | 읽을 때 LocationRect로 변환 됩니다.                                                    |
| `gpx:cmt`                | 예     | 예     |                                                                                             |
| `gpx:copyright`          | 예     | 예     |                                                                                             |
| `gpx:desc`               | 예     | 예     | 다른 XML 형식에 맞춰 읽을 때 description 속성에 복사 됩니다.               |
| `gpx:dgpsid`             | 예     | 예     |                                                                                             |
| `gpx:ele`                | 예     | 예     |                                                                                             |
| `gpx:extensions`         | 부분 | 부분 | 읽을 때 스타일 정보를 추출 합니다. 다른 모든 확장 프로그램은 단순 JSON 개체에 결합 됩니다. 셰이프 스타일 정보만 기록 됩니다. |
| `gpx:geoidheight`        | 예     | 예     |                                                                                             |
| `gpx:gpx`                | 예     | 예     |                                                                                             |
| `gpx:hdop`               | 예     | 예     |                                                                                             |
| `gpx:link`               | 예     | 예     |                                                                                             |
| `gpx:magvar`             | 예     | 예     |                                                                                             |
| `gpx:metadata`           | 예     | 예     |                                                                                             |
| `gpx:name`               | 예     | 예     |                                                                                             |
| `gpx:pdop`               | 예     | 예     |                                                                                             |
| `gpx:rte`                | 예     | 예     |                                                                                             |
| `gpx:rtept`              | 예     | 예     |                                                                                             |
| `gpx:sat`                | 예     | 예     |                                                                                             |
| `gpx:src`                | 예     | 예     |                                                                                             |
| `gpx:sym`                | 예     | 예     | 값이 캡처되고 압정 아이콘을 변경 하는 데 사용 되지 않습니다.                               |
| `gpx:text`               | 예     | 예     |                                                                                             |
| `gpx:time`               | 예     | 예     |                                                                                             |
| `gpx:trk`                | 예     | 예     |                                                                                             |
| `gpx:trkpt`              | 예     | 예     |                                                                                             |
| `gpx:trkseg`             | 예     | 예     |                                                                                             |
| `gpx:type`               | 예     | 예     |                                                                                             |
| `gpx:vdop`               | 예     | 예     |                                                                                             |
| `gpx:wpt`                | 예     | 예     |                                                                                             |
| `gpx_style:color`        | 예     | 예     |                                                                                             |
| `gpx_style:line`         | 부분 | 부분 | `color``width`, `opacity`,가 `lineCap` 지원 됩니다.                                           |
| `gpx_style:opacity`      | 예     | 예     |                                                                                             |
| `gpx_style:width`        | 예     | 예     |                                                                                             |
| `gpxx:DisplayColor`      | 예     | 아니요      | 모양의 색을 지정 하는 데 사용 됩니다. 쓰는 경우 대신 `gpx_style:line` 색이 사용 됩니다.  |
| `gpxx:RouteExtension`    | 부분 | 아니요      | 모든 속성은로 `properties`읽어옵니다. 이 경우에는 `DisplayColor`만 사용됩니다.                     |
| `gpxx:TrackExtension`    | 부분 | 아니요      | 모든 속성은로 `properties`읽어옵니다. 이 경우에는 `DisplayColor`만 사용됩니다.                     |
| `gpxx:WaypointExtension` | 부분 | 아니요      | 모든 속성은로 `properties`읽어옵니다. 이 경우에는 `DisplayColor`만 사용됩니다.                     |
| `gpx:keywords`           | 예     | 예     |                                                                                             |
| `gpx:fix`                | 예     | 예     |                                                                                             |

#### <a name="additional-notes"></a>추가 참고 사항

작성 시

- 다중 지점은 개별 waypoints 분할 됩니다.
- 다각형 및 MultiPolygons은 트랙으로 작성 됩니다. 
  
## <a name="supported-well-known-text-geometry-types"></a>지원 되는 잘 알려진 텍스트 기 하 도형 형식

| Geometry 형식 | 읽기 | 쓰기 |
|--------------|:----:|:-----:|
| 까지 | x | x |
| POINT Z | x | x | 
| POINT M | x | x<sup>[2]</sup> |
| POINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| LINESTRING | x | x |
| LINESTRING Z | x | x | 
| LINESTRING M | x | x<sup>[2]</sup> |
| LINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| [ | x | x |
| 다각형 Z | x | x |
| 다각형 M | x | x<sup>[2]</sup> |
| 다각형 ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| 다 | x | x |
| MULTIPOINT Z | x | x | 
| MULTIPOINT M | x | x<sup>[2]</sup> |
| POMULTIPOINTINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTILINESTRING | x | x |
| MULTILINESTRING Z | x | x | 
| MULTILINESTRING M | x | x<sup>[2]</sup> |
| MULTILINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTIPOLYGON | x | x |
| MULTIPOLYGON Z | x | x | 
| MULTIPOLYGON M | x | x<sup>[2]</sup> |
| MULTIPOLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| GEOMETRYCOLLECTION | x | x |
| GEOMETRYCOLLECTION Z | x | x | 
| GEOMETRYCOLLECTION M | x | x<sup>[2]</sup> | 
| GEOMETRYCOLLECTION ZM | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1\] Z 매개 변수만 캡처되고 Position 값에 세 번째 값으로 추가 됩니다.

\[2\] M 매개 변수가 캡처되지 않습니다.

## <a name="delimited-spatial-data-support"></a>구분 된 공간 데이터 지원

쉼표로 구분 된 값 파일 (CSV)과 같은 구분 된 공간 데이터에는 종종 공간 데이터를 포함 하는 열이 있습니다. 예를 들어 위도 및 경도 정보를 포함 하는 열이 있을 수 있습니다. 잘 알려진 텍스트 형식으로 공간 기 하 도형 데이터를 포함 하는 열이 있을 수 있습니다.

### <a name="spatial-data-column-detection"></a>공간 데이터 열 검색

공간 데이터를 포함 하는 구분 된 파일을 읽을 때 헤더를 분석 하 여 위치 필드를 포함 하는 열을 결정 합니다. 헤더에 형식 정보가 포함 되어 있으면 셀 값을 적절 한 형식으로 캐스팅 하는 데 사용 됩니다. 헤더를 지정 하지 않으면 첫 번째 행이 분석 되어 헤더를 생성 하는 데 사용 됩니다. 첫 번째 행을 분석 하는 경우 대/소문자를 구분 하지 않는 방식으로 열 이름이 다음 이름과 일치 하도록 검사가 실행 됩니다. 이름 순서는 파일에 둘 이상의 이름이 있는 경우 우선 순위입니다.

#### <a name="latitude"></a>위도

- `latitude`
- `lat`
- `latdd`
- `lat_dd`
- `latitude83`
- `latdecdeg`
- `y`
- `ycenter`
- `point-y`

#### <a name="longitude"></a>경도

- `longitude`
- `lon`
- `lng`
- `long`
- `longdd`
- `long_dd`
- `longitude83`
- `longdecdeg`
- `x`
- `xcenter`
- `point-x`

#### <a name="elevation"></a>상승

- `elevation`
- `elv`
- `altitude`
- `alt`
- `z`

#### <a name="geography"></a>Geography

데이터의 첫 행에는 잘 알려진 텍스트 형식의 문자열이 검색 됩니다. 

### <a name="delimited-data-column-types"></a>구분 기호로 분리 된 데이터 열 유형

머리글 행을 검색할 때 열 이름에 있는 모든 유형 정보는 추출 되어 해당 열의 셀을 캐스팅 하는 데 사용 됩니다. 다음은 유형 값이 "ColumnName (typeName)" 인 열 이름의 예입니다. 다음과 같은 대/소문자를 구분 하지 않는 형식 이름이 지원 됩니다.

#### <a name="numbers"></a>숫자

- edm. int64
- int
- long
- edm. double
- float
- double
- number

#### <a name="booleans"></a>부울

- edm. 부울
- bool
- boolean

#### <a name="dates"></a>날짜

- edm. datetime
- date
- Datetime

#### <a name="geography"></a>Geography

- edm. 지리
- geography

#### <a name="strings"></a>문자열

- edm. 문자열
- varchar
- text
- 문자열

헤더에서 형식 정보를 추출할 수 없고 읽을 때 동적 형식 지정 옵션을 사용할 수 있는 경우 각 셀은 개별적으로 분석 되어로 캐스팅 하기에 가장 적합 한 데이터 형식을 결정 합니다.

## <a name="next-steps"></a>다음 단계

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [공간 데이터 읽기 및 쓰기](spatial-io-read-write-spatial-data.md)
