---
title: 지원 되는 웹 브라우저-Azure Maps | Microsoft Docs
description: Azure Maps 웹 SDK에 대 한 지원 되는 웹 브라우저에 알아봅니다
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 2678fa7c9290c7ec0a27288e7739fde4bb0870fd
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58501273"
---
# <a name="supported-web-browsers"></a>지원되는 웹 브라우저

도우미 함수를 제공 하는 Azure Maps 웹 SDK [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-) 웹 브라우저를 최소 있는지 검색할 필요한 WebGL 지 원하는 기능을 로드 하 고 맵 컨트롤을 렌더링 합니다. 

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    //Add your map code here.
}
```

Azure Maps 웹 SDK는 다음 웹 브라우저를 지원합니다.

## <a name="desktop"></a>데스크톱

- Microsoft Edge의 현재 버전과 이전 버전 
- 현재 및 이전 버전의 Chrome 
- 현재 및 이전 버전의 Firefox 
- Safari (Mac OS X)의 현재 버전과 이전 버전 

참고 항목 [레거시 브라우저 대상](#Target-Legacy-Browsers)합니다.

## <a name="mobile"></a>모바일

-  Android
    * Android 6.0 이상에서 현재 버전의 Chrome
    * Android 6.0 이상에서 chrome 웹 보기
- iOS
    * IOS의 현재 버전과 이전 주 버전의 모바일 Safari
    * UIWebView 및 WKWebView 현재 및 이전 주요 버전의 iOS
    * IOS에 대 한 현재 버전의 Chrome

> [!TIP]
> 모바일 응용 프로그램 뷰 WebView 컨트롤 내에서 지도 포함 하는 경우 사용 하 여 알 수 있습니다 합니다 [npm 패키지는 Azure Maps 웹 SDK의](https://www.npmjs.com/package/azure-maps-control) CDN 호스트 버전의 SDK 참조 하는 것이 좋습니다. 이렇게 하면 SDK는 사용자의 장치에 이미 있을 하 고 런타임 시 다운로드할 필요가 없습니다 로드 시간이 줄어듭니다.

## <a name="nodejs"></a>Node.js

다음 웹 SDK 모듈은 node.js에서 에서도 지원 됩니다.

- Services 모듈 ([설명서](how-to-use-services-module.md) | [npm 모듈](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>대상 레거시 브라우저

와 같은 오픈 소스 맵 컨트롤을와 함께에서 Azure Maps 서비스를 사용 하도록 권장 되는 지원 하지 않거나 WebGL에 대 한 지원 제한 하는 이전 웹 브라우저를 대상으로 해야 하는 경우 [광고 지](https://leafletjs.com/)합니다. 

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + 광고 지" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
펜을을 참조 하세요 <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>광고 지 + Azure Maps</a> 에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에서 <a href='https://codepen.io'>CodePen</a>합니다.
</iframe>