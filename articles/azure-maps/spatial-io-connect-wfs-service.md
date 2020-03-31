---
title: 웹 기능 서비스(WFS) 서비스에 연결 | 마이크로소프트 Azure 지도
description: WFS 서비스에 연결한 다음 Azure Maps 웹 SDK 및 공간 IO 모듈을 사용하여 WFS 서비스를 쿼리하는 방법을 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8b511395eb61e8845aaa11e5ca7a490dc461424d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334213"
---
# <a name="connect-to-a-wfs-service"></a>WFS 서비스에 연결

웹 기능 서비스(WFS)는 OGC(개방형 지리 공간 컨소시엄)에서 정의한 표준화된 API가 있는 공간 데이터를 쿼리하는 웹 서비스입니다. 공간 `WfsClient` IO 모듈의 클래스를 통해 개발자는 WFS 서비스에 연결하고 서비스에서 데이터를 쿼리할 수 있습니다.

다음 기능은 클래스에서 `WfsClient` 지원됩니다.

- 지원되는 `1.0.0`버전: `1.1.0`및`2.0.0`
- 지원되는 필터 연산자: 이진 비교, `bbox`논리, 수학, 값 및 .
- 요청은 사용만 가능합니다. `HTTP GET`
- 지원되는 작업:

    | | |
    | :-- | :-- |
    | GetCapabilities | 유효한 WFS 작업 및 매개 변수가 있는 메타데이터 문서 생성 |
    | 겟 기능 | 데이터 원본에서 선택된 피처를 반환합니다. |
    | 설명 기능 유형 | 지원되는 피쳐 유형을 반환합니다. |

## <a name="using-the-wfs-client"></a>WFS 클라이언트 사용

공간 `atlas.io.ogc.WfsClient` IO 모듈의 클래스를 사용하면 WFS 서비스를 쿼리하고 응답을 GeoJSON 개체로 쉽게 변환할 수 있습니다. 이 GeoJSON 개체는 다른 매핑 목적으로 사용할 수 있습니다.

다음 코드는 WFS 서비스를 쿼리하고 맵에서 반환된 피처를 렌더링합니다.

<br/>

<iframe height='700' scrolling='no' title='간단한 WFS 예제' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>별 펜 <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>간단한 WFS 예제를</a> 참조하십시오.
</iframe>

## <a name="supported-filters"></a>지원되는 필터

WFS 표준에 대한 사양은 OGC 필터를 사용합니다. 아래 필터는 호출되는 서비스가 이러한 필터를 지원한다고 가정하여 WFS 클라이언트에서 지원됩니다. 사용자 지정 필터 문자열을 `CustomFilter` 클래스로 전달할 수 있습니다.

**논리 연산자**

- `And`
- `Or`
- `Not`

**값 연산자**

- `GmlObjectId`
- `ResourceId`

**수학 연산자**

- `Add`
- `Sub`
- `Mul`
- `Div`

**비교 연산자**

- `PropertyIsEqualTo`
- `PropertyIsNotEqualTo`
- `PropertyIsLessThan`
- `PropertyIsGreaterThan`
- `PropertyIsLessThanOrEqualTo`
- `PropertyIsGreaterThanOrEqualTo`
- `PropertyIsLike`
- `PropertyIsNull`
- `PropertyIsNil`
- `PropertyIsBetween`

다음 코드는 WFS 클라이언트에서 다른 필터의 사용을 보여 줍니다.

<br/>

<iframe height='500' scrolling='no' title= 'WFS 필터 예제' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>별 펜 <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>WFS 필터 예제를</a> 참조하십시오.
</iframe>

## <a name="wfs-service-explorer"></a>WFS 서비스 탐색기

다음 코드는 WFS 클라이언트를 사용하여 WFS 서비스를 탐색합니다. 서비스 내에서 속성 유형 레이어를 선택하고 연관된 범례를 확인합니다.

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'WFS 서비스 탐색기' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>별 펜 <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>WFS 서비스 탐색기를</a> 참조하십시오.
</iframe>

CORS가 활성화되지 않은 끝점에서 호스팅되는 WFS 서비스에 액세스하려면 아래와 `proxyService` 같이 CORS 지원 프록시 서비스를 WFS 클라이언트 옵션으로 전달할 수 있습니다. 

```JavaScript
//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
});
```

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Wfs클라이언트](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [Wfs서비스옵션](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [핵심 운영 활용](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [지원되는 데이터 형식 세부 정보](spatial-io-supported-data-format-details.md)
