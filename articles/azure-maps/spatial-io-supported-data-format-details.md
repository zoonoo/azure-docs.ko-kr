---
title: 지원되는 데이터 형식 세부 정보 | 마이크로소프트 Azure 지도
description: 공간 IO 모듈에서 구분된 공간 데이터가 어떻게 구문 분석되는지 알아보십시오.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3353620f1751e939a04543115fe704555fb3bc21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334094"
---
# <a name="supported-data-format-details"></a>지원되는 데이터 형식 세부 정보

이 문서에서는 모든 XML 태그 및 잘 알려진 텍스트 지오메트리 형식에 대한 읽기 및 쓰기 지원에 대한 세부 사항을 제공합니다. 또한 공간 IO 모듈에서 분리된 공간 데이터가 구문 분석되는 방식에 대해서도 자세히 설명합니다.

## <a name="supported-xml-namespaces"></a>지원되는 XML 네임스페이스

공간 IO 모듈은 다음 네임스페이스에서 XML 태그를 지원합니다.

| 네임스페이스 접두사 | 네임스페이스 URI   | 메모                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | GeoRSS 파일에서만 지원을 읽습니다.           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | GeoRSS 파일에서만 지원을 읽습니다.                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | GPX 파일에서만 지원을 읽습니다. 구문 분석 및 DisplayColor를 사용합니다. 셰이프 메타데이터에 추가된 다른 모든 속성입니다. |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | GPX 파일에서 지원됩니다. 선 색상을 사용합니다. |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | 읽기 전용입니다. GeoRSS는 Atom 형식을 사용하여 씁니다.              |

## <a name="supported-xml-elements"></a>지원되는 XML 요소

공간 IO 모듈은 다음 XML 요소를 지원합니다. 지원되지 않는 모든 XML 태그는 JSON 개체로 변환됩니다. 그런 다음 각 태그가 상위 셰이프 또는 레이어 필드의 속성으로 `properties` 추가됩니다.

### <a name="kml-elements"></a>KML 엘리먼트

공간 IO 모듈은 다음과 같은 KML 요소를 지원합니다.

