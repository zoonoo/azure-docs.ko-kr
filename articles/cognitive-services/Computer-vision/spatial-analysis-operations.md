---
title: 공간 분석 작업
titleSuffix: Azure Cognitive Services
description: 공간 분석 작업입니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: 9d1b6e20bf2decfe051e79e073736f71181260fa
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014546"
---
# <a name="spatial-analysis-operations"></a>공간 분석 작업

공간 분석을 사용하면 카메라 디바이스에서 실시간 스트리밍 비디오를 분석할 수 있습니다. 구성하는 각 카메라 디바이스에 대해 공간 분석 작업이 Azure IoT Hub 인스턴스로 전송되는 JSON 메시지의 출력 스트림을 생성합니다. 

공간 분석 컨테이너는 다음과 같은 작업을 구현 합니다.

| 작업 식별자| Description|
|---------|---------|
| cognitiveservices account spatialanalysis-personcount | 카메라의 보기 필드에서 지정 된 영역에 있는 사용자 수를 계산 합니다. <br> 초기 _personCountEvent_ 이벤트를 내보낸 다음 개수가 변경 되 면 이벤트를 _personCountEvent_ 합니다.  |
| cognitiveservices account spatialanalysis-personcrossingline | 사용자가 카메라의 보기 필드에서 지정 된 선을 교차할 때를 추적 합니다. <br>사용자가 줄을 _personLineEvent_ 방향 정보를 제공 하면 해당 이벤트를 내보냅니다. 
| cognitiveservices account spatialanalysis-personcrossingpolygon | 사용자가 카메라의 보기 필드에서 지정 된 선을 교차할 때를 추적 합니다. <br> 사용자가 영역을 _personLineEvent_ 방향 정보를 제공 하면 해당 이벤트를 내보냅니다. |
| cognitiveservices account spatialanalysis-persondistance | 사용자가 거리 규칙을 위반 하는 경우를 추적 합니다. <br> 각 거리 위반의 위치를 사용 하 여 주기적으로 _personDistanceEvent_ 을 내보냅니다. |

위의 모든 작업은 `.debug` 처리 중인 비디오 프레임을 시각화 하는 기능이 있는 버전 에서도 사용할 수 있습니다. `xhost +`비디오 프레임 및 이벤트의 시각화를 사용 하도록 설정 하려면 호스트 컴퓨터에서을 (를) 실행 해야 합니다.

| 작업 식별자| Description|
|---------|---------|
| cognitiveservices account spatialanalysis-personcount | 카메라의 보기 필드에서 지정 된 영역에 있는 사용자 수를 계산 합니다. <br> 초기 _personCountEvent_ 이벤트를 내보낸 다음 개수가 변경 되 면 이벤트를 _personCountEvent_ 합니다.  |
| cognitiveservices account spatialanalysis-personcrossingline | 사용자가 카메라의 보기 필드에서 지정 된 선을 교차할 때를 추적 합니다. <br>사용자가 줄을 _personLineEvent_ 방향 정보를 제공 하면 해당 이벤트를 내보냅니다. 
| cognitiveservices account spatialanalysis-personcrossingpolygon | 사용자가 카메라의 보기 필드에서 지정 된 선을 교차할 때를 추적 합니다. <br> 사용자가 영역을 _personLineEvent_ 방향 정보를 제공 하면 해당 이벤트를 내보냅니다. |
| cognitiveservices account spatialanalysis-persondistance | 사용자가 거리 규칙을 위반 하는 경우를 추적 합니다. <br> 각 거리 위반의 위치를 사용 하 여 주기적으로 _personDistanceEvent_ 을 내보냅니다. |

