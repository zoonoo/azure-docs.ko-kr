---
title: 성인, 외설, 폭력 콘텐츠 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API를 사용하여 이미지에서 성인 콘텐츠를 검색하는 데 관련된 개념입니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5d5961ecae2fbc154ae6f1acd74df2bb74024fa1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96532621"
---
# <a name="detect-adult-content"></a>성인 콘텐츠 검색

Computer Vision은 이미지에서 성인 자료를 검색할 수 있어서 개발자가 소프트웨어에서 이러한 이미지의 표시를 제한할 수 있습니다. 콘텐츠 플래그는 0과 1 사이의 점수로 적용되므로 개발자가 고유한 기본 설정에 따라 결과를 해석할 수 있습니다.

> [!NOTE]
> 이러한 기능의 대부분은 [Azure Content Moderator](../content-moderator/overview.md) 서비스에서 제공됩니다. 텍스트 조정, 사용자 검토 워크플로 등의 좀 더 엄격한 콘텐츠 조정 시나리오에 대한 솔루션으로 이 대안을 참조하세요.

## <a name="content-flag-definitions"></a>콘텐츠 플래그 정의

"성인" 분류에는 다음과 같은 다양한 범주가 포함되어 있습니다.

- **성인** 이미지는 기본적으로 명백히 성적이고 노출 및 성적 행위를 드러내는 경우가 많습니다.
- **외설** 이미지는 본질적으로 선정적이며 주로 **성인** 으로 태그가 지정된 이미지보다는 성적으로 덜 노골적인 콘텐츠입니다.
- **폭력** 이미지는 피를 흘리거나 잔인한 장면이 나타납니다.

## <a name="use-the-api"></a>API 사용

[Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API를 사용하여 성인 콘텐츠를 검색할 수 있습니다. **visualFeatures** 쿼리 매개 변수에 `Adult` 값을 추가하면 API는 JSON 응답에서 세 개의 부울 속성&mdash;`isAdultContent`, `isRacyContent` 및 `isGoryContent`&mdash;가 반환됩니다. 또한 이 메서드는 각 카테고리에 대해 0과 1 사이의 신뢰도 점수를 나타내는 해당 속성&mdash;`adultScore`, `racyScore` 및 `goreScore`&mdash;를 반환합니다.

- [빠른 시작: Computer Vision REST API 또는 클라이언트 라이브러리](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
