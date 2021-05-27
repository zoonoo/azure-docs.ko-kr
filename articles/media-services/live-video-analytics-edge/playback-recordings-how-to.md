---
title: Live Video Analytics로 녹음/녹화 재생 - Azure
description: 연속 비디오 녹화를 위한 IoT Edge의 Azure Live Video Analytics를 사용하여 몇 주 또는 몇 달 동안 클라우드에 비디오를 녹화할 수 있습니다. 이벤트 기반 녹화를 통해 관심 있는 클립으로 녹화를 제한할 수도 있습니다. 이 문서에서는 녹화를 재생하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: bc8698fc9317421513677eb64403ac2cdc3905c3
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110372083"
---
# <a name="playback-of-recordings-with-live-video-analytics"></a>Live Video Analytics로 녹음/녹화 재생

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

## <a name="pre-read"></a>미리 읽기  

* [비디오 재생](video-playback-concept.md)
* [연속 비디오 녹화](continuous-video-recording-concept.md)
* [이벤트 기반 비디오 녹화](event-based-video-recording-concept.md)

## <a name="background"></a>배경  

[CVR(연속 비디오 녹화)](continuous-video-recording-concept.md)용 Live Video Analytics on IoT Edge를 사용하여 몇 주 또는 몇 달 동안 클라우드에 비디오를 녹화할 수 있습니다. 또한 [EVR(이벤트 기반 비디오 녹화)](event-based-video-recording-concept.md)을 통해 관심 있는 클립으로 기록을 제한할 수 있습니다. 

