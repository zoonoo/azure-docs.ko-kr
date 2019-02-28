---
title: Azure Media Services의 필터를 정의합니다.
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
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: 09de372ffdb48c00fde9a43c07f8f8b574462d1f
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56372961"
---
# <a name="define-account-filters-and-asset-filters"></a>계정 필터 및 자산 필터 정의  

고객에게 콘텐츠를 제공(라이브 이벤트 또는 주문형 비디오를 스트리밍)하는 경우 클라이언트에게 기본 자산의 매니페스트 파일에 설명된 내용보다 더 많은 유연성이 필요할 수 있습니다. Azure Media Services를 사용하면 콘텐츠에 사용할 계정 필터 및 자산 필터를 정의할 수 있습니다. 

필터는 고객이 다음과 같은 작업을 수행할 수 있도록 하는 서버 쪽 규칙입니다. 

- 전체 비디오를 재생하는 대신 비디오의 한 섹션만 재생합니다. 예: 
  - 라이브 이벤트의 하위 클립을 표시하는 매니페스트를 줄입니다("하위 클립 필터링").
  - 비디오의 시작 부분을 자릅니다("비디오 트리밍").
- 콘텐츠를 재생하는 데 사용되는 디바이스에서 지원하는 지정된 변환 및/또는 지정된 언어 트랙만 전달합니다(“변환 필터링”). 
- 플레이어의 DVR 창 길이를 제한하기 위해 프레젠테이션 창(DVR)을 조정합니다("프레젠테이션 창 조정").

Media Services는 미리 정의된 필터에 따라 [동적 매니페스트](filters-dynamic-manifest-overview.md)를 제공합니다. 필터를 정의하고 나면 클라이언트가 스트리밍 URL에 필터를 사용할 수 있습니다. 적응 비트 전송률 스트리밍 프로토콜에 필터를 적용할 수 있습니다. Apple HLS(HTTP 라이브 스트리밍), MPEG-DASH 및 부드러운 스트리밍

다음 표에서는 필터가 있는 URL의 몇 가지 예제를 보여 줍니다.

|프로토콜|예|
|---|---|
|HLS V4|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|HLS V3|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,filter=myAccountFilter)`|
|MPEG DASH|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|부드러운 스트리밍|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=myAssetFilter)`|

## <a name="define-filters"></a>필터 정의

자산 필터에는 두 가지 유형이 있습니다. 

