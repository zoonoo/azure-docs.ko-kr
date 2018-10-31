---
title: Media Services 이벤트에 대한 Azure Event Grid 스키마
description: Azure Event Grid에서 Media Services 이벤트에 대해 제공되는 속성을 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 44e195055c74babd903cf4fb830167ab92951d4a
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376791"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Media Services 이벤트에 대한 Azure Event Grid 스키마

이 문서에서는 Media Services 이벤트에 대한 스키마와 속성을 제공합니다.

샘플 스크립트 및 자습서 목록은 [Media Services 이벤트 원본](../../event-grid/event-sources.md#azure-subscriptions)을 참조하세요.

## <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

### <a name="job-related-event-types"></a>작업 관련 이벤트 유형

Media Services는 아래에 설명된 **작업** 관련 이벤트 유형을 내보냅니다. **작업** 관련 이벤트는 “작업 상태 변경 모니터링” 및 “작업 출력 상태 변경 모니터링”의 두 가지 범주로 구분됩니다. 

JobStateChange 이벤트를 구독하여 모든 이벤트에 등록할 수 있습니다. 또는 특정 이벤트(예: JobErrored, JobFinished 및 JobCanceled와 같은 최종 상태)만 구독할 수 있습니다. 

#### <a name="monitoring-job-state-changes"></a>작업 상태 변경 모니터링

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| 모든 작업 상태 변경에 대한 이벤트를 가져옵니다. |
| Microsoft.Media.JobScheduled| 작업이 예약됨 상태로 전환되는 이벤트를 가져옵니다. |
| Microsoft.Media.JobProcessing| 작업이 처리 중 상태로 전환되는 이벤트를 가져옵니다. |
| Microsoft.Media.JobCanceling| 작업이 취소 중 상태로 전환되는 이벤트를 가져옵니다. |
| Microsoft.Media.JobFinished| 작업이 완료됨 상태로 전환되는 이벤트를 가져옵니다. 작업 출력을 포함하는 최종 상태입니다.|
| Microsoft.Media.JobCanceled| 작업이 취소됨 상태로 전환되는 이벤트를 가져옵니다. 작업 출력을 포함하는 최종 상태입니다.|
| Microsoft.Media.JobErrored| 작업이 오류 상태로 전환되는 이벤트를 가져옵니다. 작업 출력을 포함하는 최종 상태입니다.|

#### <a name="monitoring-job-output-state-changes"></a>작업 출력 상태 변경 모니터링

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| Microsoft.Media.JobOutputStateChange| 모든 작업 출력 상태 변경에 대한 이벤트를 가져옵니다. |
| Microsoft.Media.JobOutputScheduled| 작업 출력이 예약됨 상태로 전환되는 이벤트를 가져옵니다. |
| Microsoft.Media.JobOutputProcessing| 작업 출력이 처리 중 상태로 전환되는 이벤트를 가져옵니다. |
| Microsoft.Media.JobOutputCanceling| 작업 출력이 취소 중 상태로 전환되는 이벤트를 가져옵니다.|
| Microsoft.Media.JobOutputFinished| 작업 출력이 완료됨 상태로 전환되는 이벤트를 가져옵니다.|
| Microsoft.Media.JobOutputCanceled| 작업 출력이 취소됨 상태로 전환되는 이벤트를 가져옵니다.|
| Microsoft.Media.JobOutputErrored| 작업 출력이 오류 상태로 전환되는 이벤트를 가져옵니다.|

### <a name="live-event-types"></a>라이브 이벤트 유형

Media Services는 아래에 설명된 **라이브** 이벤트 유형도 내보냅니다. **라이브** 이벤트에는 스트림 수준 이벤트와 트랙 수준 이벤트의 두 가지 범주가 있습니다. 

#### <a name="stream-level-events"></a>스트림 수준 이벤트

스트림 수준 이벤트는 스트림 또는 연결마다 발생합니다. 각 이벤트에는 연결 또는 스트림을 식별하는 `StreamId` 매개 변수가 있습니다. 각 스트림 또는 연결에는 서로 다른 유형의 트랙이 하나 이상 있습니다. 예를 들어, 인코더의 연결 하나에는 하나의 오디오 트랙과 4개의 비디오 트랙이 있을 수 있습니다. 스트림 이벤트 유형은 다음과 같습니다.

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| Microsoft.Media.LiveEventConnectionRejected | 인코더의 연결 시도가 거부됩니다. |
| Microsoft.Media.LiveEventEncoderConnected | 인코더에서 라이브 이벤트와의 연결을 설정합니다. |
| Microsoft.Media.LiveEventEncoderDisconnected | 인코더에서 연결을 끊습니다. |

#### <a name="track-level-events"></a>트랙 수준 이벤트

트랙 수준 이벤트는 트랙마다 발생합니다. 추적 이벤트 유형은 다음과 같습니다.

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | 미디어 서버가 너무 늦거나 타임스탬프가 겹치기 때문에 데이터 청크가 삭제됩니다(새 데이터 청크의 타임스탬프가 이전 데이터 청크의 종료 시간보다 이전임). |
| Microsoft.Media.LiveEventIncomingStreamReceived | 미디어 서버에서 스트림 또는 연결의 각 트랙에 대한 첫 번째 데이터 청크를 받습니다. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | 미디어 서버에서 오디오 및 비디오 스트림이 동기화되지 않았음을 감지합니다. 사용자 환경이 영향을 받지 않으므로 이 이벤트는 경고로 사용합니다. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | 미디어 서버에서 외부 인코더로부터 들어오는 두 비디오 스트림 중 동기화되지 않은 것을 감지합니다. 사용자 환경이 영향을 받지 않으므로 이 이벤트는 경고로 사용합니다. |
| Microsoft.Media.LiveEventIngestHeartbeat | 라이브 이벤트가 실행될 때 각 트랙에 대해 20초마다 게시됩니다. 수집 상태 요약을 제공합니다. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | 미디어 서버에서 들어오는 트랙의 불연속성을 감지합니다. |

## <a name="event-schemas-and-properties"></a>이벤트 스키마 및 속성

### <a name="jobstatechange"></a>JobStateChange

**JobStateChange** 이벤트의 스키마를 보여 주는 예제는 다음과 같습니다. 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

데이터 개체의 속성은 다음과 같습니다.

| 자산 | type | 설명 |
| -------- | ---- | ----------- |
| previousState | string | 이벤트 전의 작업 상태입니다. |
| state | string | 이 이벤트에서 알리는 직업의 새로운 상태입니다. 예: "큐에 대기: 작업에서 리소스를 기다리고 있습니다." 또는 "예약됨: 작업을 시작할 준비가 되었습니다."|

작업 상태는 다음 중 하나일 수 있습니다. *큐에 대기됨*, *예약됨*, *처리 중*, *완료됨*, *오류*, *취소됨*, *취소 중*

### <a name="jobscheduled"></a>JobScheduled
### <a name="jobprocessing"></a>JobProcessing
### <a name="jobcanceling"></a>JobCanceling

각 비최종 작업 상태 변경(JobScheduled, JobProcessing, JobCanceling 등)에 대한 예제 스키마는 다음과 유사합니다.

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobProcessing",
  "eventTime": "2018-10-12T16:12:18.0839935",
  "id": "a0a6efc8-f647-4fc2-be73-861fa25ba2db",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing",
    "correlationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="jobfinished"></a>JobFinished
### <a name="jobcanceled"></a>JobCanceled
### <a name="joberrored"></a>JobErrored

각 최종 작업 상태 변경(JobFinished, JobCanceled, JobErrored 등)에 대한 예제 스키마는 다음과 유사합니다.

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobFinished",
  "eventTime": "2018-10-12T16:25:56.4115495",
  "id": "9e07e83a-dd6e-466b-a62f-27521b216f2a",
  "data": {
    "outputs": [
      {
        "@odata.type": "#Microsoft.Media.JobOutputAsset",
        "assetName": "output-7640689F",
        "error": null,
        "label": "VideoAnalyzerPreset_0",
        "progress": 100,
        "state": "Finished"
      }
    ],
    "previousState": "Processing",
    "state": "Finished",
    "correlationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

데이터 개체의 속성은 다음과 같습니다.

| 자산 | type | 설명 |
| -------- | ---- | ----------- |
| outputs | 배열 | 작업 출력을 가져옵니다.|

### <a name="joboutputstatechange"></a>JobOutputStateChange

**JobOutputStateChange** 이벤트의 스키마를 보여 주는 예제는 다음과 같습니다.

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputStateChange",
  "eventTime": "2018-10-12T16:25:56.0242854",
  "id": "dde85f46-b459-4775-b5c7-befe8e32cf90",
  "data": {
    "previousState": "Processing",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 100,
      "state": "Finished"
    },
    "jobCorrelationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="joboutputscheduled"></a>JobOutputScheduled
### <a name="joboutputprocessing"></a>JobOutputProcessing
### <a name="joboutputfinished"></a>JobOutputFinished
### <a name="joboutputcanceling"></a>JobOutputCanceling
### <a name="joboutputcanceled"></a>JobOutputCanceled
### <a name="joboutputerrored"></a>JobOutputErrored

각 JobOutput 상태 변경에 대한 예제 스키마는 다음과 유사합니다.

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputProcessing",
  "eventTime": "2018-10-12T16:12:18.0061141",
  "id": "f1fd5338-1b6c-4e31-83c9-cd7c88d2aedb",
  "data": {
    "previousState": "Scheduled",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 0,
      "state": "Processing"
    },
    "jobCorrelationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

**LiveEventConnectionRejected** 이벤트의 스키마를 보여 주는 예제는 다음과 같습니다. 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "StreamId":"Mystream1",
      "IngestUrl": "http://abc.ingest.isml",
      "EncoderIp": "118.238.251.xxx",
      "EncoderPort": 52859,
      "ResultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0"
  }
]
```

데이터 개체의 속성은 다음과 같습니다.

| 자산 | type | 설명 |
| -------- | ---- | ----------- |
| StreamId | string | 스트림 또는 연결에 대한 식별자입니다. 인코더 또는 고객이 이 ID를 수집 URL에 추가해야 합니다. |  
| IngestUrl | string | 라이브 이벤트에서 제공하는 수집 URL입니다. |  
| EncoderIp | string | 인코더의 IP입니다. |
| EncoderPort | string | 이 스트림이 발생한 인코더의 포트입니다. |
| ResultCode | string | 연결이 거부된 이유입니다. 결과 코드는 다음 표에 나와 있습니다. |

결과 코드는 다음과 같습니다.

| 결과 코드 | 설명 |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | 잘못된 수집 URL입니다. |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | 인코더 IP가 IP 허용 목록에 구성되어 있지 않습니다. |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | 인코더에서 스트림에 대한 메타데이터를 보내지 않았습니다. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | 지정된 코덱이 지원되지 않습니다. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED | 해당 스트림에 대한 헤더를 받기 전에 조각을 받았습니다. |
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED | 지정된 품질의 수가 최대 허용 한도를 초과합니다. |
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED | 집계된 비트 전송률이 최대 허용 한도를 초과합니다. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | 비디오 또는 오디오 FLVTag에 대한 타임스탬프가 RTMP 인코더에서 유효하지 않습니다. |

### <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

**LiveEventEncoderConnected** 이벤트의 스키마를 보여 주는 예제는 다음과 같습니다. 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderConnected",
    "eventTime": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

데이터 개체의 속성은 다음과 같습니다.

| 자산 | type | 설명 |
| -------- | ---- | ----------- |
| StreamId | string | 스트림 또는 연결에 대한 식별자입니다. 인코더 또는 고객이 이 ID를 수집 URL에 제공해야 합니다. |
| IngestUrl | string | 라이브 이벤트에서 제공하는 수집 URL입니다. |
| EncoderIp | string | 인코더의 IP입니다. |
| EncoderPort | string | 이 스트림이 발생한 인코더의 포트입니다. |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

**LiveEventEncoderDisconnected** 이벤트의 스키마를 보여 주는 예제는 다음과 같습니다. 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderDisconnected",
    "eventTime": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

데이터 개체의 속성은 다음과 같습니다.

| 자산 | type | 설명 |
| -------- | ---- | ----------- |
| StreamId | string | 스트림 또는 연결에 대한 식별자입니다. 인코더 또는 고객이 이 ID를 수집 URL에 추가해야 합니다. |  
| IngestUrl | string | 라이브 이벤트에서 제공하는 수집 URL입니다. |  
| EncoderIp | string | 인코더의 IP입니다. |
| EncoderPort | string | 이 스트림이 발생한 인코더의 포트입니다. |
| ResultCode | string | 인코더 연결이 끊어진 이유입니다. 정상적인 연결 끊기이거나 오류일 수 있습니다. 결과 코드는 다음 표에 나와 있습니다. |

오류 결과 코드는 다음과 같습니다.

| 결과 코드 | 설명 |
| ----------- | ----------- |
| MPE_RTMP_SESSION_IDLE_TIMEOUT | 허용된 시간 한도 동안 유휴 상태가 된 후 RTMP 세션 시간이 초과되었습니다. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | 비디오 또는 오디오 FLVTag에 대한 타임스탬프가 RTMP 인코더에서 유효하지 않습니다. |
| MPE_CAPACITY_LIMIT_REACHED | 데이터를 너무 빨리 보내는 인코더입니다. |
| 알 수 없는 오류 코드 | 이러한 오류 코드는 메모리 오류에서 해시 맵의 중복 항목에 이르기까지 다양할 수 있습니다. |

정상적인 연결 끊기 결과 코드는 다음과 같습니다.

| 결과 코드 | 설명 |
| ----------- | ----------- |
| S_OK | 인코더의 연결이 성공적으로 끊어졌습니다. |
| MPE_CLIENT_TERMINATED_SESSION | 인코더(RTMP)의 연결이 끊어졌습니다. |
| MPE_CLIENT_DISCONNECTED | 인코더(FMP4)의 연결이 끊어졌습니다. |
| MPI_REST_API_CHANNEL_RESET | 채널 다시 설정 명령을 받았습니다. |
| MPI_REST_API_CHANNEL_STOP | 채널 중지 명령을 받았습니다. |
| MPI_REST_API_CHANNEL_STOP | 유지 관리 중인 채널입니다. |
| MPI_STREAM_HIT_EOF | 인코더에서 EOF 스트림을 보냈습니다. |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

**LiveEventIncomingDataChunkDropped** 이벤트의 스키마를 보여 주는 예제는 다음과 같습니다. 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "TrackType": "Video",
      "TrackName": "Video",
      "Bitrate": 300000,
      "Timestamp": 36656620000,
      "Timescale": 10000000,
      "ResultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0"
  }
]
```

