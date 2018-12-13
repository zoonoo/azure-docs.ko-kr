---
title: 필터 및 Azure Media Services 동적 매니페스트 | Microsoft Docs
description: 이 항목에서는 클라이언트가 스트림의 특정 섹션을 스트리밍하는 데 사용할 수 있는 필터를 생성하는 방법을 설명합니다. 이 선택적 스트리밍을 보관하기 위해 Media Services는 동적 매니페스트를 생성합니다.
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
ms.date: 11/28/2018
ms.author: juliako
ms.openlocfilehash: 05c089c0e92672b3602bd9c7bfb3263c56db76ef
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52633302"
---
# <a name="filters-and-dynamic-manifests"></a>필터 및 동적 매니페스트

고객에게 콘텐츠를 제공(라이브 이벤트 또는 주문형 비디오를 스트리밍)하는 경우 클라이언트에게 기본 자산의 매니페스트 파일에 설명된 내용보다 더 많은 유연성이 필요할 수 있습니다. Azure Media Services를 사용하면 콘텐츠에 사용할 계정 필터 및 자산 필터를 정의할 수 있습니다. 

필터는 고객이 다음과 같은 작업을 수행할 수 있도록 하는 서버 쪽 규칙입니다. 

- 전체 비디오를 재생하는 대신 비디오의 한 섹션만 재생합니다. 예: 

    - 라이브 이벤트의 하위 클립을 표시하는 매니페스트를 줄입니다(“하위 클립 필터링”).
    - 비디오의 시작 부분을 자릅니다("비디오 트리밍").

- 콘텐츠를 재생하는 데 사용되는 디바이스에서 지원하는 지정된 변환 및/또는 지정된 언어 트랙만 전달합니다(“변환 필터링”). 
- 플레이어의 DVR 창 길이를 제한하기 위해 프레젠테이션 창(DVR)을 조정합니다(“프레젠테이션 창 조정”).

이 항목에서는 [개념](#concepts)을 설명하고 [필터 정의를 표시](#definitions)합니다. 그런 다음, [일반적인 시나리오](#common-scenarios)에 대한 자세한 내용을 제공합니다. 문서 끝부분에는 프로그래밍 방식으로 필터를 만드는 방법을 보여 주는 링크가 있습니다.  

## <a name="concepts"></a>개념

### <a name="dynamic-manifests"></a>동적 매니페스트

Media Services는 미리 정의된 [필터](#filters)를 기반으로 **동적 매니페스트**를 제공합니다. 필터를 정의하고 나면 클라이언트가 비디오의 특정 변환 또는 하위 클립을 스트리밍하는 데 해당 필터를 사용할 수 있습니다. 스트리밍 URL에서 필터를 지정할 수 있습니다. Apple HLS(HTTP 라이브 스트리밍), MPEG-DASH, 부드러운 스트리밍 등의 적응 비트 전송률 스트리밍 프로토콜에 필터를 적용할 수 있습니다. 

다음 표에서는 필터가 있는 URL의 몇 가지 예제를 보여 줍니다.

|프로토콜|예|
|---|---|
|HLS V4|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=myAccountFilter)`|
|HLS V3|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=myAccountFilter)`|
|MPEG DASH|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|부드러운 스트리밍|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=myAssetFilter)`|

> [!NOTE]
> 동적 매니페스트는 자산 및 해당 자산의 기본 매니페스트를 변경하지 않습니다. 클라이언트는 필터와 관계 없이 스트림을 요청할 수 있습니다. 
> 
> 

### <a name="manifest-files"></a>매니페스트 파일

가변 품질 스트리밍을 위해 자산을 인코딩하면 **매니페스트** (재생 목록) 파일이 만들어집니다(텍스트 기반 또는 XML 기반 파일). **매니페스트** 파일에는 트랙 유형(오디오, 비디오 또는 텍스트), 트랙 이름, 시작 및 종료 시간, 비트 전송률(품질), 트랙 언어, 프레젠테이션 창(고정 기간의 슬라이딩 창), 비디오 코덱(FourCC) 등의 스트리밍 메타 데이터가 포함됩니다. 또한 다음으로 재생할 수 있는 비디오 조각 및 위치에 대한 정보를 제공하여 다음 조각을 검색하도록 플레이어에 지시합니다. 조각(또는 세그먼트)은 비디오 콘텐츠의 실제 “청크”입니다.

다음은 HLS 매니페스트 파일의 예입니다. 

```
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(380658)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(380658)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721426)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721426)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1155246)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1155246)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2218559)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2218559)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579378)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579378)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

