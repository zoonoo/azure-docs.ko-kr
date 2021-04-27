---
title: Azure Communication Services 통화 녹화 개요
titleSuffix: An Azure Communication Services concept document
description: 통화 녹화 기능 및 API를 간략히 설명합니다.
author: joseys
manager: anvalent
services: azure-communication-services
ms.author: joseys
ms.date: 04/13/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: df9638588b4d677a7ceb80bafbe7157bb5c2df42
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730383"
---
# <a name="calling-recording-overview"></a>통화 녹화 개요

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

> [!NOTE]
> 대부분의 국가 및 주에는 사용자가 통신 녹화에 동의해야 하는 PSTN, 음성, 비디오 통화 녹화에 적용되는 법률 및 규정이 있습니다. 법률을 준수하면서 통화 녹화 기능을 사용하는 것은 사용자의 책임입니다. 각 참가자에게 적용되는 법률을 준수하면서 녹화되는 통신의 당사자로부터 동의를 받아야 합니다.

> [!NOTE]
> 개인 데이터의 유지 관리에 대한 규정에 따라 사용자 데이터를 내보낼 수 있어야 합니다. 이러한 요구 사항을 지원하기 위해 녹화 메타데이터 파일에는 `participants` 배열의 각 통화 참가자에 대한 participantId가 포함됩니다. 내부 사용자 ID가 있는 `participants` 배열의 MRI를 상호 참조하여 통화에서 참가자를 식별할 수 있습니다. 녹화 메타데이터 파일의 예는 아래에 참조용으로 제공됩니다.

통화 녹화는 녹화를 시작, 중지, 일시 중지, 다시 시작하는 API 세트를 제공합니다. 이러한 API는 서버 쪽 비즈니스 논리에서 또는 사용자 동작에 의해 트리거되는 이벤트를 통해 액세스할 수 있습니다. 녹화된 미디어 출력은 `MP4 Audio+Video` 형식이며, Teams에서 미디어를 녹화하는 데 사용하는 형식과 동일합니다. 미디어 및 메타데이터와 관련된 알림은 Event Grid를 통해 내보내집니다. 녹화는 선택한 장기 스토리지 솔루션을 검색하고 이동하기 위해 기본 제공 임시 스토리지에 48시간 동안 저장됩니다. 

## <a name="run-time-control-apis"></a>런타임 컨트롤 API
런타임 컨트롤 API를 사용하여, 그룹 통화를 만들어 대화를 녹화하는 애플리케이션과 같은 내부 비즈니스 논리 트리거를 통해 또는 서버 애플리케이션에 녹화를 시작하도록 지시하는 사용자가 트리거하는 작업에서 녹화를 관리할 수 있습니다. 어떤 시나리오에서든 `<conversation-id>`는 특정 회의 또는 통화를 녹화하는 데 필요합니다. 

#### <a name="getting-conversation-id-from-a-server-initiated-call"></a>서버에서 시작한 통화에서 대화 ID 가져오기

`ConversationId`는 `Microsoft.Communication.CallLegStateChanged` 이벤트를 통해 반환됩니다. 이 이벤트 알림은 통화가 설정된 후에 내보내집니다. 이는 `data.ConversationId` 필드에서 찾을 수 있습니다. 이 값은 `{conversationId}` 런타임 컨트롤 API에서 매개 변수로 직접 사용할 수 있습니다.
```
      {
        "id": null,
        "topic": null,
        "subject": "callLeg/<callLegId>/callState",
        "data": {
---->       "ConversationId": "<conversation-id>",    <----
            "CallLegId": "<callLegId>",
            "CallState": "Established"
        },
        "eventType": "Microsoft.Communication.CallLegStateChanged",
        "eventTime": "2021-04-14T16:32:34.1115003Z",
        "metadataVersion": null,
        "dataVersion": null
    }
```
                                                            
#### <a name="getting-the-conversation-id-from-a-user-triggered-event-on-the-client"></a>클라이언트의 사용자가 트리거한 이벤트에서 대화 ID 가져오기

JavaScript `@azure/communication-calling` 라이브러리에서 통화를 설정한 후 `let result = call.info.getConversationUrl()`을 호출하여 `conversationUrl`을 가져온 다음, **Base64Url이 `conversationUrl`을 인코딩하여 런타임 컨트롤 API를 사용하기 위해 `{conversationId}`를 가져옵니다**. 서버 또는 서버 쪽에 이벤트를 보내기 전에 클라이언트에서 인코딩을 수행할 수 있습니다.

`conversationUrl`은 Base64 인코딩(즉, btoa)과 혼동하지 않도록 *반드시* Base64Url로 인코딩해야 합니다.                                                            

### <a name="start-recording"></a>녹화 시작

#### <a name="request"></a>요청

**HTTP**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```
POST /conversations/{conversationId}/Recordings
Content-Type: application/json

{
  "operationContext": "string", // developer provided string for correlation context on each operation
  "recordingStateCallbackUri": "string"
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// start call recording
StartRecordingResponse startRecordingResponse = await conversationClient.StartRecordingAsync(
    conversationId: "<conversation-id>"
    operationContext: "<operation-context>", /// developer provided string for correlation context on each operation
    recordingStateCallbackUri: "<recording-state-callback-uri>").ConfigureAwait(false);

string recordingId = startRecordingResponse.RecordingId;
```

