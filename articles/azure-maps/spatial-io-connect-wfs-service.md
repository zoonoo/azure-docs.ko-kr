---
title: 웹 기능 서비스 (워크플로) 서비스에 연결 | Microsoft Azure 맵
description: 워크플로 서비스에 연결한 다음 Azure Maps 웹 SDK 및 공간 IO 모듈을 사용 하 여 워크플로 서비스를 쿼리 하는 방법에 대해 알아봅니다.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 18ac583837c7cb8b2dabbfa6f7d7210c8afe3fcb
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402761"
---
# <a name="connect-to-a-wfs-service"></a>워크플로 서비스에 연결

워크플로 (웹 기능 서비스)는 Open Geospatial Consortium (OGC)에서 정의 된 표준화 된 API를 포함 하는 공간 데이터를 쿼리 하는 웹 서비스입니다. 공간 IO 모듈의 `WfsClient` 클래스를 사용 하면 개발자가 워크플로 서비스에 연결 하 고 서비스에서 데이터를 쿼리할 수 있습니다.

`WfsClient` 클래스에서 지 원하는 기능은 다음과 같습니다.

- 지원 되는 버전: `1.0.0`, `1.1.0`및 `2.0.0`
- 지원 되는 필터 연산자: 이진 비교, 논리, 수학, 값 및 `bbox`합니다.
- 요청은 `HTTP GET`를 사용 하 여 수행 됩니다.
- 지원 되는 작업:

    | | |
    | :-- | :-- |
    | GetCapabilities | 유효한 워크플로 작업 및 매개 변수를 사용 하 여 메타 데이터 문서를 생성 합니다. |
    | GetFeature | 데이터 소스에서 선택 된 기능을 반환 합니다. |
    | DescribeFeatureType | 지원 되는 기능 유형을 반환 합니다. |

## <a name="using-the-wfs-client"></a>워크플로 클라이언트 사용

공간 IO 모듈의 `atlas.io.ogc.WfsClient` 클래스를 사용 하면 쉽게 워크플로 서비스를 쿼리하고 응답을 GeoJSON 개체로 변환할 수 있습니다. 그런 다음이 GeoJSON 개체를 다른 매핑 용도로 사용할 수 있습니다.

다음 코드는 워크플로 서비스를 쿼리하고 반환 된 기능을 맵에 렌더링 합니다.

<br/>

<iframe height='700' scrolling='no' title='Simple 워크플로 예제' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 Pen <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>Simple 워크플로 예제</a> 를 참조 하세요.
</iframe>

## <a name="supported-filters"></a>지원되는 필터

워크플로 표준에 대 한 사양에서는 OGC 필터를 사용 합니다. 아래 필터는 호출 되는 서비스에서 이러한 필터도 지원 한다고 가정 하 여 워크플로 클라이언트에서 지원 됩니다. 사용자 지정 필터 문자열을 `CustomFilter` 클래스에 전달할 수 있습니다.

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

<iframe height='500' scrolling='no' title= '워크플로 필터 예제' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 Pen <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>워크플로 필터 예</a> 를 참조 하세요.
</iframe>

## <a name="wfs-service-explorer"></a>워크플로 서비스 탐색기

다음 코드에서는 워크플로 client를 사용 하 여 워크플로 services를 탐색 합니다. 서비스 내의 속성 유형 계층을 선택 하 고 연결 된 범례를 확인 합니다.

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= '워크플로 서비스 탐색기' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 펜 <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>워크플로 서비스 탐색기</a> 를 참조 하세요.
</iframe>

또한 프록시 서비스를 사용 하 여 CORs를 사용 하도록 설정 되지 않은 도메인에서 호스팅되는 리소스를 로드할 수 있습니다. 먼저 프록시 서비스 url을 저장할 변수를 정의 하 고 워크플로 클라이언트에 대 한 `proxyService` 옵션을 설정 합니다. 사용자에 대 한 프록시 서비스 옵션을 렌더링 하려면 UI에 사용자 입력을 추가 합니다. 입력을 클릭 하면 서비스 url을 로드 합니다. 다음 코드 조각에서는 프록시 서비스를 사용 하는 방법을 보여 줍니다.

```JavaScript

//A variable to hold the URL of the proxy service
var proxyServiceUrl = window.location.origin + 'CorsEnabledProxyService.ashx?url=';

//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: (document.getElementById('useProxyService').checked) ? proxyServiceUrl : null
});

function proxyOptionChanged() {
    if (currentServiceUrl) {
        loadClient(currentServiceUrl);
    }
}

```

아래 HTML 코드 조각은 위의 JavaScript 코드에 해당 합니다.

```html
<!-- use the proxy service -->
<input id="useProxyService" type="checkbox" onclick="proxyOptionChanged()"/>
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
> [지원 되는 데이터 형식 세부 정보](spatial-io-supported-data-format-details.md)
