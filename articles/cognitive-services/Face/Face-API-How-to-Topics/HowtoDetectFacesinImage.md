---
title: 이미지에서 얼굴 감지 - 얼굴
titleSuffix: Azure Cognitive Services
description: 이 가이드에서는 얼굴 감지를 사용하여 지정된 이미지에서 성별, 연령, 포즈와 같은 특성을 추출하는 방법을 보여줍니다.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 3a15cce45c527a92c99e0488661e0b67bb8e2371
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101713068"
---
# <a name="get-face-detection-data"></a>얼굴 감지 데이터 가져오기

이 가이드에서는 얼굴 감지를 사용하여 지정된 이미지에서 성별, 연령, 포즈와 같은 특성을 추출하는 방법을 보여줍니다. 이 가이드의 코드 조각은 Azure Cognitive Services Face 클라이언트 라이브러리를 사용하여 C#으로 작성되었습니다. 이 기능은 [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)를 통해 사용할 수 있습니다.

이 가이드에서는 그 방법을 보여줍니다.

- 이미지에서 얼굴의 위치와 크기를 가져옵니다.
- 이미지에서 눈동자, 코, 입 등 다양한 얼굴 랜드마크의 위치를 가져옵니다.
- 감지된 얼굴의 성별, 연령, 감정 및 기타 특성을 추측합니다.

## <a name="setup"></a>설치 프로그램

이 가이드에서는 얼굴 구독 키와 엔드포인트 URL을 사용하여 `faceClient`라는 [FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) 개체를 이미 생성했다고 가정합니다. 여기서는 이 가이드에서 사용되는 [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync) 또는 [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync)를 호출하여 얼굴 감지 기능을 사용할 수 있습니다. 이 기능을 설정하는 방법에 대한 지침은 빠른 시작 중 하나를 수행합니다.

이 가이드는 전달할 수 있는 인수와 반환된 데이터로 수행할 수 있는 작업을 비롯하여 감지 호출의 세부 사항을 중점적으로 설명합니다. 필요한 기능만 쿼리하는 것이 좋습니다. 각 작업을 완료하려면 추가 시간이 필요합니다.

## <a name="get-basic-face-data"></a>기본 얼굴 데이터 가져오기

이미지에서 얼굴을 찾고 해당 위치를 가져오려면 _returnFaceId_ 매개 변수를 **true** 로 설정하여 [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync) 또는 [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync) 메서드를 호출합니다. 이 설정은 기본값입니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic1":::

반환된 [DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface) 개체의 고유 ID와 얼굴의 픽셀 좌표를 제공하는 직사각형을 쿼리할 수 있습니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic2":::

얼굴의 위치 및 크기를 구문 분석하는 방법에 대한 자세한 내용은 [FaceRectangle](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle)을 참조하세요. 일반적으로 이 직사각형에는 눈, 눈섭, 코 및 입이 포함됩니다. 정수리, 귀 및 턱은 반드시 포함되는 것은 아닙니다. 얼굴 직사각형을 사용하여 전체 머리를 자르거나 사진 ID 유형 이미지의 경우 중거리 인물 사진을 가져오려면 직사각형을 각 방향으로 확장할 수 있습니다.

## <a name="get-face-landmarks"></a>얼굴 랜드마크 가져오기

[얼굴 랜드마크](../concepts/face-detection.md#face-landmarks)는 눈동자나 코끝과 같이 얼굴에서 찾기 쉬운 지점의 집합입니다. 얼굴 랜드마크 데이터를 가져오려면 _detectionModel_ 매개 변수를 **DetectionModel.Detection01** 로 설정하고 _returnFaceLandmarks_ 매개 변수를 **true** 로 설정합니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks1":::

다음 코드는 코와 눈동자의 위치를 검색하는 방법을 보여줍니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks2":::

얼굴 랜드마크 데이터를 사용하여 얼굴의 방향을 정확하게 계산할 수도 있습니다. 예를 들어 얼굴의 방향을 입의 가운데부터 눈의 가운데까지 벡터로 정의할 수 있습니다. 다음 코드는 이 벡터를 계산합니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="direction":::

얼굴 방향을 알고 있는 경우 직사각형 얼굴을 회전하여 더 정확하게 정렬할 수 있습니다. 이미지에서 얼굴을 자르려면 얼굴이 항상 똑바로 나타나도록 이미지를 프로그래밍 방식으로 회전할 수 있습니다.

## <a name="get-face-attributes"></a>얼굴 특성 가져오기

얼굴 감지 API는 얼굴 직사각형과 랜드마크 외에도 얼굴의 여러 개념 특성을 분석할 수 있습니다. 전체 목록은 [얼굴 특성](../concepts/face-detection.md#attributes) 개념 섹션을 참조하세요.

얼굴 특성을 분석하려면 _detectionModel_ 매개 변수를 **DetectionModel.Detection01** 로 설정하고 _returnFaceAttributes_ 매개 변수를 [FaceAttributeType Enum](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype) 값 목록으로 설정합니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes1":::

그런 다음, 반환된 데이터에 대한 참조를 가져오고 필요에 따라 더 많은 작업을 수행합니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes2":::

각 특성에 대해 자세히 알아보려면 [얼굴 감지 및 특성](../concepts/face-detection.md) 개념 가이드를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 얼굴 감지의 다양한 기능을 사용하는 방법을 알아보았습니다. 다음으로, 심층 자습서에 따라 이러한 기능을 앱에 통합합니다.

- [자습서: 이미지에서 얼굴 데이터를 표시하는 WPF 앱 만들기](../Tutorials/FaceAPIinCSharpTutorial.md)

## <a name="related-topics"></a>관련 항목

- [참조 설명서(REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [참조 설명서(.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
