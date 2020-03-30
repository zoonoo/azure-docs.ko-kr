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
ms.date: 05/23/2019
ms.author: juliako
ms.openlocfilehash: fdf29924da31db0347938df89e698cb258c2336b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251467"
---
# <a name="filters"></a>필터

고객에게 콘텐츠를 제공할 때(라이브 스트리밍 이벤트 또는 주문형 비디오) 클라이언트는 기본 자산의 매니페스트 파일에 설명된 것보다 더 많은 유연성이 필요할 수 있습니다. Azure Media 서비스는 미리 정의된 필터를 기반으로 [동적 매니페스트를](filters-dynamic-manifest-overview.md) 제공합니다. 

필터는 고객이 다음과 같은 작업을 수행할 수 있도록 하는 서버 쪽 규칙입니다. 

- 전체 비디오를 재생하는 대신 비디오의 한 섹션만 재생합니다. 예를 들어:
  - 라이브 이벤트의 하위 클립을 표시하는 매니페스트를 줄입니다("하위 클립 필터링").
  - 비디오의 시작 부분을 자릅니다("비디오 트리밍").
- 콘텐츠를 재생하는 데 사용되는 디바이스에서 지원하는 지정된 변환 및/또는 지정된 언어 트랙만 전달합니다(“변환 필터링”). 
- 플레이어의 DVR 창 길이를 제한하기 위해 프레젠테이션 창(DVR)을 조정합니다("프레젠테이션 창 조정").

미디어 서비스를 사용하면 **콘텐츠에** 대한 계정 필터 및 **자산 필터를** 만들 수 있습니다. 또한 미리 만든 필터를 **스트리밍 로케이터**와 연결할 수 있습니다.

## <a name="defining-filters"></a>필터 정의

필터에는 두 가지 유형이 있습니다. 