데이터 개체의 속성은 다음과 같습니다.

| 자산 | type | 설명 |
| -------- | ---- | ----------- |
| TrackType | string | 트랙 유형입니다(예: Audio/Video). |
| TrackName | string | 트랙의 이름입니다. |
| Bitrate | 정수 | 트랙의 비트 전송률입니다. |
| 타임 스탬프 | string | 데이터 청크의 타임스탬프가 삭제되었습니다. |
| 시간 간격 | string | 타임스탬프의 시간 간격입니다. |
| ResultCode | string | 데이터 청크가 삭제된 이유입니다. **FragmentDrop_OverlapTimestamp** 또는 **FragmentDrop_NonIncreasingTimestamp**입니다. |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

**LiveEventIncomingStreamReceived** 이벤트의 스키마를 보여 주는 예제는 다음과 같습니다. 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "eventTime": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

데이터 개체의 속성은 다음과 같습니다.

| 자산 | type | 설명 |
| -------- | ---- | ----------- |
| TrackType | string | 트랙 유형입니다(예: Audio/Video). |
| TrackName | string | 트랙의 이름입니다(인코더에서 제공하거나, RTMP의 경우 서버에서 *TrackType_Bitrate* 형식으로 생성함). |
| Bitrate | 정수 | 트랙의 비트 전송률입니다. |
| IngestUrl | string | 라이브 이벤트에서 제공하는 수집 URL입니다. |
| EncoderIp | string  | 인코더의 IP입니다. |
| EncoderPort | string | 이 스트림이 발생한 인코더의 포트입니다. |
| 타임 스탬프 | string | 받은 데이터 청크의 첫 번째 타임스탬프입니다. |
| 시간 간격 | string | 타임스탬프가 표시되는 시간 간격입니다. |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