#### <a name="response"></a>응답

**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "recordingId": "string"
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 404 Not found
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="get-call-recording-state"></a>통화 녹화 상태 가져오기

#### <a name="request"></a>요청

**HTTP**
<!-- {
  "blockType": "request",
  "name": "get-recording-state"
}-->
```http
GET /conversations/{conversationId}/recordings/{recordingId}
Content-Type: application/json

{
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// get recording state
GetCallRecordingStateResponse recordingState = await conversationClient.GetRecordingStateAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>).ConfigureAwait(false);
```
#### <a name="response"></a>응답

**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "recordingState": "active"
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="stop-recording"></a>레코딩 중지
#### <a name="request"></a>요청
**HTTP**
<!-- {
  "blockType": "request",
  "name": "stop-recording"
}-->
```
DELETE /conversations/{conversationId}/recordings/{recordingId}
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// stop recording
StopRecordingResponse response = conversationClient.StopRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>응답
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="pause-recording"></a>기록 일시 중지
통화 녹화를 일시 중지하고 다시 시작하면 통화 또는 회의의 일부 녹화를 건너뛰고 단일 파일에 녹화를 다시 시작할 수 있습니다. 
#### <a name="request"></a>요청
**HTTP**
<!-- {
  "blockType": "request",
  "name": "pause-recording"
}-->
```
POST /conversations/{conversationId}/recordings/{recordingId}/Pause
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// pause recording
PauseRecordingResponse response = conversationClient.PauseRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>응답
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="resume-recording"></a>녹화 다시 시작
#### <a name="request"></a>요청
**HTTP**
<!-- {
  "blockType": "request",
  "name": "resume-recording"
}-->
```
POST /conversations/{conversationId}/recordings/{recordingId}/Resume
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// resume recording
ResumeRecordingResponse response = conversationClient.ResumeRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>응답
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

## <a name="media-output-types"></a>미디어 출력 형식
통화 녹화는 현재 혼합 오디오+비디오 MP4 출력 형식을 지원합니다. 출력 미디어는 Microsoft Teams 녹화를 통해 생성된 회의 녹화와 일치합니다.

| 채널 형식 | 콘텐츠 형식 | 동영상 | 오디오 |
| :----------- | :------------- | :---- | :--------------------------- |
| audioVideo | mp4 | 기본 타일 정렬의 모든 참가자에 대한 1920x1080 8 FPS 비디오 | 모든 참가자에 대한 16kHz mp4a 혼합 오디오 |

## <a name="event-grid-notifications"></a>Event Grid 알림
Event Grid 알림 `Microsoft.Communication.RecordingFileStatusUpdated`는 녹화를 검색할 수 있게 되었을 때 게시되며, 일반적으로 녹화 프로세스가 완료된 후(예: 모임이 종료되고 녹화가 중지됨) 1~2분 정도 걸립니다. 녹화 이벤트 알림에는 녹화된 미디어와 녹화 메타데이터 파일을 모두 검색하는 데 사용할 수 있는 문서 ID가 포함됩니다.

- <Azure_Communication_Service_Endpoint>/recording/download/{documentId}
- <Azure_Communication_Service_Endpoint>/recording/download/{documentId}/metadata

Event Grid 알림을 처리하고 녹화 및 메타데이터 파일을 다운로드하는 샘플 코드는 [여기](../../quickstarts/voice-video-calling/download-recording-file-sample.md)에서 찾을 수 있습니다. 

### <a name="notification-schema"></a>알림 스키마
```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from storage
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft", etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}
```
## <a name="file-download"></a>파일 다운로드

> Azure Communication Services는 녹화를 위한 단기 미디어 스토리지를 제공합니다. **48시간 이내에 보존하려는 녹화된 콘텐츠를 내보냅니다.** 48시간 후에는 더 이상 녹화를 사용할 수 없습니다.

### <a name="download-recording"></a>녹음/녹화 다운로드
#### <a name="request"></a>요청
**HTTP**
<!-- {
  "blockType": "request",
  "name": "download-recording"
}-->
```http
GET /recording/download/{documentId}
Content-Type: application/json

{
}
```
#### <a name="response"></a>응답
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```
HTTP/1.1 200 Success
Content-Type: video/mp4

{
string // Recording file bytes
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
### <a name="download-recording-metadata"></a>녹화 메타데이터 다운로드
#### <a name="request"></a>요청
**HTTP**
<!-- {
  "blockType": "request",
  "name": "download-recording-metadata"
}-->
```http
GET /recording/download/{documentId}/metadata
Content-Type: application/json

{
}
```
#### <a name="response"></a>응답
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "resourceId": "string",
  "callId": "string",
  "chunkDocumentId": "string",
  "chunkIndex": 0,
  "chunkStartTime": "string",
  "chunkDuration": 0,
  "pauseResumeIntervals": [
    {
      "startTime": "string",
      "duration": 0
    }
  ],
  "recordingInfo": {
    "contentType": "string",
    "channelType": "string",
    "format": "string",
    "audioConfiguration": {
      "sampleRate": "string",
      "bitRate": 0,
      "channels": 0
    },
    "videoConfiguration": {
      "longerSideLength": 0,
      "shorterSideLength": 0,
      "framerate": 0,
      "bitRate": 0
    }
  },
  "participants": [
    {
      "participantId": "string"
    }
  ]
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