비디오 AI 모듈로 [라이브 비디오 분석](../../media-services/live-video-analytics-edge/spatial-analysis-tutorial.md) 으로 공간 분석을 실행할 수도 있습니다. 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| 작업 식별자| Description|
|---------|---------|
| cognitiveservices account spatialanalysis-personcount. livevideoanalytics | 카메라의 보기 필드에서 지정 된 영역에 있는 사용자 수를 계산 합니다. <br> 초기 _personCountEvent_ 이벤트를 내보낸 다음 개수가 변경 되 면 이벤트를 _personCountEvent_ 합니다.  |
| cognitiveservices account spatialanalysis-personcrossingline. livevideoanalytics | 사용자가 카메라의 보기 필드에서 지정 된 선을 교차할 때를 추적 합니다. <br>사용자가 줄을 _personLineEvent_ 방향 정보를 제공 하면 해당 이벤트를 내보냅니다. 
| cognitiveservices account spatialanalysis-personcrossingpolygon. livevideoanalytics | 사용자가 카메라의 보기 필드에서 지정 된 선을 교차할 때를 추적 합니다. <br> 사용자가 영역을 _personLineEvent_ 방향 정보를 제공 하면 해당 이벤트를 내보냅니다. |
| cognitiveservices account spatialanalysis-persondistance. livevideoanalytics | 사용자가 거리 규칙을 위반 하는 경우를 추적 합니다. <br> 각 거리 위반의 위치를 사용 하 여 주기적으로 _personDistanceEvent_ 을 내보냅니다. |

라이브 비디오 분석 작업은 `.debug` 처리 중인 비디오 프레임을 시각화 하는 기능이 포함 된 버전 (예: spatialanalysis-personcount. cognitiveservices account) 에서도 사용할 수 있습니다. `xhost +`비디오 프레임 및 이벤트의 시각화를 사용 하도록 설정 하려면 호스트 컴퓨터에서를 실행 해야 합니다.

> [!IMPORTANT]
> 컴퓨터 시각 AI 모델은 휴먼 본문 주위의 경계 상자를 사용 하 여 비디오 푸티지와 사용자의 현재 상태를 검색 하 고 찾습니다. AI 모델은 얼굴을 감지 하거나 개인의 id 또는 인구 통계를 검색 하려고 하지 않습니다.

이러한 각 공간 분석 작업에 필요한 매개 변수는 다음과 같습니다.

| 조작 매개 변수| Description|
|---------|---------|
| OperationID | 위의 테이블에 있는 작업 식별자입니다.|
| 사용 | 부울: true 또는 false|
| VIDEO_URL| 카메라 장치에 대 한 RTSP url입니다 (예: `rtsp://username:password@url` ). 공간 분석은 RTSP, http 또는 mp4를 통해 h.264로 인코딩된 스트림을 지원 합니다. |
| VIDEO_SOURCE_ID | 카메라 장치 또는 비디오 스트림의 이름입니다. 이는 이벤트 JSON 출력과 함께 반환 됩니다.|
| VIDEO_IS_LIVE| 카메라 장치에 대해 True 기록 된 비디오의 경우 false입니다.|
| VIDEO_DECODE_GPU_INDEX| 비디오 프레임을 디코딩하는 GPU입니다. 기본적으로 0입니다. 는와 같은 `gpu_index` 다른 노드 구성의와 동일 해야 합니다 `VICA_NODE_CONFIG` `DETECTOR_NODE_CONFIG` .|
| DETECTOR_NODE_CONFIG | 탐지기 노드를 실행할 GPU를 나타내는 JSON입니다. 는 다음과 같은 형식 이어야 합니다. `"{ \"gpu_index\": 0 }",`|
| SPACEANALYTICS_CONFIG | 아래에 설명 된 영역 및 줄에 대 한 JSON 구성|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>Cognitiveservices account에 대 한 영역 구성 spatialanalysis-personcount

 영역을 구성 하는 SPACEANALYTICS_CONFIG 매개 변수에 대 한 JSON 입력의 예입니다. 이 작업에 대 한 여러 영역을 구성할 수 있습니다.

```json
{
"zones":[{
    "name": "lobbycamera"
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 50.00,
    "events":[{
        "type": "count",
        "config":{
            "trigger": "event",
            "output_frequency": 1
      }
    }]
}
```

