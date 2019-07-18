---
title: 필터 및 Azure Media Services 동적 매니페스트 | Microsoft Docs
description: 이 항목에서는 클라이언트가 스트림의 특정 섹션을 스트리밍하는 데 사용할 수 있는 필터를 생성하는 방법을 설명합니다. 이 선택적 스트리밍은 Media Services가 동적 매니페스트를 생성하여 이루어집니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: ee0200f7c007b437a27b8e9d0f36becc13b8f611
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835812"
---
# <a name="pre-filtering-manifests-by-using-dynamic-packager"></a>동적 Packager를 사용 하 여 매니페스트를 사전 필터링

장치에 콘텐츠를 스트리밍 적응 비트 전송률을 전달할 때는 대상 특정 장치 기능에 사용 가능한 네트워크 대역폭 매니페스트의 여러 버전을 게시 해야 하는 경우가 많습니다. 합니다 [동적 Packager](dynamic-packaging-overview.md) 해상도, 오디오 및 비트 전송률을 조합에 즉석 복사본을 여러 개 만들 필요가 추적할 특정 코덱, 필터링 할 수 있는 필터를 지정할 수 있습니다. 대상 장치 (iOS, Android, SmartTV, 또는 브라우저) 및 네트워크 기능 (높은 대역폭, 모바일 또는 낮은 대역폭 시나리오)를 구성 하는 필터의 특정 집합을 사용 하 여 새 URL을 게시 해야 하는 하기만 하면 됩니다. 이 경우 쿼리 문자열을 통해 콘텐츠 스트리밍이 클라이언트를 조작할 수 있습니다 (사용할 수를 지정 하 여 [자산 필터 또는 계정 필터](filters-concept.md)) 필터를 사용 하 여 스트림의 특정 섹션을 스트림 합니다.

일부 배달 시나리오 아닌지 고객이 특정 추적에 액세스할 수 있도록 해야 합니다. 예를 들어, 특정 구독자 계층 HD 트랙을 포함 하는 매니페스트를 게시 하지 않을 수 있습니다. 또는 추가 추적에 활용할 수 있는 특정 장치에 배달 비용을 줄이기 위해 특정 적응 비트 전송률 (ABR) 트랙을 제거 하는 것이 좋습니다. 이 경우에 사용 하 여 미리 작성된 된 필터의 목록을 연결할 수 있습니다 하 [스트리밍 로케이터](streaming-locators-concept.md) 생성 합니다. 이 경우 클라이언트는 콘텐츠가 스트리밍됩니다 방법을 조작할 수 없습니다가 정의한 합니다 **스트리밍 로케이터**합니다.

