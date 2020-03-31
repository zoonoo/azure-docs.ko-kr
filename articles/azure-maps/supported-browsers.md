---
title: 웹 SDK 지원 브라우저 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure Maps Web SDK용 지원되는 브라우저와 브라우저가 지원되는 브라우저인지 확인하는 방법에 대해 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: e81b15b974469d319384a67b08512130b7876a30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988790"
---
# <a name="web-sdk-supported-browsers"></a>웹 SDK 지원 브라우저

Azure Maps 웹 SDK는 [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-)라는 도우미 함수를 제공합니다. 이 함수는 웹 브라우저에 맵 컨트롤 로드 및 렌더링을 지원하는 데 필요한 최소 WebGL 기능 집합이 있는지 여부를 검색합니다. 함수를 사용하는 방법의 예는 다음과 같습니다.

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>바탕 화면

Azure Maps 웹 SDK는 다음 데스크톱 브라우저를 지원합니다.

- 마이크로소프트 에지 (현재 와 이전 버전)
- 구글 크롬 (현재와 이전 버전)
- 모질라 파이어 폭스 (현재와 이전 버전)
- 애플 사파리 (맥 OS X) (현재와 이전 버전)

이 문서의 후반부에서 [레거시 브라우저 대상을 참조하세요.](#Target-Legacy-Browsers)

## <a name="mobile"></a>휴대폰

Azure Maps 웹 SDK는 다음 모바일 브라우저를 지원합니다.

- Android
  - 안드로이드에 크롬의 현재 버전 6.0 이상
  - 안드로이드 에 크롬 웹 뷰 6.0 이상
- iOS
  - iOS의 현재 및 이전 주요 버전에서 모바일 사파리
  - uiWebView 및 WKWebView iOS의 현재 및 이전 주요 버전에
  - iOS용 크롬의 현재 버전

> [!TIP]
> WebView 컨트롤을 사용 하 여 모바일 응용 프로그램 내에 맵을 포함 하는 경우 Azure 콘텐츠 배달 네트워크에서 호스팅 되는 SDK의 버전을 참조 하는 대신 [Azure 지도 웹 SDK의 npm 패키지를](https://www.npmjs.com/package/azure-maps-control) 사용 하는 것이 좋습니다. 이 방법은 SDK가 이미 사용자의 장치에 있고 런타임에 다운로드할 필요가 없기 때문에 로드 시간을 줄입니다.

## <a name="nodejs"></a>Node.js

다음 웹 SDK 모듈은 Node.js에서도 지원됩니다.

- 서비스 모듈[(문서](how-to-use-services-module.md) | [npm 모듈)](https://www.npmjs.com/package/azure-maps-rest)

## <a name="target-legacy-browsers"></a><a name="Target-Legacy-Browsers"></a>레거시 브라우저 대상 지정

WebGL을 지원하지 않거나 지원이 제한된 이전 브라우저를 대상으로 지정할 수 있습니다. 이러한 경우 [리플렛](https://leafletjs.com/)과 같은 오픈 소스 맵 컨트롤과 함께 Azure Maps 서비스를 사용하는 것이 좋습니다. 예를 들면 다음과 같습니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure 지도 + 전단지" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>펜 Azure 지도 +<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure 지도별 <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>리플렛</a> () 을 참조하십시오.
</iframe>


## <a name="next-steps"></a>다음 단계

Azure 지도 웹 SDK에 대해 자세히 알아보십시오.

> [!div class="nextstepaction"]
> [지도 제어](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [서비스 모듈](how-to-use-services-module.md)
