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
ms.date: 05/22/2019
ms.author: juliako
ms.openlocfilehash: 041a73cd2840e0b6a1840e15629d9c0e284e9890
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66225518"
---
# <a name="pre-filtering-manifests-with-dynamic-packager"></a>동적 Packager를 사용 하 여 매니페스트를 사전 필터링

적응 비트 전송률 스트리밍 장치로 콘텐츠를 제공 하는 경우 대상 특정 장치 기능에 사용 가능한 네트워크 대역폭 매니페스트의 여러 버전을 게시 해야 하는 경우가 많습니다. 합니다 [동적 Packager](dynamic-packaging-overview.md) 해상도, 오디오 및 비트 전송률을 조합에 즉석 복사본을 여러 개 만들 필요가 추적할 특정 코덱, 필터링 할 수 있는 필터를 지정할 수 있습니다. 대상 장치 (iOS, Android, SmartTV, 또는 브라우저) 및 네트워크 기능 (높은 대역폭, 모바일 또는 낮은 대역폭 시나리오)를 구성 하는 필터의 특정 집합을 사용 하 여 새 URL을 게시 해야 하는 하기만 하면 됩니다. 이 경우 쿼리 문자열을 통해 콘텐츠 스트리밍이 클라이언트를 조작할 수 있습니다 (사용할 수를 지정 하 여 [자산 필터 또는 계정 필터](filters-concept.md)) 필터를 사용 하 여 스트림의 특정 섹션을 스트림 합니다.

일부 배달 시나리오 아닌지 고객이 특정 추적에 액세스할 수 있도록 해야 합니다. 예를 들어, 특정 구독자 계층 HD 트랙을 포함 하는 매니페스트를 게시 하지 않을 수 있습니다. 또는 추가 추적에 활용할 수 있는 특정 장치에 배달 비용을 줄이기 위해 특정 적응 비트 전송률 (ABR) 트랙을 제거 하는 것이 좋습니다. 이 경우에 사용 하 여 미리 작성된 된 필터의 목록을 연결할 수 있습니다 하 [스트리밍 로케이터](streaming-locators-concept.md) 생성 합니다. 이 경우 클라이언트는 콘텐츠가 스트리밍됩니다 방법을 조작할 수 없습니다가 정의한 합니다 **스트리밍 로케이터**합니다.