미디어 서비스 계정은 Azure Storage 계정에 연결되어 있으며, 클라우드에 비디오를 녹화하는 경우 콘텐츠가 [미디어 서비스 자산](terminology.md#asset)에 기록됩니다. Media Services를 사용하면 녹화가 완료된 후 또는 녹화가 진행되는 동안 [해당 콘텐츠를 스트리밍](terminology.md#streaming)할 수 있습니다. Media Services는 HLS 또는 MPEG-DASH 프로토콜을 통해 재생 디바이스(클라이언트)로 스트림을 전달하는 데 필요한 기능을 제공합니다. 자세한 내용은 [비디오 재생](video-playback-concept.md) 문서를 참조하세요. Media Service API를 사용하여 클라이언트에서 비디오 및 오디오를 재생하는 데 사용하는 필수 스트리밍 URL을 생성합니다. 자산에 대한 스트리밍 URL을 생성하려면 [스트리밍 로케이터 생성 및 URL 빌드](../latest/create-streaming-locator-build-url.md)를 참조하세요. 자산에 대한 스트리밍 URL을 만든 후에는 콘텐츠 관리 시스템에 URL과 비디오 원본(카메라) 간 연결을 저장해야 합니다.

예를 들어 HLS를 통해 스트리밍할 때 스트리밍 URL은 `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8`과 같습니다. MPEG-DASH의 경우 `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=mpd-time-cmaf).mpd`과 같습니다.

이는 매니페스트 파일을 반환합니다. 이 파일에는 배달되는 스트림의 전체 지속 시간, 콘텐츠가 사전 녹화인지 아니면 '라이브' 피드인지 등이 설명되어 있습니다.

### <a name="live-vs-vod"></a>라이브 또는 VoD  

HLS 및 MPEG-DASH 같은 스트리밍 프로토콜은 라이브 비디오 스트리밍은 물론 TV 쇼 및 동영상과 같은 주문형/사전 녹화 콘텐츠 스트리밍과 같은 시나리오를 처리하도록 작성되었습니다. 라이브 비디오의 경우 HLS 및 MPEG-DASH 클라이언트는 '가장 최근' 시간부터 재생을 시작하도록 설계되었습니다. 그러나 영화의 경우 시청자가 처음부터 시작하고 원하는 경우 건너뛸 수 있어야 합니다. HLS 및 MPEG-DASH 매니페스트에는 비디오가 라이브 스트림인지 또는 사전 녹화된 콘텐츠인지 여부를 클라이언트에 나타내는 플래그가 있습니다.
이 개념은 Live Video Analytics on IoT Edge를 사용하여 녹화된 비디오를 포함하는 자산의 HLS 및 MPEG-DASH 스트림에도 적용됩니다.

## <a name="browsing-and-selective-playback-of-recordings"></a>녹화본 찾기 및 선택적 재생  

Live Video Analytics on IoT Edge를 사용하여 전체 학년 동안 등교일에 오전 8시부터 오전 10시까지만 비디오를 녹화하는 시나리오를 고려해 보세요. 또는 공휴일에 오전 7시부터 오후 7시까지 비디오를 녹화할 수 있습니다. 이러한 두 시나리오 중 하나에서 비디오 녹화본을 찾고 보려고 할 때 다음이 필요합니다.

* 녹화본에 기록된 비디오 날짜/시간을 확인하는 방법.
* 재생할 녹화 부분(예: 1월 1일 오전 9시부터 오전 11시까지)을 선택하는 방법.

Media Services는 쿼리 API(availableMedia)를 제공하여 첫 번째 문제를 해결하고 시간 범위 필터(startTime, endTime)를 제공하여 두 번째 문제를 해결합니다.

## <a name="query-api"></a>쿼리 API 

CVR을 사용하는 경우 재생 디바이스(클라이언트)는 전체 녹화본을 포괄하는 매니페스트를 요청할 수 없습니다.  다년도 녹화의 경우 매니페스트 파일이 재생할 수 없을 정도로 클 수 있고 클라이언트 쪽에서 사용 가능한 부분으로 구문 분석하기 어려울 수 있습니다.  클라이언트는 많은 추측 없이 유효한 요청을 수행할 수 있도록 녹화 데이터의 시간/날짜 범위를 알고 있어야 합니다. 이때 새 쿼리 API가 필요합니다. 클라이언트는 이제 이 서버 쪽 API를 사용하여 콘텐츠를 검색할 수 있습니다.

여기서 전체 자릿수 값은 연도, 월, 일 또는 전체 중 하나일 수 있습니다(아래 표 참조). 

|전체 자릿수|연도|month|일|전체|
|---|---|---|---|---|
|쿼리|`/availableMedia?precision=year&startTime=2018&endTime=2019`|`/availableMedia?precision=month& startTime=2018-01& endTime=2019-02`|`/availableMedia?precision=day& startTime=2018-01-15& endTime=2019-02-02`|`/availableMedia?precision=full& startTime=2018-01-15T10:08:11.123& endTime=2019-01-015T12:00:01.123`|
|응답|`{  "timeRanges":[{ "start":"2018", "end":"2019" }]}`|`{  "timeRanges":[{ "start":"2018-03", "end":"2019-01" }]}`|`{  "timeRanges":[    { "start":"2018-03-01", "end":"2018-03-07" },    { "start":"2018-03-09", "end":"2018-03-31" }  ]}`|전체 충실도 응답입니다. 간격이 전혀 없는 경우 시작은 startTime이고 종료는 endTime이 됩니다.|
|제약 조건|&#x2022;startTime <= endTime<br/>&#x2022;모두 YYYY 형식이어야 합니다. 그렇지 않으면 오류가 반환됩니다.<br/>&#x2022;값은 몇 년이든 차이가 날 수 있습니다.<br/>&#x2022;값은 경계를 포함합니다.|&#x2022;startTime <= endTime<br/>&#x2022;모두 YYYY-MM 형식이어야 합니다. 그렇지 않으면 오류가 반환됩니다.<br/>&#x2022;값은 최대 12개월까지 차이가 날 수 있습니다.<br/>&#x2022;값은 경계를 포함합니다.|&#x2022;startTime <= endTime<br/>&#x2022;모두 YYYY-MM-DD 형식이어야 합니다. 그렇지 않으면 오류가 반환됩니다.<br/>&#x2022;값은 최대 31일까지 차이가 날 수 있습니다.<br/>값은 경계를 포함합니다.|&#x2022;startTime < endTime<br/>&#x2022;값은 최대 25시간까지 차이가 날 수 있습니다.<br/>&#x2022;값은 경계를 포함합니다.|

#### <a name="additional-request-format-considerations"></a>추가 요청 형식 고려 사항

* 모든 시간은 UTC입니다.
* 전체 자릿수 쿼리 문자열 매개 변수가 필수입니다.  
* 월, 일 및 전체 자릿수 값에는 startTime 및 endTime 쿼리 문자열 매개 변수가 필수입니다.  
* 연도 전체 자릿수 값에는 startTime 및 endTime 쿼리 문자열 매개 변수가 선택 사항입니다(없음, 둘 중 하나 또는 둘 모두가 지원됨).  

    * startTime을 생략하면 첫 번째 연도 녹화본으로 간주됩니다.
    * endTime을 생략하면 작년도 녹화본으로 간주됩니다.
    * 예를 들어 녹화가 2011년에서 시작되고 2020년까지 계속된 경우

        * /availableMedia?precision=year는 /availableMedia?precision=year&startTime=2011&endTime=2020과 동일합니다.
        * /availableMedia?precision=year&startTime=2015는 /availableMedia?precision=year&startTime=2015&endTime=2020과 동일합니다.
        * /availableMedia?precision=year&endTime=2018은 /availableMedia?precision=year&startTime=2011&endTime=2018과 동일합니다.

해당 시간 범위에 사용할 수 있는 미디어 데이터가 없으면 빈 목록이 반환됩니다.

자산이 미디어 그래프를 통한 녹화본을 포함하지 않는 경우 이 기능을 미디어 그래프를 통해 녹화된 콘텐츠에만 사용할 수 있음을 설명하는 오류 메시지와 함께 HTTP 400 응답이 반환됩니다.

매개 변수로 지정된 기간이 지정된 쿼리 형식의 최대 시간 범위에서 허용하는 것보다 큰 경우 요청된 쿼리 형식에 허용되는 최대 시간 범위를 설명하는 오류 메시지와 함께 HTTP 400이 반환됩니다.

지정된 매개 변수에 대해 시간 범위가 유효한 경우 녹화 데이터의 기간을 벗어난 쿼리에 대해서는 오류가 반환되지 않습니다.  녹화가 7시간 전에 시작되었지만 고객이 {UtcNow – 24 hours}부터 UtcNow까지 사용 가능한 미디어를 요청하는 경우에는 {UtcNow – 7} 시간 동안의 데이터만 반환합니다.

### <a name="response-format"></a>응답 형식  

availableMedia 호출은 시간 값 집합을 반환합니다.

응답은 JSON 본문으로, timeRanges 값이 요청된 전체 자릿수에 따라 연도(YYYY 형식), 월(YYYY-MM 형식) 또는 일(YYYY-MM-DD 형식)의 ISO 8601 UTC 날짜의 배열입니다.  전체 timeRanges에는 ISO 8601 UTC 날짜 시간(in YYYY-MM-DDThh:mm:ss.sssZ 형식)으로 서식이 지정된 시작 값과 끝 값이 모두 포함됩니다.

availableMedia 쿼리의 경우 API는 비디오 타임라인에서 키를 해제합니다. 타임라인의 모든 불연속성은 응답에서 간격으로 표시됩니다.

#### <a name="response-example-for-availablemedia"></a>availableMedia 응답 예

##### <a name="example-1-live-recording-with-no-gaps"></a>예 1: 간격이 없는 라이브 녹화

다음은 녹화가 100% 완료된 2019년 12월 21일에 사용 가능한 모든 미디어를 표시하는 응답입니다. 응답에는 하나의 시작/끝 쌍만 있습니다.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00.000Z", 
         "end":"2019-12-22T00:00:00.000Z"
    }
   ]
}
```

##### <a name="example-2-live-recording-with-a-2-second-gap"></a>예 2: 2초 간격이 있는 라이브 녹화

몇 가지 이유 때문에 카메라가 하루에 2초 동안 유효한 비디오를 전송하지 못했습니다. 다음은 2019년 12월 21일에 사용 가능한 모든 미디어를 표시하는 응답입니다.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

##### <a name="example-3-live-recording-with-an-8-hour-gap"></a>예 3: 8시간 간격이 있는 라이브 녹화

카메라 및/또는 온-프레미스 시설에서 오전 4시 UTC부터 오후 12시 UTC까지 8시간 동안 정전이 발생하고 백업 전원이 없는 경우를 가정합니다. 다음은 해당 날짜에 사용 가능한 모든 미디어를 표시하는 응답입니다.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

#### <a name="example-4-live-recording-where-no-video-is-recorded-over-summer-holidays"></a>예 4: 여름 방학 동안 비디오가 녹화되지 않은 라이브 녹화

학기 동안만 비디오가 녹화되고 6월 17일부터 9월 6일까지 녹화가 중단되었다고 가정합니다. 사용 가능한 월에 대한 쿼리는 다음과 같습니다.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=month&startTime=2019-01&endTime=2019-12
{
   "timeRanges":[
    {
         "start":"2019-01", 
         "end":"2019-06"
    },
    {
         "start":"2019-09", 
         "end":"2019-12"
    }
   ]
}
```