| Name | Type| Description|
|---------|---------|---------|
| `zones` | list| 영역 목록입니다. |
| `name` | 문자열| 이 영역의 이름입니다.|
| `polygon` | list| 각 값 쌍은 다각형의 꼭 짓 점에 대 한 x, y를 나타냅니다. 다각형은 사람들이 추적 하거나 계산 하는 영역을 나타내며, 다각형 지점은 정규화 된 좌표 (0-1)를 기반으로 합니다. 여기서 왼쪽 위 모퉁이는 (0.0, 0.0)이 고 오른쪽 아래 모퉁이는 (1.0, 1.0)입니다.   
| `threshold` | float| AI 모델의 신뢰도가이 값 보다 크거나 같으면 이벤트가 egressed 됩니다. |
| `type` | 문자열| Cognitiveservices account의 경우 **spatialanalysis-personcount** 여야 `count` 합니다.|
| `trigger` | 문자열| 이벤트를 보내기 위한 트리거의 유형입니다. 지원 되는 값은 개수가 변경 `event` `interval` 되었는지 여부에 관계 없이 이벤트가 변경 되거나 정기적으로 이벤트를 보낼 때 이벤트를 전송 하는 데 사용할 수 있습니다.
| `interval` | 문자열| 이벤트를 발생 하기 전에 사용자 수가 집계 되는 시간 (초)입니다. 작업은 계속 해 서 일정 한 속도로 장면을 분석 하 고 해당 간격에 대해 가장 일반적인 개수를 반환 합니다. 집계 간격은 및에 모두 적용 됩니다 `event` `interval` .|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>Cognitiveservices account에 대 한 줄 구성 spatialanalysis-personcrossingline

줄을 구성 하는 SPACEANALYTICS_CONFIG 매개 변수에 대 한 JSON 입력의 예입니다. 이 작업에 대 한 여러 줄을 구성할 수 있습니다.

```json
{
"lines":[{
    "name": "doorcamera" 
    "line": {
        "start": {"x": 0, "y": 0.5},
        "end": {"x": 1, "y": 0.5}
            },
    "threshold": 50.00,
    "events":[{
        "type": "linecrossing",
        "config":{
            "trigger": "event"
            }
        }]
    }]
}
```

| Name | Type| Description|
|---------|---------|---------|
| `lines` | list| 줄 목록입니다.|
| `name` | 문자열| 이 줄에 대 한 친숙 한 이름입니다.|
| `line` | list| 줄의 정의입니다. "항목"과 "종료"를 이해할 수 있는 방향 줄입니다.|
| `start` | 값 쌍| 선의 시작점에 대 한 x, y 좌표입니다. Float 값은 왼쪽 위 모퉁이를 기준으로 하는 꼭 짓 점의 위치를 나타냅니다. 절대 x, y 값을 계산 하려면 이러한 값을 프레임 크기와 곱합니다. |
| `end` | 값 쌍| 선의 끝 지점에 대 한 x, y 좌표입니다. Float 값은 왼쪽 위 모퉁이를 기준으로 하는 꼭 짓 점의 위치를 나타냅니다. 절대 x, y 값을 계산 하려면 이러한 값을 프레임 크기와 곱합니다. |
| `threshold` | float| AI 모델의 신뢰도가이 값 보다 크거나 같으면 이벤트가 egressed 됩니다. |
| `type` | 문자열| Cognitiveservices account의 경우 **spatialanalysis-personcrossingline** 여야 `linecrossing` 합니다.|
|`trigger`|문자열|이벤트를 보내기 위한 트리거의 유형입니다.<br>지원 되는 값: "event": 누군가가 선을 교차할 때 발생 합니다.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>Cognitiveservices account에 대 한 영역 구성 spatialanalysis-personcrossingpolygon

영역을 구성 하는 SPACEANALYTICS_CONFIG 매개 변수에 대 한 JSON 입력의 예입니다. 이 작업에 대 한 여러 영역을 구성할 수 있습니다.

 ```json
{
"zones":[{
    "name": "queuecamera"
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 50.00,
    "events":[{
        "type": "zone_crossing",
        "config":{
            "trigger": "event"
            }
        }]
    }]
}
```