| 요소 이름         | 읽기    | 쓰기   | 메모                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | 부분 | 예     | 오브젝트는 구문 분석되지만 셰이프를 배치하는 데 는 사용되지 않습니다.                                                                    |
| `AddressDetails`     | 부분 | 아니요      | 오브젝트는 구문 분석되지만 셰이프를 배치하는 데 는 사용되지 않습니다.                                                                    |
| `atom:author`        | 예     | 예     |                                                                                                                            |
| `atom:link`          | 예     | 예     |                                                                                                                            |
| `atom:name`          | 예     | 예     |                                                                                                                            |
| `BalloonStyle`       | 부분 | 부분 | `displayMode`는 지원되지 않습니다. `PopupTemplate`로 변환됩니다. 작성하려면 `popupTemplate` 속성을 작성하려는 기능의 속성으로 추가합니다. |
| `begin`              | 예     | 예     |                                                                                                                            |
| `color`              | 예     | 예     | 포함 `#AABBGGRR` `#BBGGRR`및 . CSS 컬러 스트링으로 구문 분석                                                           |
| `colorMode`          | 예     | 아니요      |                                                                                                                            |
| `coordinates`        | 예     | 예     |                                                                                                                            |
| `Data`               | 예     | 예     |                                                                                                                            |
| `description`        | 예     | 예     |                                                                                                                            |
| `displayName`        | 예     | 예     |                                                                                                                            |
| `Document`           | 예     | 예     |                                                                                                                            |
| `drawOrder`          | 부분 | 아니요      | 접지 오버레이를 읽고 정렬하는 데 사용됩니다. 
| `east`               | 예     | 예     |                                                                                                                            |
| `end`                | 예     | 예     |                                                                                                                            |
| `ExtendedData`       | 예     | 예     | 형식이 없는 `Data` `SimpleData` 및 `Schema`폼의 `$[dataName]`엔터티 대체를 지원합니다.                      |
| `extrude`            | 부분 | 부분 | 다각형에 대해서만 지원됩니다. 높이가 다른 다각형이 있는 다중 형상은 개별 피쳐로 나뉩습니다. 선 스타일은 지원되지 않습니다. 고도가 0인 다각형은 플랫 다각형으로 렌더링됩니다. 읽을 때 외부 링의 첫 번째 좌표의 고도가 다각형의 높이 속성으로 추가됩니다. 그런 다음 첫 번째 좌표의 고도가 맵에서 다각형을 렌더링하는 데 사용됩니다. |
| `fill`               | 예     | 예     |                                                                                                                            |
| `Folder`             | 예     | 예     |                                                                                                                            |
| `GroundOverlay`      | 예     | 예     | `color`지원되지 않음                                                                                                   |
| `heading`            | 부분 | 아니요      | 구문 분석되었지만 에 `SimpleDataLayer`의해 렌더링되지 않았습니다. 데이터가 셰이프의 속성에 저장되어 있는 경우에만 씁니다.                 |
| `hotSpot`            | 예     | 부분 | 데이터가 셰이프의 속성에 저장되어 있는 경우에만 씁니다. 단위는 "픽셀"로만 출력됩니다.                         |
| `href`               | 예     | 예     |                                                                                                                            |
| `Icon`               | 부분 | 부분 | 구문 분석되었지만 에 `SimpleDataLayer`의해 렌더링되지 않았습니다. URI 데이터가 포함된 경우에만 셰이프의 아이콘 속성을 씁니다. `href`만 지원됩니다. |
| `IconStyle`          | 부분 | 부분 | `icon``colorMode`및 `heading` `hotspots` 값은 구문 분석되지만`SimpleDataLayer`         |
| `innerBoundaryIs`    | 예     | 예     |                                                                                                                            |
| `kml`                | 예     | 예     |                                                                                                                            |
| `LabelStyle`         | 아니요      | 아니요      |                                                                                                                            |
| `LatLonBox`          | 예     | 예     |                                                                                                                            |
| `gx:LatLonQuad`      | 예     | 예     |                                                                                                                            |
| `LinearRing`         | 예     | 예     |                                                                                                                            |
| `LineString`         | 예     | 예     |                                                                                                                            |
| `LineStyle`          | 예     | 예     | `colorMode`는 지원되지 않습니다.                                                                                         |
| `Link`               | 예     | 아니요      | 네트워크 `href` 링크에는 속성만 지원됩니다.                                                                   |
| `MultiGeometry`      | 부분 | 부분 | 읽을 때 개별 기능으로 나눌 수 있습니다.                                                                     |
| `name`               | 예     | 예     |                                                                                                                            |
| `NetworkLink`        | 예     | 아니요      | 링크는 문서와 동일한 도메인에 있어야 합니다.                                                                  |
| `NetworkLinkControl` | 아니요      | 아니요      |                                                                                                                            |
| `north`              | 예     | 예     |                                                                                                                            |
| `open`               | 예     | 예     |                                                                                                                            |
| `outerBoundaryIs`    | 예     | 예     |                                                                                                                            |
| `outline`            | 예     | 예     |                                                                                                                            |
| `overlayXY`          | 아니요      | 아니요      |                                                                                                                            |
| `Pair`               | 부분 | 아니요      | a의 `normal` `StyleMap` 스타일만 지원됩니다. `highlight`는 지원되지 않습니다.                                   |
| `phoneNumber`        | 예     | 예     |                                                                                                                            |
| `PhotoOverlay`       | 아니요      | 아니요      |                                                                                                                            |
| `Placemark`          | 예     | 예     |                                                                                                                            |
| `Point`              | 예     | 예     |                                                                                                                            |
| `Polygon`            | 예     | 예     |                                                                                                                            |
| `PolyStyle`          | 예     | 예     |                                                                                                                            |
| `Region`             | 부분 | 부분 | `LatLongBox`문서 수준에서 지원됩니다.                                                                      |
| `rotation`           | 아니요      | 아니요      |                                                                                                                            |
| `rotationXY`         | 아니요      | 아니요      |                                                                                                                            |
| `scale`              | 아니요      | 아니요      |                                                                                                                            |
| `Schema`             | 예     | 예     |                                                                                                                            |
| `SchemaData`         | 예     | 예     |                                                                                                                            |
| `schemaUrl`          | 부분 | 예     | KMZ에 포함되지 않은 외부 문서의 로딩 스타일은 지원하지 않습니다.                             |
| `ScreenOverlay`      | 아니요      | 아니요      |                                                                                                                            |
| `screenXY`           | 아니요      | 아니요      |                                                                                                                            |
| `SimpleData`         | 예     | 예     |                                                                                                                            |
| `SimpleField`        | 예     | 예     |                                                                                                                            |
| `size`               | 아니요      | 아니요      |                                                                                                                            |
| `Snippet`            | 부분 | 부분 | `maxLines`특성이 무시됩니다.                                                                                  |
| `south`              | 예     | 예     |                                                                                                                            |
| `Style`              | 예     | 예     |                                                                                                                            |
| `StyleMap`           | 부분 | 아니요      | a의 `StyleMap` 일반 스타일만 지원됩니다.                                                                        |
| `styleUrl`           | 부분 | 예     | 외부 스타일 URL은 지원되지 않습니다.                                                                         |
| `text`               | 예     | 예     | 교체가 `$[geDirections]` 지원되지 않음                                                                          |
| `textColor`          | 예     | 예     |                                                                                                                            |
| `TimeSpan`           | 예     | 예     |                                                                                                                            |
| `TimeStamp`          | 예     | 예     |                                                                                                                            |
| `value`              | 예     | 예     |                                                                                                                            |
| `viewRefreshMode`    | 부분 | 아니요      |  WMS 서비스를 가리키는 경우 접지 오버레이에 대해서만 `onStop` 지원됩니다. URL에 `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` 부가되고 맵이 이동함에 따라 업데이트됩니다.  |
| `visibility`         | 예     | 예     |                                                                                                                            |
| `west`               | 예     | 예     |                                                                                                                            |
| `when`               | 예     | 예     |                                                                                                                            |
| `width`              | 예     | 예     |                                                                                                                            |