**LiveEventIncomingStreamsOutOfSync** 이벤트의 스키마를 보여 주는 예제는 다음과 같습니다. 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "eventTime": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video",
      "timescaleOfMinLastTimestamp": "10000000", 
      "timescaleOfMaxLastTimestamp": "10000000"       
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

데이터 개체의 속성은 다음과 같습니다.

| 자산 | type | 설명 |
| -------- | ---- | ----------- |
| MinLastTimestamp | string | 모든 트랙(오디오 또는 비디오) 중에서 마지막 타임스탬프의 최솟값입니다. |
| TypeOfTrackWithMinLastTimestamp | string | 마지막 타임스탬프가 최소인 트랙 유형(오디오 또는 비디오)입니다. |
| MaxLastTimestamp | string | 모든 트랙(오디오 또는 비디오) 중에서 모든 타임스탬프의 최댓값입니다. |
| TypeOfTrackWithMaxLastTimestamp | string | 마지막 타임스탬프가 최대인 트랙 유형(오디오 또는 비디오)입니다. |
| TimescaleOfMinLastTimestamp| string | “MinLastTimestamp”가 표시되는 시간 간격을 가져옵니다.|
| TimescaleOfMaxLastTimestamp| string | “MaxLastTimestamp”가 표시되는 시간 간격을 가져옵니다.|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

