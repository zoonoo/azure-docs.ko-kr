<properties 
	pageTitle="Azure CDN 개요" 
	description="Azure CDN(콘텐츠 배달 네트워크) 정의와 Blob 및 정적 콘텐츠를 캐시하여 고대역폭 콘텐츠를 배달하는 데 사용하는 방법을 알아봅니다." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="casoper"/>

# Azure CDN(콘텐츠 배달 네트워크) 개요

Azure CDN(콘텐츠 배달 네트워크)은 클라우드 서비스에서 사용하는 Azure Blob 및 정적 콘텐츠를 전략적으로 배치된 위치에 캐시하여 사용자에게 콘텐츠를 배달하는 데 최대 대역폭을 제공합니다.

기존 CDN 고객인 경우 이제 [Microsoft Azure 포털](https://portal.azure.com)을 통해 CDN 끝점을 관리할 수 있습니다.


CDN은 전 세계 물리적 노드에 콘텐츠를 캐시하여 고대역폭 콘텐츠를 배달하기 위한 글로벌 솔루션을 개발자에게 제공합니다. CDN 노드 위치의 현재 목록은 [Azure CDN(콘텐츠 배달 네트워크) POP 위치](cdn-pop-locations.md)를 참조하세요.

CDN을 사용하여 Azure 데이터를 캐시할 경우의 이점은 다음과 같습니다.

- 콘텐츠 원본에서 멀리 떨어져 있으며 콘텐츠를 로드하기 위해 많은 '인터넷 이동'이 필요한 응용 프로그램을 사용 중인 최종 사용자의 성능 및 사용자 환경 향상
- 제품 런칭 이벤트 시작 시와 같이 순간적인 높은 부하를 더 효율적으로 처리하기 위한 대규모 분산 


>[AZURE.IMPORTANT]CDN 끝점을 만들거나 사용하도록 설정할 때 전 세계에 전파하려면 최대 90분이 걸릴 수 있습니다.
 
CDN에 개체 요청을 처음 수행하는 경우 개체의 소스 원본 위치에서 개체가 직접 검색됩니다. 이 원본은 Azure 저장소 계정, 웹앱, 클라우드 서비스 또는 공용 웹 요청을 허용하는 모든 사용자 지정 원본이 될 수 있습니다. CDN 구문을 사용하여 요청을 수행하는 경우 개체에 대한 액세스를 제공하기 위해 요청이 수행된 위치에 가장 가까운 CDN 끝점으로 요청이 리디렉션됩니다. 해당 끝점에 개체가 없을 경우 서비스에서 검색되어 끝점에 캐시되며, 해당 끝점에서 캐시된 개체에 대한 TTL(Time-To-Live) 설정이 유지 관리됩니다.

## 표준 기능

표준 CDN 계층에는 이러한 기능이 포함됩니다.

- [저장소](cdn-create-a-storage-account-with-cdn.md), 웹앱 및 미디어 서비스와 같은 Azure와의 간편한 통합
- [쿼리 문자열 캐싱](cdn-query-string.md)
- [사용자 지정 도메인 이름 지원](cdn-map-content-to-custom-domain.md)
- [국가 필터링](cdn-restrict-access-by-country.md)
- [핵심 분석](cdn-analyze-usage-patterns.md)
- [사용자 지정 콘텐츠 원본](cdn-map-content-to-custom-domain.md)
- [HTTPS 지원](cdn-how-to-use-cdn.md#accessing-cached-content-over-https)
- 부하 분산
- DDOS 보호
- [빠른 삭제](cdn-purge-endpoint.md)

 
## 프리미엄 기능

프리미엄 CDN 계층에는 표준 계층의 모든 기능과 함께 이러한 추가 기능이 포함됩니다.

- [사용자 지정이 가능한 규칙 기반의 콘텐츠 배달 엔진](cdn-rules-engine.md)
- [고급 HTTP 보고서](cdn-advanced-http-reports.md)
- [실시간 통계](cdn-read-time-stats.md)

<!---HONumber=AcomDC_1210_2015-->