---
title: Face 서비스를 사용할 때 대기 시간을 완화하는 방법
titleSuffix: Azure Cognitive Services
description: Face 서비스를 사용할 때 대기 시간을 완화하는 방법을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 1/5/2021
ms.author: pafarley
ms.openlocfilehash: f4d3e61fdfc629a0d9051a066fd861d3e8013e25
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529086"
---
# <a name="how-to-mitigate-latency-when-using-the-face-service"></a>방법: Face 서비스를 사용할 때 대기 시간 완화

Face 서비스를 사용할 때 대기 시간이 발생할 수 있습니다. 대기 시간은 네트워크를 통해 통신할 때 발생하는 모든 종류의 지연을 나타냅니다. 일반적으로 대기 시간의 가능한 원인은 다음과 같습니다.
- 각 패킷이 원본에서 대상으로 이동해야 하는 물리적 거리.
- 전송 매체와 관련된 문제.
- 전송 경로를 따라 라우터 또는 스위치의 오류.
- 바이러스 백신 애플리케이션, 방화벽 및 기타 보안 메커니즘에서 패킷을 검사하는 데 필요한 시간.
- 클라이언트 또는 서버 애플리케이션의 오작동.

이 항목에서는 Azure Cognitive Services 사용과 관련된 대기 시간의 가능한 원인과 이 원인을 완화하는 방법을 살펴봅니다.

> [!NOTE]
> Azure Cognitive Services는 대기 시간에 관한 SLA(서비스 수준 약정)를 제공하지 않습니다.

## <a name="possible-causes-of-latency"></a>대기 시간의 가능한 원인

### <a name="slow-connection-between-the-cognitive-service-and-a-remote-url"></a>Cognitive Service와 원격 URL 간 느린 연결

일부 Azure Cognitive Services는 사용자가 제공하는 원격 URL에서 데이터를 가져오는 메서드를 제공합니다. 예를 들어, Face 서비스의 [DetectWithUrlAsync 메서드](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithUrlAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_String_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_)를 호출하면 서비스가 얼굴을 감지하려고 시도하는 이미지의 URL을 지정할 수 있습니다.

```csharp
var faces = await client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
```

그런 다음, Face 서비스가 원격 서버에서 이미지를 다운로드해야 합니다. Face 서비스에서 원격 서버로 연결 속도가 느리면 Detect 메서드의 응답 시간에 영향을 줍니다.

이를 완화하려면 [Azure Premium Blob Storage에 이미지를 저장](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet)하는 것이 좋습니다. 예를 들어:

``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```

### <a name="large-upload-size"></a>큰 업로드 크기

일부 Azure Cognitive Services는 업로드하는 파일에서 데이터를 가져오는 메서드를 제공합니다. 예를 들어, Face 서비스의 [DetectWithStreamAsync 메서드](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithStreamAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_IO_Stream_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_)를 호출하면 서비스가 얼굴을 감지하려고 시도하는 이미지를 업로드할 수 있습니다.

```csharp
using FileStream fs = File.OpenRead(@"C:\images\face.jpg");
System.Collections.Generic.IList<DetectedFace> faces = await client.Face.DetectWithStreamAsync(fs, detectionModel: DetectionModel.Detection02);
```

업로드할 파일이 큰 경우 다음 이유로 `DetectWithStreamAsync` 메서드의 응답 시간에 영향을 줍니다.
- 파일을 업로드하는 데 더 오래 걸립니다.
- 파일 크기에 비례하여 서비스가 파일을 처리하는 데 더 오래 걸립니다.

해결 방법:
- [Azure Premium Blob Storage에 이미지를 저장](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet)하는 것이 좋습니다. 예를 들어:
``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```
- 더 작은 파일을 업로드하는 것이 좋습니다.
    - [얼굴 감지를 위한 입력 데이터](../concepts/face-detection.md#input-data) 및 [얼굴 인식을 위한 입력 데이터](../concepts/face-recognition.md#input-data)에 관한 지침을 참조하세요.
    - 얼굴 감지를 위해 `DetectionModel.Detection01` 검색 모델을 사용하는 경우 이미지 파일 크기를 줄이면 처리 속도가 향상됩니다. `DetectionModel.Detection02` 검색 모델을 사용할 때 이미지 파일 크기를 줄이면 이미지 파일이 1920x1080보다 작은 경우에만 처리 속도가 향상됩니다.
    - 얼굴 인식을 위해 얼굴 크기를 200x200픽셀로 줄여도 인식 모델의 정확도에 영향을 주지 않습니다.
    - 또한 `DetectWithUrlAsync` 및 `DetectWithStreamAsync` 메서드의 성능은 이미지에 있는 얼굴 수에 따라 달라집니다. Face 서비스는 이미지에 대해 최대 100개 얼굴을 반환할 수 있습니다. 얼굴은 얼굴 사각형 크기를 기준으로 큰 것부터 작은 것까지 순위가 매겨집니다.
    - 여러 서비스 메서드를 호출해야 하는 경우 애플리케이션 디자인에서 허용하면 병렬로 호출하는 것이 좋습니다. 예를 들어, 얼굴 비교를 수행하기 위해 두 이미지에서 얼굴을 검색해야 하는 경우:
```csharp
var faces_1 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
var faces_2 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1NDY3OTIxMzExNzM3NjE3/john-f-kennedy---debating-richard-nixon.jpg");
Task.WaitAll (new Task<IList<DetectedFace>>[] { faces_1, faces_2 });
IEnumerable<DetectedFace> results = faces_1.Result.Concat (faces_2.Result);
```

### <a name="slow-connection-between-your-compute-resource-and-the-face-service"></a>컴퓨팅 리소스와 Face 서비스 간 느린 연결

컴퓨터가 Face 서비스에 느리게 연결되면 서비스 메서드의 응답 시간에 영향을 줍니다.

해결 방법:
- Face 구독을 만들 때 애플리케이션이 호스트되는 위치와 가장 가까운 지역을 선택해야 합니다.
- 여러 서비스 메서드를 호출해야 하는 경우 애플리케이션 디자인에서 허용하면 병렬로 호출하는 것이 좋습니다. 예제는 이전 섹션을 참조하세요.
- 더 긴 대기 시간이 사용자 환경에 영향을 주는 경우 API 호출을 다시 시도하기 전에 시간 제한 임계값(예: 최대 5초)을 선택합니다.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 Face 서비스를 사용할 때 대기 시간을 완화하는 방법을 알아보았습니다. 다음으로, 기존 PersonGroup 및 FaceList에서 각각 LargePersonGroup 및 LargeFaceList 개체로 스케일 업하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [예제: 대규모 기능 사용](how-to-use-large-scale.md)

## <a name="related-topics"></a>관련 항목

- [참조 설명서(REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [참조 설명서(.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/face-readme)