**LiveEventIncomingVideoStreamsOutOfSync** 이벤트의 스키마를 보여 주는 예제는 다음과 같습니다. 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "FirstTimestamp": "2162058216",
      "FirstDuration": "2000",
      "SecondTimestamp": "2162057216",
      "SecondDuration": "2000",
      "timescale": "10000000"      
    },
    "dataVersion": "1.0"
  }
]
```

데이터 개체의 속성은 다음과 같습니다.

| 자산 | type | 설명 |
| -------- | ---- | ----------- |
| FirstTimestamp | string | 비디오 유형의 트랙/품질 수준 중 하나에 대해 받은 타임스탬프입니다. |
| FirstDuration | string | 첫 번째 타임스탬프가 있는 데이터 청크의 기간입니다. |
| SecondTimestamp | string  | 비디오 유형의 다른 트랙/품질 수준 일부에 대해 받은 타임스탬프입니다. |
| SecondDuration | string | 두 번째 타임스탬프가 있는 데이터 청크의 기간입니다. |
| 시간 간격 | string | 타임스탬프 및 지속 기간의 시간 간격입니다.|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

**LiveEventIngestHeartbeat** 이벤트의 스키마를 보여 주는 예제는 다음과 같습니다. 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIngestHeartbeat",
    "eventTime": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

데이터 개체의 속성은 다음과 같습니다.

| 자산 | type | 설명 |
| -------- | ---- | ----------- |
| TrackType | string | 트랙 유형입니다(예: Audio/Video). |
| TrackName | string | 트랙의 이름입니다(인코더에서 제공하거나, RTMP의 경우 서버에서 *TrackType_Bitrate* 형식으로 생성함). |
| Bitrate | 정수 | 트랙의 비트 전송률입니다. |
| IncomingBitrate | 정수 | 인코더에서 들어오는 데이터 청크에 기반하여 계산된 비트 전송률입니다. |
| LastTimestamp | string | 마지막 20초 동안 한 트랙에 대해 받은 최신 타임스탬프입니다. |
| 시간 간격 | string | 타임스탬프가 표시되는 시간 간격입니다. |
| OverlapCount | 정수 | 마지막 20초 동안 타임스탬프가 겹쳐진 데이터 청크의 수입니다. |
| DiscontinuityCount | 정수 | 마지막 20초 동안 관찰된 불연속성의 수입니다. |
| NonIncreasingCount | 정수 | 마지막 20초 동안 받은 과거의 타임스탬프가 있는 데이터 청크의 수입니다. |
| UnexpectedBitrate | bool | 마지막 20초 동안 허용 한도를 초과하여 예상 및 실제 비트 전송률이 다릅니다. IncomingBitrate >= 2 * bitrate OR IncomingBitrate <= bitrate/2 OR IncomingBitrate = 0인 경우에만 true입니다. |
| 시스템 상태 | string | 라이브 이벤트의 상태입니다. |
| Healthy | bool | 횟수 및 플래그에 기반하여 수집이 정상인지 여부를 나타냅니다. OverlapCount = 0 && DiscontinuityCount = 0 && NonIncreasingCount = 0 && UnexpectedBitrate = false이면 Healthy가 true입니다. |

### <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

**LiveEventTrackDiscontinuityDetected** 이벤트의 스키마를 보여 주는 예제는 다음과 같습니다. 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "eventTime": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

데이터 개체의 속성은 다음과 같습니다.

| 자산 | type | 설명 |
| -------- | ---- | ----------- |
| TrackType | string | 트랙 유형입니다(예: Audio/Video). |
| TrackName | string | 트랙의 이름입니다(인코더에서 제공하거나, RTMP의 경우 서버에서 *TrackType_Bitrate* 형식으로 생성함). |
| Bitrate | 정수 | 트랙의 비트 전송률입니다. |
| PreviousTimestamp | string | 이전 조각의 타임스탬프입니다. |
| NewTimestamp | string | 현재 조각의 타임스탬프입니다. |
| DiscontinuityGap | string | 위의 두 타임스탬프 사이의 간격입니다. |
| 시간 간격 | string | 타임스탬프와 불연속성 간격이 모두 표시되는 시간 간격입니다. |

### <a name="common-event-properties"></a>일반 이벤트 속성

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 자산 | type | 설명 |
| -------- | ---- | ----------- |
| 토픽 | string | EventGrid 항목입니다. 이 속성에는 Media Services 계정에 대한 리소스 ID가 있습니다. |
| 제목 | string | Media Services 계정에 속한 Media Services 채널에 대한 리소스 경로입니다. topic과 subject를 연결하면 작업에 대한 리소스 ID가 제공됩니다. |
| eventType | string | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. 예: "Microsoft.Media.JobStateChange". |
| eventTime | string | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| id | string | 이벤트에 대한 고유 식별자입니다. |
| 데이터 | object | Media Services 이벤트 데이터입니다. |
| dataVersion | string | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | string | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

## <a name="next-steps"></a>다음 단계

[작업 상태 변경 이벤트 등록](job-state-events-cli-how-to.md)
