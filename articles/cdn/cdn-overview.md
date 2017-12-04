---
title: "Azure CDN 개요 | Microsoft 문서"
description: "Azure CDN(Content Delivery Network) 정의와 Blob 및 정적 콘텐츠를 캐시하여 고대역폭 콘텐츠를 배달하는 데 사용하는 방법을 알아봅니다."
services: cdn
documentationcenter: 
author: smcevoy
manager: akucer
editor: 
ms.assetid: 866e0c30-1f33-43a5-91f0-d22f033b16c6
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/08/2017
ms.author: v-semcev
ms.openlocfilehash: 411c5a43d8a3245fc4642596b3725dadf8745728
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2017
---
# <a name="overview-of-the-azure-content-delivery-network-cdn"></a>Azure CDN(Content Delivery Network) 개요
Azure CDN(Content Delivery Network)은 전략적으로 배치된 위치에서 정적 웹 콘텐츠를 캐싱하여 사용자에게 콘텐츠를 배달하기 위한 최대 처리량을 제공합니다. CDN은 전 세계 물리적 노드에 콘텐츠를 캐시하여 고대역폭 콘텐츠를 배달하기 위한 글로벌 솔루션을 개발자에게 제공합니다. 

> [!NOTE]
> 이 문서에서는 Azure CDN, 작동 원리 및 각 Azure CDN 제품의 기능을 설명합니다. 이 정보를 건너뛰고 CDN 엔드포인트를 만드는 방법에 대한 자습서를 보려면 [Azure CDN 시작](cdn-create-new-endpoint.md)을 참조하세요. 현재 CDN 노드 위치 목록을 보려면 [Azure CDN POP 위치](cdn-pop-locations.md)를 참조하세요.
> 

CDN을 사용하여 웹 사이트 자산을 캐시하면 다음과 같은 이점이 있습니다.

* 콘텐츠를 로드하기 위해 많은 왕복이 필요한 응용 프로그램을 사용 중인 최종 사용자의 성능 및 사용자 환경 향상.
* 제품 런칭 이벤트 시작 시와 같이 순간적인 높은 부하를 더 효율적으로 처리하기 위한 대규모 크기 조정.
* 사용자 요청을 분산하고 에지 서버에서 바로 콘텐츠를 제공하므로 원본으로 전송되는 트래픽 양이 감소.


## <a name="how-it-works"></a>작동 방법
![CDN 개요](./media/cdn-overview/cdn-overview.png)

1. 사용자(Alice)가 특수 도메인 이름(예: `<endpointname>.azureedge.net`)으로 URL을 사용하여 파일(자산이라고도 함)을 요청합니다.  DNS는 최고 성능의 POP(상호 접속 위치) 위치로 요청을 라우팅합니다.  일반적으로 이것은 지리적으로 사용자에게 가장 가까운 POP입니다.
2. POP의 에지 서버의 캐시에 파일이 없으면, 에지 서버는 원본에서 파일을 요청합니다.  원본은 Azure Web App, Azure Cloud Service, Azure Storage 계정 또는 공개적으로 액세스할 수 있는 웹 서버입니다.
3. 원본은 파일의 TTL(Time-to-Live)을 설명하는 선택적인 HTTP 헤더를 포함하여, 파일을 에지 서버에 반환합니다.
4. 에지 서버는 파일을 캐싱하고 원래 요청자(Alice)에게 파일을 반환합니다.  파일은 TTL이 만료될 때가지 에지 서버에 캐싱된 상태로 남습니다.  원본이 TTL을 지정하지 않은 경우, 기본 TTL은 7일입니다.
5. 추가 사용자는 같은 URL을 사용하여 같은 파일을 요청할 수 있고, 같은 POP으로 전달될 수 있습니다.
6. 파일의 TTL이 만료되지 않았으면, 에지 서버는 캐시로부터 파일을 반환합니다.  이렇게 하면 보다 신속하고 응답성이 뛰어난 사용자 환경이 가능합니다.

## <a name="azure-cdn-features"></a>Azure CDN 기능
Azure CDN 제품은 **Akamai의 Azure CDN Standard**, **Verizon의 Azure CDN Standard**, **Verizon의 Azure CDN Premium**, 세 가지가 있습니다.  다음 표는 각 제품에 사용할 수 있는 기능입니다.