* [계정 필터](https://docs.microsoft.com/rest/api/media/accountfilters)(전역) - Azure Media Services 계정의 모든 자산에 적용 가능하며 계정의 수명 동안 보유합니다.
* [자산 필터](https://docs.microsoft.com/rest/api/media/assetfilters)(로컬) -필터를 만들 때 연결된 자산에만 적용 가능하며 자산의 수명 동안 보유합니다. 

[계정 필터](https://docs.microsoft.com/rest/api/media/accountfilters) 및 [자산 필터](https://docs.microsoft.com/rest/api/media/assetfilters) 유형은 필터를 정의/설명하기 위한 정확히 동일한 속성을 가집니다. **자산 필터**를 작성하는 경우 외에는 필터를 연결할 자산 이름을 지정해야 합니다.

시나리오에 따라 더 적합한 필터 유형이 다릅니다(자산 필터 또는 계정 필터). 계정 필터는 일반적으로 디바이스 프로필에 적합(변환 필터링)한 반면, 자산 필터는 특정 자산을 트리밍하는 데 사용할 수 있습니다.

다음 속성을 사용하여 필터를 설명합니다. 

|Name|설명|
|---|---|
|firstQuality|필터의 첫 번째 품질 비트 전송률입니다.|
|presentationTimeRange|프레젠테이션 시간 범위입니다. 이 속성은 매니페스트 시작/종료 지점, 프레젠테이션 창 길이 및 라이브 시작 위치를 필터링하는 데 사용됩니다. <br/>자세한 내용은 [PresentationTimeRange](#PresentationTimeRange)를 참조하세요.|
|tracks|트랙 선택 조건입니다. 자세한 내용은 [tracks](#tracks)를 참조하세요.|

### <a name="presentationtimerange"></a>PresentationTimeRange

이 속성은 **자산 필터**와 함께 사용합니다. **계정 필터**를 사용하여 이 속성을 설정하지 않는 것이 좋습니다.

|Name|설명|
|---|---|
|**endTimestamp**|절대 종료 시간 경계입니다. VoD(주문형 비디오)에 적용됩니다. 라이브 프레젠테이션의 경우 이 속성은 자동으로 무시되며 프레젠테이션이 종료되고 스트림이 VoD가 되면 적용됩니다.<br/><br/>값은 스트림의 절대 종료 지점을 나타냅니다. 가장 가까운 다음 GOP 시작 지점으로 반올림됩니다.<br/><br/>재생 목록(매니페스트)을 트리밍하는 데 StartTimestamp 및 EndTimestamp를 사용합니다. 예를 들어 StartTimestamp=40000000 및 EndTimestamp = 100000000이면 StartTimestamp 및 EndTimestamp 사이의 미디어를 포함하는 재생 목록이 생성됩니다. 경계에 걸쳐 있는 조각인 경우 전체 조각이 매니페스트에 포함됩니다.<br/><br/>다음에 나오는 **forceEndTimestamp** 정의를 참조하세요.|
|**forceEndTimestamp**|라이브 필터에 적용됩니다.<br/><br/>**forceEndTimestamp**는 **endTimestamp**가 유효한 값으로 설정되었는지 여부를 표시하는 부울입니다. <br/><br/>값이 **true**이면 **endTimestamp** 값을 지정해야 합니다. 지정되지 않은 경우 잘못된 요청이 반환됩니다.<br/><br/>예를 들어 입력 비디오 5분에 시작되어 스트림 끝까지 지속되는 필터를 정의하려면 **forceEndTimestamp**를 false로 설정하고 **endTimestamp**설정을 생략합니다.|
|**liveBackoffDuration**|라이브 전용에 적용됩니다. 이 속성은 라이브 재생 위치를 정의하는 데 사용됩니다. 이 규칙을 사용하여 라이브 재생 위치를 지연하고 플레이어에 대한 서버 쪽 버퍼를 생성할 수 있습니다. LiveBackoffDuration은 라이브 위치에 대해 상대적입니다. 최대 라이브 백오프 기간은 300초입니다.|
|**presentationWindowDuration**|라이브에 적용됩니다. **presentationWindowDuration**을 사용하여 재생 목록에 슬라이딩 윈도우를 적용합니다. 예를 들어presentationWindowDuration=1200000000을 설정하면 2분 슬라이딩 윈도우가 적용됩니다. 라이브 에지 2분 이내의 미디어가 재생 목록에 포함됩니다. 경계에 걸쳐 있는 조각인 경우 전체 조각이 재생 목록에 포함됩니다. 최소 프레젠테이션 기간은 60초입니다.|
|**startTimestamp**|VoD 또는 라이브 스트림에 적용됩니다. 값은 스트림의 절대 시작 지점을 나타냅니다. 값은 가장 가까운 다음 GOP 시작 지점으로 반올림됩니다.<br/><br/>재생 목록(매니페스트)을 트리밍하는 데 **startTimestamp** 및 **endTimestamp**를 사용합니다. 예를 들어 startTimestamp=40000000 및 endTimestamp = 100000000이면 StartTimestamp 및 EndTimestamp 사이의 미디어를 포함하는 재생 목록이 생성됩니다. 경계에 걸쳐 있는 조각인 경우 전체 조각이 매니페스트에 포함됩니다.|
|**timescale**|VoD 또는 라이브 스트림에 적용됩니다. 위에 지정된 타임스탬프 및 지속 기간에서 사용되는 시간 간격입니다. 기본 시간 간격은 10000000입니다. 대체 시간 간격을 사용할 수 있습니다. 기본값은 10000000HNS(백 나노초)입니다.|

### <a name="tracks"></a>tracks

동적으로 작성된 매니페스트에 포함해야 하는 스트림(라이브 또는 주문형 비디오)의 트랙을 기반으로 필터 트랙 속성 조건(FilterTrackPropertyConditions)의 목록을 지정합니다. 필터는 논리 **AND** 및 **OR** 연산을 사용하여 결합됩니다.

필터 트랙 속성 조건은 트랙 유형, 값(다음 표에 설명) 및 연산(Equal, NotEqual)을 설명합니다. 

|Name|설명|
|---|---|
|**Bitrate**|필터링을 위해 트랙의 비트 전송률을 사용합니다.<br/><br/>권장 값은 비트 전송률의 범위(초당 비트 수)입니다. 예를 들어 “0-2427000”입니다.<br/><br/>참고: 특정 비트 전송률 값을 예를 들어 250000(초당 비트 수)과 같이 사용할 수 있지만, 정확한 비트 전송률이 자산별로 변동될 수 있으므로 이 방법은 권장되지 않습니다.|
|**FourCC**|필터링을 위해 트랙의 FourCC 값을 사용합니다.<br/><br/>값은 [RFC 6381](https://tools.ietf.org/html/rfc6381)에 지정된 코덱 형식의 첫 번째 요소입니다. 현재 지원되는 코덱은 다음과 같습니다. <br/>비디오: “avc1”, “hev1”, “hvc1”<br/>오디오: “mp4a”, “ec-3”<br/><br/>자산의 트랙에 대한 FourCC 값을 확인하려면 [매니페스트 파일 가져오기 및 검사](#get-and-examine-manifest-files)를 참조하세요.|
|**언어**|필터링을 위해 트랙의 언어를 사용합니다.<br/><br/>값은 RFC 5646에 지정된, 포함할 언어의 태그입니다. 예: "en".|
|**Name**|필터링을 위해 트랙의 이름을 사용합니다.|
|**형식**|필터링을 위해 트랙의 유형을 사용합니다.<br/><br/>허용되는 값은 “video”, “audio” 또는 “text”입니다.|

## <a name="example"></a>예

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

## <a name="next-steps"></a>다음 단계

다음 문서에서는 프로그래밍 방식으로 필터를 만드는 방법을 설명합니다.  

- [REST API를 사용하여 필터 만들기](filters-dynamic-manifest-rest-howto.md)
- [.NET을 사용하여 필터 만들기](filters-dynamic-manifest-dotnet-howto.md)
- [CLI를 사용하여 필터 만들기](filters-dynamic-manifest-cli-howto.md)