| Name | Type| Description|
|---------|---------|---------|
| `zones` | list| 영역 목록입니다. |
| `name` | 문자열| 이 영역의 이름입니다.|
| `polygon` | list| 각 값 쌍은 다각형의 꼭 짓 점에 대 한 x, y를 나타냅니다. 다각형은 사람들이 추적 하거나 계산 하는 영역을 나타냅니다. Float 값은 왼쪽 위 모퉁이를 기준으로 하는 꼭 짓 점의 위치를 나타냅니다. 절대 x, y 값을 계산 하려면 이러한 값을 프레임 크기와 곱합니다. 
| `threshold` | float| AI 모델의 신뢰도가이 값 보다 크거나 같으면 이벤트가 egressed 됩니다. |
| `type` | 문자열| **Cognitiveservices account의 spatialanalysis-personcrossingpolygon** 는 또는 여야 합니다. `enter` `exit`|
| `trigger`|문자열|이벤트를 보내기 위한 트리거의 유형입니다.<br>지원 되는 값: "event": 누군가가 영역을 입력 하거나 종료할 때 발생 합니다.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>Cognitiveservices account에 대 한 영역 구성 spatialanalysis-persondistance

Cognitiveservices account에 대 한 영역을 구성 하는 SPACEANALYTICS_CONFIG 매개 변수에 대 한 JSON 입력의 예입니다. **spatialanalysis-persondistance**. 이 작업에 대 한 여러 영역을 구성할 수 있습니다.

```json
{
"zones":[{
    "name": "lobbycamera",
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 35.00,
    "events":[{
        "type": "persondistance",
        "config":{
            "trigger": "event",
            "output_frequency":1,
            "minimum_distance_threshold":6.0,
            "maximum_distance_threshold":35.0
            }
        }]
    }]
}
```

| Name | Type| Description|
|---------|---------|---------|
| `zones` | list| 영역 목록입니다. |
| `name` | 문자열| 이 영역의 이름입니다.|
| `polygon` | list| 각 값 쌍은 다각형의 꼭 짓 점에 대 한 x, y를 나타냅니다. 다각형은 사용자가 계산 되는 영역을 나타내며 사용자 간의 거리를 측정 합니다. Float 값은 왼쪽 위 모퉁이를 기준으로 하는 꼭 짓 점의 위치를 나타냅니다. 절대 x, y 값을 계산 하려면 이러한 값을 프레임 크기와 곱합니다. 
| `threshold` | float| AI 모델의 신뢰도가이 값 보다 크거나 같으면 이벤트가 egressed 됩니다. |
| `type` | 문자열| Cognitiveservices account의 경우 **spatialanalysis-persondistance** 여야 `people_distance` 합니다.|
| `trigger` | 문자열| 이벤트를 보내기 위한 트리거의 유형입니다. 지원 되는 값은 개수가 변경 `event` `interval` 되었는지 여부에 관계 없이 이벤트가 변경 되거나 정기적으로 이벤트를 보낼 때 이벤트를 전송 하는 데 사용할 수 있습니다.
| `interval` | 문자열 | 이벤트가 발생 하기 전에 위반이 집계 되는 시간 (초)입니다. 집계 간격은 및에 모두 적용 됩니다 `event` `interval` .|
| `output_frequency` | int | 이벤트가 egressed는 속도입니다. `output_frequency`= X 인 경우 x 이벤트는 모두 egressed, 예: `output_frequency` = 2는 다른 모든 이벤트가 출력 됨을 의미 합니다. Output_frequency은 및 둘 다에 적용 됩니다 `event` `interval` .|
| `minimum_distance_threshold` | float| 사용자가 멀리 떨어져 있을 때 "TooClose" 이벤트를 트리거할 거리 (미터)입니다.|
| `maximum_distance_threshold` | float| 사용자가 멀리 떨어져 있을 때 "TooFar" 이벤트를 트리거하는 거리 (미터)입니다.|

**Cognitiveservices account spatialanalysis-persondistance** 영역을 구성 하는 DETECTOR_NODE_CONFIG 매개 변수에 대 한 JSON 입력의 예입니다.

```json
{ 
"gpu_index": 0, 
"do_calibration": true
}
```

| Name | Type| Description|
|---------|---------|---------|
| `gpu_index` | 문자열| 이 작업이 실행 될 GPU 인덱스입니다.|
| `do_calibration` | 문자열 | 보정 기능이 설정 되어 있음을 나타냅니다. `do_calibration`**spatialanalysis-persondistance** 가 제대로 작동 하려면 true 여야 합니다.|