* [계정 필터](https://docs.microsoft.com/rest/api/media/accountfilters)(전역) - Azure Media Services 계정의 모든 자산에 적용 가능하며 계정의 수명 동안 보유합니다.
* [자산 필터](https://docs.microsoft.com/rest/api/media/assetfilters)(로컬) -필터를 만들 때 연결된 자산에만 적용 가능하며 자산의 수명 동안 보유합니다. 

계정 필터 및 **자산 필터** 유형은 **필터를** 정의/설명하기 위한 것과 정확히 동일한 속성을 갖습니다. **자산 필터**를 작성하는 경우 외에는 필터를 연결할 자산 이름을 지정해야 합니다.

시나리오에 따라 더 적합한 필터 유형이 다릅니다(자산 필터 또는 계정 필터). 계정 필터는 일반적으로 디바이스 프로필에 적합(변환 필터링)한 반면, 자산 필터는 특정 자산을 트리밍하는 데 사용할 수 있습니다.

다음 속성을 사용하여 필터를 설명합니다. 

|이름|설명|
|---|---|
|firstQuality|필터의 첫 번째 품질 비트 전송률입니다.|
|presentationTimeRange|프레젠테이션 시간 범위입니다. 이 속성은 매니페스트 시작/종료 지점, 프레젠테이션 창 길이 및 라이브 시작 위치를 필터링하는 데 사용됩니다. <br/>자세한 내용은 [PresentationTimeRange](#presentationtimerange)를 참조하세요.|
|tracks|트랙 선택 조건입니다. 자세한 내용은 [tracks](#tracks)를 참조하세요.|

### <a name="presentationtimerange"></a>presentationTimeRange

이 속성은 **자산 필터**와 함께 사용합니다. **계정 필터**를 사용하여 이 속성을 설정하지 않는 것이 좋습니다.

|이름|설명|
|---|---|
|**endTimestamp**|VoD(주문형 비디오)에 적용됩니다.<br/>라이브 스트리밍 프레젠테이션의 경우 프레젠테이션이 종료되고 스트림이 VoD가 되면 자동으로 무시되고 적용됩니다.<br/>이 값은 프레젠테이션의 절대 끝점을 나타내는 긴 값으로, 가장 가까운 다음 GOP 시작으로 반올림됩니다. 단위는 시간 척도이므로 18000000000의 끝타임 스탬프는 3 분 동안입니다.<br/>startTimestamp 및 endTime스탬프를 사용하여 재생 목록(매니페스트)에 있을 조각을 트리밍합니다.<br/>예를 들어 기본 타임스케일을 사용하여 startTimestamp=40000000 및 endTimestamp=100000000은 VoD 프레젠테이션의 4초에서 10초 사이의 조각을 포함하는 재생 목록을 생성합니다. 경계에 걸쳐 있는 조각인 경우 전체 조각이 매니페스트에 포함됩니다.|
|**forceEndTimestamp**|라이브 스트리밍에만 적용됩니다.<br/>endTimestamp 속성이 있어야 하는지 여부를 나타냅니다. true이면 endTimestamp를 지정하거나 잘못된 요청 코드가 반환되어야 합니다.<br/>허용되는 값: true, false|
|**liveBackoffDuration**|라이브 스트리밍에만 적용됩니다.<br/> 이 값은 클라이언트가 추구할 수 있는 최신 라이브 위치를 정의합니다.<br/>이 속성을 사용하면 라이브 재생 위치를 지연하고 플레이어를 위한 서버 쪽 버퍼를 만들 수 있습니다.<br/>이 속성의 단위는 시간 표시입니다(아래 참조).<br/>최대 라이브 백 오프 지속 시간은 300초(30000000000)입니다.<br/>예를 들어 값이 200000000이면 사용 가능한 최신 콘텐츠가 실제 라이브 에지에서 20초 지연된다는 것을 의미합니다.|
|**presentationWindowDuration**|라이브 스트리밍에만 적용됩니다.<br/>프리젠 테이션을 사용하여WindowDuration 재생 목록에 포함 할 조각의 슬라이딩 창을 적용합니다.<br/>이 속성의 단위는 시간 표시입니다(아래 참조).<br/>예를 들어presentationWindowDuration=1200000000을 설정하면 2분 슬라이딩 윈도우가 적용됩니다. 라이브 에지 2분 이내의 미디어가 재생 목록에 포함됩니다. 경계에 걸쳐 있는 조각인 경우 전체 조각이 재생 목록에 포함됩니다. 최소 프레젠테이션 기간은 60초입니다.|
|**startTimestamp**|주문형 비디오(VoD) 또는 라이브 스트리밍에 적용됩니다.<br/>이 값은 스트림의 절대 시작점을 나타내는 긴 값입니다. 값은 가장 가까운 다음 GOP 시작 지점으로 반올림됩니다. 단위는 타임스케일이므로 150000000의 시작타임 스탬프는 15초 동안입니다.<br/>startTimestamp 및 endTimestampp를 사용하여 재생 목록(매니페스트)에 있을 조각을 트리밍합니다.<br/>예를 들어 기본 타임스케일을 사용하여 startTimestamp=40000000 및 endTimestamp=100000000은 VoD 프레젠테이션의 4초에서 10초 사이의 조각을 포함하는 재생 목록을 생성합니다. 경계에 걸쳐 있는 조각인 경우 전체 조각이 매니페스트에 포함됩니다.|
|**timescale**|1초 의 증분 수로 지정된 프레젠테이션 시간 범위의 모든 타임스탬프 및 기간에 적용됩니다.<br/>기본값은 1000000 -1초에 1,000만 씩 증가하며 각 증분은 100나노초길이입니다.<br/>예를 들어 시작타임스탬프를 30초로 설정하려면 기본 타임스케일을 사용할 때 300000000의 값을 사용합니다.|

### <a name="tracks"></a>tracks

스트림의 트랙(라이브 스트리밍 또는 주문형 비디오)을 동적으로 생성된 매니페스트에 포함할 필터 트랙 속성 조건(FilterTrackPropertyConditions) 목록을 지정합니다. 필터는 논리 **AND** 및 **OR** 연산을 사용하여 결합됩니다.

필터 트랙 속성 조건은 트랙 유형, 값(다음 표에 설명) 및 연산(Equal, NotEqual)을 설명합니다. 

|이름|설명|
|---|---|
|**Bitrate**|필터링을 위해 트랙의 비트 전송률을 사용합니다.<br/><br/>권장 값은 비트 전송률의 범위(초당 비트 수)입니다. 예를 들어 “0-2427000”입니다.<br/><br/>참고: 특정 비트 전송률 값을 예를 들어 250000(초당 비트 수)과 같이 사용할 수 있지만, 정확한 비트 전송률이 자산별로 변동될 수 있으므로 이 방법은 권장되지 않습니다.|
|**FourCC**|필터링을 위해 트랙의 FourCC 값을 사용합니다.<br/><br/>값은 [RFC 6381](https://tools.ietf.org/html/rfc6381)에 지정된 코덱 형식의 첫 번째 요소입니다. 현재 지원되는 코덱은 다음과 같습니다. <br/>비디오: “avc1”, “hev1”, “hvc1”<br/>오디오: “mp4a”, “ec-3”<br/><br/>자산의 트랙에 대한 FourCC 값을 확인하려면 매니페스트 파일 가져오기 및 검사를 참조하세요.|
|**언어**|필터링을 위해 트랙의 언어를 사용합니다.<br/><br/>값은 RFC 5646에 지정된, 포함할 언어의 태그입니다. 예: "en".|
|**이름**|필터링을 위해 트랙의 이름을 사용합니다.|
|**유형**|필터링을 위해 트랙의 유형을 사용합니다.<br/><br/>허용되는 값은 “video”, “audio” 또는 “text”입니다.|

### <a name="example"></a>예제

다음 예제에서는 라이브 스트리밍 필터를 정의합니다. 

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

## <a name="associating-filters-with-streaming-locator"></a>스트리밍 로케이터와 필터 연결

[스트리밍 로케이터](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body)에서 [자산 또는 계정 필터](filters-concept.md) 목록을 지정할 수 있습니다. [동적 패키지는](dynamic-packaging-overview.md) 클라이언트가 URL에서 지정한 필터와 함께 이 필터 목록을 적용합니다. 이 조합은 스트리밍 로케이터에서 지정한 URL + 필터의 필터를 기반으로 하는 [동적 매니페스트를](filters-dynamic-manifest-overview.md)생성합니다. 

다음 예제를 참조하세요.

* [스트리밍 로케이터와 필터 연결 - .NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [스트리밍 로케이터와 필터 연결 - CLI](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>필터 업데이트
 
필터를 업데이트할 수 있는 동안 **스트리밍 로케이터는** 업데이터 로케이터가 아닙니다. 

특히 CDN이 활성화된 경우 적극적으로 게시된 **스트리밍 로케이터와**관련된 필터의 정의를 업데이트하지 않는 것이 좋습니다. 스트리밍 서버와 CDN에는 내부 캐시가 있을 수 있으며, 이로 인해 캐시된 데이터가 부실로 인해 반환될 수 있습니다. 

필터 정의를 변경해야 하는 경우 새 필터를 만들고 **스트리밍 로케이터** URL에 추가하거나 필터를 직접 참조하는 새 **스트리밍 로케이터를** 게시하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서는 프로그래밍 방식으로 필터를 만드는 방법을 설명합니다.  

- [REST API를 사용하여 필터 만들기](filters-dynamic-manifest-rest-howto.md)
- [.NET을 사용하여 필터 만들기](filters-dynamic-manifest-dotnet-howto.md)
- [CLI를 사용하여 필터 만들기](filters-dynamic-manifest-cli-howto.md)