그런 다음 6월에 사용 가능한 날짜를 쿼리하면 다음이 표시됩니다.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2019-06-01&endTime=2019-06-30
{
   "timeRanges":[
    {
         "start":"2019-06-01", 
         "end":"2019-06-17"
    }
   ]
}
```

##### <a name="example-5-live-recording-where-no-video-is-recorded-over-weekends-or-holidays"></a>예 5: 주말 또는 휴일에 비디오가 녹화되지 않은 라이브 녹화

업무 시간 중에만 비디오를 녹화했다고 가정합니다. 사용 가능한 날짜에 대한 쿼리는 다음과 같습니다.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2020-02-01&endTime=2020-02-29
{
   "timeRanges":[
    {
         "start":"2020-02-03", 
         "end":"2020-02-07"
    },
    {
         "start":"2020-02-10", 
         "end":"2020-02-14"
    },
    {
         "start":"2020-02-18", // Monday Feb 17th was a holiday
         "end":"2020-02-21"
    },
    {
         "start":"2020-02-24", 
         "end":"2020-02-28"
    }
   ]
}
```

## <a name="time-range-filters"></a>시간 범위 필터

위에서 설명한 것처럼 이러한 필터는 재생할 녹화 부분(예: 1월 1일 오전 9시부터 오전 11시까지)을 선택하는 데 도움이 됩니다. HLS를 통해 스트리밍할 때 스트리밍 URL은 `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8`과 같습니다. 녹화의 일부를 선택하기 위해 startTime 및 endTime 매개 변수(예: `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00Z,endTime=2019-12-21T10:00:00Z).m3u8`)를 추가합니다. 따라서 시간 범위 필터는 스트리밍 매니페스트에 포함된 녹화의 타임라인 부분을 설명하는 데 사용되는 URL 한정자입니다.

