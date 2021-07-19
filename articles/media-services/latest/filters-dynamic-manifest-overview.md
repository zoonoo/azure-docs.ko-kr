---
title: Dynamic Packager를 사용하여 매니페스트 필터링
description: 매니페스트를 필터링하고 선택적으로 스트리밍하기 위해 Dynamic Packager를 사용하여 필터를 만드는 방법을 알아봅니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 3ffdb41752630e0e5e22303ff58ecd798595a890
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897665"
---
# <a name="filter-your-manifests-using-dynamic-packager"></a>Dynamic Packager를 사용하여 매니페스트 필터링

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

적응 비트 전송률 스트리밍 콘텐츠를 디바이스에 전달하는 경우, 특정 디바이스 기능 또는 사용 가능한 네트워크 대역폭을 대상으로 지정하기 위해 여러 버전의 매니페스트를 게시해야 할 수 있습니다. [Dynamic Packager](dynamic-packaging-overview.md)를 사용하면 특정 코덱, 해상도, 비트 전송률 및 오디오 트랙 조합을 빠르게 필터링할 수 있는 필터를 지정할 수 있습니다. 이러한 필터링을 통해 여러 복사본을 만들 필요를 없앨 수 있습니다. 단순히 대상 디바이스(iOS, Android, SmartTV 또는 브라우저) 및 네트워크 기능(고대역폭, 모바일 또는 저대역폭 시나리오)으로 구성된 특정 필터 집합이 포함된 새 URL을 게시하면 됩니다. 이 경우 클라이언트는 쿼리 문자열(사용 가능한 [자산 필터 또는 계정 필터](filters-concept.md) 지정)을 통해 콘텐츠 스트리밍을 조작하고 필터를 사용하여 스트림의 특정 섹션을 스트리밍할 수 있습니다.

일부 배달 시나리오에서는 고객이 특정 트랙에 액세스할 수 없는지 확인해야 합니다. 예를 들어 HD 트랙이 포함된 매니페스트를 특정 구독자 계층에 게시하지 않아야 할 수 있습니다. 또는 추가 트랙을 활용하지 않는 특정 디바이스에 대한 전송 비용을 줄이기 위해 특정 ABR(적응 비트 전송률) 트랙을 제거해야 할 수 있습니다. 이 경우 미리 만든 필터 목록을 만들 때 [스트리밍 로케이터](streaming-locators-concept.md)와 연결할 수 있습니다. 그러면 **스트리밍 로케이터** 로 정의되기 때문에 클라이언트가 콘텐츠 스트리밍 방법을 조작할 수 없습니다.

