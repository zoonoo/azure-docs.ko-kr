---
title: 웹 기능 서비스 (워크플로) 서비스에 연결 | Microsoft Azure 맵
description: 워크플로 서비스에 연결한 다음 Azure Maps 웹 SDK 및 공간 IO 모듈을 사용 하 여 워크플로 서비스를 쿼리 하는 방법에 대해 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: ab6eb4851b9f2574fc235056f8f512edcb2054bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91321784"
---
# <a name="connect-to-a-wfs-service"></a>WFS 서비스에 연결

워크플로 (웹 기능 서비스)는 Open Geospatial Consortium (OGC)에 정의 된 표준화 된 API를 포함 하는 공간 데이터를 쿼리 하는 웹 서비스입니다. `WfsClient`공간 IO 모듈의 클래스를 통해 개발자는 워크플로 서비스에 연결 하 고 서비스에서 데이터를 쿼리할 수 있습니다.

클래스에서 지원 되는 기능은 다음과 `WfsClient` 같습니다.

- 지원 되는 버전: `1.0.0` , `1.1.0` 및 `2.0.0`
- 지원 되는 필터 연산자: 이진 비교, 논리, 수학, 값 및 `bbox` 입니다.
- 는를 사용 하 여 요청을 수행 `HTTP GET` 합니다.
- 지원 되는 작업:

    | 작업(Operation) | Description |
    | :-- | :-- |
    | GetCapabilities | 유효한 워크플로 작업 및 매개 변수를 사용 하 여 메타 데이터 문서를 생성 합니다. |
    | GetFeature | 데이터 소스에서 선택 된 기능을 반환 합니다. |
    | DescribeFeatureType | 지원 되는 기능 유형을 반환 합니다. |

## <a name="using-the-wfs-client"></a>워크플로 클라이언트 사용

`atlas.io.ogc.WfsClient`공간 IO 모듈의 클래스를 사용 하면 쉽게 워크플로 서비스를 쿼리하고 응답을 GeoJSON 개체로 변환할 수 있습니다. 그런 다음이 GeoJSON 개체를 다른 매핑 용도로 사용할 수 있습니다.

다음 코드는 워크플로 서비스를 쿼리하고 반환 된 기능을 맵에 렌더링 합니다.

<br/>

<iframe height='700' scrolling='no' title='Simple 워크플로 예제' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>CodePen의 Azure Maps ()에서 Pen <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>SIMPLE 워크플로 예제</a> 를 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>지원되는 필터

워크플로 표준에 대 한 사양에서는 OGC 필터를 사용 합니다. 아래 필터는 호출 되는 서비스에서 이러한 필터도 지원 한다고 가정 하 여 워크플로 클라이언트에서 지원 됩니다. 사용자 지정 필터 문자열을 클래스에 전달할 수 있습니다 `CustomFilter` .

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

다음 코드에서는 워크플로 클라이언트에 다른 필터를 사용 하는 방법을 보여 줍니다.

<br/>

<iframe height='500' scrolling='no' title= '워크플로 필터 예제' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>CodePen의 Azure Maps ()에서 Pen <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>워크플로 필터 예</a> 를 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="wfs-service-explorer"></a>워크플로 서비스 탐색기

다음 코드에서는 워크플로 client를 사용 하 여 워크플로 services를 탐색 합니다. 서비스 내의 속성 유형 계층을 선택 하 고 연결 된 범례를 확인 합니다.

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= '워크플로 서비스 탐색기' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>CodePen에서 Azure Maps ()로 Pen <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>워크플로 서비스 탐색기</a> 를 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

CORS를 사용 하지 않는 끝점에서 호스트 되는 워크플로 서비스에 액세스 하기 위해 다음과 같이 CORS 사용 프록시 서비스를 `proxyService` 워크플로 클라이언트의 옵션에 전달할 수 있습니다. 

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
> [WfsClient](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [핵심 작업 활용](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [지원되는 데이터 형식 세부 정보](spatial-io-supported-data-format-details.md)
