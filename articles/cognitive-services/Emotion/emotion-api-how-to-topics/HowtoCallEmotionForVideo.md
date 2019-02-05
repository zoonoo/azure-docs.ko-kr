---
title: '예제: 비디오용 Emotion API 호출'
titlesuffix: Azure Cognitive Services
description: Cognitive Services에서 비디오용 Emotion API를 호출하는 방법을 알아봅니다.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: sample
ms.date: 02/06/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: dd2df32ed43fd540a0516b7d5c1debc6a4f49f4f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211171"
---
# <a name="example-call-emotion-api-for-video"></a>예제: 비디오용 Emotion API 호출

> [!IMPORTANT]
> Emotion API는 2019년 2월 15일부터 더 이상 사용되지 않습니다. 이제 감정 인식 기능은 [Face API](https://docs.microsoft.com/azure/cognitive-services/face/)의 일부로 일반 공급됩니다. 

이 가이드는 비디오용 Emotion API를 호출하는 방법을 보여줍니다. 샘플은 비디오용 Emotion API 클라이언트 라이브러리를 사용하여 C#에서 작성됩니다.

### <a name="Prep">준비</a>
비디오용 Emotion API를 사용하려면 사람을 포함하는 비디오, 특히 사람이 카메라를 대면하고 있는 비디오가 필요합니다.

### <a name="Step1">1단계: API 호출 권한 부여</a>
비디오용 Emotion API에 대한 모든 호출은 구독 키가 필요합니다. 이 키는 쿼리 문자열 매개 변수를 통해 전달되거나 요청 헤더에서 지정되어야 합니다. 쿼리 문자열을 통해 구독 키를 전달하려면 예를 들어 비디오용 Emotion API에 대한 아래 요청 URL을 참조합니다.

```
https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognizeInVideo&subscription-key=<Your subscription key>
```

대안으로 구독 키는 HTTP 요청 헤더에서 지정될 수도 있습니다.

```
ocp-apim-subscription-key: <Your subscription key>
```

클라이언트 라이브러리를 사용하는 경우 구독 키는 VideoServiceClient 클래스의 생성자를 통해 전달됩니다. 예: 

```
var emotionServiceClient = new emotionServiceClient("Your subscription key");
```
구독 키를 얻으려면 [구독](https://azure.microsoft.com/try/cognitive-services/)을 참조하세요.

### <a name="Step2">2단계: 서비스에 비디오 업로드 및 상태 확인</a>
비디오용 Emotion API 호출을 수행하는 가장 기초적인 방법은 직접 비디오를 업로드하는 것입니다. 비디오 파일에서 데이터 판독과 함께 애플리케이션/옥텟 스트림 콘텐츠 형식으로 "POST" 요청을 보내 수행합니다. 비디오의 최대 크기는 100MB입니다.

클라이언트 라이브러리를 사용하면 업로드를 통한 안정화는 스트리밍 개체를 전달하여 수행됩니다. 아래 예제를 참조하세요.

```
Operation videoOperation;
using (var fs = new FileStream(@"C:\Videos\Sample.mp4", FileMode.Open))
{
      videoOperation = await videoServiceClient.CreateOperationAsync(fs, OperationType.recognizeInVideo);
}
```

VideoServiceClient의 CreateOperationAsync 메서드는 비동기입니다. await 절을 사용하려면 호출 메서드도 비동기로 표시되어야 합니다.
비디오가 이미 웹에 있으며 공개 URL을 갖고 있는 경우 해당 URL을 제공하여 비디오용 Emotion API에 액세스할 수 있습니다. 이 예제에서 요청 본문은 URL이 포함된 JSON 문자열입니다.

클라이언트 라이브러리를 사용하면 URL을 통한 안정화는 CreateOperationAsync 메서드의 다른 오버로드를 사용하여 쉽게 실행될 수 있습니다.


```
var videoUrl = "http://www.example.com/sample.mp4";
Operation videoOperation = await videoServiceClient.CreateOperationAsync(videoUrl, OperationType. recognizeInVideo);

```

이 업로드 메서드는 모든 비디오용 Emotion API 호출에 대해 동일합니다.

비디오를 업로드하면 수행하려는 다음 작업은 비디오 상태를 확인하는 것입니다. 비디오 파일은 대개 다른 파일보다 더 다양하고 더 크기 때문에 사용자는 이 단계에서 긴 처리 시간을 예상할 수 있습니다. 시간은 파일의 크기 및 길이에 따라 다릅니다.

클라이언트 라이브러리를 사용하면 GetOperationResultAsync 메서드를 사용하여 작업 상태 및 결과를 검색할 수 있습니다.


```
var operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);

```
보통 클라이언트 측은 상태가 “성공” 또는 “실패”로 표시될 때까지 작업 상태를 정기적으로 검색해야 합니다.

```
OperationResult operationResult;
while (true)
{
      operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);
      if (operationResult.Status == OperationStatus.Succeeded || operationResult.Status == OperationStatus.Failed)
      {
           break;
      }

      Task.Delay(30000).Wait();
}

```

VideoOperationResult의 상태가 “성공”으로 표시되는 경우 VideoOperationResult를 VideoOperationInfoResult<VideoAggregateRecognitionResult>에 캐스팅하고 ProcessingResult 필드에 액세스하여 결과를 검색할 수 있습니다.

```
var emotionRecognitionJsonString = ((VideoOperationInfoResult<VideoAggregateRecognitionResult>)operationResult).ProcessingResult;
```

### <a name="Step3">3단계: 감정 인식 검색 및 이해 그리고 JSON 출력 추적</a>

출력 결과는 JSON 형식의 지정된 파일 내의 얼굴에서 메타데이터를 포함합니다.

2단계에서 설명된 대로 JSON 출력은 상태가 “성공”으로 표시되는 경우 OperationResult의 ProcessingResult 필드에서 사용할 수 있습니다.

얼굴 검색 및 추적 JSON에는 다음 특성이 포함됩니다.

특성 | 설명
-------------|-------------
버전 | 비디오용 Emotion API JSON 버전을 참조합니다.
시간 간격 | 동영상의 초당 "틱"
Offset  |타임스탬프의 시간 오프셋입니다. 비디오용 Emotion API의 버전 1.0에서는 항상 0이 됩니다. 향후 지원하는 시나리오에서는 이 값이 변경될 수 있습니다.
프레임 속도 | 동영상의 초당 프레임 수입니다.
조각   | 메타데이터는 조각이라고 하는 다른 더 작은 부분으로 분할됩니다. 각 조각에는 시작, 기간, 간격 번호 및 이벤트가 포함됩니다.
시작   | “틱” 단위의 첫 이벤트 시작 시간입니다.
기간 |  "틱" 단위의 조각 길이입니다.
간격 |  "틱" 단위의 조각 내 각 이벤트 길이입니다.
이벤트  | 이벤트 배열입니다. 외부 배열은 하나의 시간 간격을 나타냅니다. 내부 배열은 해당 특정 시점에 발생한 0개 이상의 이벤트로 구성됩니다.
windowFaceDistribution |    퍼센트는 이벤트 동안 특별한 감정을 가질 가능성입니다.
windowMeanScores |  이미지의 얼굴에 담긴 각 감정에 대한 평균 점수입니다.

JSON을 이 방식으로 포맷하는 이유는 메타데이터를 신속하게 검색하고 결과의 대규모 스트리밍을 관리하는 것이 중요한 미래 시나리오에 대해 API를 설정하기 위함입니다. 이 포맷 방식은 조각화(메타데이터를 필요한 것만 다운로드할 수 있는 시간 기반 부분으로 나눌 수 있음) 및 분할(이벤트가 너무 큰 경우 나눌 수 있음) 기술 모두를 사용합니다. 몇 가지 간단한 계산으로 데이터를 변환할 수 있습니다. 예를 들어 이벤트가 6300(틱)에서 시작하고 날짜 표시줄이 2997(틱/초), 프레임 속도가 29.97(프레임/초)인 경우 다음과 같습니다.

*   시작/날짜 표시줄 = 2.1초
*   초 x (프레임 속도/날짜 표시줄) = 63프레임

다음은 얼굴 검색 및 추적을 위해 JSON을 프레임 형식별로 추출하는 간단한 샘플 예제입니다.

```
var emotionRecognitionTrackingResultJsonString = operationResult.ProcessingResult;
var emotionRecognitionTracking = JsonConvert.DeserializeObject<EmotionRecognitionResult>(emotionRecognitionTrackingResultJsonString, settings);
```
감정은 시간이 지남에 따라 부드러워지므로 원본 비디오에 결과를 오버레이하기 위한 시각화를 빌드하는 경우 제공된 타임스탬프에서 250밀리초를 뺍니다.

### <a name="Summary">요약</a>
이 가이드에서는 비디오용 Emotion API의 기능, 즉 비디오를 업로드하고 상태를 확인하고 감성 인식 메타데이터를 검색할 수 있는 방법에 대해 알아봤습니다.

API 세부 정보에 대한 자세한 내용은 API 참조 가이드 “[비디오용 Emotion API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f8d40e1984551ec0a0984e)”를 참조합니다.