### <a name="georss-elements"></a>지오RSS 엘리먼트

공간 IO 모듈은 다음 GeoRSS 요소를 지원합니다.

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
| `geo:lat`                | 예     | 아니요    | `georss:point`로 작성됩니다.                                                                   |
| `geo:lon`                | 예     | 아니요    | `georss:point`로 작성됩니다.                                                                   |
| `geo:long`               | 예     | 아니요    | `georss:point`로 작성됩니다.                                                                   |
| `georss:box`             | 예     | 아니요    | 다각형으로 읽고 "사각형"의 속성이 `subType` 부여됩니다.                                |
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
| `geourl:latitude`        | 예     | 아니요    | `georss:point`로 작성됩니다.                                                                   |
| `geourl:longitude`       | 예     | 아니요    | `georss:point`로 작성됩니다.                                                                   |
| `position`               | 예     | 아니요    | 일부 XML 피드는 `georss:where` GML을 태그로 래핑하는 대신 위치 태그로 래핑합니다. 이 태그를 읽습니다만, `georss:where` 태그를 사용 하 여 쓸 수 있습니다. |
| `rss`                    | 예     | 아니요    | ATOM 형식으로 작성된 GeoRSS.                                                                 |
| `rss:author`             | 예     | 부분 | `atom:author`로 작성됩니다.                                                                 |
| `rss:category`           | 예     | 부분 | `atom:category`로 작성됩니다.                                                               |
| `rss:channel`            | 예     | 아니요    |                                                                                                |
| `rss:cloud`              | 예     | 아니요    |                                                                                                |
| `rss:comments`           | 예     | 아니요    |                                                                                                |
| `rss:copyright`          | 예     | 부분 | `atom:rights` if 셰이프로 작성된 속성이 `rights` 이미 없습니다. `properties`       |
| `rss:description`        | 예     | 부분 | `atom:content` if 셰이프로 작성된 속성이 `content` 이미 없습니다. `properties`      |
| `rss:docs`               | 예     | 아니요    |                                                                                                |
| `rss:enclosure`          | 예     | 아니요    |                                                                                                |
| `rss:generator`          | 예     | 아니요    |                                                                                                |
| `rss:guid`               | 예     | 부분 | `atom:id` if 셰이프로 작성된 속성은 `id` `properties` 이미 없습니다.         |
| `rss:image`              | 예     | 부분 | `atom:logo` if 셰이프로 작성된 속성이 `logo` 이미 없습니다. `properties`      |
| `rss:item`               | 예     | 부분 | `atom:entry`로 작성됩니다.                                                                  |
| `rss:language`           | 예     | 아니요    |                                                                                                |
| `rss:lastBuildDate`      | 예     | 부분 | `atom:updated` if 셰이프로 작성된 속성은 `updated` `properties` 이미 없습니다.     |
| `rss:link`               | 예     | 부분 | `atom:link`로 작성됩니다.                                                                   |
| `rss:managingEditor`     | 예     | 부분 | `atom:contributor`로 작성됩니다.                                                            |
| `rss:pubDate`            | 예     | 부분 | `atom:published` if 셰이프로 작성된 속성이 `published` 이미 없습니다. `properties`  |
| `rss:rating`             | 예     | 아니요    |                                                                                                |
| `rss:skipDays`           | 예     | 아니요    |                                                                                                |
| `rss:skipHours`          | 예     | 아니요    |                                                                                                |
| `rss:source`             | 예     | 부분 | `atom:link`을 `atom:source` 포함하는 것으로 작성되었습니다.                                       |
| `rss:textInput`          | 예     | 아니요    |                                                                                                |
| `rss:title`              | 예     | 부분 | `atom:title`로 작성됩니다.                                                                  |
| `rss:ttl`                | 예     | 아니요    |                                                                                                |
| `rss:webMaster`          | 예     | 아니요    |                                                                                                |

