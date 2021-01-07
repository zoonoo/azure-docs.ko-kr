---
title: 녹음/녹화 재생-Azure
description: 연속 비디오 녹화 IoT Edge에서 라이브 비디오 분석을 사용 하 여 몇 주 또는 몇 달 동안 클라우드에 비디오를 녹화할 수 있습니다. 이벤트 기반 기록을 통해 관심 있는 클립으로 기록을 제한할 수도 있습니다. 이 문서에서는 녹화를 재생 하는 방법을 설명 합니다.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 6222d2c05b2fe05945d4bcbef6dbb0d64bd4726a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84260386"
---
# <a name="playback-of-recordings"></a>녹음/녹화 재생 

## <a name="pre-read"></a>미리 읽기  

* [비디오 재생](video-playback-concept.md)
* [연속 비디오 녹화](continuous-video-recording-concept.md)
* [이벤트 기반 비디오 녹화](event-based-video-recording-concept.md)

## <a name="background"></a>배경  

Cvr ( [연속 비디오 기록](continuous-video-recording-concept.md) )에 대 한 IoT Edge에서 라이브 비디오 분석을 사용 하 여 몇 주 또는 몇 달 동안 클라우드에 비디오를 녹화할 수 있습니다. 또한 [이벤트 기반 비디오 기록](event-based-video-recording-concept.md) (evr)을 통해 관심 있는 클립으로 기록을 제한할 수 있습니다. 

