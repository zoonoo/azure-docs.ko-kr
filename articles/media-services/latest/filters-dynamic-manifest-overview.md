---
title: 동적 패키지를 사용하여 매니페스트 필터링
titleSuffix: Azure Media Services
description: 동적 패키지러를 사용하여 매니페스트를 필터링하고 선택적으로 스트리밍하는 필터를 만드는 방법을 알아봅니다.
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
ms.openlocfilehash: cd955f97a2f26543f799d95b7dc0b1de235333c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186210"
---
# <a name="filter-your-manifests-using-dynamic-packager"></a>동적 패키지를 사용하여 매니페스트 필터링

적응형 비트전송률 스트리밍 콘텐츠를 장치에 제공하는 경우 특정 장치 기능 또는 사용 가능한 네트워크 대역폭을 대상으로 여러 버전의 매니페스트를 게시해야 하는 경우가 있습니다. [동적 패키지는](dynamic-packaging-overview.md) 특정 코덱, 해상도, 비트 레이트 및 오디오 트랙 조합을 즉석에서 필터링 할 수있는 필터를 지정할 수 있습니다. 이 필터링을 통해 여러 복사본을 만들 필요가 없습니다. 대상 장치(iOS, Android, SmartTV 또는 브라우저)와 네트워크 기능(고대역폭, 모바일 또는 저대역폭 시나리오)에 구성된 특정 필터 집합을 사용하여 새 URL을 게시하기만 하면 됩니다. 이 경우 클라이언트는 사용 가능한 [자산 필터 또는 계정 필터를](filters-concept.md)지정하여 쿼리 문자열을 통해 콘텐츠 스트리밍을 조작하고 필터를 사용하여 스트림의 특정 섹션을 스트리밍할 수 있습니다.

일부 배달 시나리오에서는 고객이 특정 트랙에 액세스할 수 없도록 해야 합니다. 예를 들어 HD 트랙이 포함된 매니페스트를 특정 구독자 계층에 게시하지 않으려는 경우를 볼 수 있습니다. 또는 특정 적응 비트 레이트(ABR) 트랙을 제거하여 추가 트랙의 이점을 얻지 못하는 특정 장치로의 전송 비용을 줄이려고 할 수도 있습니다. 이 경우 미리 만들어진 필터 목록을 생성 시 [스트리밍 로케이터와](streaming-locators-concept.md) 연결할 수 있습니다. 그러면 클라이언트는 **스트리밍 로케이터에**의해 정의되므로 콘텐츠가 스트리밍되는 방식을 조작할 수 없습니다.

