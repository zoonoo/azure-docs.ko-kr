---
title: Face API란?
titleSuffix: Azure Cognitive Services
description: Face 서비스를 사용하여 이미지에서 얼굴을 감지하고 분석하는 방법을 알아봅니다.
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: overview
ms.date: 10/29/2018
ms.author: sbowles
ms.openlocfilehash: a15b6678b15bf5d1a3078494e12da3a08c57bed3
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633464"
---
# <a name="what-is-the-azure-face-api"></a>Azure Face API란?

Azure Face API는 이미지에서 사람 얼굴을 감지, 인식 및 분석하기 위한 알고리즘을 제공하는 인지 서비스입니다. 얼굴 정보를 처리하는 기능은 보안, 자연스러운 사용자 인터페이스, 이미지 콘텐츠 분석 및 관리, 모바일 앱, 로봇과 같은 다양한 소프트웨어 시나리오에서 중요합니다.

Face API는 각각 다음 섹션에 설명된 다양한 기능을 제공합니다. 각 기능에 대해 자세히 알아보고 필요에 맞는지 확인하려면 참조하세요.

## <a name="face-detection"></a>얼굴 감지

Face API는 이미지에서 사람 얼굴을 감지하고 해당 위치의 사각형 좌표를 반환할 수 있습니다. 필요에 따라 얼굴 감지는 포즈, 성별, 연령, 머리 포즈, 수염 및 안경과 같은 일련의 얼굴 관련 속성을 추출할 수 있습니다.

![얼굴 주위에 사각형이 그려지고 연령 및 성별이 표시된 여자와 남자의 이미지](./Images/Face.detection.jpg)

얼굴 감지 기능은 [Computer Vision API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)를 통해 제공되지만 얼굴 데이터로 추가 작업을 수행하려는 경우 Face API(이 서비스)를 사용해야 합니다. 얼굴 감지에 대한 자세한 내용은 [Detect API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)를 참조하세요.

## <a name="face-verification"></a>얼굴 확인

Verify API는 감지된 두 얼굴에 대한 인증 또는 하나의 얼굴 개체에 대한 하나의 감지된 얼굴에서 인증을 수행합니다. 실질적으로 두 얼굴이 같은 사람인지 여부를 평가합니다. 이는 보안 시나리오에서 잠재적으로 유용합니다. 자세한 내용은 [Verify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)를 참조하세요.

## <a name="find-similar-faces"></a>유사 얼굴 찾기

Find Similar API는 대상 얼굴과 일련의 후보 얼굴을 사용하고 대상 얼굴과 가장 비슷해 보이는 몇 개의 얼굴을 찾습니다. **matchPerson** 및 **matchFace** 등의 두 가지 작업 모드를 지원합니다. **matchPerson** 모드는 동일한 사람에 대해 필터링한 후 유사한 얼굴을 반환합니다([Verify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) 사용). **matchFace** 모드는 같은 사람 필터를 무시하고 동일한 사람이거나 동일한 사람이 아닐 수 있는 유사한 후보 얼굴의 목록을 반환합니다.

다음 예제에서 다음은 대상 얼굴입니다.

![웃는 여자](./Images/FaceFindSimilar.QueryFace.jpg)

다음은 후보 얼굴입니다.

![5개의 웃는 사람들의 이미지 이미지 a) 및 b)는 동일한 사람](./Images/FaceFindSimilar.Candidates.jpg)

비슷한 얼굴을 찾으려면 **matchPerson** 모드가 같은 사람을 대상 얼굴로 묘사하는 (a) 및 (b)를 반환합니다. **matchFace** 모드는 일부가 대상과 동일한 사람이 아니거나 유사성이 떨어지더라도 정확히 4개의 후보인 (a), (b), (c) 및 (d)를 반환합니다. 자세한 내용은 [Find Similar API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)를 참조하세요.

## <a name="face-grouping"></a>얼굴 그룹화

Group API는 알 수 없는 얼굴 집합을 유사성에 따라 여러 그룹으로 나눕니다. 각 그룹은 원래 얼굴 집합의 비연속 적절한 하위 집합입니다. 그룹의 모든 얼굴은 동일한 사람일 가능성이 있지만 단일 사용자에 대해 여러 다른 그룹이 있을 수 있습니다(예: 식과 같은 다른 요소로 구분됨). 자세한 내용은 [Group API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)를 참조하세요.

## <a name="person-identification"></a>사람 식별

사용자 데이터베이스에 대해 감지된 얼굴을 식별하는 데 Identify API를 사용할 수 있습니다. 이는 사진 관리 소프트웨어에서 자동 이미지 태그 지정에 유용할 수 있습니다. 데이터베이스를 미리 만들고, 시간이 지남에 따라 편집할 수 있습니다.

다음 이미지는 "myfriends"라는 데이터베이스의 예제를 보여줍니다. 각 그룹은 최대 1,000,000개의 다른 사람 개체를 포함하고, 각 사용자 개체는 최대 248개의 등록된 얼굴을 가질 수 있습니다.

![각각 3개의 얼굴 이미지 행이 있는 다른 사람에 대한 3개의 열이 있는 그리드](./Images/person.group.clare.jpg)

데이터베이스가 생성 및 학습되면 감지된 새 얼굴이 있는 그룹에 대해 인식을 수행할 수 있습니다. 얼굴이 그룹의 사람으로 식별되면 해당 사람 개체가 반환됩니다.

사람 인식에 대한 자세한 내용은 [Identify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)를 참조하세요.

## <a name="use-containers"></a>컨테이너 사용

[얼굴 컨테이너를 사용](face-how-to-install-containers.md)하여 표준화된 Docker 컨테이너를 데이터와 가까이 설치하고 얼굴을 검색, 인지 및 식별할 수 있습니다.

## <a name="sample-apps"></a>샘플 앱

다음 애플리케이션 예제는 Face API를 사용할 수 있는 몇 가지 방법을 소개합니다.

- [Microsoft Face API: Windows 클라이언트 라이브러리 및 샘플](https://github.com/Microsoft/Cognitive-Face-Windows) - 얼굴 감지, 분석 및 식별에 해당하는 몇 가지 시나리오를 보여주는 WPF 앱입니다.
- [FamilyNotes UWP 앱](https://github.com/Microsoft/Windows-appsample-familynotes) - 제품군 노트 공유 시나리오에서 음성, Cortana, 잉크 및 카메라와 함께 얼굴 인식을 사용하는 UWP(유니버설 Windows 플랫폼) 앱입니다.

## <a name="next-steps"></a>다음 단계

코드에서 간단한 얼굴 감지 시나리오를 구현하는 빠른 시작을 수행합니다.
- [빠른 시작: C#과 함께 .NET SDK를 사용하여 이미지에서 얼굴 감지](quickstarts/csharp.md)(다른 언어 가능)
