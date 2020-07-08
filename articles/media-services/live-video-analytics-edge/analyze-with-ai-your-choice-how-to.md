---
title: 선택한 AI를 사용 하 여 라이브 비디오 분석-Azure
description: 이 문서에서는 사용자가 선택한 컴퓨터 비전 모델을 사용 하 여 라이브 비디오를 분석 하기 위해 IoT Edge 라이브 비디오 분석과 통합 될 수 있는 IoT Edge 모듈을 빌드하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 0ac2af280eefd5ce293a8be422551d5ee6f6d3f3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260646"
---
# <a name="analyze-live-video-with-ai-of-your-choice"></a>선택한 AI를 사용하여 라이브 비디오 분석

이 문서에서는 사용자가 선택한 컴퓨터 비전 모델을 사용 하 여 라이브 비디오를 분석 하기 위해 IoT Edge 라이브 비디오 분석과 통합 될 수 있는 IoT Edge 모듈을 빌드하는 방법을 알아봅니다. 

## <a name="pre-reading"></a>미리 읽기

[미디어 그래프 개념](media-graph-concept.md)

## <a name="media-graph-extension"></a>미디어 그래프 확장

IoT Edge의 Live Video Analytics는 그래프 확장을 통해 미디어 그래프 처리 기능을 사용자 고유의 미디어 분석 구성 요소로 확장할 수 있는 확장성 모델을 정의 합니다. 분석 구성 요소는 기존의 이미지 처리 기술 또는 컴퓨터 비전 AI 모델을 사용할 수 있습니다. 그래프 확장은 미디어 그래프에 [HTTP 확장 프로세서](media-graph-concept.md#http-extension-processor) 노드를 포함 하 여 사용할 수 있습니다. HTTP 확장 프로세서는 비디오 프레임을 사용자가 지정 하는 HTTP 끝점으로 릴레이할 수 있으며 해당를 통해 구성 요소에 대 한 인터페이스 역할을 합니다. 로컬 또는 원격 끝점에 대 한 연결을 설정할 수 있으며, 필요한 경우 인증 및 TLS 암호화를 통해 보안을 설정할 수 있습니다. 또한 프로세서를 사용 하면 비디오 프레임이 릴레이 되기 전에 선택적으로 크기를 조정 하 고 비디오 프레임을 인코딩할 수 있습니다.

사용자 고유의 docker 컨테이너에서 ONNX, TensorFlow, PyTorch 등의 사용 가능한 모든 유추 런타임에 사용자가 선택한 유추 모델을 실행 하도록 선택할 수 있습니다. 원하는 언어와 라이브러리를 사용 하 여 사용자 고유의 컨테이너에 있는 HTTP 서버를 통해 이미지 추론 기능을 노출할 수도 있습니다. 최상의 성능을 위해 추론 컨테이너를 Live Video Analytics edge 모듈과 함께 배포 해야 하며, 그런 다음 그래프 토폴로지의 HTTP 확장 프로세서를 통해 호출 됩니다.
또한 필요에 따라 사용자 지정 모듈에 대 한 호출의 빈도는 [동작 탐지기 프로세서](media-graph-concept.md#motion-detection-processor) 및 [프레임 전송률 필터 프로세서](media-graph-concept.md#frame-rate-filter-processor) 업스트림을 HTTP 확장 프로세서에 추가 하 여 제한 될 수 있습니다.

아래 다이어그램은 상위 수준의 데이터 흐름을 보여 줍니다.

![에지 디바이스](./media/analyze-live-video-with-ai-your-choice-how-to/edge-device.png)

이렇게 하면 원하는 AI 모델을 사용 하 여 고유한 비즈니스 요구 사항을 충족 하는 비디오를 분석할 수 있습니다. AI 모델은 오픈 소스 커뮤니티 또는 자신의 데이터 과학자 또는 특수 AI 공급자에서 제공 될 수 있습니다.

## <a name="media-graph-http-extension-contract-definitions"></a>미디어 그래프 HTTP 확장 계약 정의

이 섹션에서는 데이터 흐름을 정의 하는 HTTP 계약을 정의 합니다.

### <a name="http-extensibility-protocol"></a>HTTP 확장성 프로토콜  

HTTP 계약은 다음과 같이 정의 됩니다.

* 모듈은 HTTP 서버 역할을 합니다.
* IoT Edge 모듈의 Live Video Analytics는 HTTP 클라이언트 역할을 합니다.

### <a name="request"></a>요청

라이브 비디오 분석 모듈에서 모듈로의 요청은 다음과 같습니다.

|||
|---|---|
|POST| `https://hostname/optional-path?optional-query`|
|동의함|application/json,*/*|
|권한 부여| 기본, 다이제스트, 전달자 (사용자 지정 헤더 지원)|
|콘텐츠 형식|image/jpeg<br/>image/png<br/>image/bmp<br/>이미지/x 원시|
|Content-Length|본문 길이 (바이트)|
|User-Agent|Azure Media Services|
|본문|지원 되는 콘텐츠 형식 중 하나에서 이진 인코딩된 이미지 바이트입니다.|

#### <a name="example"></a>예제

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

### <a name="response"></a>응답

모듈에서 라이브 비디오 분석 모듈로의 응답은 다음과 같아야 합니다.

|||
|---|---|
|상태 코드|200 OK-유추 결과 발견<br/>204 콘텐츠가 없습니다. AI에서 콘텐츠를 찾을 수 없음<br/>400 잘못 된 요청-필요 하지 않음<br/>500 내부 서버 오류-필요 하지 않음<br/>503 서버 사용 중-헤더가 미리 설정 되지 않은 경우에는 "다시 시도" 헤더 또는 기본 시간 간격에 따라 AMS가 백오프 됩니다.|
|콘텐츠 형식|application/json|
|Content-Length|    본문 길이 (바이트)|
|본문|단일 "추론" 속성을 포함 하는 JSON 개체입니다.|

#### <a name="example"></a>예제

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

아래에 정의 된 미리 설정 된 스키마에 따라 유효한 JSON 문서를 사용 하 여 응답을 반환 하는 것이 좋습니다. 이렇게 하면 다른 구성 요소와의 상호 운용성이 향상 되 고, 라이브 비디오 분석 모듈에 추가 되는 향후 기능이 제공 됩니다.

모듈에서 콘텐츠 형식이 "application/json"이 아닌 응답을 반환 하는 경우 Live Video Analytics는 메시지를 기본 64 콘텐츠로 인코딩하고 불투명 한 JSON 페이로드로 serialize 합니다.

모듈에서 콘텐츠 형식이 "application/json" 인 응답을 반환 하지만 JSON 스키마가 [아래에 설명 된 유추 메타 데이터 스키마](#inference-metadata-schema)를 따르지 않는 경우 메시지 페이로드는 파이프라인을 통해 전달 되지만 상호 운용성이 감소 됩니다.

> [!NOTE]
> 모듈이 결과를 생성 하지 않는 경우 빈 응답 본문이 포함 된 HTTP 204 상태 코드 (콘텐츠 없음)를 반환 해야 합니다. Live Video Analytics는이를 빈 결과로 인식 하 고 파이프라인 전체에서 이벤트를 전달 하지 않습니다.

### <a name="inference-metadata-schema"></a>유추 메타 데이터 스키마

각 유추 개체는이 상위 집합 스키마를 따릅니다.

```
{
  "type": "[classification|motion|entity|text|other]",
  "subtype": "",              // Free form subtype id

  // Object has *at most one of* the following
  "classification":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ]
  },

  "motion":
  {
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
    // "regionId": ""
  },

  "entity":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ],
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
  },

  "text":
  {
    "value": "",              // Text value
    "language": "",           // Optional BCP47 Language Code (https://tools.ietf.org/html/bcp47)
    "startTimestamp": 0,      // Optional start timestamp
    "endTimestamp": 0         // Optional end timestamp
  },

  // Plus every object can have zero or more extensions
  "extensions":
  {
    "name1": "value1",
    "name2": "value2"
    // ...
  }
}
```
 
### <a name="data-contracts---class-hierarchy"></a>데이터 계약-클래스 계층 구조

![데이터 계약-클래스 계층 구조](./media/analyze-live-video-with-ai-your-choice-how-to/data-contracts.png)

### <a name="examples"></a>예  

아래 예제에는 지원 되는 모든 유추 형식이 포함 된 단일 이벤트가 포함 되어 있습니다.

```
{
  "inferences": [
    // Light detection
    {
      "type": "classification",
      "subtype": "lightDetection",
      "classification": {
        "tag": { "value": "daylight", "confidence": 0.86 },
        "attributes": [
            { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 }
        ]
      }
    },

    // Motion detection
    {
      "type": "motion",
      "subtype": "motionDetection",
      "motion":
      {
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Object detection
    {
      "type": "entity",
      "subtype": "objectDetection",    
      "entity":
      {
        "tag": { "value": "dog", "confidence": 0.97 },
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Vehicle identification
    {
      "type": "entity",
      "subtype": "vehicleIdentification",    
      "entity":
      {
        "tag": { "value": "007-SPY", "confidence": 0.82 },
        "attributes":[  
          { "name": "color", "value": "black", "confidence": 0.90 },
          { "name": "body", "value": "coupe", "confidence": 0.87 },
          { "name": "make", "value": "Aston Martin", "confidence": 0.35 },
          { "name": "model", "value": "DBS V12", "confidence": 0.33 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // People identification
    {
      "type": "entity",
      "subtype": "peopleIdentification",    
      "entity":
      {
        "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 },
        "attributes":[  
          { "name": "age", "value": "73", "confidence": 0.87 },
          { "name": "glasses", "value": "yes", "confidence": 0.94 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      },
    }
  ]
}
```
 
## <a name="sample-http-extension-modules"></a>샘플 HTTP 확장 모듈

몇 가지 샘플 HTTP 확장 모듈은 [라이브 비디오 분석 GitHub](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis)리포지토리에서 찾을 수 있습니다. 이러한 [비디오 분석 샘플](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) 중 하나에서는 [Yolov3](https://pjreddie.com/darknet/yolo/) [onnx](http://onnx.ai/) 모델을 사용 하 여 개체 검색을 위한 IoT Edge 모듈을 빌드하는 방법을 보여 줍니다. 동일한 방법을 사용 하 여 사용자가 선택한 AI 모델을 사용 하 여 사용자 고유의 모듈을 빌드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[문제 해결](troubleshoot-how-to.md)
