---
title: "Azure CDN을 통해 미디어 스트리밍 최적화"
description: "원활한 배달을 위해 스트리밍 미디어 파일 최적화"
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: v-semcev
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 582eb69ccd2830643644760753cca1c96d72128d
ms.contentlocale: ko-kr
ms.lasthandoff: 06/28/2017

---
# <a name="media-streaming-optimization-via-azure-cdn"></a>Azure CDN을 통해 미디어 스트리밍 최적화 
 
고화질 비디오가 인터넷에서 점점 더 많은 부분을 차지하게 되면서 인터넷을 통해 이러한 대용량 파일을 효율적으로 배달하는 것과 관련하여 다양한 문제가 발생합니다. 고객은 전 세계의 다양한 네트워크 및 클라이언트에서 VOD 또는 라이브 비디오 자산이 원활하게 재생되기를 바랍니다. 미디어 스트리밍 파일에 대해 더 빠르고 효율적인 배달 메커니즘을 제공하는 것은 원활하고 즐거운 소비자 환경을 보장하는 데 중요합니다.  

라이브 스트리밍 미디어는 크기가 크고 동시 시청자 수가 많을 뿐만 아니라 지연도 사용자에게는 문제가 되기 때문에 특히 배달하기가 어렵습니다. 라이브 스트림은 미리 캐시할 수 없고 긴 대기 시간은 시청자에게 허용되지 않으므로 비디오 조각을 시기 적절하게 배달해야 합니다. 

스트리밍의 요청 패턴 또한 몇 가지 새로운 과제를 제공합니다. 인기 있는 라이브 스트림의 경우나 새로운 시리즈가 VOD(주문형 비디오)로 릴리스되는 경우 수천 수백만 명의 시청자가 동시에 스트림을 요청할 수 있습니다. 이 경우 스마트 요청 통합은 자산이 아직 캐시되지 않은 경우 원본 서버에 과부하가 걸리지 않도록 합니다.
 
Akamai의 Azure CDN은 규모에 따라 전 세계의 최종 사용자에게 효율적으로 스트리밍 미디어 자산을 배달하고 원본 서버의 부하를 줄이는 동시에 대기 시간을 줄일 수 있는 기능을 제공합니다. 이 기능은 "Standard Akamai" 가격 책정 계층을 사용하여 Azure CDN 프로필에 만들어진 Azure CDN 끝점에서 "Optimized For"(최적화됨) 기능을 통해 사용할 수 있습니다. VOD 자산에는 "주문형 비디오 미디어 스트리밍" 최적화를 사용하고, 라이브 및 VOD 스트리밍 조합에는 "일반 미디어 스트리밍" 최적화를 사용해야 합니다.

Verizon의 Azure CDN은 "일반 웹 배달" 최적화 형식에서 직접 스트리밍 미디어를 배달할 수 있습니다.
 
## <a name="configuring-cdn-endpoint-to-optimize-delivery-of-media-streaming-in-azure-cdn-from-akamai"></a>Akamai의 Azure CDN에서 미디어 스트리밍의 배달을 최적화하도록 CDN 끝점 구성
 
끝점을 만드는 동안 "Optimized For"(최적화됨) 속성 선택에서 "일반 미디어 스트리밍"이나 "주문형 비디오 미디어 스트리밍" 옵션을 선택하기만 하면 Azure Portal을 통해 대용량 파일의 배달을 최적화하도록 CDN 끝점을 구성할 수 있습니다. REST API나 클라이언트 SDK를 사용할 수도 있습니다. 아래 스크린샷에서는 Azure Portal을 통한 프로세스를 보여 줍니다. 
  
![새 끝점 추가](./media/cdn-media-streaming-optimization/01_Adding.png)

*그림 1: CDN 프로필에서 새 CDN 끝점 추가*

![스트리밍 선택됨](./media/cdn-media-streaming-optimization/02_Creating.png) 
  
*그림 2: 주문형 비디오 미디어 스트리밍이 선택된 CDN 끝점 만들기* 
 
CDN 끝점을 만든 다음에는 특정 조건에 맞는 모든 파일에 대해 최적화를 적용합니다. 다음 섹션에서는 이 부분에 대해 자세히 설명합니다. 
 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Akamai의 Azure CDN에 대한 미디어 스트리밍 최적화
 
Akamai의 미디어 스트리밍 최적화는 점진적 다운로드나 바이트 범위 요청을 통해 전송되는 하나의 큰 자산과 반대로 배달에 개별 미디어 조각을 사용하는 라이브 또는 VOD 스트리밍 미디어에 적용됩니다. 해당 스타일의 미디어 배달은 [대용량 파일 최적화](cdn-large-file-optimization.md)를 확인하세요.

