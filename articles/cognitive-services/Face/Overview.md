---
title: Face API Service 개요 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 이 용어집에서는 Face API Service를 사용할 때 나타날 수 있는 용어에 대해 설명합니다.
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: fb1d14ff80bf53adc3008d79cc998739ffffde1b
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048672"
---
# <a name="what-is-face-api"></a>Face API란?

최고급 얼굴 알고리즘을 제공하는 클라우드 기반 서비스인 Microsoft Face API에 오신 것을 환영합니다. Face API는 특성을 사용한 얼굴 감지와 얼굴 인식이라는 두 가지 주요 기능을 제공합니다.

## <a name="face-detection"></a>얼굴 감지

Face API는 이미지에서 고정밀도 얼굴 위치를 갖는 최대 64개의 인간 얼굴을 감지합니다. 또한 이미지를 파일(바이트) 또는 유효한 UR로 지정할 수 있습니다.

![개요 - 얼굴 감지](./Images/Face.detection.jpg)

이미지에서 얼굴의 위치를 나타내는 얼굴 사각형(왼쪽, 위쪽, 너비 및 높이)은 감지된 각 얼굴과 함께 반환됩니다. 필요에 따라 얼굴 감지는 포즈, 성별, 연령, 머리 포즈, 수염 및 안경과 같은 일련의 얼굴 관련 속성을 추출합니다. 자세한 내용은 [얼굴 - 감지](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)를 참조하세요.

## <a name="face-recognition"></a>얼굴 인식

얼굴 인식은 보안, 자연스러운 사용자 인터페이스, 이미지 콘텐츠 분석 및 관리, 모바일 앱, 로봇을 비롯한 다양한 시나리오에서 널리 사용됩니다. 얼굴 확인, 비슷한 얼굴 찾기, 얼굴 그룹화 및 개인 식별의 네 가지 얼굴 인식 기능이 제공됩니다.

### <a name="face-verification"></a>얼굴 확인

Face API 확인은 감지된 두 얼굴에 대한 인증 또는 감지된 하나의 얼굴에서 하나의 사람 개체로의 인증을 수행합니다. 자세한 내용은 [얼굴 - 확인](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)을 참조하세요.

### <a name="finding-similar-face"></a>비슷한 얼굴 찾기

감지된 대상 얼굴과 검색할 일련의 후보 얼굴들이 제공될 경우, 이 서비스는 대상 얼굴과 가장 비슷해 보이는 몇 개의 얼굴을 찾습니다. 두 작업 모드 `matchFace` 및 `matchPerson`이 지원됩니다. `matchPerson` 모드는 [얼굴 - 확인](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)에서 파생된 동일 인물 임계값을 적용한 후 비슷한 얼굴을 반환합니다. `matchFace` 모드는 동일 인물 임계값을 무시하고 유사한 상위 후보 얼굴을 반환합니다. 다음 예제를 진행하면 후보 얼굴이 나열됩니다.
![개요 - 비슷한 얼굴 찾기](./Images/FaceFindSimilar.Candidates.jpg) 쿼리 얼굴은 ![개요 - 비슷한 얼굴 찾기](./Images/FaceFindSimilar.QueryFace.jpg)입니다.

4개의 비슷한 얼굴을 찾기 위해 `matchPerson` 모드는 쿼리 얼굴과 같은 사람에 속하는 (a)와 (b)를 반환합니다. `matchFace` 모드는 유사성이 떨어지더라도 정확히 4개의 후보인 (a), (b), (c) 및 (d)를 반환합니다. 자세한 내용은 [얼굴 - 비슷한 얼굴 찾기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)를 참조하세요.

### <a name="face-grouping"></a>얼굴 그룹화

알려지지 않은 하나의 얼굴 집합이 제공될 경우 Face API는 이러한 얼굴 집합을 자동으로 유사성에 따라 몇 개의 그룹으로 나눕니다. 각 그룹은 원래 알 수 없는 얼굴 집합의 일관되지 않은 하위 집합입니다. 또한 같은 그룹에 속하는 모든 얼굴이 동일한 사람 개체에 속하는 것으로 간주될 수 있습니다. 자세한 내용은 [얼굴 - 그룹](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)을 참조하세요.

### <a name="face-identification"></a>얼굴 인식