* `starttime`은 매니페스트에서 반환된 비디오 타임라인의 원하는 시작 시간을 설명하는 ISO 8601 DateTime 스탬프입니다.
* `endtime`은 매니페스트에서 반환된 비디오 타임라인의 원하는 종료 시간을 설명하는 ISO 8601 DateTime 스탬프입니다.

이러한 매니페스트의 최대 길이(시간)는 24시간을 초과할 수 없습니다.

다음은 필터 제약 조건입니다.

|startTime| endTime |결과|
|---|---|---|
|Absent |Absent |자산의 최신 비디오 부분을 최대 4시간 분량까지 반환합니다.<br/><br/>자산이 최근에 기록되지 않은 경우(새로운 비디오 데이터를 제공하지 않음) VoD(주문형) 매니페스트가 반환됩니다. 그렇지 않으면 라이브 매니페스트가 반환됩니다.|
|표시|Absent|    startTime보다 더 최신인 비디오를 사용할 수 있는 매니페스트를 반환합니다(해당 매니페스트가 24시간보다 짧은 경우).<br/>매니페스트 길이가 24 시간을 초과하는 경우 오류를 반환합니다.<br/>자산이 최근에 기록되지 않은 경우(새로운 비디오 데이터를 제공하지 않음) VoD(주문형) 매니페스트가 반환됩니다. 그렇지 않으면 라이브 매니페스트가 반환됩니다.
|Absent|표시 |오류 – startTime이 있으면 endTime은 필수입니다.|
|표시|표시|startTime과 endTime 사이에 비디오를 사용할 수 있는 VoD 매니페스트를 반환합니다.<br/>범위(startTime, endTime)는 24시간을 초과할 수 없습니다.|

### <a name="response-examples"></a>응답 예  

#### <a name="example-1-live-recording-with-no-gaps"></a>예 1: 간격이 없는 라이브 녹화

다음은 녹화가 100% 완료된 2019년 12월 21일에 사용 가능한 모든 미디어를 표시하는 응답입니다. 응답에는 하나의 시작/끝 쌍만 있습니다.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

녹화가 연속적인 경우 해당 시작/끝 쌍 내에서 임의의 시간 동안 HLS 또는 DASH 매니페스트를 요청할 수 있습니다. 단, 범위는 24시간 이하여야 합니다. 위의 4시간 HLS 매니페스트 요청에 대한 예제는 다음과 같습니다.

`GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:00:00.000Z,endTime=2019-12-21T18:00:00.000Z).m3u8`

#### <a name="example-2-live-recording-with-a-2-second-gap"></a>예 2: 2초 간격이 있는 라이브 녹화

몇 가지 이유 때문에 카메라가 하루에 2초 동안 유효한 비디오를 전송하지 못했습니다. 다음은 2019년 12월 21일에 사용 가능한 미디어를 표시하는 응답입니다.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