### <a name="gml-elements"></a>GML 요소

공간 IO 모듈은 다음 GML 요소를 지원합니다. 

| 요소 이름            | 읽기 | 쓰기 | 메모                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | 예  | 아니요    | 로 `gml:posList`작성됩니다.                                                              |
| `gml:curveMember`       | 예  | 아니요    |                                                                                        |
| `gml:curveMembers`      | 예  | 아니요    |                                                                                        |
| `gml:Box`               | 예  | 아니요    | 로 `gml:Envelope`작성됩니다.                                                             |
| `gml:description`       | 예  | 예   |                                                                                        |
| `gml:Envelope`          | 예  | 예   |                                                                                        |
| `gml:exterior`          | 예  | 예   |                                                                                        |
| `gml:Feature`           | 예  | 아니요    | 모양으로 작성되었습니다.                                                                    |
| `gml:FeatureCollection` | 예  | 아니요    | 지오메트리 컬렉션으로 작성되었습니다.                                                      |
| `gml:featureMember`     | 예  | 아니요    | 지오메트리 컬렉션으로 작성되었습니다.                                                      |
| `gml:geometry`          | 예  | 아니요    | 모양으로 작성되었습니다.                                                                    |
| `gml:geometryMember`    | 예  | 예   |                                                                                        |
| `gml:geometryMembers`   | 예  | 예   |                                                                                        |
| `gml:identifier`        | 예  | 예   |                                                                                        |
| `gml:innerBoundaryIs`   | 예  | 아니요    | 을 `gml.interior`사용하여 작성됩니다.                                                          |
| `gml:interior`          | 예  | 예   |                                                                                        |
| `gml:LinearRing`        | 예  | 예   |                                                                                        |
| `gml:LineString`        | 예  | 예   |                                                                                        |
| `gml:lineStringMember`  | 예  | 예   |                                                                                        |
| `gml:lineStringMembers` | 예  | 아니요    |                                                                                        |
| `gml:MultiCurve`        | 예  | 아니요    | 멤버만 `gml:LineString` 읽습니다. 로 작성`gml.MultiLineString`                  |
| `gml:MultiGeometry`     | 부분  | 부분   | 기능 컬렉션으로만 읽습니다.                                              |
| `gml:MultiLineString`   | 예  | 예   |                                                                                        |
| `gml:MultiPoint`        | 예  | 예   |                                                                                        |
| `gml:MultiPolygon`      | 예  | 예   |                                                                                        |
| `gml:MultiSurface`      | 예  | 아니요    | 멤버만 `gml:Polygon` 읽습니다. 로 작성`gml.MultiPolygon`                        |
| `gml:name`              | 예  | 예   |                                                                                        |
| `gml:outerBoundaryIs`   | 예  | 아니요    | 을 `gml.exterior`사용하여 작성됩니다.                                                          |
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