지정 하 여 필터링을 결합할 수 있습니다 [스트리밍 로케이터에 대 한 필터](filters-concept.md#associating-filters-with-streaming-locator) + 클라이언트 URL에 지정 하는 추가 장치 특정 필터입니다. 이 메타 데이터 또는 이벤트 스트림, 오디오 언어 또는 설명이 포함 된 오디오 트랙와 같은 추가 추적을 제한 해야 할 수 있습니다. 

프로그램 스트림에서 다른 필터를 지정 하는이 기능을 제공 강력한 **동적 매니페스트** 조작 솔루션 대상 장치에 대해 여러 사용 사례 시나리오를 대상으로 합니다. 이 항목에서는 **동적 매니페스트**와 관련된 개념을 설명하고, 이 기능을 사용하는 것이 좋은 시나리오 예제를 제공합니다.

> [!NOTE]
> 동적 매니페스트는 자산 및 해당 자산의 기본 매니페스트를 변경하지 않습니다. 
> 

## <a name="manifests-overview"></a>매니페스트 개요

Media Services는 HLS, MPEG DASH, 부드러운 스트리밍 프로토콜을 지원 합니다. 일부로 [동적 패키징](dynamic-packaging-overview.md), 스트리밍 클라이언트 매니페스트 (HLS 마스터 재생 목록, DASH MPD 미디어 프레젠테이션 설명 (), 및 부드러운 스트리밍)의 URL 형식 선택기에 따라 동적으로 생성 됩니다. 배달 프로토콜 참조 [이 섹션에서는](dynamic-packaging-overview.md#delivery-protocols)합니다. 

### <a name="get-and-examine-manifest-files"></a>매니페스트 파일 가져오기 및 검사

동적으로 작성된 매니페스트에 포함해야 하는 스트림(라이브 또는 주문형 비디오)의 트랙을 기반으로 필터 트랙 속성 조건의 목록을 지정합니다. 트랙의 속성을 가져오고 검사하려면 먼저 부드러운 스트리밍 매니페스트를 로드해야 합니다.

[Upload, encode, and stream files with .NET](stream-files-tutorial-with-api.md)(.NET을 사용하여 파일 업로드, 인코딩 및 스트림) 자습서에서는 .NET을 사용하여 스트리밍 URL을 작성하는 방법을 설명합니다. [building URLs](stream-files-tutorial-with-api.md#get-streaming-urls)(URL 작성) 섹션을 참조하세요. 앱을 실행하는 경우 URL 중 하나가 부드러운 스트리밍 매니페스트를 가리킵니다. `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`<br/>URL을 복사하여 브라우저의 주소 표시줄에 붙여넣습니다. 파일이 다운로드됩니다. 원하는 텍스트 편집기에서 열 수 있습니다.

REST 예제는 [Upload, encode, and stream files with REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls)(REST를 사용하여 파일 업로드, 인코딩 및 스트림)를 참조하세요.

### <a name="monitor-the-bitrate-of-a-video-stream"></a>비디오 스트림의 비트 전송률 모니터링

[Azure Media Player 데모 페이지](https://aka.ms/amp)를 사용하여 비디오 스트림의 비트 전송률을 모니터링할 수 있습니다. 데모 페이지의 **진단** 탭에 진단 정보가 표시됩니다.

![Azure Media Player 진단][amp_diagnostics]
 
### <a name="examples-urls-with-filters-in-query-string"></a>예제: Url 쿼리 문자열에서 필터를 사용 하 여

적응 비트 전송률 스트리밍 프로토콜에 필터를 적용할 수 있습니다. HLS, MPEG-DASH 및 부드러운 스트리밍. 다음 표에서는 필터가 있는 URL의 몇 가지 예제를 보여 줍니다.

|프로토콜|예|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|부드러운 스트리밍|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>변환 필터링

자산을 여러 인코딩 프로필(H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) 및 여러 품질의 비트 전송률로 인코딩할 수 있습니다. 그러나 모든 클라이언트 디바이스가 모든 자산 프로필 및 비트 전송률을 지원하는 것은 아닙니다. 예를 들어 이전 Android 디바이스는 H.264 Baseline+AACL만 지원합니다. 이점을 얻을 수 없는 디바이스에 높은 비트 전송률로 전송하면 대역폭과 디바이스 계산 성능을 낭비하게 됩니다. 이런 디바이스가 해당하는 모든 정보를 표시하기 위해서는 디코딩을 통해 규모를 축소해야 합니다.

동적 매니페스트를 사용하면 모바일, 콘솔, HD/SD 등과 같은 디바이스 프로필을 만들고 각 프로필의 일부로 제공할 트랙 및 품질을 포함할 수 있습니다.

![변환 필터링 예제][renditions2]

다음 예제에서는 7개의 ISO MP4 비디오 변환(180p에서 1080p까지)으로 메자닌 자산을 인코드하는 데 인코더가 사용되었습니다. 인코딩된 자산은 될 수 있습니다 [동적으로 패키징](dynamic-packaging-overview.md) 다음 스트리밍 프로토콜 중 하나로: HLS, MPEG DASH 및 부드러운 스트리밍  다이어그램의 맨 위에 필터가 없는 자산을 위한 HLS 매니페스트가 나와 있습니다(7개의 모든 변환 포함).  왼쪽 아래에는 "ott" 필터가 적용된 HLS 매니페스트가 표시되어 있습니다. “ott” 필터는 1Mbps 미만의 모든 비트 전송률을 제거하도록 지정하며, 이 때문에 응답에서 맨 아래의 두 품질 수준이 제거됩니다. 오른쪽 아래에는 "mobile" 필터가 적용된 HLS 매니페스트가 나와 있습니다. "mobile" 필터는 해상도가 720p보다 높은 변환을 제거하도록 지정하며, 이 때문에 두 개의 1080p 변환이 제거됩니다.

![변환 필터링][renditions1]

## <a name="removing-language-tracks"></a>언어 트랙 제거
자산에는 영어, 스페인어, 프랑스어 등 여러 오디오 언어가 포함될 수 있습니다. 일반적으로 플레이어 SDK는 기본 오디오 트랙 선택 및 사용자별로 사용 가능한 오디오 트랙 선택을 관리합니다. 이러한 플레이어 SDK를 개발하는 것은 까다로운 일로, 디바이스별 플레이어 프레임워크 간에 서로 다른 구현이 필요합니다. 또한 일부 플랫폼은 플레이어 API가 제한되어 있고 오디오 선택 기능이 포함되어 있지 않으므로 사용자는 기본 오디오 트랙을 선택하거나 변경할 수 없습니다. 자산 필터를 사용하면 원하는 오디오 언어만 포함하는 필터를 만들어 동작을 제어할 수 있습니다.

![언어 트랙 필터링][language_filter]

## <a name="trimming-start-of-an-asset"></a>자산의 시작 부분 트리밍
대부분의 라이브 스트리밍 이벤트에서 운영자는 실제 이벤트 전에 일부 테스트를 실행합니다. 예를 들어 이벤트의 시작하기 전에 "프로그램이 곧 시작됩니다"와 같은 슬레이트를 포함할 수 있습니다. 프로그램을 보관하는 경우 테스트 및 슬레이트 데이터도 보관되며 프레젠테이션에도 포함됩니다. 그러나 이 정보가 클라이언트에는 표시되지 않아야 합니다. 동적 매니페스트를 사용하면 시작 시간 필터를 만들고 매니페스트에서 원치 않는 데이터를 제거할 수 있습니다.

![트리밍 시작][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>라이브 보관에서 하위 클립(뷰) 만들기
많은 라이브 이벤트가 장시간 실행되며 라이브 보관에는 여러 이벤트가 포함될 수 있습니다. 라이브 이벤트가 끝난 후 브로드캐스터는 라이브 보관을 논리 프로그램 시작 및 중단 시퀀스로 구분하려고 할 수 있습니다. 다음으로 후처리 없이 별도의 자산을 만들지 않고 가상 프로그램을 개별적으로 게시합니다. 따라서 CDN에서 캐시된 기존 조각의 이점을 얻지 못하게 됩니다. 이러한 가상 프로그램의 예로는 축구 또는 농구 게임의 쿼터, 야구의 이닝 또는 스포츠 프로그램의 개별 행사 등이 있습니다.

동적 매니페스트를 사용하면 시작/종료 시간을 사용하여 필터를 만들고 라이브 보관의 위에 가상 뷰를 만들 수 있습니다. 

![하위 클립 필터][subclip_filter]

필터링된 자산:

![스키][skiing]

## <a name="adjusting-presentation-window-dvr"></a>프레젠테이션 창(DVR) 조정
현재, Azure Media Services는 5분에서 25시간까지 기간을 구성할 수 있는 순환식 보관을 제공합니다. 매니페스트 필터링을 사용하면 미디어를 삭제하지 않고도 보관 위에 롤링 DVR 창을 만들 수 있습니다. 브로드캐스터가 라이브 에지와 함께 이동하는 제한된 DVR 창을 제공하는 동시에 더 큰 보관 창을 유지하는 등의 여러 시나리오가 있습니다. 브로드캐스터는 DVR 창 외부의 데이터를 사용하여 클립을 강조하거나 여러 디바이스에 대해 서로 다른 DVR 창을 제공하려고 할 수 있습니다. 예를 들어 대부분의 모바일 디바이스는 큰 DVR 창을 처리하지 못합니다. 모바일 디바이스의 경우 2분, 데스크톱 클라이언트의 경우 1시간 동안 DVR 창을 표시할 수 있습니다.

![DVR 창][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>LiveBackoff(라이브 위치) 조정
매니페스트 필터링을 사용하면 라이브 프로그램의 라이브 에지에서 몇 초 정도 제거할 수 있습니다. 필터링을 통해 뷰어가 스트림을 수신하기 전에 브로드캐스터가 미리 보기 게시 지점에서 프레젠테이션을 보고 광고 삽입 지점을 만들 수 있습니다(30초까지 백오프됨). 그런 다음, 브로드캐스터는 이러한 광고가 수신되는 시간에 맞춰 해당 광고를 클라이언트 프레임워크에 푸시하고 광고 기회 전에 정보를 처리할 수 있습니다.

광고 지원 외에도 LiveBackoff 설정은 뷰어 위치를 조정하는 데 사용할 수 있습니다. 따라서 클라이언트가 라이브 에지를 생성하고 적중할 때 HTTP 404 또는 412 오류를 발생시키지 않고 서버의 조각을 가져올 수 있습니다.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>단일 필터에 여러 규칙 결합
단일 필터에 여러 필터링 규칙을 결합할 수 있습니다. 예를 들어 라이브 보관에서 슬레이트를 제거하고 사용 가능한 비트 전송률을 필터링하는 "범위 규칙"을 정의할 수 있습니다. 여러 필터링 규칙을 적용할 때 최종 결과는 모든 규칙의 교집합입니다.

![다중 규칙][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>여러 개의 필터 결합(필터 컴퍼지션)

단일 URL로 여러 개의 필터를 결합할 수도 있습니다. 

다음 시나리오는 필터를 결합하는 이유를 보여 줍니다.

1. 비디오 품질을 제한하기 위해 Android 또는 iPAD와 같은 모바일 디바이스에 대한 비디오 품질을 필터링해야 합니다. 원치 않는 품질을 제거하기 위해 디바이스 프로필에 적합한 계정 필터를 만듭니다. 계정 필터는 다른 추가적인 연결 없이 동일한 미디어 서비스 계정의 모든 자산에 사용할 수 있습니다. 
2. 또한 자산의 시작 및 종료 시간을 트리밍하려고 합니다. 이를 위해서 자산 필터를 만들고 시작/종료 시간을 설정합니다. 
3. 트리밍 필터에 품질 필터링을 추가해야 하는 결합은 필터 사용을 더 어렵게 합니다. 이렇게 하지 않고 이러한 필터 두 개를 결합하고자 합니다.

필터를 결합하려면 필터 이름을 세미콜론으로 구분한 매니페스트/재생 목록 URL로 설정해야 합니다. 품질을 필터링하는 *MyMobileDevice*라는 필터와 특정 시작 시간으로 설정된 *MyStartTime*이라는 필터가 있다고 가정해 봅시다. 이러한 필터를 다음과 같이 결합할 수 있습니다.

최대 3개의 필터를 결합할 수 있습니다. 

자세한 내용은 [이 블로그](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) 를 참조하세요.

## <a name="considerations-and-limitations"></a>고려 사항 및 제한 사항

- **forceEndTimestamp**, **presentationWindowDuration** 및 **liveBackoffDuration**의 값은 VoD 필터에 대해서는 설정해서는 안 됩니다. 라이브 필터 시나리오에만 사용됩니다. 
- 동적 매니페스트는 GOP 경계(키 프레임)에서 작동하므로 트리밍에는 GOP 정확도가 있습니다. 
- 계정 필터와 자산 필터에 동일한 필터 이름을 사용할 수 있습니다. 자산 필터의 우선 순위가 더 높으며 계정 필터에 우선합니다.
- 필터를 업데이트 하는 경우 규칙을 새로 고치는 스트리밍 끝점에 대 한 최대 2 분까지 걸릴 수 있습니다. 콘텐츠가 일부 필터로 처리된 경우(및 프록시와 CDN 캐시에서 캐시된 경우) 이들 필터를 업데이트하면 플레이어 오류가 발생할 수 있습니다. 필터 업데이트 후에는 캐시를 지우는 것이 좋습니다. 이 옵션을 사용할 수 없는 경우에 서로 다른 필터를 사용 하는 것이 좋습니다.
- 고객은 수동으로 매니페스트를 다운로드하고 정확한 startTimestamp 및 시간 간격을 구문 분석해야 합니다.
    
    - 자산의 트랙의 속성을 확인하려면 [매니페스트 파일 가져오기 및 검사](#get-and-examine-manifest-files)를 참조하세요.
    - 자산 필터 타임스탬프 속성을 설정하는 공식은 다음과 같습니다. <br/>startTimestamp = &lt;매니페스트의 시작 시간&gt; +  &lt;예상 필터 시작 시간(초 단위)&gt;*timescale

## <a name="next-steps"></a>다음 단계

다음 문서에서는 프로그래밍 방식으로 필터를 만드는 방법을 설명합니다.  

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