위와 같은 녹화를 사용하여 startTime/endTime 쌍으로 HLS 및 DASH 매니페스트를 요청할 수 있습니다(범위가 24시간 미만이어야 함). 이러한 값 사이에 04:01:08AM UTC의 간격이 존재하는 경우 반환된 매니페스트는 해당 프로토콜의 관련 사양에 따라 미디어 타임라인의 불연속성을 표시합니다.

#### <a name="example-3-live-recording-with-an-8-hour-gap"></a>예 3: 8시간 간격이 있는 라이브 녹화

카메라 및/또는 온-프레미스 시설에서 오전 4시 UTC부터 오후 12시 UTC까지 8시간 동안 정전이 발생하고 백업 전원이 없는 경우를 가정합니다. 다음은 해당 날짜에 사용 가능한 모든 미디어를 표시하는 응답입니다.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

이러한 녹화에서

* startTime/endTime 범위 필터가 모두 타임라인의 '사용 가능한' 부분(예: 오전 12시부터 오전 4시 또는 오후 12시부터 오전 12시) 안에 있는 매니페스트를 요청하면 서비스는 startTime에서 endTime까지의 시간을 포함하는 매니페스트를 반환합니다. 예를 들면 다음과 같습니다.

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:01:00.000Z,endTime=2019-12-21T03:00:00.000Z).m3u8`
* startTime 및 endTime이 중간의 '구멍' 안에 포함되는 매니페스트를 요청하는 경우(예: 오전 8시 UTC에서 오전 10시 UTC까지) 서비스는 자산 필터가 빈 결과를 생성하는 것과 동일한 방식으로 동작합니다.

    [빈 응답을 가져오는 요청] `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00.000Z,endTime=2019-12-21T10:00:00.000Z).m3u8`
* startTime 또는 endTime 중 하나만 '구멍' 안에 포함되는 매니페스트를 요청하면 반환된 매니페스트에는 해당 시간 간격 부분만 포함됩니다. startTime 또는 endTime 값을 가장 가까운 올바른 경계에 맞춥니다. 예를 들어 오전 10시부터 오후 1시까지 3시간 스트림을 요청한 경우 응답에는 오후 12부터 오후 1시까지 1시간 분량의 미디어가 포함됩니다.

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T10:00:00.000Z,endTime=2019-12-21T13:00:00.000Z).m3u8`
    
    요청에서는 오전 10시부터 시작을 요청하더라도 반환된 매니페스트는 2019-12-21T12:00:00.000Z부터 시작됩니다. 플레이어 플러그 인을 사용하여 비디오 관리 시스템을 빌드하는 경우 이를 시청자에 알리려면 주의를 기울여야 합니다. 잘 모르는 시청자라면 재생 타임라인이 요청한 대로 오전 10시가 아니라 오후 12시부터 시작하는 이유를 혼동할 수 있습니다.

## <a name="recording-and-playback-latencies"></a>녹화 및 재생 대기 시간

Live Video Analytics on IoT Edge를 사용하여 자산에 녹화하는 경우 클라우드에 기록되기 전 최소 비디오 기간(초)을 집계하도록 모듈에 지시하는 segmentLength 속성을 지정합니다. 예를 들어 segmentLength가 300으로 설정된 경우 모듈은 5분 분량의 비디오를 누적하여 5분 '청크'를 하나 업로드한 후 다음 5분 동안 누적 모드로 전환한 후 다시 업로드합니다. segmentLength를 높이면 Azure Storage 트랜잭션 비용을 낮추는 이점이 있습니다. 읽기 및 쓰기가 segmentLength(초)마다 한 번만 발생하기 때문입니다.

결과적으로 Media Services로부터의 비디오 스트리밍은 그 시간만큼 지연됩니다. 

재생 대기 시간(카메라 앞에서 이벤트가 발생한 시간과 재생 디바이스에서 해당 이벤트를 볼 수 있는 시간 사이의 지연)을 결정하는 또 다른 요소는 [GOP](https://en.wikipedia.org/wiki/Group_of_pictures)(group-of-picture) 기간입니다. [3개의 간단한 기술을 사용하여 라이브 스트림 지연 단축](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641)에서 설명하듯이 GOP 기간이 길수록 대기 시간이 길어집니다. 일반적으로 감시 및 보안 시나리오에서 사용되는 IP 카메라는 30초 이상의 GOP를 사용하도록 구성됩니다. 이는 전체 대기 시간에 큰 영향을 미칩니다.

## <a name="next-steps"></a>다음 단계

[연속 비디오 녹화 자습서](continuous-video-recording-tutorial.md)
