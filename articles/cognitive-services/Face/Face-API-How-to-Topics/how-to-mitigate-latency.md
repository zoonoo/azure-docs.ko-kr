---
title: Face 서비스를 사용할 때 대기 시간을 완화 하는 방법
titleSuffix: Azure Cognitive Services
description: 얼굴 서비스를 사용할 때 대기 시간을 줄이는 방법에 대해 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 1/5/2021
ms.author: v-jawe
ms.openlocfilehash: 2c771509de5ac246bac0d8e006a5d0b884a410b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706812"
---
# <a name="how-to-mitigate-latency-when-using-the-face-service"></a>방법: Face 서비스를 사용할 때 대기 시간 완화

Face 서비스를 사용 하는 경우 대기 시간이 발생할 수 있습니다. 대기 시간은 네트워크를 통해 통신할 때 발생 하는 모든 종류의 지연을 나타냅니다. 일반적으로 대기 시간의 가능한 원인은 다음과 같습니다.
- 각 패킷이 원본에서 대상으로 이동 해야 하는 실제 거리입니다.
- 전송 매체에 문제가 있습니다.
- 전송 경로를 따라 라우터 또는 스위치에 오류가 발생 했습니다.
- 바이러스 백신 응용 프로그램, 방화벽 및 기타 보안 메커니즘에서 패킷을 검사 하는 데 필요한 시간입니다.
- 클라이언트 또는 서버 응용 프로그램의 오작동.

이 항목에서는 Azure Cognitive Services 사용과 관련 된 대기 시간의 가능한 원인과 이러한 원인을 완화 하는 방법에 대해 설명 합니다.

> [!NOTE]
> Azure Cognitive Services는 대기 시간에 대 한 Service Level Agreement(서비스 수준 약정) (SLA)를 제공 하지 않습니다.

## <a name="possible-causes-of-latency"></a>가능한 대기 시간 원인

### <a name="slow-connection-between-the-cognitive-service-and-a-remote-url"></a>인식 서비스와 원격 URL 간의 저속 연결

일부 Azure Cognitive Services는 사용자가 제공 하는 원격 URL에서 데이터를 가져오는 메서드를 제공 합니다. 예를 들어 Face 서비스의 [DetectWithUrlAsync 메서드](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithUrlAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_String_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) 를 호출 하는 경우 서비스에서 얼굴을 감지 하려고 하는 이미지의 URL을 지정할 수 있습니다.

```csharp
var faces = await client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
```

그러면 Face 서비스가 원격 서버에서 이미지를 다운로드 해야 합니다. Face 서비스에서 원격 서버로의 연결이 느리면 검색 방법의 응답 시간에 영향을 줍니다.

이를 완화 하려면 [Azure Premium Blob Storage에 이미지를 저장 하는](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet)것이 좋습니다. 예를 들면 다음과 같습니다.

``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```

### <a name="large-upload-size"></a>큰 업로드 크기

일부 Azure Cognitive Services는 업로드 하는 파일에서 데이터를 가져오는 메서드를 제공 합니다. 예를 들어 Face 서비스의 [DetectWithStreamAsync 메서드](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithStreamAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_IO_Stream_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) 를 호출 하는 경우 서비스에서 얼굴을 감지 하려고 하는 이미지를 업로드할 수 있습니다.

```csharp
using FileStream fs = File.OpenRead(@"C:\images\face.jpg");
System.Collections.Generic.IList<DetectedFace> faces = await client.Face.DetectWithStreamAsync(fs, detectionModel: DetectionModel.Detection02);
```

업로드할 파일이 크면 다음과 같은 이유로 메서드의 응답 시간에 영향을 줍니다 `DetectWithStreamAsync` .
- 파일을 업로드 하는 데 더 오래 걸립니다.
- 파일 크기에 비례하여 서비스를 처리 하는 데 더 많은 시간이 걸립니다.

해결 방법:
- [Azure Premium Blob Storage에 이미지를 저장 하는](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet)것이 좋습니다. 예를 들면 다음과 같습니다.
``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```
- 더 작은 파일을 업로드 하는 것이 좋습니다.
    - 얼굴 [검색을 위한 입력 데이터](../concepts/face-detection.md#input-data) 와 [얼굴 인식에 대 한 입력 데이터](../concepts/face-recognition.md#input-data)에 대 한 지침을 참조 하세요.
    - 얼굴 감지의 경우 검색 모델을 사용할 때 `DetectionModel.Detection01` 이미지 파일 크기를 줄이면 처리 속도가 향상 됩니다. 검색 모델을 사용 하는 경우 이미지 파일 `DetectionModel.Detection02` 크기를 줄이면 이미지 파일이 1920 x 1080 보다 작은 경우에만 처리 속도가 향상 됩니다.
    - 얼굴 인식을 위해 얼굴 크기를 200 x 200 픽셀로 줄이면 인식 모델의 정확도에 영향을 주지 않습니다.
    - `DetectWithUrlAsync`및 `DetectWithStreamAsync` 메서드의 성능은 이미지에 있는 면 수에 따라서도 달라 집니다. 얼굴 서비스는 이미지에 대해 최대 100 얼굴을 반환할 수 있습니다. 얼굴은 표면 사각형 크기를 크고 작은 것으로 순위가 매겨집니다.
    - 여러 서비스 메서드를 호출 해야 하는 경우 응용 프로그램 디자인에서 허용 하는 경우 병렬로 호출 하는 것이 좋습니다. 예를 들어 얼굴 비교를 수행 하기 위해 두 이미지에서 얼굴을 검색 해야 하는 경우:
```csharp
var faces_1 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
var faces_2 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1NDY3OTIxMzExNzM3NjE3/john-f-kennedy---debating-richard-nixon.jpg");
Task.WaitAll (new Task<IList<DetectedFace>>[] { faces_1, faces_2 });
IEnumerable<DetectedFace> results = faces_1.Result.Concat (faces_2.Result);
```

### <a name="slow-connection-between-your-compute-resource-and-the-face-service"></a>계산 리소스와 얼굴 서비스 간의 저속 연결

컴퓨터의 연결 속도가 Face 서비스와 연결 되어 있는 경우 서비스 메서드의 응답 시간에 영향을 줍니다.

해결 방법:
- 얼굴 구독을 만들 때 응용 프로그램이 호스트 되는 위치와 가장 가까운 지역을 선택 해야 합니다.
- 여러 서비스 메서드를 호출 해야 하는 경우 응용 프로그램 디자인에서 허용 하는 경우 병렬로 호출 하는 것이 좋습니다. 예제는 이전 섹션을 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 Face 서비스를 사용할 때 대기 시간을 완화 하는 방법을 배웠습니다. 다음으로, 기존 PersonGroup 및 FaceList 개체에서 각각 LargePersonGroup 및 LargeFaceList 개체로 확장 하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [예제: 대규모 기능 사용](how-to-use-large-scale.md)

## <a name="related-topics"></a>관련 항목

- [참조 설명서 (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [참조 설명서 (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)