지정 하 여 필터링을 결합할 수 있습니다 [스트리밍 로케이터에 대 한 필터](filters-concept.md#associating-filters-with-streaming-locator) + 클라이언트 URL에 지정 하는 추가 장치 특정 필터입니다. 이 메타 데이터 또는 이벤트 스트림, 오디오 언어 또는 설명이 포함 된 오디오 트랙와 같은 추가 추적을 제한 해야 할 수 있습니다. 

프로그램 스트림에서 다른 필터를 지정 하는이 기능을 제공 강력한 **동적 매니페스트** 조작 솔루션 대상 장치에 대해 여러 사용 사례 시나리오를 대상으로 합니다. 이 항목에서는 **동적 매니페스트**와 관련된 개념을 설명하고, 이 기능을 사용하는 것이 좋은 시나리오 예제를 제공합니다.

> [!NOTE]
> 동적 매니페스트는 자산 및 해당 자산의 기본 매니페스트를 변경하지 않습니다. 
> 

##  <a name="overview-of-manifests"></a>매니페스트 개요

Azure Media Services는 HLS, MPEG DASH 및 부드러운 스트리밍 프로토콜을 지원합니다. 일부로 [동적 패키징](dynamic-packaging-overview.md), 스트리밍 클라이언트 매니페스트 (HLS 마스터 재생 목록, 미디어 프레젠테이션 설명 [MPD] DASH 및 부드러운 스트리밍)의 URL 형식 선택기에 따라 동적으로 생성 됩니다. 배달 프로토콜 참조 [일반적인 주문형 워크플로](dynamic-packaging-overview.md#delivery-protocols)합니다. 

### <a name="get-and-examine-manifest-files"></a>매니페스트 파일 가져오기 및 검사

필터 추적 속성 조건 (라이브 또는 주문형 [VOD] 비디오) 스트림의 추적을 동적으로 만들어진 매니페스트에 포함시킬지에 따라 목록을 지정할 수 있습니다. 트랙의 속성을 가져오고 검사하려면 먼저 부드러운 스트리밍 매니페스트를 로드해야 합니다.

합니다 [업로드, 인코딩 및.NET을 사용 하 여 파일 스트림](stream-files-tutorial-with-api.md#get-streaming-urls) 자습서에서는.NET을 사용 하 여 스트리밍 Url을 작성 하는 방법을 보여 줍니다. 앱을 실행하는 경우 URL 중 하나가 부드러운 스트리밍 매니페스트를 가리킵니다. `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`<br/> URL을 복사하여 브라우저의 주소 표시줄에 붙여넣습니다. 파일이 다운로드됩니다. 원하는 텍스트 편집기에서 열 수 있습니다.

REST 예제를 보려면 [업로드, 인코딩 및 REST 사용 하 여 파일 스트림](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls)합니다.

### <a name="monitor-the-bitrate-of-a-video-stream"></a>비디오 스트림의 비트 전송률 모니터링

[Azure Media Player 데모 페이지](http://aka.ms/azuremediaplayer)를 사용하여 비디오 스트림의 비트 전송률을 모니터링할 수 있습니다. 데모 페이지에 진단 정보를 표시 합니다 **진단** 탭:

![Azure Media Player 진단][amp_diagnostics]
 
### <a name="examples-urls-with-filters-in-query-string"></a>예를 들면 다음과 같습니다. Url 쿼리 문자열에서 필터를 사용 하 여

ABR 스트리밍 프로토콜에 필터를 적용할 수 있습니다. HLS, MPEG-DASH 및 부드러운 스트리밍. 다음 표에서는 필터가 있는 URL의 몇 가지 예제를 보여 줍니다.

|프로토콜|예제|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|부드러운 스트리밍|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>변환 필터링

자산을 여러 인코딩 프로필 ((h.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) 및 여러 품질의 비트 전송률로 인코딩하는 데 선택할 수 있습니다. 그러나 모든 클라이언트 디바이스가 모든 자산 프로필 및 비트 전송률을 지원하는 것은 아닙니다. 예를 들어 이전 Android 장치에만 H.264 Baseline + AACL 지원합니다. 대역폭과 장치 계산을 낭비 하는 이점을 얻을 수 없습니다. 장치에 높은 비트 전송률로 전송 합니다. 이러한 장치는 표시에 대 한 규모 축소에 지정 된 모든 정보를 디코딩할 해야 합니다.

동적 매니페스트를 사용 하 여 장치 프로필을 만들 수 있습니다 (모바일 등 콘솔 또는 HD/SD) 추적 하 고 각 프로필의 일부가 되도록 하려는 품질을 포함 합니다. 변환 필터링 이라고 합니다. 다음 다이어그램의 예제를 보여 줍니다.

![변환 필터링 예제][renditions2]

다음 예제에서는 7개의 ISO MP4 비디오 변환(180p에서 1080p까지)으로 메자닌 자산을 인코드하는 데 인코더가 사용되었습니다. 인코딩된 자산은 될 수 있습니다 [동적으로 패키징](dynamic-packaging-overview.md) 다음 스트리밍 프로토콜 중 하나로: HLS, MPEG DASH 및 부드러운 스트리밍 

다음 다이어그램의 맨 위에 HLS 필터가 없는 자산에 대 한 매니페스트를 보여 줍니다. (7 개의 모든 변환 포함 합니다.)  다이어그램, 왼쪽 아래에는 "ott" 필터가 적용 된 HLS 매니페스트가 보여 줍니다. 응답에서 제거 된 아래쪽 두 품질 수준이 있으므로, 1mbps 아래의 모든 비트 전송률의 제거를 지정 하는 "ott" 필터입니다. 오른쪽 아래 다이어그램에는 "mobile" 필터가 적용 된 HLS 매니페스트가 보여 줍니다. "Mobile" 필터는 해상도 p 변환을 제거 된 두 1080 있도록 720p 보다 큰 변환의 제거를 지정 합니다.

![변환 필터링][renditions1]

## <a name="removing-language-tracks"></a>언어 트랙 제거
자산에는 영어, 스페인어, 프랑스어 등 여러 오디오 언어가 포함될 수 있습니다. 일반적으로 플레이어 SDK는 기본 오디오 트랙 선택 및 사용자별로 사용 가능한 오디오 트랙 선택을 관리합니다.

이러한 플레이어 Sdk 개발 어렵습니다, 장치별 플레이어 프레임 워크 간에 서로 다른 구현이 필요 하므로. 또한 일부 플랫폼에서는 플레이어 Api 제한 되며 사용자 선택 하거나 기본 오디오 트랙을 변경할 수 없습니다 오디오 선택 기능이 포함 되지 않습니다. 자산 필터를 사용하면 원하는 오디오 언어만 포함하는 필터를 만들어 동작을 제어할 수 있습니다.

![언어 트랙 필터링][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>자산의 시작 부분 트리밍

대부분의 라이브 스트리밍 이벤트에서 운영자는 실제 이벤트 전에 일부 테스트를 실행합니다. 예를 들어 이벤트의 시작하기 전에 "프로그램이 곧 시작 됩니다." 

프로그램을 보관하는 경우 테스트 및 슬레이트 데이터도 보관되며 프레젠테이션에도 포함됩니다. 그러나 이 정보가 클라이언트에는 표시되지 않아야 합니다. 동적 매니페스트를 사용하면 시작 시간 필터를 만들고 매니페스트에서 원치 않는 데이터를 제거할 수 있습니다.

![트리밍 시작][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>라이브 보관에서 하위 클립(뷰) 만들기

많은 라이브 이벤트가 장시간 실행되며 라이브 보관에는 여러 이벤트가 포함될 수 있습니다. 라이브 이벤트가 끝난 후 브로드캐스터는 라이브 보관을 논리 프로그램 시작 및 중단 시퀀스로 구분하려고 할 수 있습니다. 

(Cdn에서 캐시 된 기존 조각의 이점을 얻지 않습니다)는 post는 라이브 보관을 처리 하 고 별도 자산을 만들지 않고 가상 프로그램을 별도로 게시할 수 있습니다. 이러한 가상 프로그램의 예로는 축구 또는 농구 게임의 쿼터, 야구의 이닝 또는 스포츠 프로그램의 개별 행사 등이 있습니다.

동적 매니페스트를 사용 하 여 필터 시작/종료 시간을 사용 하 여 만들고 라이브 보관의 위에 가상 뷰를 만들 수 있습니다. 

![하위 클립 필터][subclip_filter]

필터링 된 자산은 다음과 같습니다.

![스키][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>프레젠테이션 창 (DVR) 조정

현재 Azure Media Services는 5 분에서 25 시간 사이의 기간을 구성할 수 있는 순환식 보관을 제공 합니다. 매니페스트 필터링을 사용하면 미디어를 삭제하지 않고도 보관 위에 롤링 DVR 창을 만들 수 있습니다. 브로드캐스터가 라이브 에지와 함께 이동하는 제한된 DVR 창을 제공하는 동시에 더 큰 보관 창을 유지하는 등의 여러 시나리오가 있습니다. 브로드캐스터는 DVR 창 외부의 데이터를 사용하여 클립을 강조하거나 여러 디바이스에 대해 서로 다른 DVR 창을 제공하려고 할 수 있습니다. 예를 들어, 대부분의 모바일 장치 (데스크톱 클라이언트에 대 한 모바일 장치에 대 한 고 1 시간 2 분 DVR 창을 수)는 큰 DVR 창을 처리 하지 못합니다.

![DVR 창][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>LiveBackoff(라이브 위치) 조정

매니페스트 필터링을 사용하면 라이브 프로그램의 라이브 에지에서 몇 초 정도 제거할 수 있습니다. 필터링 브로드캐스터가 미리 보기 게시 지점에서 프레젠테이션을 보고 광고 삽입 지점을 뷰어 (30 초 지원) 스트림을 수신 하기 전에 만들을 수 있습니다. 방송사 광고 기회 전에 정보를 수신 하 고 처리 하는 시간에 이러한 광고를 해당 클라이언트 프레임 워크에 푸시할 수 있습니다.

광고 지원 외에도 라이브 백오프 설정은 클라이언트 드리프트를 라이브에 지에 도달 하는 경우는 여전히 조각을 얻을 수는 서버에서 있도록 뷰어 위치를 조정 하려면 사용할 수 있습니다. 이런 방식으로 클라이언트는 HTTP 404 또는 412 오류 얻이 없습니다.

![라이브 백오프에 대 한 필터][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>단일 필터에 여러 규칙 결합

단일 필터에 여러 필터링 규칙을 결합할 수 있습니다. 예를 들어 라이브 보관에서 슬레이트를 제거하고 사용 가능한 비트 전송률을 필터링하는 "범위 규칙"을 정의할 수 있습니다. 여러 필터링 규칙을 적용 하는 경우 최종 결과 모든 규칙의 교집합입니다.

![여러 필터링 규칙][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>여러 개의 필터 결합(필터 컴퍼지션)

단일 URL로 여러 개의 필터를 결합할 수도 있습니다. 다음 시나리오는 필터를 결합하는 이유를 보여 줍니다.

1. 비디오 품질을 제한) (위해 Android 또는 iPad와 같은 모바일 장치에 대 한 비디오 품질을 필터링 해야 합니다. 원치 않는 품질을 제거 하려면 만들어야 계정 필터를 사용 하는 장치 프로필에 적합 합니다. 다른 추가적인 연결 없이 동일한 Media Services 계정의 모든 자산에 대 한 계정 필터를 사용할 수 있습니다.
1. 또한 자산의 시작 및 종료 시간을 트리밍하려고 합니다. 이 위해 자산 필터를 만들고 시작/종료 시간을 설정 합니다. 
1. 이러한 필터 두 결합 해야 합니다. 결합 하지 않고 필터 사용을 더 어렵게 만드는 트리밍 필터에 품질 필터링을 추가 해야 합니다.


필터를 결합 하려면 필터 이름을 매니페스트/재생 목록으로 설정 해야 형식의 세미콜론으로 구분 된 URL입니다. 필터가 있다고 가정해 보겠습니다 *MyMobileDevice* 품질을 필터링 하는 및 이라는 있다고 *MyStartTime* 설정 하려면 특정 시작 시간입니다. 최대 3개의 필터를 결합할 수 있습니다. 

자세한 내용은 [이 블로그 게시물](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)에 게시해 주세요.

## <a name="considerations-and-limitations"></a>고려 사항 및 제한 사항

- 에 대 한 값 **forceEndTimestamp**를 **presentationWindowDuration**, 및 **liveBackoffDuration** VOD 필터에 대 한 설정 하지 않아야 합니다. 라이브 필터링 시나리오에 대해서만 사용 합니다. 
-  동적 매니페스트는 GOP 경계 (키 프레임)에서 작동에 따라서 트리밍 GOP 정확도입니다.
-  계정 및 자산 필터에 대 한 동일한 필터 이름을 사용할 수 있습니다. 자산 필터는 더 높은 우선 순위 및 계정 필터를 재정의 합니다.
- 필터를 업데이트 하는 경우 규칙을 새로 고치는 스트리밍 끝점에 대 한 최대 2 분까지 걸릴 수 있습니다. 필터를 사용 하는 콘텐츠를 제공 하 고 프록시의 콘텐츠를 캐시과 CDN 캐시 하는 경우 이러한 필터 업데이트 플레이어 오류가 발생할 수 있습니다. 필터를 업데이트 한 후 캐시를 지우는 것이 좋습니다. 이 옵션을 사용할 수 없는 경우에 다른 필터를 사용 하는 것이 좋습니다.
- 고객이 수동으로 매니페스트를 다운로드 하 고 정확한 시작 타임 스탬프를 구문 분석 및 시간 단위를 해야 합니다.
    
    - 자산에서 추적의 속성을 확인 하려면 [매니페스트 파일을 검사 하 고](#get-and-examine-manifest-files)입니다.
    - 자산 필터 타임 스탬프 속성을 설정 하는 수식은 다음과 같습니다. <br/>startTimestamp = &lt;매니페스트에 시작 시간&gt; +  &lt;필터 시작 시간 (초) 예상&gt; * 날짜 표시줄

## <a name="next-steps"></a>다음 단계

다음 문서를 프로그래밍 방식으로 필터를 만드는 방법을 보여 줍니다.  

- [REST API를 사용하여 필터 만들기](filters-dynamic-manifest-rest-howto.md)
- [.NET을 사용하여 필터 만들기](filters-dynamic-manifest-dotnet-howto.md)
- [CLI를 사용하여 필터 만들기](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