|  | Standard Akamai | Standard Verizon | Premium Verizon |
| --- | --- | --- | --- |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; __성능 기능과 최적화__ |
| [동적 사이트 가속](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[동적 사이트 가속 - 적응 이미지 압축](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-akamai-only) | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[동적 사이트 가속 - 개체 프리페치](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-akamai-only) | **&#x2713;**  |  |  |
| [비디오 스트리밍 최적화](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization) | **&#x2713;**  | \* |  \* |
| [큰 파일 최적화](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization) | **&#x2713;**  | \* |  \* |
| [전역 서버 부하 분산(GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [빠른 삭제](cdn-purge-endpoint.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [자산 미리 로드](cdn-preload-endpoint.md) | |**&#x2713;** |**&#x2713;** |
| [쿼리 문자열 캐싱](cdn-query-string.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| IPv4/IPv6 이중 스택 |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTP/2 지원](cdn-http2.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; __보안__ |
| CDN 끝점에 HTTPS 지원 |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [사용자 지정 도메인 HTTPS](cdn-custom-ssl.md) | |**&#x2713;** |**&#x2713;** |
| [사용자 지정 도메인 이름 지원](cdn-map-content-to-custom-domain.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [지역 필터링](cdn-restrict-access-by-country.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [토큰 인증](cdn-token-auth.md)|  |  |**&#x2713;**| 
| [DDOS 보호](https://www.us-cert.gov/ncas/tips/ST04-015) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; __분석 및 보고__ |
| [핵심 분석](cdn-analyze-usage-patterns.md) | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [고급 HTTP 보고서](cdn-advanced-http-reports.md) | | |**&#x2713;** |
| [실시간 통계](cdn-real-time-stats.md) | | |**&#x2713;** |
| [실시간 경고](cdn-real-time-alerts.md) | | |**&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; __사용 편의성__ |
| [Storage](cdn-create-a-storage-account-with-cdn.md), [Cloud Services](cdn-cloud-service-with-cdn.md), [Web Apps](../app-service/app-service-web-tutorial-content-delivery-network.md), [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md)와 같은 Azure와 간편하게 통합 |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [REST API](https://msdn.microsoft.com/library/mt634456.aspx), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) 또는 [PowerShell](cdn-manage-powershell.md)을 통한 관리. |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [사용자 지정이 가능한 규칙 기반의 콘텐츠 배달 엔진](cdn-rules-engine.md) | | |**&#x2713;** |
| 캐시/헤더 설정( [규칙 엔진](cdn-rules-engine.md)사용) | | |**&#x2713;** |
| URL 리디렉션/다시 쓰기([규칙 엔진](cdn-rules-engine.md)사용) | | |**&#x2713;** |
| 모바일 장치 규칙( [규칙 엔진](cdn-rules-engine.md)사용) | | |**&#x2713;** |

\*Verizon은 일반 웹 배달을 통해 큰 파일 및 미디어를 직접 지원합니다.


> [!TIP]
> Azure CDN에서 참조하려는 기능이 있나요?  [피드백 보내기](https://feedback.azure.com/forums/169397-cdn) 
> 
> 

## <a name="next-steps"></a>다음 단계
CDN을 시작하려면 [Azure CDN 시작](cdn-create-new-endpoint.md)을 참조하세요.

기존 CDN 고객인 경우 이제 [Microsoft Azure Portal](https://portal.azure.com) 또는 [PowerShell](cdn-manage-powershell.md)을 통해 CDN 끝점을 관리할 수 있습니다.

작동 중인 CDN 작업을 보려면 [빌드 2016 세션 비디오](https://azure.microsoft.com/documentation/videos/build-2016-leveraging-the-new-azure-cdn-apis-to-build-wicked-fast-applications/)를 확인하세요.

[.NET](cdn-app-dev-net.md) 또는 [Node.js](cdn-app-dev-node.md)를 사용하여 Azure CDN을 자동화하는 방법을 알아봅니다.

가격 책정 정보는 [Content Delivery Network 가격 책정](https://azure.microsoft.com/pricing/details/cdn/)을 참조하세요.