### <a name="get-and-examine-manifest-files"></a>매니페스트 파일 가져오기 및 검사

동적으로 작성된 매니페스트에 포함해야 하는 스트림(라이브 또는 주문형 비디오)의 트랙을 기반으로 필터 트랙 속성 조건의 목록을 지정합니다. 트랙의 속성을 가져오고 검사하려면 먼저 부드러운 스트리밍 매니페스트를 로드해야 합니다.

[Upload, encode, and stream files with .NET](stream-files-tutorial-with-api.md)(.NET을 사용하여 파일 업로드, 인코딩 및 스트림) 자습서에서는 .NET을 사용하여 스트리밍 URL을 작성하는 방법을 설명합니다. [building URLs](stream-files-tutorial-with-api.md#get-streaming-urls)(URL 작성) 섹션을 참조하세요. 앱을 실행하는 경우 URL 중 하나가 부드러운 스트리밍 매니페스트를 가리킵니다. `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`<br/>URL을 복사하여 브라우저의 주소 표시줄에 붙여넣습니다. 파일이 다운로드됩니다. 원하는 텍스트 편집기에서 열 수 있습니다.

REST 예제는 [Upload, encode, and stream files with REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls)(REST를 사용하여 파일 업로드, 인코딩 및 스트림)를 참조하세요.

### <a name="monitor-the-bitrate-of-a-video-stream"></a>비디오 스트림의 비트 전송률 모니터링

[Azure Media Player 데모 페이지](http://aka.ms/amp)를 사용하여 비디오 스트림의 비트 전송률을 모니터링할 수 있습니다. 데모 페이지의 **진단** 탭에 진단 정보가 표시됩니다.

![Azure Media Player 진단][amp_diagnostics]

## <a name="defining-filters"></a>필터 정의

자산 필터에는 두 가지 유형이 있습니다. 

* [계정 필터](https://docs.microsoft.com/rest/api/media/accountfilters)(전역) - Azure Media Services 계정의 모든 자산에 적용 가능하며 계정의 수명 동안 보유합니다.
* [자산 필터](https://docs.microsoft.com/rest/api/media/assetfilters)(로컬) -필터를 만들 때 연결된 자산에만 적용 가능하며 자산의 수명 동안 보유합니다. 

[계정 필터](https://docs.microsoft.com/rest/api/media/accountfilters) 및 [자산 필터](https://docs.microsoft.com/rest/api/media/assetfilters) 유형은 필터를 정의/설명하기 위한 정확히 동일한 속성을 가집니다. **자산 필터**를 작성하는 경우 외에는 필터를 연결할 자산 이름을 지정해야 합니다.

시나리오에 따라 더 적합한 필터 유형이 다릅니다(자산 필터 또는 계정 필터). 계정 필터는 일반적으로 디바이스 프로필에 적합(변환 필터링)한 반면, 자산 필터는 특정 자산을 트리밍하는 데 사용할 수 있습니다.

다음 속성을 사용하여 필터를 설명합니다. 

|이름|설명|
|---|---|
|firstQuality|필터의 첫 번째 품질 비트 전송률입니다.|
|presentationTimeRange|프레젠테이션 시간 범위입니다. 이 속성은 매니페스트 시작/종료 지점, 프레젠테이션 창 길이 및 라이브 시작 위치를 필터링하는 데 사용됩니다. <br/>자세한 내용은 [PresentationTimeRange](#PresentationTimeRange)를 참조하세요.|
|tracks|트랙 선택 조건입니다. 자세한 내용은 [tracks](#tracks)를 참조하세요.|

### <a name="presentationtimerange"></a>PresentationTimeRange

이 속성은 **자산 필터**와 함께 사용합니다. **계정 필터**를 사용하여 이 속성을 설정하지 않는 것이 좋습니다.

|이름|설명|
|---|---|
|**endTimestamp**|절대 종료 시간 경계입니다. VoD(주문형 비디오)에 적용됩니다. 라이브 프레젠테이션의 경우 이 속성은 자동으로 무시되며 프레젠테이션이 종료되고 스트림이 VoD가 되면 적용됩니다.<br/><br/>값은 스트림의 절대 종료 지점을 나타냅니다. 가장 가까운 다음 GOP 시작 지점으로 반올림됩니다.<br/><br/>재생 목록(매니페스트)을 트리밍하는 데 StartTimestamp 및 EndTimestamp를 사용합니다. 예를 들어 StartTimestamp=40000000 및 EndTimestamp = 100000000이면 StartTimestamp 및 EndTimestamp 사이의 미디어를 포함하는 재생 목록이 생성됩니다. 경계에 걸쳐 있는 조각인 경우 전체 조각이 매니페스트에 포함됩니다.<br/><br/>다음에 나오는 **forceEndTimestamp** 정의를 참조하세요.|
|**forceEndTimestamp**|라이브 필터에 적용됩니다.<br/><br/>**forceEndTimestamp**는 **endTimestamp**가 유효한 값으로 설정되었는지 여부를 표시하는 부울입니다. <br/><br/>값이 **true**이면 **endTimestamp** 값을 지정해야 합니다. 지정되지 않은 경우 잘못된 요청이 반환됩니다.<br/><br/>예를 들어 입력 비디오 5분에 시작되어 스트림 끝까지 지속되는 필터를 정의하려면 **forceEndTimestamp**를 false로 설정하고 **endTimestamp**설정을 생략합니다.|
|**liveBackoffDuration**|라이브 전용에 적용됩니다. 이 속성은 라이브 재생 위치를 정의하는 데 사용됩니다. 이 규칙을 사용하여 라이브 재생 위치를 지연하고 플레이어에 대한 서버 쪽 버퍼를 생성할 수 있습니다. LiveBackoffDuration은 라이브 위치에 대해 상대적입니다. 최대 라이브 백오프 지속 기간은 60초입니다.|
|**presentationWindowDuration**|라이브에 적용됩니다. **presentationWindowDuration**을 사용하여 재생 목록에 슬라이딩 윈도우를 적용합니다. 예를 들어presentationWindowDuration=1200000000을 설정하면 2분 슬라이딩 윈도우가 적용됩니다. 라이브 에지 2분 이내의 미디어가 재생 목록에 포함됩니다. 경계에 걸쳐 있는 조각인 경우 전체 조각이 재생 목록에 포함됩니다. 최소 프레젠테이션 창 지속 기간은 120초입니다.|
|**startTimestamp**|VoD 또는 라이브 스트림에 적용됩니다. 값은 스트림의 절대 시작 지점을 나타냅니다. 값은 가장 가까운 다음 GOP 시작 지점으로 반올림됩니다.<br/><br/>재생 목록(매니페스트)을 트리밍하는 데 **startTimestamp** 및 **endTimestamp**를 사용합니다. 예를 들어 startTimestamp=40000000 및 endTimestamp = 100000000이면 StartTimestamp 및 EndTimestamp 사이의 미디어를 포함하는 재생 목록이 생성됩니다. 경계에 걸쳐 있는 조각인 경우 전체 조각이 매니페스트에 포함됩니다.|
|**timescale**|VoD 또는 라이브 스트림에 적용됩니다. 위에 지정된 타임스탬프 및 지속 기간에서 사용되는 시간 간격입니다. 기본 시간 간격은 10000000입니다. 대체 시간 간격을 사용할 수 있습니다. 기본값은 10000000HNS(백 나노초)입니다.|

### <a name="tracks"></a>tracks

동적으로 작성된 매니페스트에 포함해야 하는 스트림(라이브 또는 주문형 비디오)의 트랙을 기반으로 필터 트랙 속성 조건(FilterTrackPropertyConditions)의 목록을 지정합니다. 필터는 논리 **AND** 및 **OR** 연산을 사용하여 결합됩니다.

필터 트랙 속성 조건은 트랙 유형, 값(다음 표에 설명) 및 연산(Equal, NotEqual)을 설명합니다. 

|이름|설명|
|---|---|
|**Bitrate**|필터링을 위해 트랙의 비트 전송률을 사용합니다.<br/><br/>권장 값은 비트 전송률의 범위(초당 비트 수)입니다. 예를 들어 “0-2427000”입니다.<br/><br/>참고: 특정 비트 전송률 값을 예를 들어 250000(초당 비트 수)과 같이 사용할 수 있지만, 정확한 비트 전송률이 자산별로 변동될 수 있으므로 이 방법은 권장되지 않습니다.|
|**FourCC**|필터링을 위해 트랙의 FourCC 값을 사용합니다.<br/><br/>값은 [RFC 6381](https://tools.ietf.org/html/rfc6381)에 지정된 코덱 형식의 첫 번째 요소입니다. 현재 지원되는 코덱은 다음과 같습니다. <br/>비디오: “avc1”, “hev1”, “hvc1”<br/>오디오: “mp4a”, “ec-3”<br/><br/>자산의 트랙에 대한 FourCC 값을 확인하려면 [매니페스트 파일 가져오기 및 검사](#get-and-examine-manifest-files)를 참조하세요.|
|**언어**|필터링을 위해 트랙의 언어를 사용합니다.<br/><br/>값은 RFC 5646에 지정된, 포함할 언어의 태그입니다. 예: "en".|
|**Name**|필터링을 위해 트랙의 이름을 사용합니다.|
|**형식**|필터링을 위해 트랙의 유형을 사용합니다.<br/><br/>허용되는 값은 “video”, “audio” 또는 “text”입니다.|

### <a name="example"></a>예

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="rendition-filtering"></a>변환 필터링

자산을 여러 인코딩 프로필(H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) 및 여러 품질의 비트 전송률로 인코딩할 수 있습니다. 그러나 모든 클라이언트 장치가 모든 자산 프로필 및 비트 전송률을 지원하는 것은 아닙니다. 예를 들어 이전 Android 디바이스는 H.264 Baseline+AACL만 지원합니다. 이점을 얻을 수 없는 디바이스에 높은 비트 전송률로 전송하면 대역폭과 디바이스 계산 성능을 낭비하게 됩니다. 이런 장치가 해당하는 모든 정보를 표시하기 위해서는 디코딩을 통해 규모를 축소해야 합니다.

동적 매니페스트를 사용하면 모바일, 콘솔, HD/SD 등과 같은 디바이스 프로필을 만들고 각 프로필의 일부로 제공할 트랙 및 품질을 포함할 수 있습니다.

![변환 필터링 예제][renditions2]

다음 예제에서는 7개의 ISO MP4 비디오 변환(180p에서 1080p까지)으로 메자닌 자산을 인코드하는 데 인코더가 사용되었습니다. 인코딩된 자산은 HLS, MPEG DASH, 부드러운 스트리밍 등의 스트리밍 프로토콜 중 하나로 동적으로 패키징할 수 있습니다.  다이어그램의 맨 위에 필터가 없는 자산을 위한 HLS 매니페스트가 나와 있습니다(7개의 모든 변환 포함).  왼쪽 아래에는 "ott" 필터가 적용된 HLS 매니페스트가 표시되어 있습니다. “ott” 필터는 1Mbps 미만의 모든 비트 전송률을 제거하도록 지정하며, 이 때문에 응답에서 맨 아래의 두 품질 수준이 제거됩니다. 오른쪽 아래에는 "mobile" 필터가 적용된 HLS 매니페스트가 나와 있습니다. "mobile" 필터는 해상도가 720p보다 높은 변환을 제거하도록 지정하며, 이 때문에 두 개의 1080p 변환이 제거됩니다.

![변환 필터링][renditions1]

## <a name="removing-language-tracks"></a>언어 트랙 제거
자산에는 영어, 스페인어, 프랑스어 등 여러 오디오 언어가 포함될 수 있습니다. 일반적으로 플레이어 SDK는 기본 오디오 트랙 선택 및 사용자별로 사용 가능한 오디오 트랙 선택을 관리합니다. 이러한 플레이어 SDK를 개발하는 것은 까다로운 일로, 장치별 플레이어 프레임워크 간에 서로 다른 구현이 필요합니다. 또한 일부 플랫폼은 플레이어 API가 제한되어 있고 오디오 선택 기능이 포함되어 있지 않으므로 사용자는 기본 오디오 트랙을 선택하거나 변경할 수 없습니다. 자산 필터를 사용하면 원하는 오디오 언어만 포함하는 필터를 만들어 동작을 제어할 수 있습니다.

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

1. 비디오 품질을 제한하기 위해 Android 또는 iPAD와 같은 모바일 장치에 대한 비디오 품질을 필터링해야 합니다. 원치 않는 품질을 제거하기 위해 디바이스 프로필에 적합한 계정 필터를 만듭니다. 계정 필터는 다른 추가적인 연결 없이 동일한 미디어 서비스 계정의 모든 자산에 사용할 수 있습니다. 
2. 또한 자산의 시작 및 종료 시간을 트리밍하려고 합니다. 이를 위해서 자산 필터를 만들고 시작/종료 시간을 설정합니다. 
3. 트리밍 필터에 품질 필터링을 추가해야 하는 결합은 필터 사용을 더 어렵게 합니다. 이렇게 하지 않고 이러한 필터 두 개를 결합하고자 합니다.

필터를 결합하려면 필터 이름을 세미콜론으로 구분한 매니페스트/재생 목록 URL로 설정해야 합니다. 품질을 필터링하는 *MyMobileDevice*라는 필터와 특정 시작 시간으로 설정된 *MyStartTime*이라는 필터가 있다고 가정해 봅시다. 이러한 필터를 다음과 같이 결합할 수 있습니다.

최대 3개의 필터를 결합할 수 있습니다. 

자세한 내용은 [이 블로그](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) 를 참조하세요.

## <a name="considerations-and-limitations"></a>고려 사항 및 제한 사항

- **forceEndTimestamp**, **presentationWindowDuration** 및 **liveBackoffDuration**의 값은 VoD 필터에 대해서는 설정해서는 안 됩니다. 라이브 필터 시나리오에만 사용됩니다. 
- 동적 매니페스트는 GOP 경계(키 프레임)에서 작동하므로 트리밍에는 GOP 정확도가 있습니다. 
- 계정 필터와 자산 필터에 동일한 필터 이름을 사용할 수 있습니다. 자산 필터의 우선 순위가 더 높으며 계정 필터에 우선합니다.
- 필터를 업데이트 하는 경우, 규칙을 새로 고치는 스트리밍 엔드포인트에서 최대 2분이 소요될 수 있습니다. 콘텐츠가 일부 필터로 처리된 경우(및 프록시와 CDN 캐시에서 캐시된 경우) 이들 필터를 업데이트하면 플레이어 오류가 발생할 수 있습니다. 필터 업데이트 후에는 캐시를 지우는 것이 좋습니다. 이 옵션을 사용할 수 없는 경우에 서로 다른 필터를 사용 하는 것이 좋습니다.
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