- 멤버 요소는 자식 요소 내에 묻힐 수 있는 형상을 검색합니다. 이 검색 작업은 GML에서 확장되는 많은 XML 형식이 멤버 요소의 직접 자식으로 형상을 배치하지 않을 수 있기 때문에 필요합니다.
- `srsName`WGS84 좌표 및 다음 코드에 대해 부분적으로 지원됩니다:[EPSG:4326)](https://epsg.io/4326)및 웹 메르카토르(EPSG:3857 또는 대체 코드 중 하나).[EPSG:3857](https://epsg.io/3857) 다른 좌표계는 WGS84로 구문 분석됩니다.
- XML 피드를 읽을 때 지정하지 않는 한 축 순서는 XML 피드의 힌트를 기반으로 결정됩니다. "위도, 경도" 축 순서에 대한 기본 설정이 제공됩니다.
- GML 파일에 쓸 때 속성에 대한 사용자 지정 GML 네임스페이스가 지정되지 않으면 추가 속성 정보가 추가되지 않습니다.

### <a name="gpx-elements"></a>GPX 요소

공간 IO 모듈은 다음과 같은 GPX 요소를 지원합니다.

| 요소 이름             | 읽기    | 쓰기   | 메모                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | 예     | 예     |                                                                                             |
| `gpx:author`             | 예     | 예     |                                                                                             |
| `gpx:bounds`             | 예     | 예     | 읽을 때 위치 Rect로 변환됩니다.                                                    |
| `gpx:cmt`                | 예     | 예     |                                                                                             |
| `gpx:copyright`          | 예     | 예     |                                                                                             |
| `gpx:desc`               | 예     | 예     | 다른 XML 형식과 정렬하기 위해 읽을 때 설명 속성으로 복사됩니다.               |
| `gpx:dgpsid`             | 예     | 예     |                                                                                             |
| `gpx:ele`                | 예     | 예     |                                                                                             |
| `gpx:extensions`         | 부분 | 부분 | 읽을 때 스타일 정보가 추출됩니다. 다른 모든 확장은 간단한 JSON 개체로 병합됩니다. 셰이프 스타일 정보만 작성됩니다. |
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
| `gpx:sym`                | 예     | 예     | 값이 캡처되지만 푸시핀 아이콘을 변경하는 데 는 사용되지 않습니다.                               |
| `gpx:text`               | 예     | 예     |                                                                                             |
| `gpx:time`               | 예     | 예     |                                                                                             |
| `gpx:trk`                | 예     | 예     |                                                                                             |
| `gpx:trkpt`              | 예     | 예     |                                                                                             |
| `gpx:trkseg`             | 예     | 예     |                                                                                             |
| `gpx:type`               | 예     | 예     |                                                                                             |
| `gpx:vdop`               | 예     | 예     |                                                                                             |
| `gpx:wpt`                | 예     | 예     |                                                                                             |
| `gpx_style:color`        | 예     | 예     |                                                                                             |
| `gpx_style:line`         | 부분 | 부분 | `color`을 `opacity` `width`받습니다. `lineCap`                                           |
| `gpx_style:opacity`      | 예     | 예     |                                                                                             |
| `gpx_style:width`        | 예     | 예     |                                                                                             |
| `gpxx:DisplayColor`      | 예     | 아니요      | 셰이프의 색상을 지정하는 데 사용됩니다. 글을 쓸 `gpx_style:line` 때, 대신 색상이 사용됩니다.  |
| `gpxx:RouteExtension`    | 부분 | 아니요      | 모든 속성은 `properties`로 읽습니다. 이 경우에는 `DisplayColor`만 사용됩니다.                     |
| `gpxx:TrackExtension`    | 부분 | 아니요      | 모든 속성은 `properties`로 읽습니다. 이 경우에는 `DisplayColor`만 사용됩니다.                     |
| `gpxx:WaypointExtension` | 부분 | 아니요      | 모든 속성은 `properties`로 읽습니다. 이 경우에는 `DisplayColor`만 사용됩니다.                     |
| `gpx:keywords`           | 예     | 예     |                                                                                             |
| `gpx:fix`                | 예     | 예     |                                                                                             |

#### <a name="additional-notes"></a>추가 참고 사항

글을 쓸 때;

- 멀티포인트는 개별 웨이포인트로 나뉩타게됩니다.
- 다각형과 다각형은 트랙으로 기록됩니다. 
  
## <a name="supported-well-known-text-geometry-types"></a>지원되는 잘 알려진 텍스트 형상 유형

| 지오메트리 유형 | 읽기 | 쓰기 |
|--------------|:----:|:-----:|
| 지점 | x | x |
| 포인트 Z | x | x | 
| 포인트 M | x | x<sup>[2]</sup> |
| 포인트 ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Linestring | x | x |
| 줄 줄 줄 | x | x | 
| 줄줄 M | x | x<sup>[2]</sup> |
| 스트링 스트링 ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| 다각형 | x | x |
| 폴리곤 Z | x | x |
| 폴리곤 M | x | x<sup>[2]</sup> |
| 폴리곤 ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| 멀티 | x | x |
| 멀티포인트 Z | x | x | 
| 멀티포인트 M | x | x<sup>[2]</sup> |
| 포멀티포인틴트 ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| 다중 줄 문자열 | x | x |
| 다중 줄 문자열 Z | x | x | 
| 다중 줄 문자열 M | x | x<sup>[2]</sup> |
| 다중 줄 문자열 ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| 멀티 폴리곤 | x | x |
| 멀티폴리곤 Z | x | x | 
| 멀티폴리곤 M | x | x<sup>[2]</sup> |
| 멀티폴리곤 ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| 지오메트리 컬렉션 | x | x |
| 지오메트리컬렉션 Z | x | x | 
| 지오메트리컬렉션 M | x | x<sup>[2]</sup> | 
| 지오메트리컬렉션 ZM | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1\] Z 매개변수만 캡처되어 위치 값의 세 번째 값으로 추가됩니다.

