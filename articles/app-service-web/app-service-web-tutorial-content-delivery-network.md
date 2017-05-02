---
title: "Azure App Service에 Content Delivery Network 추가 | Microsoft Docs"
description: "에지 노드의 정적 파일을 배달하려면 Azure App Service에 Content Delivery Network를 추가합니다."
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 04/03/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 7ba3737566401152a3171e8926beca188045230c
ms.lasthandoff: 04/22/2017

---
# <a name="add-a-content-deliver-network-on-an-azure-app-service"></a>Azure App Service에 Content Delivery Network 추가

이 자습서에서는 Azure App Service에 CDN(Content Delivery Network)을 추가하여 에지 서버에 정적 콘텐츠를 노출할 것입니다. 최대 10개의 CDN 끝점 컬렉션인 CDN 프로필을 만들 것입니다.

Content Delivery Network는 전략적으로 배치된 위치에 정적 웹 콘텐츠를 캐싱하여 사용자에게 콘텐츠를 배달하기 위한 최대 처리량을 제공합니다. CDN을 사용하여 웹 사이트 자산을 캐시할 경우의 이점은 다음과 같습니다.

* 콘텐츠를 로드하기 위해 많은 왕복이 필요한 응용 프로그램을 사용 중인 최종 사용자의 성능 및 사용자 환경 향상
* 제품 런칭 이벤트 시작 시와 같이 순간적인 높은 부하를 더 효율적으로 처리하기 위한 대규모 조정
* 사용자 요청을 분산하고 에지 서버에서 콘텐츠를 제공하여 원본으로 전송되는 트래픽 양이 감소합니다.

