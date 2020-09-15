---
title: HTTP 확장 프로토콜 - Azure
description: 이 문서에서는 HTTP 확장 프로토콜을 사용하여 Live Video Analytics 모듈과 AI 또는 CV 모듈 간에 메시지를 보내는 방법에 대해 알아봅니다.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: bfc8672185d284abc8f2985b44cea92000bc73db
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89448064"
---
# <a name="http-extension-protocol"></a>HTTP 확장 프로토콜

이 문서에서는 HTTP 확장 프로토콜을 사용하여 Live Video Analytics 모듈과 AI 또는 CV 모듈 간에 메시지를 보내는 방법에 대해 알아봅니다.

HTTP 계약은 다음 두 구성 요소 간에 정의됩니다.

* HTTP 서버
* Live Video Analytics on IoT Edge 모듈은 HTTP 클라이언트 역할을 합니다.

## <a name="request"></a>요청

Live Video Analytics 모듈에서 HTTP 서버로의 요청은 다음과 같습니다.

|키|값|
|---|---|
|POST|`https://hostname/optional-path?optional-query`|
|동의함|application/json, */*|
|권한 부여|기본, 다이제스트, 전달자(사용자 지정 헤더 지원을 통해)|
|콘텐츠 형식|image/jpeg<br/>image/png<br/>image/bmp<br/>image/x-raw|
|Content-Lengt 본문 길이(바이트)|
|User-Agent|Azure Media Services|
|본문|지원되는 콘텐츠 형식 중 하나로 인코딩된 이진 파일인 이미지 바이트입니다.|

### <a name="example"></a>예

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

## <a name="response"></a>응답

모듈에서 라이브 비디오 분석 모듈로의 응답은 다음과 같아야 합니다.

|키|값|
|---|---|
|상태 코드|200 OK - 유추 결과 찾음<br/>204 콘텐츠가 없음 - AI에서 콘텐츠를 찾을 수 없음<br/>400 잘못된 요청 - 예상되지 않음<br/>500 내부 서버 오류 - 예상되지 않음<br/>503 서버 사용 중 - AMS는 "Retry-After" 헤더에 기반하거나 헤더가 미리 설정되지 않은 경우 기본 시간을 기준으로 백 오프됩니다.|
|콘텐츠 형식|application/json|
|Content-Length|본문 길이(바이트)|
|본문|단일 "추론" 속성이 있는 JSON 개체입니다.|

### <a name="example"></a>예

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 468
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 17 Apr 2020 04:44:01 GMT

{
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.9048132 },
        "box": { "l": 0.42681578, "t": 0.47660735, "w": 0.019501392, "h": 0.020954132 }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.8953932 },
        "box": { "l": 0.55083525, "t": 0.4843858, "w": 0.046550274, "h": 0.046502113 }
      }
    }    
  ]
}
```

아래에 정의된 미리 설정된 스키마에 따라 유효한 JSON 문서를 사용하여 응답을 반환하는 것이 좋습니다. 이렇게 하면 다른 구성 요소와의 상호 운용성과 Live Video Analytics 모듈에 추가되는 향후 기능이 보다 효과적으로 보장됩니다.

모듈에서 콘텐츠 형식이 "application/json"이 아닌 응답을 반환하는 경우 Live Video Analytics는 메시지를 기본 64 콘텐츠로 인코딩하고 불투명한 JSON 페이로드로 직렬화합니다.

모듈에서 콘텐츠 형식이 "application/json"인 응답을 반환하지만 JSON 스키마가 아래에 설명된 유추 메타데이터 스키마를 따르지 않는 경우 메시지 페이로드는 파이프라인을 통해 전달되지만 상호 운용성은 감소됩니다.

> [!NOTE]
> 모듈이 결과를 생성하지 않는 경우 빈 응답 본문이 포함된 HTTP 204 상태 코드(콘텐츠 없음)를 반환해야 합니다. Live Video Analytics는 이를 빈 결과로 인식하고 파이프라인 전체에 이벤트를 전달하지 않습니다.

## <a name="data-contracts---class-hierarchy"></a>데이터 계약 - 클래스 계층 구조

![클래스 계층 구조](./media/http-extension-protocol/class-hierarchy.png)

## <a name="next-steps"></a>다음 단계

[gRPC 데이터 계약](grpc-data-contract.md)
