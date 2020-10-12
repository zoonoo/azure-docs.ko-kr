---
title: Open Geospatial Consortium (OGC) 지도 계층 추가 | Microsoft Azure 맵
description: OGC 지도 계층을 지도에 오버레이 하는 방법 및 OgcMapLayer 클래스에서 다른 옵션을 사용 하는 방법에 대해 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 6efc6b27090ecc7171bb66deb303a4764d9b6f04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87128559"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Open Geospatial Consortium에서 지도 계층 추가 (OGC)

`atlas.layer.OgcMapLayer`클래스는 지도에서 WMS (웹 맵 서비스) 이미지와 WMTS (웹 맵 타일 서비스) 이미지를 오버레이로 사용할 수 있습니다. WMS는 인터넷을 통해 georeferenced map 이미지를 제공 하기 위해 OGC에서 개발한 표준 프로토콜입니다. Image georeferencing는 지리적 위치에 이미지를 연결 하는 프로세스입니다. WMTS는 OGC에서 개발한 표준 프로토콜 이기도 합니다. 미리 렌더링 된 georeferenced 맵 타일을 제공 하기 위해 설계 되었습니다.

다음 섹션에서는 클래스에서 지 원하는 웹 맵 서비스 기능을 간략하게 설명 합니다 `OgcMapLayer` .

**WMS (웹 맵 서비스)**

- 지원 되는 버전: `1.0.0` ,, `1.1.0` `1.1.1` 및 `1.3.0`
- 서비스에서 `EPSG:3857` 프로젝션 시스템을 지원 하거나 다시 프로젝션을 처리 해야 합니다.
- GetFeatureInfo를 사용 하려면 서비스가 다시 `EPSG:4326` 프로젝션을 지원 하거나 처리 해야 합니다. 
- 지원 되는 작업:

    | 작업(Operation) | Description |
    | :-- | :-- |
    | GetCapabilities | 지원 되는 기능을 통해 서비스에 대 한 메타 데이터를 검색 합니다. |
    | GetMap | 지정 된 영역에 대 한 지도 이미지를 검색 합니다. |
    | GetFeatureInfo | `feature_info`기능에 대 한 기본 데이터를 포함 하는를 검색 합니다. |

**WMTS (웹 맵 타일 서비스)**

- 지원 되는 버전: `1.0.0`
- 타일은 사각형 이어야 합니다 `TileWidth == TileHeight` .
- 지원 되는 CRS: `EPSG:3857` 또는 `GoogleMapsCompatible` 
- TileMatrix identifier는 맵의 확대/축소 수준에 해당 하는 정수 값 이어야 합니다. Azure 맵에서 확대/축소 수준은 및 사이의 값입니다 `"0"` `"22"` . 따라서 `"0"` 는 지원 되지만 `"00"` 지원 되지 않습니다.
- 지원 되는 작업:

    | 작업(Operation) | Description |
    | :-- | :-- |
    | GetCapabilities | 지원 되는 작업 및 기능을 검색 합니다. |
    | GetTile | 특정 타일에 대 한 이미지를 검색 합니다. |

## <a name="overlay-an-ogc-map-layer"></a>OGC 지도 계층 오버레이

는 서비스의 `url` 기본 url 이거나 서비스의 기능을 얻기 위한 쿼리를 사용 하는 전체 url 일 수 있습니다. 제공 된 세부 정보에 따라 워크플로 클라이언트는 서비스에 처음 액세스 하는 방법을 결정 하기 위해 몇 가지 표준 URL 형식을 사용해 볼 수 있습니다.

다음 코드에서는 OGC 지도 계층을 지도에 오버레이 하는 방법을 보여 줍니다.

<br/>

<iframe height='700' scrolling='no' title='OGC 지도 계층 예제' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>CodePen에서 Azure Maps ()로 Pen <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>OGC 지도 계층 예제</a> 를 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="ogc-map-layer-options"></a>OGC 지도 계층 옵션

아래 샘플은 다양 한 OGC 지도 계층 옵션을 보여 줍니다. 오른쪽 위 모서리에서 코드 펜 단추를 클릭 하 여 코드 펜을 편집할 수 있습니다.

<br/>

<iframe height='700' scrolling='no' title='OGC 지도 계층 옵션' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>CodePen에서 Azure Maps ()로 Pen <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>OGC 지도 계층 옵션</a> 을 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="ogc-web-map-service-explorer"></a>OGC 웹 맵 서비스 탐색기

다음 도구는 WMS (웹 맵 서비스)와 WMTS (웹 맵 타일 서비스)에서 계층으로 이미지를 오버레이 합니다. 맵에서 렌더링 되는 계층을 선택할 수 있습니다. 이러한 계층에 대 한 연결 된 범례도 볼 수 있습니다.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='OGC 웹 맵 서비스 탐색기' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>CodePen에서 Azure Maps ()로 Pen <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>OGC 웹 맵 서비스 탐색기</a> 를 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>

프록시 서비스를 사용 하도록 맵 설정을 지정할 수도 있습니다. 프록시 서비스를 사용 하면 CORS를 사용 하지 않는 도메인에서 호스트 되는 리소스를 로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [OgcMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

맵에 추가할 수 있는 코드 샘플을 포함 하는 다음 문서를 참조 하세요.

> [!div class="nextstepaction"]
> [WFS 서비스에 연결](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [핵심 작업 활용](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [지원되는 데이터 형식 세부 정보](spatial-io-supported-data-format-details.md)