일반 미디어 배달 또는 VOD 미디어 배달 최적화 형식을 사용하면 시간에 따라 학습된 모범 사례를 기반으로 하는 미디어 자산의 구성뿐만 아니라 백 엔드 최적화가 적용된 CDN 네트워크를 사용하여 미디어 자산을 더 빠르게 배달합니다.

### <a name="caching"></a>구성

Akamai의 Azure CDN에서 자산이 스트리밍 매니페스트 또는 조각(전체 목록은 아래 참조)임이 감지되면 CDN은 일반 배달에서 서로 다른 캐싱 만료 시간을 사용합니다. 항상 원본에서 전송된 cache-control 또는 expires 헤더가 적용되며 미디어 자산이 아닐 경우 일반 웹 배달에 만료 시간을 사용하여 캐시합니다.

짧은 음수 캐싱 시간은 해당 시간(초)에 원본에서 패킷을 사용할 수 없는 라이브 스트림에서처럼 많은 사용자가 아직 존재하지 않는 조각을 요청하는 경우의 원본 오프로드에 유용합니다. 비디오 콘텐츠는 일반적으로 수정되지 않기 때문에 더 길어진 캐싱 간격도 원본의 오프로드 요청에 도움이 됩니다.
 

|    | 일반<br> web<br>배달 | 일반<br> 미디어<br> 스트리밍 | VOD<br>미디어<br> 스트리밍  
--- | --- | --- | ---
캐싱 - 양수 <br> HTTP 200, 203, 300, <br> 301, 302 및 410 | 7 일 |365일 | 365일   
캐싱 - 음수 <br> HTTP 204, 305, 404 <br> 및 405 | 없음 | 1초 | 1초
 
### <a name="dealing-with-origin-failure"></a>원본 오류 처리  

일반 미디어 배달 및 VOD 미디어 배달에도 일반 요청 패턴에 대한 모범 사례를 기반으로 하는 원본 시간 제한 및 다시 시도 로그가 있습니다. 예를 들어 일반 미디어 배달은 라이브 및 VOD 미디어 배달을 모두 대상으로 하기 때문에 시간이 중요한 라이브 스트리밍의 특성으로 인해 훨씬 더 짧은 연결 시간 제한을 사용합니다.

연결 시간이 초과되면 CDN은 504 게이트웨이 시간 초과 오류를 클라이언트로 보내기 전에 특정 횟수 만큼 다시 시도합니다. 

파일이 파일 형식 및 크기 조건 목록과 일치하면 CDN은 미디어 스트리밍에 대한 동작을 사용하고, 그러지 않으면 일반 웹 배달에 대한 동작을 사용합니다. 
   
### <a name="conditions-for-media-streaming-optimization"></a>미디어 스트리밍 최적화에 대한 조건 

다음 표에서는 이 최적화를 위해 충족되어야 하는 조건 집합을 보여 줍니다. 
 
지원되는 스트리밍 유형 | 파일 확장명  
--- | ---  
Apple HLS | m3u8, m3u, m3ub, key, ts, aac
Adobe HDS | f4m, f4x, drmmeta, bootstrap, f4f,<br>Seg-Frag URL 구조 <br> (일치하는 regex: ^(/.*)Seq(\d+)-Frag(\d+)
DASH | mpd, dash, divx, ismv, m4s, m4v, mp4, mp4v, <br> sidx, webm, mp4a, m4a, isma
부드러운 스트리밍 | /manifest/,/QualityLevels/Fragments/
  

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Verizon의 Azure CDN에 대한 미디어 스트리밍 최적화

Verizon의 Azure CDN은 ‘일반 웹 배달’ 최적화 형식을 사용하여 직접 스트리밍 미디어 자산을 배달할 수 있습니다. 기본적으로 CDN에는 미디어 자산 배달에 직접 도움이 되는 몇 가지 기능도 있습니다.

### <a name="partial-cache-sharing"></a>부분 캐시 공유

따라서 부분적으로 캐시된 콘텐츠를 CDN에서 새 요청에 제공할 수 있습니다. 즉, 처음 CDN에 요청하면 캐시가 누락되고 요청은 원본으로 전송됩니다. 이 콘텐츠가 CDN 캐시에 계속 로드되지만 완전하지 않으면 CDN에 대한 다른 요청에서 이 데이터 가져오기를 시작할 수 있습니다. 

### <a name="cache-fill-wait-time"></a>캐시 채우기 대기 시간

부분 캐시 공유와 함께 유용한 캐시 채우기 대기 시간 기능은 HTTP 응답 헤더가 원본 서버에서 도착할 때까지 에지 서버에서 동일한 리소스에 대한 향후 요청을 보류하도록 합니다. 타이머가 만료되기 전에 원본 서버의 HTTP 응답 헤더가 도착하면 보류된 모든 요청이 증가하는 캐시에서 처리되고 동시에 캐시는 원본의 데이터로 채워집니다. 기본적으로 캐시 채우기 대기 시간은 3000밀리초로 설정됩니다. 