미디어 서비스 계정은 Azure Storage 계정에 연결 되어 있으며, 클라우드에 비디오를 기록 하는 경우 콘텐츠가 [미디어 서비스 자산](terminology.md#asset)에 기록 됩니다. Media Services를 사용 하면 기록이 완료 된 후 또는 기록이 진행 되는 동안 [해당 콘텐츠를 스트리밍할](terminology.md#streaming)수 있습니다. Media Services는 HLS 또는 MPEG-대시 프로토콜을 통해 재생 장치 (클라이언트)로 스트림을 전달 하는 데 필요한 기능을 제공 합니다. 자세한 내용은 [비디오 재생](video-playback-concept.md) 문서를 참조 하세요. 미디어 서비스 Api를 사용 하 여 클라이언트에서 비디오 & 오디오를 재생 하는 데 사용 하는 필수 스트리밍 Url을 생성 합니다. 자산에 대 한 스트리밍 URL을 생성 하려면 [스트리밍 로케이터 및 빌드 url 만들기](../latest/create-streaming-locator-build-url.md)를 참조 하세요. 자산에 대 한 스트리밍 URL을 만든 후에는 콘텐츠 관리 시스템에서 URL의 연결을 비디오 원본 (카메라)과 함께 저장 해야 합니다.

예를 들어 HLS를 통해 스트리밍할 때 스트리밍 URL은 처럼 보입니다 `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` . MPEG 대시의 경우 처럼 보입니다 `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=mpd-time-cmaf).mpd` .

이렇게 하면 매니페스트 파일이 반환 됩니다. 여기에는 전달 되는 스트림의 전체 지속 기간, 그리고 미리 기록 된 콘텐츠를 나타내는지 아니면 진행 중인 ' 라이브 ' 피드가 설명 되어 있습니다.

### <a name="live-vs-vod"></a>라이브 및 VoD  

HLS 및 MPEG와 같은 스트리밍 프로토콜은 라이브 비디오 스트리밍 같은 시나리오를 처리 하 고 TV 쇼 및 동영상과 같은 주문형/미리 기록 된 콘텐츠의 스트리밍을 처리 하도록 작성 되었습니다. 라이브 비디오의 경우 HLS 및 MPEG 대시 클라이언트는 ' 가장 최근 ' 시간부터 재생을 시작 하도록 설계 되었습니다. 그러나 영화를 사용 하면 처음부터 시작 하 여 선택 하는 경우 주위로 이동할 수 있는 것으로 간주 됩니다. HLS 및 MPEG 대시 매니페스트에는 비디오에서 라이브 스트림을 나타내는지 또는 미리 기록 된 콘텐츠 인지 여부를 클라이언트에 나타내는 플래그가 있습니다.
이 개념은 IoT Edge의 Live Video Analytics를 사용 하 여 녹화 된 비디오를 포함 하는 자산의 HLS 및 MPEG 대시 스트림에도 적용 됩니다.

## <a name="browsing-and-selective-playback-of-recordings"></a>녹음/녹화의 찾아보기 및 선택적 재생  

IoT Edge에서 Live Video Analytics를 사용 하 여 전체 교육 연도에 대해 학교가 열려 있는 경우에만 오전 8 시에서 오전 10 시까지 비디오를 기록 하는 시나리오를 고려해 보세요. 또는 국가별 휴일에서 오후 7 시부터 오후 7 시까지 비디오를 기록 하 고 있을 수 있습니다. 이러한 두 시나리오 중 하나에서 비디오 녹화를 찾아보고 보려고 할 때 다음이 필요 합니다.

* 기록에 있는 비디오의 날짜/시간을 확인 하는 방법입니다.
* 재생에 기록 하는 부분 (예: 오전 9 시에서 오전 11 시 오전 11 시)을 선택 하는 방법입니다.

Media Services는 쿼리 API (availableMedia)를 제공 하 여 첫 번째 문제를 해결 하 고 시간 범위 필터 (startTime, endTime)를 제공 하 여 두 번째 문제를 해결 합니다.

## <a name="query-api"></a>쿼리 API 

CVR를 사용 하는 경우 재생 장치 (클라이언트)는 전체 기록 재생을 포괄 하는 매니페스트를 요청할 수 없습니다.  다중 연도 기록은 재생할 수 없는 매니페스트 파일을 생성 하 고 클라이언트 쪽에서 사용 가능한 부분으로 구문 분석 하기가 어렵습니다.  클라이언트는 많은 추측 작업 없이 유효한 요청을 수행할 수 있도록 기록의 데이터가 포함 된 datetime 범위를 알고 있어야 합니다. 새 쿼리 API가 제공 되는 위치입니다. 클라이언트는 이제이 서버 쪽 API를 사용 하 여 콘텐츠를 검색할 수 있습니다.

여기서 전체 자릿수 값은 연도, 월, 일 또는 전체 (아래와 같이) 중 하나일 수 있습니다. 

|자릿수|연도|month|일|전체|
|---|---|---|---|---|
|쿼리|`/availableMedia?precision=year&startTime=2018&endTime=2019`|`/availableMedia?precision=month& startTime=2018-01& endTime=2019-02`|`/availableMedia?precision=day& startTime=2018-01-15& endTime=2019-02-02`|`/availableMedia?precision=full& startTime=2018-01-15T10:08:11.123& endTime=2019-01-015T12:00:01.123`|
|응답|`{  "timeRanges":[{ "start":"2018", "end":"2019" }]}`|`{  "timeRanges":[{ "start":"2018-03", "end":"2019-01" }]}`|`{  "timeRanges":[    { "start":"2018-03-01", "end":"2018-03-07" },    { "start":"2018-03-09", "end":"2018-03-31" }  ]}`|전체 충실도 응답입니다. 간격이 전혀 없는 경우 시작은 startTime이 고 end는 endTime이 됩니다.|
|구속|&#x2022;startTime <= endTime<br/>&#x2022;모두 YYYY 형식 이어야 합니다. 그렇지 않으면 오류를 반환 합니다.<br/>&#x2022;값은 연도가 떨어져 있을 수 있습니다.<br/>&#x2022;값이 포함 됩니다.|&#x2022;startTime <= endTime<br/>&#x2022;모두 YYYY-MM-DD 형식 이어야 합니다. 그렇지 않으면 오류를 반환 합니다.<br/>&#x2022;값은 12 개월 이상 떨어져 있을 수 있습니다.<br/>&#x2022;값이 포함 됩니다.|&#x2022;startTime <= endTime<br/>&#x2022;모두 YYYY-MM-DD 형식 이어야 합니다. 그렇지 않으면 오류를 반환 합니다.<br/>&#x2022;값은 31 일 이상 떨어져 있을 수 있습니다.<br/>값은 포함 되어 있습니다.|&#x2022;startTime < endTime<br/>&#x2022;값은 최대 25 시간 정도 떨어질 수 있습니다.<br/>&#x2022;값이 포함 됩니다.|

#### <a name="additional-request-format-considerations"></a>추가 요청 형식 고려 사항

* 모든 시간은 UTC 시간입니다.
* Precision 쿼리 문자열 매개 변수가 필요 합니다.  
* Month, day 및 full 전체 자릿수 값에는 startTime 및 endTime 쿼리 문자열 매개 변수가 필요 합니다.  
* StartTime 및 endTime 쿼리 문자열 매개 변수는 year 전체 자릿수 값에 대 한 선택 사항입니다. (none, 둘 중 하나 또는 둘 다 지원 됨)  

    * StartTime을 생략 하면 기록의 첫 번째 연도로 간주 됩니다.
    * EndTime이 생략 되 면 기록의 작년 인 것으로 간주 됩니다.
    * 예를 들어 기록이 2011에서 시작 되 고 2020까지 계속 되 면 다음을 수행 합니다.

        * /availableMedia? precision = year는/availableMedia? precision = year&startTime = 2011&endTime = 2020와 동일 합니다.
        * /availableMedia? precision = year&startTime = 2015는/availableMedia? precision = year&startTime = 2015&endTime = 2020와 동일 합니다.
        * /availableMedia? precision = year&endTime = 2018은/availableMedia? precision = year&startTime = 2011&endTime = 2018와 동일 합니다.

시간 범위에 사용할 수 있는 미디어 데이터가 없으면 빈 목록이 반환 됩니다.

자산이 미디어 그래프에서 기록을 포함 하지 않는 경우에는이 기능을 미디어 그래프를 통해 기록 된 콘텐츠에만 사용할 수 있다는 것을 설명 하는 오류 메시지와 함께 HTTP 400 응답이 반환 됩니다.

매개 변수로 지정 된 기간이 지정 된 쿼리 형식에 대 한 최대 시간 범위에서 허용 하는 것 보다 큰 경우 요청 된 쿼리 형식에 대해 허용 되는 최대 시간 범위를 설명 하는 오류 메시지와 함께 HTTP 400이 반환 됩니다.

지정 된 매개 변수에 대해 시간 범위가 유효 하다 고 가정 하면 기록 데이터의 기간을 벗어난 쿼리에 대해 오류가 반환 되지 않습니다.  기록이 7 시간 전에 시작 되었지만 고객이 {UtcNow – 24 시간}에서 UtcNow로 사용할 수 있는 미디어를 요청 하는 경우에는 {UtcNow – 7} 시간 동안의 데이터만 반환 합니다.

### <a name="response-format"></a>응답 형식  

AvailableMedia 호출은 시간 값 집합을 반환 합니다.

응답은 JSON 본문이 며,이는 timeRanges 값이 요청 된 전체 자릿수에 따라 연도 (YYYY 형식), 월 (yyyy-MM 형식) 또는 일 (YYYY-MM-DD)의 ISO 8601 UTC 날짜의 배열입니다.  전체 timeRanges에는 ISO 8601 UTC 날짜 시간으로 서식이 지정 된 시작 값과 끝 값 (YYYY-MM-Yyyy-mm-ddthh: MM: ss. sssZ 형식)이 모두 포함 됩니다.

AvailableMedia 쿼리의 경우 API는 비디오 타임 라인에서 키를 해제 합니다. 타임 라인의 모든 불연속성 응답의 간격으로 표시 됩니다.

#### <a name="response-example-for-availablemedia"></a>AvailableMedia에 대 한 응답 예제

##### <a name="example-1-live-recording-with-no-gaps"></a>예 1: 간격이 없는 라이브 녹음

다음은 2019 년 12 월 21 일에 사용 가능한 모든 미디어를 표시 하는 응답입니다 .이 응답은 기록이 100% 완료 되었습니다. 응답에는 하나의 시작/끝 쌍만 있습니다.

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

##### <a name="example-2-live-recording-with-a-2-second-gap"></a>예 2:2 초 간격으로 라이브 기록

몇 가지 이유 때문에 카메라에서 하루에 2 초 간격으로 유효한 비디오를 보내지 못했습니다. 2019 년 12 월 21 일에 사용 가능한 모든 미디어를 표시 하는 응답은 다음과 같습니다.

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

##### <a name="example-3-live-recording-with-an-8-hour-gap"></a>예 3:8 시간 간격이 있는 라이브 기록

카메라 및/또는 온-프레미스 시설에서 오전 4 시 UTC부터 정오 까지의 8 시간 동안 전원이 손실 되 고 백업 전원 원본이 없는 경우를 가정 합니다. 다음은 해당 날짜에 사용 가능한 모든 미디어를 표시 하는 응답입니다.

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

#### <a name="example-4-live-recording-where-no-video-is-recorded-over-summer-holidays"></a>예제 4: 여름 휴일을 통해 비디오가 녹화 되지 않는 라이브 녹음

학교의 세션이 있었던 경우에만 비디오를 녹화 했 고 기록이 6 월 17 일에서 9 월 6 일까가 중지 되었다고 가정 합니다. 사용 가능한 월에 대 한 쿼리는 다음과 같습니다.

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

6 월에 사용 가능한 날짜를 묻는 메시지가 표시 되 면 다음이 표시 됩니다.

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

##### <a name="example-5-live-recording-where-no-video-is-recorded-over-weekends-or-holidays"></a>예 5: 주말 또는 휴일에 비디오를 녹화 하지 않는 라이브 녹음

업무 시간 중에만 비디오를 녹화 했다고 가정 합니다. 사용 가능한 일에 대 한 쿼리는 다음과 같습니다.

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

위에서 설명한 것 처럼 이러한 필터는 재생을 위해 기록 부분 (예: 오전 9 시에서 오전 11 시부터 오전 11 시까지)을 선택 하는 데 도움이 됩니다. HLS를 통해 스트리밍할 때 스트리밍 URL은 처럼 보입니다 `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` . 기록의 일부를 선택 하기 위해 startTime 및 endTime 매개 변수 (예:)를 추가 `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00Z,endTime=2019-12-21T10:00:00Z).m3u8` 합니다. 따라서 시간 범위 필터는 스트리밍 매니페스트에 포함 된 기록의 타임 라인 부분을 설명 하는 데 사용 되는 URL 한정자입니다.

* `starttime` 반환 된 매니페스트에서 비디오 타임 라인의 원하는 시작 시간을 설명 하는 ISO 8601 DateTime 스탬프입니다.
* `endtime` 는 매니페스트에서 반환 되는 비디오 타임 라인의 원하는 종료 시간을 설명 하는 ISO 8601 DateTime 스탬프입니다.

이러한 매니페스트의 최대 길이 (시간)는 24 시간을 초과할 수 없습니다.

다음은 필터에 대 한 제약 조건입니다.

|startTime| endTime |결과|
|---|---|---|
|Absent |Absent |최대 4 시간 길이까지 자산의 최신 비디오 부분을 반환 합니다.<br/><br/>자산이 최근에 기록 되지 않은 경우 (새로운 비디오 데이터를 제공 하지 않음), VoD (주문형) 매니페스트가 반환 됩니다. 그렇지 않으면 라이브 매니페스트가 반환 됩니다.|
|표시|Absent|    StartTime 보다 더 최신인 비디오를 사용할 수 있는 매니페스트를 반환 합니다. 해당 매니페스트가 24 시간 보다 짧은 경우<br/>매니페스트의 길이가 24 시간을 초과 하는 경우 오류를 반환 합니다.<br/>자산이 최근에 기록 되지 않은 경우 (새로운 비디오 데이터를 제공 하지 않음), VoD (주문형) 매니페스트가 반환 됩니다. 그렇지 않으면 라이브 매니페스트가 반환 됩니다.
|Absent|표시 |오류 – startTime이 있으면 endTime이 필수입니다.|
|표시|표시|StartTime 및 endTime 사이에 어떤 비디오를 사용할 수 있는 VoD 매니페스트를 반환 합니다.<br/>범위 (startTime, endTime)는 24 시간을 초과할 수 없습니다.|

### <a name="response-examples"></a>응답 예제  

#### <a name="example-1-live-recording-with-no-gaps"></a>예 1: 간격이 없는 라이브 녹음

다음은 2019 년 12 월 21 일에 사용 가능한 모든 미디어를 표시 하는 응답입니다 .이 응답은 기록이 100% 완료 되었습니다. 응답에는 하나의 시작/끝 쌍만 있습니다.

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

기록이 연속 되 면 해당 시작/끝 쌍 내에서 임의 시간 동안 HLS 또는 대시 매니페스트를 요청할 수 있습니다. 범위가 24 시간 이하인 경우에 한 합니다. 위의 4 시간 HLS 매니페스트 요청에 대 한 예제는 다음과 같습니다.

`GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:00:00.000Z,endTime=2019-12-21T18:00:00.000Z).m3u8`

#### <a name="example-2-live-recording-with-a-2-second-gap"></a>예 2:2 초 간격으로 라이브 기록

몇 가지 이유 때문에 카메라에서 하루에 2 초 간격으로 유효한 비디오를 보내지 못했습니다. 2019 년 12 월 21 일에 사용 가능한 미디어를 표시 하는 응답은 다음과 같습니다.

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

위와 같은 기록을 사용 하 여, 범위가 24 시간 미만인 동안에는 startTime/endTime 쌍으로 HLS 및 대시 매니페스트를 요청할 수 있습니다. 이러한 값이 04:01:08AM UTC로 straddled 경우 반환 된 매니페스트는 해당 프로토콜에 대 한 관련 사양에 따라 미디어 타임 라인의 불연속성에 게 알립니다.

#### <a name="example-3-live-recording-with-an-8-hour-gap"></a>예 3:8 시간 간격이 있는 라이브 기록

카메라 및/또는 온-프레미스 시설에서 오전 4 시 UTC부터 정오 까지의 8 시간 동안 전원이 손실 되 고 백업 전원 원본이 없는 경우를 가정 합니다. 다음은 해당 날짜에 사용 가능한 모든 미디어를 표시 하는 응답입니다.

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

이러한 기록 사용:

* StartTime/endTime 범위 필터가 모두 타임 라인의 ' 사용 가능한 ' 부분 (자정에서 오전 4 시 또는 정오에서 자정) 안에 있는 매니페스트를 요청 하면 서비스는 startTime에서 endTime 까지의 시간을 포함 하는 매니페스트를 반환 합니다. 예를 들면 다음과 같습니다.

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:01:00.000Z,endTime=2019-12-21T03:00:00.000Z).m3u8`
* StartTime 및 endTime이 중간의 ' 구멍 ' 내에 있었던 매니페스트를 요청 하는 경우 (예를 들어 오전 8 시에서 오전 10 시 UTC로), 서비스는 자산 필터가 빈 결과를 생성 하는 것과 동일한 방식으로 동작 합니다.

    [빈 응답을 가져오는 요청] `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00.000Z,endTime=2019-12-21T10:00:00.000Z).m3u8`
* StartTime 또는 endTime 중 하나만 ' 구멍 ' 안에 있는 매니페스트를 요청 하면 반환 된 매니페스트에는 해당 timespan의 일부만 포함 됩니다. StartTime 또는 endTime 값을 가장 가까운 올바른 경계에 맞춥니다. 예를 들어 오전 10 시에서 오후 1 시로 3 시간 스트림을 요청 하는 경우 응답에는 12 오후 1 시에 해당 하는 미디어의 미디어가 포함 됩니다.

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T10:00:00.000Z,endTime=2019-12-21T13:00:00.000Z).m3u8`
    
    반환 된 매니페스트는 요청에서 오전 10 시를 요청 하더라도 2019-12-21T12:00:00.000 Z에서 시작 됩니다. 플레이어 플러그 인을 사용 하 여 비디오 관리 시스템을 빌드하는 경우이를 뷰어에 알리기 위해 주의를 기울여야 합니다. 인식 하지 않는 뷰어는 재생 타임 라인이 정오부터 시작 되 고 오전 10 시가 아니라는 것과 혼동 됩니다.