[스트리밍 로케이터에 대한 필터](filters-concept.md#associating-filters-with-streaming-locator) 및 클라이언트가 URL에 지정하는 추가적인 디바이스별 필터를 지정하여 필터링을 결합할 수 있습니다. 이 조합은 메타데이터 또는 이벤트 스트림, 오디오 언어 또는 설명 오디오 트랙과 같은 추가 트랙을 제한하기 위해 사용됩니다.

스트림에 서로 다른 필터를 지정하는 이러한 기능은 대상 디바이스에 대해 여러 개의 사용 사례 시나리오를 대상으로 지정할 수 있는 강력한 **동적 매니페스트** 조작 솔루션을 제공합니다. 이 항목에서는 **동적 매니페스트** 와 관련된 개념을 설명하고 이 기능을 사용할 수 있는 시나리오 예제를 제공합니다.

> [!NOTE]
> 동적 매니페스트는 자산 및 해당 자산의 기본 매니페스트를 변경하지 않습니다.

## <a name="overview-of-manifests"></a>매니페스트 개요

Azure Media Services는 HLS, MPEG DASH 및 부드러운 스트리밍 프로토콜을 지원합니다. [동적 패키징](dynamic-packaging-overview.md) 중에 스트리밍 클라이언트 매니페스트(HLS 마스터 재생 목록, DASH MPD[Media Presentation Description] 및 부드러운 스트리밍)는 URL에서 형식 선택기를 기준으로 동적으로 생성됩니다. 자세한 내용은 [일반적인 주문형 워크플로](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery)의 배달 프로토콜을 참조하세요.

### <a name="get-and-examine-manifest-files"></a>매니페스트 파일 가져오기 및 검사

동적으로 생성된 매니페스트에 포함해야 하는 스트림 트랙(라이브 또는 VOD[주문형 비디오])을 기준으로 필터 트랙 속성 조건 목록을 지정합니다. 트랙의 속성을 가져오고 검사하려면 먼저 부드러운 스트리밍 매니페스트를 로드해야 합니다.

[.NET을 사용하여 파일 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md#get-streaming-urls) 자습서에서는 .NET을 사용하여 스트리밍 URL을 작성하는 방법을 보여줍니다. 앱을 실행하는 경우 URL 중 하나가 부드러운 스트리밍 매니페스트를 가리킵니다. `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`<br/> URL을 복사하여 브라우저의 주소 표시줄에 붙여넣습니다. 파일이 다운로드됩니다. 모든 텍스트 편집기에서 열 수 있습니다.

REST 예제는 [REST를 사용하여 파일 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls)을 참조하세요.

### <a name="monitor-the-bitrate-of-a-video-stream"></a>비디오 스트림의 비트 전송률 모니터링

[Azure Media Player 데모 페이지](https://aka.ms/azuremediaplayer)를 사용하여 비디오 스트림의 비트 전송률을 모니터링할 수 있습니다. 데모 페이지의 **진단** 탭에 진단 정보가 표시됩니다.

![Azure Media Player 진단][amp_diagnostics]

### <a name="examples-urls-with-filters-in-query-string"></a>예: 쿼리 문자열에 필터가 있는 URL

ABR 스트리밍 프로토콜에 필터 적용 가능: HLS, MPEG-DASH 및 부드러운 스트리밍. 다음 표에서는 필터가 있는 URL의 몇 가지 예제를 보여 줍니다.

|프로토콜|예제|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|부드러운 스트리밍|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>변환 필터링

자산을 여러 인코딩 프로필(H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) 및 여러 품질의 비트 전송률로 인코딩하도록 선택할 수 있습니다. 그러나 모든 클라이언트 디바이스가 모든 자산 프로필 및 비트 전송률을 지원하는 것은 아닙니다. 예를 들어 오래된 Android 디바이스는 H.264 Baseline+AACL만 지원합니다. 이점을 얻을 수 없는 디바이스에 높은 비트 전송률을 전송하면 대역폭 및 디바이스 계산 능력이 낭비됩니다. 이러한 디바이스는 제공된 모든 정보를 디코딩해야 하므로, 디스플레이 목적으로만 축소합니다.

동적 매니페스트를 사용하면 모바일, 콘솔 또는 HD/SD와 같은 디바이스 프로필을 만들고 각 프로필의 일부로 제공할 트래픽 및 품질을 포함할 수 있습니다. 이를 변환 필터링이라고 부릅니다. 다음 다이어그램은 이에 대한 예를 보여줍니다.

![동적 매니페스트를 사용한 변환 필터링 예][renditions2]

다음 예제에서는 7개의 ISO MP4 비디오 변환(180p에서 1080p까지)으로 메자닌 자산을 인코드하는 데 인코더가 사용되었습니다. 인코딩된 자산은 HLS, MPEG DASH, 부드러운 스트리밍 등의 스트리밍 프로토콜 중 하나로 [동적으로 패키징](dynamic-packaging-overview.md)할 수 있습니다.

다음 다이어그램의 위쪽은 필터가 없는 자산의 HLS 매니페스트를 보여줍니다. (7개 변환 모두 포함.) 왼쪽 아래에서 다이어그램은 이름이 “ott”인 필터가 적용된 HLS 매니페스트를 보여줍니다. "ott" 필터는 1Mbps 미만인 모든 비트 전송률을 제거하도록 지정하므로, 맨 아래 2개의 품질 수준이 응답에서 삭제되었습니다. 오른쪽 아래에서 다이어그램은 이름이 “mobile”인 필터가 적용된 HLS 매니페스트를 보여줍니다. "mobile" 필터는 해상도가 720p보다 큰 변환을 제거하도록 지정하므로 2개의 1080p 변환이 삭제되었습니다.

![동적 매니페스트를 사용한 변환 필터링][renditions1]

## <a name="removing-language-tracks"></a>언어 트랙 제거
자산에는 영어, 스페인어, 프랑스어 등 여러 오디오 언어가 포함될 수 있습니다. 일반적으로 플레이어 SDK는 기본 오디오 트랙 선택 및 사용자별로 사용 가능한 오디오 트랙 선택을 관리합니다.

이러한 플레이어 SDK를 개발하는 것은 디바이스 특정 플레이어 프레임워크 간에 서로 다른 구현이 필요하기 때문에 까다로운 작업입니다. 또한 일부 플랫폼에서는 플레이어 API가 제한되고 사용자가 기본 오디오 트랙을 선택하거나 변경할 수 없도록 오디오 선택 기능을 포함하지 않습니다. 자산 필터를 사용하면 원하는 오디오 언어만 포함하는 필터를 만들어 이러한 동작을 제어할 수 있습니다.

![동적 매니페스트를 사용하는 언어 트랙 필터링][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>자산의 시작 부분 자르기

대부분의 라이브 스트리밍 이벤트에서 운영자는 실제 이벤트 전에 일부 테스트를 실행합니다. 예를 들어 이벤트의 시작하기 전에 "프로그램이 곧 시작됩니다"와 같은 슬레이트를 포함할 수 있습니다.

프로그램을 보관하는 경우 테스트 및 슬레이트 데이터도 보관되며 프레젠테이션에도 포함됩니다. 그러나 이 정보가 클라이언트에는 표시되지 않아야 합니다. 동적 매니페스트를 사용하면 시작 시간 필터를 만들고 매니페스트에서 원치 않는 데이터를 제거할 수 있습니다.

![동적 매니페스트를 사용하여 자산 시작 부분 자르기][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>라이브 보관에서 하위 클립(뷰) 만들기

많은 라이브 이벤트가 장시간 실행되며 라이브 보관에는 여러 이벤트가 포함될 수 있습니다. 라이브 이벤트가 끝난 후 브로드캐스터는 라이브 보관을 논리 프로그램 시작 및 중단 시퀀스로 구분하려고 할 수 있습니다.

라이브 보관을 후처리하지 않고 CDN에서 기존 캐시된 조각의 이점을 활용하지 않는 개별 자산을 만들지 않고 별도로 이러한 가상 프로그램을 게시할 수 있습니다. 이러한 가상 프로그램의 예로는 축구 또는 농구 게임의 쿼터, 야구의 이닝 또는 스포츠 프로그램의 개별 행사 등이 있습니다.

동적 매니페스트를 사용하면 시작/종료 시간을 사용하여 필터를 만들고 라이브 보관의 위에 가상 뷰를 만들 수 있습니다.

![동적 매니페스트를 사용하여 필터 서브클리핑][subclip_filter]

필터링된 자산은 다음과 같습니다.

![동적 매니페스트를 사용하여 필터링된 자산][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>프레젠테이션 창 조정(DVR)

현재 Azure Media Services는 1분에서 25시간까지 기간을 구성할 수 있는 순환식 보관을 제공합니다. 매니페스트 필터링을 사용하면 미디어를 삭제하지 않고도 보관 위에 롤링 DVR 창을 만들 수 있습니다. 브로드캐스터가 라이브 에지와 함께 이동하는 제한된 DVR 창을 제공하는 동시에 더 큰 보관 창을 유지하는 등의 여러 시나리오가 있습니다. 브로드캐스터는 DVR 창 외부의 데이터를 사용하여 클립을 강조하거나 여러 디바이스에 대해 서로 다른 DVR 창을 제공하려고 할 수 있습니다. 예를 들어 대부분의 모바일 디바이스는 큰 DVR 창을 처리하지 못합니다. 모바일 디바이스의 경우 2분, 데스크톱 클라이언트의 경우 1시간 동안 DVR 창을 표시할 수 있습니다.

![동적 매니페스트를 사용하는 DVR 창][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>LiveBackoff(라이브 위치) 조정

매니페스트 필터링을 사용하면 라이브 프로그램의 라이브 에지에서 몇 초 정도 제거할 수 있습니다. 필터링을 통해 뷰어가 스트림을 수신하기 전에 브로드캐스터가 미리 보기 게시 지점에서 프레젠테이션을 보고 광고 삽입 지점을 만들 수 있습니다(30초까지 백오프됨). 그런 다음, 브로드캐스터는 이러한 광고가 수신되는 시간에 맞춰 해당 광고를 클라이언트 프레임워크에 푸시하고 광고 기회 전에 정보를 처리할 수 있습니다.

광고 지원 외에도 라이브 백오프 설정을 사용하여 뷰어 위치를 조정할 수 있습니다. 따라서 클라이언트가 라이브 에지를 생성하고 적중할 때 계속 서버의 조각을 가져올 수 있습니다. 이렇게 하면 클라이언트에 HTTP 404 또는 412 오류가 발생하지 않습니다.

![동적 매니페스트를 사용하는 라이브 백오프를 위한 필터링][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>단일 필터에 여러 규칙 결합

단일 필터에 여러 필터링 규칙을 결합할 수 있습니다. 예를 들어 라이브 보관에서 슬레이트를 제거하고 사용 가능한 비트 전송률을 필터링하는 "범위 규칙"을 정의할 수 있습니다. 여러 필터링 규칙을 적용할 때 최종 결과는 모든 규칙의 교집합입니다.

![동적 매니페스트를 사용하는 여러 필터링 규칙][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>여러 개의 필터 결합(필터 컴퍼지션)

단일 URL로 여러 개의 필터를 결합할 수도 있습니다. 다음 시나리오는 필터를 결합하는 이유를 보여 줍니다.

1. 비디오 품질을 제한하기 위해 Android 또는 iPad와 같은 모바일 디바이스에 대한 비디오 품질을 필터링해야 합니다. 원치 않는 품질을 제거하기 위해 디바이스 프로필에 적합한 계정 필터를 만듭니다. 다른 추가적인 연결 없이 동일한 미디어 서비스 계정으로 모든 자산에 대해 계정 필터를 사용할 수 있습니다.
1. 또한 자산의 시작 및 종료 시간을 트리밍하려고 합니다. 트리밍을 수행하려면 자산 필터를 만들고 시작/종료 시간을 설정합니다.
1. 이러한 두 필터를 결합할 수 있습니다. 결합하지 않으면 트리밍 필터에 품질 필터링을 추가해야 해서 필터 사용이 어려워질 수 있습니다.

필터를 결합하려면 세미콜론으로 구분된 형식으로 매니페스트/재생 목록 URL에 필터 이름을 설정합니다. 품질을 필터링하는 *MyMobileDevice* 라는 필터와 특정 시작 시간으로 설정된 *MyStartTime* 이라는 필터가 있다고 가정해 봅시다. 최대 3개의 필터를 결합할 수 있습니다.

자세한 내용은 [이 블로그 게시물](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)을 참조하세요.

## <a name="considerations-and-limitations"></a>고려 사항 및 제한 사항

- **forceEndTimestamp**, **presentationWindowDuration** 및 **liveBackoffDuration** 의 값은 VOD 필터에 대해서는 설정해서는 안 됩니다. 라이브 필터 시나리오에만 사용됩니다.
- 동적 매니페스트는 GOP 경계(키 프레임)에서 작동하므로 트리밍에 GOP 정확도가 포함됩니다.
- 계정 및 자산 필터에 동일한 필터 이름을 사용할 수 있습니다. 자산 필터의 우선 순위가 더 높으며 계정 필터에 우선합니다.
- 필터를 업데이트하는 경우, 규칙을 새로 고치는 스트리밍 엔드포인트에서 최대 2분이 소요될 수 있습니다. 콘텐츠 제공을 위해 필터를 사용하고 프록시 및 CDN 캐시로 콘텐츠를 캐시한 경우 이러한 필터를 업데이트하면 플레이어 오류가 발생할 수 있습니다. 필터 업데이트 후에는 캐시를 지우는 것이 좋습니다. 이 옵션을 사용할 수 없으면 다른 필터를 사용하는 것이 좋습니다.
- 고객은 수동으로 매니페스트를 다운로드하고 정확한 시작 시간 스탬프 및 시간 간격을 구문 분석해야 합니다.

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