Face API는 감지된 얼굴 및 사람 데이터베이스(LargePersonGroup/PersonGroup으로 정의)에 따라 사람을 식별하는 데 사용될 수 있습니다. 이 데이터베이스를 미리 만들고, 시간이 지남에 따라 편집할 수 있습니다.

다음 그림은 "myfriends"라는 LargePersonGroup/PersonGroup의 예입니다. 각 그룹은 최대 1,000,000/10,000개의 사람 개체를 포함할 수 있습니다. 한편, 각 사람 개체에 대해 최대 248개의 얼굴을 등록할 수 있습니다.

![개요 - LargePersonGroup/PersonGroup](./Images/person.group.clare.jpg)

LargePersonGroup/PersonGroup이 생성되고 학습되면 그룹 및 감지된 새 얼굴에 대해 인식을 수행할 수 있습니다. 얼굴이 그룹의 사람 개체로 식별되면 해당 사람 개체가 반환됩니다.

사람 인식에 대한 자세한 내용은 다음 API 가이드를 참조하세요.

[얼굴 - 인식](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)
[PersonGroup - 만들기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
[PersonGroup 사람 - 만들기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
[PersonGroup - 학습](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)
[LargePersonGroup - 만들기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)
[LargePersonGroup 사람 - 만들기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)
[LargePersonGroup - 학습](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)

### <a name="face-storage"></a>Face Storage

Face Storage를 사용하면 Face API를 사용한 인식 또는 유사성 일치를 위해 LargePersonGroup/PersonGroup 사람 개체([PersonGroup 사람 - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)/[LargePersonGroup 사람 - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)) 또는 LargeFaceLists/FaceLists([FaceList - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)/[LargeFaceList - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3))를 사용할 때 표준 구독을 통해 보관되는 추가 얼굴을 저장할 수 있습니다. 저장된 이미지는 1,000개 얼굴 기준으로 $0.5로 청구되며, 이 요금은 일별로 계산됩니다. 무료 계층 구독은 무료이지만 총 1,000명의 사람으로 제한됩니다.

Face Storage의 가격은 일별로 계산됩니다. 예를 들어 계정에서 한 달 중 처음 15일을 매일 10,000개의 보관된 얼굴을 사용하고 나머지 15일은 사용하지 않은 경우 저장한 일수 동안 10,000개의 얼굴에 대해서만 청구됩니다. 또는 해당 달에 매일 1,000개의 얼굴을 몇 시간 동안 보관한 다음 매일 밤 삭제해도 매일 1,000개의 보관된 얼굴에 대해 청구됩니다.

## <a name="getting-started-tutorials"></a>시작 자습서

다음 자습서에서는 Face API 기본 기능 및 구독 프로세스를 보여 줍니다.

- [에서 Face API 시작 자습서](Tutorials/FaceAPIinCSharpTutorial.md)
- [Android용 Java에서 Face API 시작 자습서](Tutorials/FaceAPIinJavaForAndroidTutorial.md)
- [Python에서 Face API 시작 자습서](Tutorials/FaceAPIinPythonTutorial.md)

## <a name="sample-apps"></a>샘플 앱

Face API를 사용하는 이러한 샘플 응용 프로그램을 살펴보세요.

- [Microsoft Face API: Windows 클라이언트 라이브러리 및 샘플](https://github.com/Microsoft/Cognitive-Face-Windows)
  - 얼굴 감지, 분석 및 식별에 해당하는 몇 가지 시나리오를 보여 주는 WPF 샘플 앱입니다.
- [FamilyNotes UWP 앱](https://github.com/Microsoft/Windows-appsample-familynotes)
  - 가족 메모 공유 시나리오를 통해 음성, Cortana, 잉크 및 카메라 사용을 보여 주는 UWP(유니버설 Windows 플랫폼) 샘플 앱입니다.
- [비디오 프레임 분석 샘플](https://github.com/microsoft/cognitive-samples-videoframeanalysis)
  - Face, Computer Vision 및 Emotion API를 사용하여 거의 실시간으로 라이브 비디오 스트림 분석을 보여 주는 UWP(유니버설 Windows 플랫폼) 샘플 앱입니다.

## <a name="related-topics"></a>관련 항목

- [Face API 버전 1.0 릴리스 정보](ReleaseNotes.md)
- [이미지에서 얼굴을 감지하는 방법](Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
- [이미지에서 얼굴을 식별하는 방법](Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)