## <a name="recording-and-playback-latencies"></a>기록 및 재생 대기 시간

IoT Edge에서 Live Video Analytics를 사용 하 여 자산에 기록 하는 경우 클라우드에 기록 되기 전의 최소 비디오 기간 (초)을 집계 하도록 모듈에 지시 하는 segmentLength 속성을 지정 합니다. 예를 들어 segmentLength가 300로 설정 된 경우 1 5 분 "청크"를 업로드 하기 전에 모듈에서 5 분 분량의 비디오를 누적 한 후 다음 5 분 동안 누적 모드로 전환 하 고 다시 업로드 합니다. SegmentLength를 높이면 읽기 및 쓰기 수가 segmentLength 초 마다 한 번만 더 자주 발생 하지 않으므로 Azure Storage 트랜잭션 비용을 낮출 수 있습니다.

따라서 Media Services의 비디오 스트리밍은 최소한의 시간 만큼 지연 됩니다. 

재생 대기 시간을 결정 하는 또 다른 요소는 재생 대기 시간을 결정 합니다 (카메라 앞에서 이벤트가 발생 하는 시간 사이에 재생 장치에서 볼 수 있는 시간까지 지연)은 그림 그룹 [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) 기간입니다. [3 개의 간단한 기술을 사용 하 여 라이브 스트림의 지연을 줄이면](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641) GOP 기간이 길수록 대기 시간이 길어집니다. 일반적으로 Gop를 사용 하도록 구성 된 감시 및 보안 시나리오에서 사용 되는 IP 카메라가 30 초 보다 깁니다. 이는 전체 대기 시간에 큰 영향을 미칩니다.

## <a name="next-steps"></a>다음 단계

[연속 비디오 녹화 자습서](continuous-video-recording-tutorial.md)