> [!TIP]
> [Azure CDN POP 위치](https://docs.microsoft.com/en-us/azure/cdn/cdn-pop-locations)의 최신 목록을 검토합니다.
>

## <a name="deploy-the-sample"></a>샘플 배포

이 자습서를 완료하려면 웹앱에 배포된 응용 프로그램이 필요합니다. [정적 HTML 빠른 시작](app-service-web-get-started-html.md)을 따라 이 자습서의 기본 사항을 제공합니다.

## <a name="step-1---login-to-azure-portal"></a>1단계 - Azure Portal에 로그인

먼저 자주 사용하는 브라우저를 열고 Azure [Portal](https://portal.azure.com)로 이동합니다.

## <a name="step-2---create-a-cdn-profile"></a>2단계 - CDN 프로필 만들기

왼쪽 탐색 창에서 `+ New` 단추를 클릭하고 **웹 + 모바일**을 클릭합니다. [웹 + 모바일] 범주 아래에서 **CDN**을 선택합니다.

다음 필드를 지정합니다.

| 필드 | 샘플 값 | 설명 |
|---|---|---|
| 이름 | myCDNProfile | CDN 프로필의 이름입니다. |
| 위치 | 서유럽 | CDN 프로필 정보가 저장될 Azure 위치입니다. CDN 끝점 위치에는 영향을 주지 않습니다. |
| 리소스 그룹 | myResourceGroup | 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md#resource-groups)를 참조하세요. |
| 가격 책정 계층  | Standard Akamai | 가격 책정 계층 비교는 [CDN 개요](../cdn/cdn-overview.md#azure-cdn-features)를 참조하세요. |

**만들기**를 클릭합니다.

왼쪽 탐색에서 리소스 그룹 허브를 열고 **myResourceGroup**을 선택합니다. 리소스 목록에서 **myCDNProfile**을 선택합니다.

![azure-cdn-profile-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-profile-created.png)

## <a name="step-3---create-a-cdn-endpoint"></a>3단계 - CDN 끝점 만들기

검색 상자 옆에 있는 명령의 **+ 끝점**을 클릭하면 끝점 생성 블레이드가 시작됩니다.

다음 필드를 지정합니다.

| 필드 | 샘플 값 | 설명 |
|---|---|
| 이름 |  | 이 이름은 `<endpointname>.azureedge.net` 도메인의 캐시된 리소스에 액세스하기 위해 사용됩니다. |
| 원본 형식 | 웹앱 | 원본 형식을 선택하면 나머지 필드에 대한 상황에 맞는 메뉴가 제공됩니다. 사용자 지정 원본을 선택하면 원본 호스트 이름에 대한 텍스트 필드가 제공됩니다. |
| 원본 호스트 이름 | |  사용자가 지정한 모든 사용 가능한 원본 형식이 드롭다운에 나열됩니다. 원본 형식으로 사용자 지정 원본을 선택한 경우 사용자 지정 원본의 도메인을 입력합니다.  |

**추가**를 클릭합니다.

끝점이 만들어지고 Content Delivery Network 끝점이 생성되면 상태가 **실행 중**으로 업데이트됩니다.

![azure-cdn-endpoint-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-endpoint-created.png)

## <a name="step-4---serve-from-azure-cdn"></a>4단계 - Azure CDN에서 서비스 제공

이제 CDN 끝점이 **실행 중**이므로 CDN 끝점에서 콘텐츠에 액세스할 수 있습니다.

이 자습서의 기반으로 [정적 HTML 빠른 시작](app-service-web-get-started-html.md)을 사용했기 때문에 CDN에 `css`, `img`, `js` 폴더가 있습니다.

웹앱 URL `http://<app_name>.azurewebsites.net/img/` 및 CDN 끝점 URL `http://<endpointname>.azureedge.net/img/` 사이의 콘텐츠 경로는 동일합니다. 다시 말해서 CDN에서 서비스를 제공할 모든 정적 콘텐츠의 CDN 끝점 도메인을 간단하게 대체할 수 있습니다.

자주 사용하는 웹 브라우저에서 다음 url로 이동하여 CDN 끝점에서 첫 번째 이미지를 가져오겠습니다.

```bash
http://<endpointname>.azureedge.net/img/03-enterprise.png
```

CDN에서 정적 콘텐츠를 사용할 수 있으므로, CDN 끝점을 사용하여 최종 사용자에게 콘텐츠를 제공하도록 응용 프로그램을 업데이트할 수 있습니다.

사이트의 기반 언어에 따라 CDN 대체에 도움이 되는 여러 프레임워크가 있을 수 있습니다. 예를 들어 ASP.NET는 [묶음 및 축소](https://docs.microsoft.com/en-us/aspnet/mvc/overview/performance/bundling-and-minification#using-a-cdn) 지원을 제공하며 CDN 대체 기능도 지원합니다.

사용하는 언어에서 CDN 장애 복구 지원을 위한 기본 제공 라이브러리를 제공하지 않을 경우 [스크립트](https://github.com/dolox/fallback/tree/master/examples/loading-scripts), [스타일 시트](https://github.com/dolox/fallback/tree/master/examples/loading-stylesheets) 및 [이미지](https://github.com/dolox/fallback/tree/master/examples/loading-images)를 지원하는 [FallbackJS](http://fallback.io/) 같은 javascript 프레임워크를 사용하면 됩니다.

## <a name="step-5---purge-the-cdn"></a>5단계 - CDN 제거

때때로 TTL(Time-to-Live)이 만료되기 전에 콘텐츠를 만료하기 위해 CDN을 강제로 제거해야 하는 경우가 있습니다.

CDN 프로필 블레이드 또는 CDN 끝점 블레이드에서 Azure CDN을 수동으로 제거할 수 있습니다. 프로필 페이지에서 제거하는 방법을 선택할 경우 어떤 끝점을 제거할 것인지 선택해야 합니다.

콘텐츠를 제거하려면 제거할 콘텐츠 경로를 입력합니다. 완전한 파일 경로를 전달하여 개별 파일을 제거할 수도 있고, 경로 세그먼트를 전달하여 특정 폴더의 콘텐츠를 제거하고 새로 고칠 수도 있습니다.

제거할 모든 콘텐츠 경로를 입력한 후 **제거**를 클릭합니다.

![app-service-web-purge-cdn](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

## <a name="step-6---map-a-custom-domain"></a>6단계 - 사용자 지정 도메인 매핑

사용자 지정 도메인을 CDN 끝점에 매핑하면 웹 응용 프로그램에 대한 균일한 도메인이 제공됩니다.

사용자 지정 도메인을 CDN 끝점에 매핑하려면 도메인 등록 기관을 사용하여 CNAME 레코드를 만들어야 합니다.

> [!NOTE]
> CNAME 레코드는 `www.contosocdn.com` 또는 `static.contosocdn.com` 같은 원본 도메인을 대상 도메인에 매핑하는 DNS 기능입니다.

이 예에서는 `static.contosocdn.com` 원본 도메인을 추가하고, CDN 끝점인 대상 도메인을 가리킬 것입니다.

| 원본 도메인 | 대상 도메인 |
|---|---|
| static.contosocdn.com | &lt;endpointname&gt;.azureedge.net |

CDN 끝점 개요 블레이드에서 `+ Custom domain` 단추를 클릭합니다.

[사용자 지정 도메인 추가] 블레이드에서 하위 도메인을 비롯한 사용자 지정 도메인을 대화 상자에 입력합니다. 예를 들어 `static.contosocdn.com` 형식으로 도메인 이름을 입력합니다.

**추가**를 클릭합니다.

## <a name="step-7---version-content"></a>7단계 - 버전 내용

CDN 끝점 왼쪽 탐색 영역의 [설정] 머리글 아래에서 **캐시**를 선택합니다.

**캐시** 블레이드를 사용하여 CDN이 요청의 쿼리 문자열을 처리하는 방법을 구성할 수 있습니다.

> [!NOTE]
> 쿼리 문자열 캐싱 동작 옵션에 대한 설명은 [쿼리 문자열을 사용하여 Azure CDN 캐싱 동작 제어](../cdn/cdn-query-string.md) 토픽을 참조하세요.

쿼리 문자열 캐싱 동작의 드롭다운에서 **모든 고유한 URL 캐시**를 선택합니다.

**저장**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

* [Azure CDN이란?](../best-practices-cdn.md?toc=%2fazure%2fcdn%2ftoc.json)
* [Azure CDN 사용자 지정 도메인에서 HTTPS를 사용하도록 설정](../cdn/cdn-custom-ssl.md)
* [Azure CDN에서 파일을 압축하여 성능 향상](../cdn/cdn-improve-performance.md)
* [Azure CDN 끝점에 자산 미리 로드](../cdn/cdn-preload-endpoint.md)