[스트리밍 로케이터](filters-concept.md#associating-filters-with-streaming-locator) + 클라이언트가 URL에서 지정하는 추가 장치별 필터에 필터를 지정하여 필터링을 결합할 수 있습니다. 이 조합은 메타데이터 또는 이벤트 스트림, 오디오 언어 또는 설명 오디오 트랙과 같은 추가 트랙을 제한하는 데 유용합니다.

스트림에서 서로 다른 필터를 지정하는 이 기능은 대상 장치에 대한 여러 사용 사례 시나리오를 대상으로 하는 강력한 **동적 매니페스트** 조작 솔루션을 제공합니다. 이 항목에서는 동적 **매니페스트와** 관련된 개념을 설명하고 이 기능을 사용할 수 있는 시나리오의 예를 제공합니다.

> [!NOTE]
> 동적 매니페스트는 해당 자산에 대한 자산 및 기본 매니페스트를 변경하지 않습니다.

## <a name="overview-of-manifests"></a>매니페스트 개요

Azure 미디어 서비스는 HLS, MPEG DASH 및 부드러운 스트리밍 프로토콜을 지원합니다. [동적 패키징의](dynamic-packaging-overview.md)일부로 스트리밍 클라이언트 매니페스트(HLS 마스터 재생 목록, DASH 미디어 프레젠테이션 설명 [MPD]) 및 부드러운 스트리밍)은 URL의 형식 선택기에 따라 동적으로 생성됩니다. 자세한 내용은 [공통 주문형 워크플로의](dynamic-packaging-overview.md#delivery-protocols)배달 프로토콜을 참조하십시오.

### <a name="get-and-examine-manifest-files"></a>매니페스트 파일 가져오기 및 검사

스트림의 트랙(라이브 또는 주문형 비디오 [VOD])을 동적으로 만든 매니페스트에 포함해야 하는 필터 트랙 속성 조건 목록을 지정합니다. 트랙의 속성을 가져오고 검사하려면 먼저 부드러운 스트리밍 매니페스트를 로드해야 합니다.

.NET 자습서를 [사용하여 파일을 업로드, 인코딩 및 스트리밍하면](stream-files-tutorial-with-api.md#get-streaming-urls) .NET을 사용하여 스트리밍 URL을 빌드하는 방법을 확인할 수 있습니다. 앱을 실행하는 경우 URL 중 하나가 부드러운 스트리밍 매니페스트를 가리킵니다. `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`<br/> URL을 복사하여 브라우저의 주소 표시줄에 붙여넣습니다. 파일이 다운로드됩니다. 모든 텍스트 편집기에서 열 수 있습니다.

REST 예제의 경우 [REST가 있는 파일 업로드, 인코딩 및 스트림을](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls)참조하십시오.

### <a name="monitor-the-bitrate-of-a-video-stream"></a>비디오 스트림의 비트 전송률 모니터링

[Azure Media Player 데모 페이지](https://aka.ms/azuremediaplayer)를 사용하여 비디오 스트림의 비트 전송률을 모니터링할 수 있습니다. 데모 페이지에진단탭에 진단 **Diagnostics** 정보가 표시됩니다.

![Azure Media Player 진단][amp_diagnostics]

### <a name="examples-urls-with-filters-in-query-string"></a>예: 쿼리 문자열에 필터가 있는 URL

HLS, MPEG-DASH 및 부드러운 스트리밍과 같은 ABR 스트리밍 프로토콜에 필터를 적용할 수 있습니다. 다음 표에서는 필터가 있는 URL의 몇 가지 예제를 보여 줍니다.

|프로토콜|예제|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|부드러운 스트리밍|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>변환 필터링

자산을 여러 인코딩 프로필(H.264 기준, H.264 높음, AACL, AACH, Dolby Digital Plus) 및 여러 품질 비트 레이트에 인코딩하도록 선택할 수 있습니다. 그러나 모든 클라이언트 디바이스가 모든 자산 프로필 및 비트 전송률을 지원하는 것은 아닙니다. 예를 들어 구형 Android 장치는 H.264 기준선+AACL만 지원합니다. 이점을 얻을 수 없는 장치에 더 높은 비트 전송을 보내면 대역폭과 장치 계산이 낭비됩니다. 이러한 장치는 제공된 모든 정보를 디코딩해야 하며 표시를 위해 축소해야 합니다.

동적 매니페스트를 사용하면 장치 프로필(예: 모바일, 콘솔 또는 HD/SD)을 만들고 각 프로필의 일부가 되고자 하는 트랙과 품질을 포함할 수 있습니다. 이를 변환 필터링이라고 합니다. 다음 다이어그램은 그 예입니다.

![동적 매니페스트를 통해 변환 필터링의 예][renditions2]

다음 예제에서는 7개의 ISO MP4 비디오 변환(180p에서 1080p까지)으로 메자닌 자산을 인코드하는 데 인코더가 사용되었습니다. 인코딩된 자산은 HLS, MPEG DASH 및 Smooth와 같은 스트리밍 프로토콜 중 어느 [데이터든으로 동적으로 패키징될](dynamic-packaging-overview.md) 수 있습니다.

다음 다이어그램의 맨 위에는 필터가 없는 자산에 대한 HLS 매니페스트가 표시됩니다. (일곱 가지 변환이 모두 포함되어 있습니다.)  왼쪽 하단에 다이어그램은 "ott"라는 필터가 적용된 HLS 매니페스트를 보여 주며 있습니다. "ott" 필터는 1Mbps 미만의 모든 비트레이트 제거를 지정하므로 응답에서 아래쪽 두 품질 수준이 제거되었습니다. 오른쪽 하단에 다이어그램은 "mobile"이라는 필터가 적용된 HLS 매니페스트를 보여 주며 있습니다. "모바일" 필터는 해상도가 720p보다 큰 변환제거를 지정하여 두 개의 1080p 변환을 제거했습니다.

![동적 매니페스트를 통해 변환 필터링][renditions1]

## <a name="removing-language-tracks"></a>언어 트랙 제거
자산에는 영어, 스페인어, 프랑스어 등과 같은 여러 오디오 언어가 포함될 수 있습니다. 일반적으로 Player SDK는 사용자 선택당 기본 오디오 트랙 선택 및 사용 가능한 오디오 트랙을 관리합니다.

이러한 플레이어 SDK를 개발하는 것은 장치별 플레이어 프레임워크에 따라 서로 다른 구현이 필요하기 때문에 어렵습니다. 또한 일부 플랫폼에서는 플레이어 API가 제한되어 있으며 사용자가 기본 오디오 트랙을 선택하거나 변경할 수 없는 오디오 선택 기능은 포함되지 않습니다. 자산 필터를 사용하면 원하는 오디오 언어만 포함하는 필터를 만들어 동작을 제어할 수 있습니다.

![동적 매니페스트와 언어 트랙의 필터링][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>자산의 시작 자르기

대부분의 라이브 스트리밍 이벤트에서 운영자는 실제 이벤트 전에 일부 테스트를 실행합니다. 예를 들어 이벤트가 시작되기 전에 "프로그램이 일시적으로 시작됩니다."와 같은 슬레이트를 포함할 수 있습니다.

프로그램을 보관하는 경우 테스트 및 슬레이트 데이터도 보관되며 프레젠테이션에도 포함됩니다. 그러나 이 정보는 클라이언트에 표시되어서는 안 됩니다. 동적 매니페스트를 사용하면 시작 시간 필터를 만들고 매니페스트에서 원치 않는 데이터를 제거할 수 있습니다.

![동적 매니페스트를 사용하여 자산의 시작 트리밍][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>라이브 보관에서 하위 클립(뷰) 만들기

많은 라이브 이벤트가 장시간 실행되며 라이브 보관에는 여러 이벤트가 포함될 수 있습니다. 라이브 이벤트가 끝난 후 브로드캐스터는 라이브 보관을 논리 프로그램 시작 및 중단 시퀀스로 구분하려고 할 수 있습니다.

라이브 아카이브를 사후 처리하고 별도의 에셋을 만들지 않고 이러한 가상 프로그램을 별도로 게시할 수 있습니다(CDN의 기존 캐시된 조각의 이점을 얻지 못함). 이러한 가상 프로그램의 예로는 축구 또는 농구 게임의 쿼터, 야구의 이닝 또는 스포츠 프로그램의 개별 행사 등이 있습니다.

동적 매니페스트를 사용하면 시작/종료 시간을 사용하여 필터를 만들고 라이브 아카이브 상단에 가상 보기를 만들 수 있습니다.

![동적 매니페스트가 있는 서브클립 필터][subclip_filter]

필터링된 에셋은 다음과 같습니다.

![동적 매니페스트를 사용하여 필터링된 자산][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>프레젠테이션 창 조정(DVR)

현재 Azure Media Services는 1분에서 25시간 사이에 기간을 구성할 수 있는 순환 아카이브를 제공합니다. 매니페스트 필터링을 사용하면 미디어를 삭제하지 않고도 보관 위에 롤링 DVR 창을 만들 수 있습니다. 브로드캐스터가 라이브 에지와 함께 이동하는 제한된 DVR 창을 제공하는 동시에 더 큰 보관 창을 유지하는 등의 여러 시나리오가 있습니다. 브로드캐스터는 DVR 창에 없는 데이터를 사용하여 클립을 강조 표시하거나 다른 장치에 대해 서로 다른 DVR 창을 제공할 수 있습니다. 예를 들어 대부분의 모바일 디바이스는 큰 DVR 창을 처리하지 못합니다. 모바일 디바이스의 경우 2분, 데스크톱 클라이언트의 경우 1시간 동안 DVR 창을 표시할 수 있습니다.

![동적 매니페스트가 있는 DVR 창][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>LiveBackoff(라이브 위치) 조정

매니페스트 필터링을 사용하면 라이브 프로그램의 라이브 에지에서 몇 초 정도 제거할 수 있습니다. 필터링을 사용하면 브로드캐스터가 미리 보기 게시 지점에서 프레젠테이션을 시청하고 시청자가 스트림을 수신하기 전에 광고 삽입 지점을 만들 수 있습니다(30초 뒤로 백업). 그런 다음 브로드캐스터는 이러한 광고를 클라이언트 프레임워크에 푸시하여 광고 기회 전에 정보를 수신하고 처리할 수 있습니다.

광고 지원 외에도 라이브 백오프 설정을 사용하여 뷰어의 위치를 조정하여 클라이언트가 라이브 엣지에 도달할 때 서버에서 조각을 얻을 수 있습니다. 이렇게 하면 클라이언트에 HTTP 404 또는 412 오류가 발생하지 않습니다.

![동적 매니페스트를 통해 라이브 백오프를 위한 필터][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>단일 필터에 여러 규칙 결합

단일 필터에 여러 필터링 규칙을 결합할 수 있습니다. 예를 들어 라이브 아카이브에서 슬레이트를 제거하고 사용 가능한 비트 레이트도 필터링하는 "범위 규칙"을 정의할 수 있습니다. 여러 필터링 규칙을 적용하는 경우 최종 결과는 모든 규칙의 교차점입니다.

![동적 매니페스트가 있는 여러 필터링 규칙][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>여러 개의 필터 결합(필터 컴퍼지션)

단일 URL로 여러 개의 필터를 결합할 수도 있습니다. 다음 시나리오는 필터를 결합하는 이유를 보여 줍니다.

1. Android 또는 iPad와 같은 모바일 기기의 동영상 품질을 필터링해야 합니다(비디오 품질을 제한하기 위해). 원치 않는 품질을 제거하려면 장치 프로필에 적합한 계정 필터를 만듭니다. 더 이상 연결하지 않고 동일한 미디어 서비스 계정의 모든 자산에 대해 계정 필터를 사용할 수 있습니다.
1. 또한 자산의 시작 및 종료 시간을 트리밍하려고 합니다. 트리밍을 수행하려면 자산 필터를 만들고 시작/종료 시간을 설정합니다.
1. 이러한 필터를 모두 결합하려고 합니다. 조합하지 않으면 트리밍 필터에 품질 필터링을 추가해야 하므로 필터 사용이 더 어려워질 수 있습니다.

필터를 결합하려면 필터 이름을 세미콜론 으로 구분된 형식으로 매니페스트/재생 목록 URL로 설정합니다. 품질을 필터링하는 *MyMobileDevice라는* 필터가 있고 특정 시작 시간을 설정하기 위해 *MyStartTime이라는* 다른 필터가 있다고 가정해 보겠습니다. 최대 3개의 필터를 결합할 수 있습니다.

자세한 내용은 [이 블로그 게시물을](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)참조하십시오.

## <a name="considerations-and-limitations"></a>고려 사항 및 제한 사항

- **forceEndTimestamp,** 프리젠 테이션에 대한 값 **[기간지속]** 및 **liveBackoffDurationVOD** 필터에 대해 설정되지 않아야 합니다. 라이브 필터 시나리오에만 사용됩니다.
- 동적 매니페스트는 GOP 경계(키 프레임)에서 작동하므로 트리밍은 GOP 정확도를 가합니다.
- 거래처 및 자산 필터에 동일한 필터 이름을 사용할 수 있습니다. 자산 필터의 우선 순위가 높으며 계정 필터를 재정의합니다.
- 필터를 업데이트하는 경우 스트리밍 끝점이 규칙을 새로 고치는 데 최대 2분이 걸릴 수 있습니다. 필터를 사용하여 콘텐츠를 제공하고 프록시 및 CDN 캐시에 콘텐츠를 캐시한 경우 이러한 필터를 업데이트하면 플레이어 오류가 발생할 수 있습니다. 필터를 업데이트한 후 캐시를 지우는 것이 좋습니다. 이 옵션을 사용할 수 없는 경우 다른 필터를 사용하는 것이 좋습니다.
- 고객은 매니페스트를 수동으로 다운로드하고 정확한 시작 시간 스탬프와 시간 척도를 구문 분석해야 합니다.

    - 에셋에서 트랙의 속성을 확인하려면 [매니페스트 파일을 가져옵니다.](#get-and-examine-manifest-files)
    - 자산 필터 타임스탬프 속성을 설정하는 수식은 다음과 같은 것입니다. <br/>startTimestamp &lt;= 매니페스트&gt; +  &lt;예상 필터 시작&gt; 시간 초 * 시간 표시에서 시작 시간

## <a name="next-steps"></a>다음 단계

다음 문서에서는 프로그래밍 방식으로 필터를 만드는 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 준다  

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
