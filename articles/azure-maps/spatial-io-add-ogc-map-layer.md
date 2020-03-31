---
title: 열린 지리 공간 컨소시엄 (OGC) 지도 레이어 추가 | 마이크로소프트 Azure 지도
description: 맵에서 OGC 맵 레이어를 오버레이하는 방법과 OgcMapLayer 클래스의 다양한 옵션을 사용하는 방법에 대해 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b753ecfc07cfb3806838f8a05dbe33ef0bb92730
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334293"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>열린 지리 공간 컨소시엄(OGC)에서 맵 레이어 추가

클래스는 `atlas.layer.OgcMapLayer` 맵에서 웹 맵 서비스(WMS) 이미지와 웹 맵 타일 서비스(WMTS) 이미지를 오버레이할 수 있습니다. WMS는 OGC가 인터넷을 통해 지리 참조지도 이미지를 제공하기 위해 개발 한 표준 프로토콜입니다. 이미지 지리 참조는 이미지를 지리적 위치에 연결하는 프로세스입니다. WMTS는 OGC에서 개발한 표준 프로토콜이기도 합니다. 사전 렌더링 및 지리 참조 맵 타일을 제공하도록 설계되었습니다.

다음 섹션에서는 클래스에서 지원하는 웹 맵 `OgcMapLayer` 서비스 기능에 대해 간략하게 설명합니다.

**웹 맵 서비스(WMS)**

- 지원되는 `1.0.0`버전: `1.1.0` `1.1.1`, 및`1.3.0`
- 서비스는 `EPSG:3857` 프로젝션 시스템을 지원하거나 재투영을 처리해야 합니다.
- GetFeatureInfo 지원 `EPSG:4326` 하거나 다시 투영을 처리 하는 서비스가 필요 합니다. 
- 지원되는 작업:

    | | |
    | :-- | :-- |
    | GetCapabilities | 지원되는 기능을 통해 서비스에 대한 메타데이터를 검색합니다. |
    | 겟맵 | 지정된 영역에 대한 맵 이미지 검색 |
    | GetFeature정보 | `feature_info`기능에 대한 기본 데이터가 포함된 검색 |

**웹 맵 타일 서비스(WMTS)**

- 지원되는 버전:`1.0.0`
- 타일은 `TileWidth == TileHeight`사각형이어야합니다.
- CRS 지원: `EPSG:3857` 또는`GoogleMapsCompatible` 
- TileMatrix 식별자는 맵의 확대/축소 수준에 해당하는 정수 값이어야 합니다. Azure 맵에서 확대/축소 수준은 및 `"0"` `"22"`의 값입니다. 따라서 `"0"` 지원되지만 `"00"` 지원되지는 않습니다.
- 지원되는 작업:

    | | |
    | :-- | :-- |
    | GetCapabilities | 지원되는 작업 및 기능 검색 |
    | 겟타일 | 특정 타일에 대한 이미지 검색 |

## <a name="overlay-an-ogc-map-layer"></a>OGC 맵 레이어 오버레이

서비스의 `url` 기본 URL이거나 서비스의 기능을 얻기 위한 쿼리가 있는 전체 URL일 수 있습니다. 제공된 세부 정보에 따라 WFS 클라이언트는 여러 표준 URL 형식을 시도하여 서비스에 처음 액세스하는 방법을 결정할 수 있습니다.

다음 코드는 맵에 OGC 맵 레이어를 오버레이하는 방법을 보여 주며,

<br/>

<iframe height='700' scrolling='no' title='OGC 맵 레이어 예제' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>별 펜 <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>OGC 맵 레이어 예제를</a> 참조하십시오.
</iframe>

## <a name="ogc-map-layer-options"></a>OGC 맵 레이어 옵션

아래 샘플에서는 다양한 OGC 맵 레이어 옵션을 보여 줍니다. 오른쪽 상단 모서리에 있는 코드 펜 버튼을 클릭하여 코드 펜을 편집할 수 있습니다.

<br/>

<iframe height='700' scrolling='no' title='OGC 맵 레이어 옵션' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>별 펜 <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>OGC 맵 레이어 옵션을</a> 참조하십시오.
</iframe>

## <a name="ogc-web-map-service-explorer"></a>OGC 웹맵 서비스 탐색기

다음 도구는 웹 맵 서비스(WMS) 및 웹 맵 타일 서비스(WMTS)의 이미지를 레이어로 오버레이합니다. 서비스에서 렌더링할 레이어를 선택할 수 있습니다. 이러한 도면층의 연관된 범례를 볼 수도 있습니다.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='OGC 웹맵 서비스 탐색기' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()에<a href='https://codepen.io/azuremaps'>@azuremaps</a>의한 펜 <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>OGC 웹 맵 서비스 탐색기를</a> 참조하십시오.
</iframe>

프록시 서비스를 사용하도록 맵 설정을 지정할 수도 있습니다. 프록시 서비스를 사용하면 CORS를 사용하도록 설정하지 않은 도메인에서 호스팅되는 리소스를 로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [오그맵레이어](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [오그맵레이어옵션](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

맵에 추가할 수 있는 코드 샘플이 포함된 다음 문서를 참조하십시오.

> [!div class="nextstepaction"]
> [WFS 서비스에 연결](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [핵심 운영 활용](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [지원되는 데이터 형식 세부 정보](spatial-io-supported-data-format-details.md)