영역 및 선 구성에 대해 알아보려면 [카메라 배치](spatial-analysis-camera-placement.md)  지침을 참조 하세요.

## <a name="spatial-analysis-operation-output"></a>공간 분석 작업 출력

각 작업의 이벤트는 JSON 형식의 Azure IoT Hub egressed 됩니다.

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>Cognitiveservices account에 대 한 JSON 형식 spatialanalysis-personcount AI Insights

이 작업에의 한 이벤트 출력에 대 한 샘플 JSON입니다.

```json
{
    "events": [
        {
            "id": "b013c2059577418caa826844223bb50b",
            "type": "personCountEvent",
            "detectionIds": [
                "bc796b0fc2534bc59f13138af3dd7027",
                "60add228e5274158897c135905b5a019"
            ],
            "properties": {
                "personCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:57.224Z",
        "width": 608,
        "height": 342,
        "frameId": "1400",
        "cameraCalibrationInfo": {
            "status": "Complete",
            "cameraHeight": 10.306597709655762,
            "focalLength": 385.3199462890625,
            "tiltupAngle": 1.0969393253326416
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "bc796b0fc2534bc59f13138af3dd7027",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.612683747944079,
                        "y": 0.25340268765276636
                    },
                    {
                        "x": 0.7185954043739721,
                        "y": 0.6425260577285499
                    }
                ]
            },
            "confidence": 0.9559211134910583,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "60add228e5274158897c135905b5a019",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.22326200886776573,
                        "y": 0.17830915618361087
                    },
                    {
                        "x": 0.34922296122500773,
                        "y": 0.6297955429344847
                    }
                ]
            },
            "confidence": 0.9389744400978088,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| 이벤트 필드 이름 | 형식| Description|
|---------|---------|---------|
| `id` | 문자열| 이벤트 ID|
| `type` | 문자열| 이벤트 유형|
| `detectionsId` | array| 이 이벤트를 트리거한 사용자 검색의 고유 식별자 크기 1의 배열입니다.|
| `properties` | collection| 값 컬렉션|
| `trackinId` | 문자열| 검색 된 사용자의 고유 식별자입니다.|
| `status` | 문자열| ' Enter ' 또는 ' Exit '|
| `side` | int| 사용자가 교차 한 다각형의 양입니다.|
| `zone` | 문자열 | 교차 된 영역을 나타내는 polygon의 "이름" 필드|
| `trigger` | 문자열| 트리거 형식은의 값에 따라 ' event ' 또는 ' interval '입니다 `trigger` SPACEANALYTICS_CONFIG|

| 검색 필드 이름 | 형식| Description|
|---------|---------|---------|
| `id` | 문자열| 검색 ID|
| `type` | 문자열| 검색 유형|
| `region` | collection| 값 컬렉션|
| `type` | 문자열| 영역 유형|
| `points` | collection| 영역 형식이 사각형 인 경우 왼쪽 위 및 오른쪽 아래 요소 |
| `confidence` | float| 알고리즘 신뢰도|

| SourceInfo 필드 이름 | 형식| Description|
|---------|---------|---------|
| `id` | 문자열| 카메라 ID|
| `timestamp` | date| JSON 페이로드를 내보낸 UTC 날짜|
| `width` | int | 비디오 프레임 너비|
| `height` | int | 비디오 프레임 높이|
| `frameId` | int | 프레임 식별자|
| `cameraCallibrationInfo` | collection | 값 컬렉션|
| `status` | 문자열 | 접지 평면에 대 한 카메라 보정의 "완료" 여부를 나타냅니다.|
| `cameraHeight` | float | 접지 위에 있는 카메라의 높이 (피트)입니다. 자동 보정에서 유추 됩니다. |
| `focalLength` | float | 카메라의 초점 길이 (픽셀)입니다. 자동 보정에서 유추 됩니다. |
| `tiltUpAngle` | float | 세로 방향의 카메라 기울기 각도입니다. 자동 보정에서 유추 됩니다.|

| SourceInfo 필드 이름 | 형식| Description|
|---------|---------|---------|
| `id` | 문자열| 카메라 ID|
| `timestamp` | date| JSON 페이로드를 내보낸 UTC 날짜|
| `width` | int | 비디오 프레임 너비|
| `height` | int | 비디오 프레임 높이|
| `frameId` | int | 프레임 식별자|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>Cognitiveservices account에 대 한 JSON 형식 spatialanalysis-personcrossingline AI Insights

이 작업의 검색 출력에 대 한 샘플 JSON입니다.

```json
{
    "events": [
        {
            "id": "3733eb36935e4d73800a9cf36185d5a2",
            "type": "personLineEvent",
            "detectionIds": [
                "90d55bfc64c54bfd98226697ad8445ca"
            ],
            "properties": {
                "trackingId": "90d55bfc64c54bfd98226697ad8445ca",
                "status": "CrossLeft"
            },
            "zone": "doorcamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:53.261Z",
        "width": 608,
        "height": 342,
        "frameId": "1340",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "90d55bfc64c54bfd98226697ad8445ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.491627341822574,
                        "y": 0.2385801348769874
                    },
                    {
                        "x": 0.588894994635331,
                        "y": 0.6395559924387793
                    }
                ]
            },
            "confidence": 0.9005028605461121,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```
| 이벤트 필드 이름 | 형식| Description|
|---------|---------|---------|
| `id` | 문자열| 이벤트 ID|
| `type` | 문자열| 이벤트 유형|
| `detectionsId` | array| 이 이벤트를 트리거한 사용자 검색의 고유 식별자 크기 1의 배열입니다.|
| `properties` | collection| 값 컬렉션|
| `trackinId` | 문자열| 검색 된 사용자의 고유 식별자입니다.|
| `status` | 문자열| 줄 교차의 방향 (' 왼쪽으로 왼쪽 ' 또는 '가는 오른쪽 ')입니다.|
| `zone` | 문자열 | 교차 된 줄의 "이름" 필드|

| 검색 필드 이름 | 형식| Description|
|---------|---------|---------|
| `id` | 문자열| 검색 ID|
| `type` | 문자열| 검색 유형|
| `region` | collection| 값 컬렉션|
| `type` | 문자열| 영역 유형|
| `points` | collection| 영역 형식이 사각형 인 경우 왼쪽 위 및 오른쪽 아래 요소 |
| `confidence` | float| 알고리즘 신뢰도|

| SourceInfo 필드 이름 | 형식| Description|
|---------|---------|---------|
| `id` | 문자열| 카메라 ID|
| `timestamp` | date| JSON 페이로드를 내보낸 UTC 날짜|
| `width` | int | 비디오 프레임 너비|
| `height` | int | 비디오 프레임 높이|
| `frameId` | int | 프레임 식별자|


> [!IMPORTANT]
> AI 모델은 사용자가 카메라를 향해 떨어져 있는지 여부와 관계 없이 사용자를 검색 합니다. AI 모델은 얼굴 감지 또는 인식을 실행 하지 않으며 생체 인식 정보를 내보내지 않습니다. 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>Cognitiveservices account에 대 한 JSON 형식 spatialanalysis-personcrossingpolygon AI Insights

이 작업의 검색 출력에 대 한 샘플 JSON입니다.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": ""
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| 이벤트 필드 이름 | 형식| Description|
|---------|---------|---------|
| `id` | 문자열| 이벤트 ID|
| `type` | 문자열| 이벤트 유형|
| `detectionsId` | array| 이 이벤트를 트리거한 사용자 검색의 고유 식별자 크기 1의 배열입니다.|
| `properties` | collection| 값 컬렉션|
| `trackinId` | 문자열| 검색 된 사용자의 고유 식별자입니다.|
| `status` | 문자열| ' Enter ' 또는 ' Exit ' 인 polygon 교차의 방향입니다.|
| `zone` | 문자열 | 교차 된 영역을 나타내는 polygon의 "이름" 필드|

| 검색 필드 이름 | 형식| Description|
|---------|---------|---------|
| `id` | 문자열| 검색 ID|
| `type` | 문자열| 검색 유형|
| `region` | collection| 값 컬렉션|
| `type` | 문자열| 영역 유형|
| `points` | collection| 영역 형식이 사각형 인 경우 왼쪽 위 및 오른쪽 아래 요소 |
| `confidence` | float| 알고리즘 신뢰도|

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>Cognitiveservices account에 대 한 JSON 형식 spatialanalysis-persondistance AI Insights

이 작업의 검색 출력에 대 한 샘플 JSON입니다.

```json
{
    "events": [
        {
            "id": "9c15619926ef417aa93c1faf00717d36",
            "type": "personDistanceEvent",
            "detectionIds": [
                "9037c65fa3b74070869ee5110fcd23ca",
                "7ad7f43fd1a64971ae1a30dbeeffc38a"
            ],
            "properties": {
                "personCount": 5,
                "averageDistance": 20.807043981552123,
                "minimumDistanceThreshold": 6.0,
                "maximumDistanceThreshold": "Infinity",
                "eventName": "TooClose",
                "distanceViolationPersonCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:17:25.309Z",
        "width": 608,
        "height": 342,
        "frameId": "1199",
        "cameraCalibrationInfo": {
            "status": "Complete",
            "cameraHeight": 12.9940824508667,
            "focalLength": 401.2800598144531,
            "tiltupAngle": 1.057669997215271
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "9037c65fa3b74070869ee5110fcd23ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.39988183975219727,
                        "y": 0.2719132942065858
                    },
                    {
                        "x": 0.5051516984638414,
                        "y": 0.6488402517218339
                    }
                ]
            },
            "confidence": 0.948630690574646,
            "centerGroundPoint": {
                "x": -1.4638760089874268,
                "y": 18.29732322692871
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "7ad7f43fd1a64971ae1a30dbeeffc38a",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.5200299714740954,
                        "y": 0.2875368218672903
                    },
                    {
                        "x": 0.6457497446160567,
                        "y": 0.6183311060855263
                    }
                ]
            },
            "confidence": 0.8235412240028381,
            "centerGroundPoint": {
                "x": 2.6310102939605713,
                "y": 18.635927200317383
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| 이벤트 필드 이름 | 형식| Description|
|---------|---------|---------|
| `id` | 문자열| 이벤트 ID|
| `type` | 문자열| 이벤트 유형|
| `detectionsId` | array| 이 이벤트를 트리거한 사용자 검색의 고유 식별자 크기 1의 배열입니다.|
| `properties` | collection| 값 컬렉션|
| `personCount` | int| 이벤트를 내보낼 때 검색 된 사용자 수|
| `averageDistance` | float| 검색 된 모든 사용자 사이의 평균 거리 (미터)|
| `minimumDistanceThreshold` | float| 사용자가 멀리 떨어져 있을 때 "TooClose" 이벤트를 트리거할 거리 (미터)입니다.|
| `maximumDistanceThreshold` | float| 거리가 떨어져 있는 경우 "TooFar" 이벤트를 트리거할 거리 (미터)입니다.|
| `eventName` | 문자열| 이벤트 이름이 `TooClose` (가) `minimumDistanceThreshold` 위반 되거나 `TooFar` 가 `maximumDistanceThreshold` 위반 되거나 `unknown` 자동 보정이 완료 되지 않은 경우|
| `distanceViolationPersonCount` | int| 또는 위반 시 검색 된 사용자 수 `minimumDistanceThreshold``maximumDistanceThreshold`|
| `zone` | 문자열 | 사용자 간 distancing 대해 모니터링 된 영역을 나타내는 polygon의 "이름" 필드|
| `trigger` | 문자열| 트리거 형식은의 값에 따라 ' event ' 또는 ' interval '입니다 `trigger` SPACEANALYTICS_CONFIG|

| 검색 필드 이름 | 형식| Description|
|---------|---------|---------|
| `id` | 문자열| 검색 ID|
| `type` | 문자열| 검색 유형|
| `region` | collection| 값 컬렉션|
| `type` | 문자열| 영역 유형|
| `points` | collection| 영역 형식이 사각형 인 경우 왼쪽 위 및 오른쪽 아래 요소 |
| `confidence` | float| 알고리즘 신뢰도|
| `centerGroundPoint` | float 값 2 개| `x`- `y` 땅에서 개인의 유추 된 위치 좌표를 포함 하는 값입니다. `x` 카메라에서 땅에 투영 된 카메라 이미지 평면에 수직인 거리입니다. `y` 카메라에서 세로 방향으로 투영 된 이미지 평면 까지의 거리입니다.|

| SourceInfo 필드 이름 | 형식| Description|
|---------|---------|---------|
| `id` | 문자열| 카메라 ID|
| `timestamp` | date| JSON 페이로드를 내보낸 UTC 날짜|
| `width` | int | 비디오 프레임 너비|
| `height` | int | 비디오 프레임 높이|
| `frameId` | int | 프레임 식별자|
| `cameraCallibrationInfo` | collection | 값 컬렉션|
| `status` | 문자열 | 접지 평면에 대 한 카메라 보정의 "완료" 여부를 나타냅니다.|
| `cameraHeight` | float | 접지 위에 있는 카메라의 높이 (피트)입니다. 자동 보정에서 유추 됩니다. |
| `focalLength` | float | 카메라의 초점 길이 (픽셀)입니다. 자동 보정에서 유추 됩니다. |
| `tiltUpAngle` | float | 세로 방향의 카메라 기울기 각도입니다. 자동 보정에서 유추 됩니다.|


## <a name="use-the-output-generated-by-the-container"></a>컨테이너에 의해 생성 된 출력 사용

공간 분석 검색 또는 이벤트를 응용 프로그램에 통합 하는 것이 좋습니다. 고려해 야 할 몇 가지 방법은 다음과 같습니다. 

* 선택한 프로그래밍 언어에 대 한 Azure Event Hub SDK를 사용 하 여 Azure IoT Hub 끝점에 연결 하 고 이벤트를 수신 합니다. 자세한 내용은 [기본 제공 끝점에서 장치-클라우드 메시지 읽기](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) 를 참조 하세요. 
* Azure IoT Hub에 대 한 **메시지 라우팅을** 설정 하 여 이벤트를 다른 끝점으로 보내거나 데이터 저장소에 이벤트를 저장 합니다. 자세한 내용은 [IoT Hub 메시지 라우팅](../../iot-hub/iot-hub-devguide-messages-d2c.md) 을 참조 하세요. 
* 이벤트를 수신 하 고 시각화를 만들 때 실시간으로 이벤트를 처리 하는 Azure Stream Analytics 작업을 설정 합니다. 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>대규모로 공간 분석 작업 배포 (여러 카메라)

Gpu의 최고 성능 및 사용률을 얻기 위해 그래프 인스턴스를 사용 하 여 여러 카메라에 공간 분석 작업을 배포할 수 있습니다. 다음은 `cognitiveservices.vision.spatialanalysis-personcount` 5 개의 카메라에서 작업을 실행 하는 샘플입니다.

```json
 "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
          "personcount": {
              "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
              "version": 1,
              "enabled": true,
              "sharedNodes": {
                  "shared_detector1": {
                      "node": "PersonCountGraph.detector",
                      "parameters": {
                          "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 5}",
                      }
                  }
              },
              "parameters": {
                  "VIDEO_DECODE_GPU_INDEX": 0,
                  "VIDEO_IS_LIVE": true
              },
              "instances": {
                  "1": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                          "VIDEO_SOURCE_ID": "camera 1",
                          "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"zone5\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]],\"threshold\":50.0, \"events\":[{\"type\":\"count\", \"output_frequency\": 1}]}]}"
                      }
                  },
                  "2": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                          "VIDEO_SOURCE_ID": "camera 2",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "3": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                          "VIDEO_SOURCE_ID": "camera 3",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "4": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                          "VIDEO_SOURCE_ID": "camera 4",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "5": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                          "VIDEO_SOURCE_ID": "camera 5",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  }
              }
          }
      }
  }
  ```
| Name | Type| Description|
|---------|---------|---------|
| `batch_size` | int | 작업에 사용 되는 카메라 수를 나타냅니다. |

## <a name="next-steps"></a>다음 단계

* [사용자를 계산 하는 웹 응용 프로그램 배포](spatial-analysis-web-app.md)
* [로깅 및 문제 해결](spatial-analysis-logging.md)
* [카메라 배치 가이드](spatial-analysis-camera-placement.md)
* [영역 및 줄 배치 가이드](spatial-analysis-zone-line-placement.md)