\[2M\] 매개변수가 캡처되지 않습니다.

## <a name="delimited-spatial-data-support"></a>구분된 공간 데이터 지원

쉼표로 구분된 값 파일(CSV)과 같이 구분된 공간 데이터에는 공간 데이터가 포함된 열이 있는 경우가 많습니다. 예를 들어 위도 및 경도 정보를 포함하는 열이 있을 수 있습니다. 잘 알려진 텍스트 형식에는 공간 형상 데이터가 포함된 열이 있을 수 있습니다.

### <a name="spatial-data-column-detection"></a>공간 데이터 컬럼 감지

공간 데이터가 포함된 제한된 파일을 읽을 때 헤더가 분석되어 위치 필드가 포함된 열을 결정합니다. 헤더에 형식 정보가 포함된 경우 셀 값을 적절한 유형으로 캐스팅하는 데 사용됩니다. 헤더를 지정하지 않으면 첫 번째 행이 분석되어 헤더를 생성하는 데 사용됩니다. 첫 번째 행을 분석할 때 대/소문자를 구분하지 않는 방식으로 열 이름과 다음 이름을 일치시키는 검사가 실행됩니다. 파일에 두 개 이상의 이름이 있는 경우 이름의 순서가 우선 순위입니다.

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

데이터의 첫 번째 행은 잘 알려진 텍스트 형식의 문자열에 대해 검색됩니다. 

### <a name="delimited-data-column-types"></a>데이터 열 유형 구분

헤더 행을 검색할 때 열 이름에 있는 모든 형식 정보가 추출되어 해당 열의 셀을 캐스팅하는 데 사용됩니다. 다음은 "ColumnName(typeName)"이라는 형식 값이 있는 열 이름의 예입니다. 다음과 같은 대/소문자 구분 형식 이름이 지원됩니다.

#### <a name="numbers"></a>숫자

- edm.int64
- int
- long
- edm.더블
- float
- double
- number

#### <a name="booleans"></a>부울

- edm.부울
- bool
- boolean

#### <a name="dates"></a>날짜

- edm.datetime
- date
- Datetime

#### <a name="geography"></a>Geography

- edm.geography
- geography

#### <a name="strings"></a>문자열

- edm.문자열
- varchar
- text
- 문자열

헤더에서 형식 정보를 추출할 수 없고 읽을 때 동적 입력 옵션이 활성화된 경우 각 셀을 개별적으로 분석하여 캐스팅하는 것이 가장 적합한 데이터 형식을 결정합니다.

## <a name="next-steps"></a>다음 단계

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [공간 데이터 읽기 및 쓰기](spatial-io-read-write-spatial-data.md)
