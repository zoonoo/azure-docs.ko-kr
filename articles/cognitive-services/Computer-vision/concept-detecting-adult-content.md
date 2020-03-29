---
title: 성인, 외설, 피투성이 콘텐츠 - 컴퓨터 비전
titleSuffix: Azure Cognitive Services
description: 컴퓨터 비전 APi를 사용하여 이미지에서 성인 콘텐츠 감지와 관련된 개념입니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ee18916a59bb081d65494f46e7aba7c29c7177cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "71718525"
---
# <a name="detect-adult-content"></a>성인 콘텐츠 감지

컴퓨터 Vision은 개발자가 소프트웨어에서 이러한 이미지의 표시를 제한할 수 있도록 이미지에서 성인 자료를 감지할 수 있습니다. 콘텐츠 플래그는 0과 1 사이의 점수로 적용되므로 개발자가 고유한 기본 설정에 따라 결과를 해석할 수 있습니다.

> [!NOTE]
> 이 기능의 대부분은 [Azure 콘텐츠 중재자](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) 서비스에서 제공합니다. 텍스트 조정, 사용자 검토 워크플로 등의 좀 더 엄격한 콘텐츠 조정 시나리오에 대한 솔루션으로 이 대안을 참조하세요.

## <a name="content-flag-definitions"></a>콘텐츠 플래그 정의

"성인" 분류 에는 다음과 같은 여러 가지 범주가 있습니다.

- **성인** 이미지는 본질적으로 명시적으로 성적이며 종종 누드 및 성적 행위를 묘사하는 이미지로 정의됩니다.
- **외설** 이미지는 본질적으로 선정적이며 주로 **성인**으로 태그가 지정된 이미지보다 성적으로 덜 노골적인 콘텐츠를 포함하는 이미지로 정의됩니다.
- **고리** 이미지는 고어를 묘사하는 이미지로 정의됩니다.

## <a name="use-the-api"></a>API 사용

[이미지 분석](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API를 통해 성인 콘텐츠를 검색할 수 있습니다. `Adult` **visualFeatures** 쿼리 매개 변수에 값을 추가 하면 API 는&mdash;`isAdultContent`세 `isRacyContent`개의 `isGoryContent` &mdash;부울 속성 및 JSON 응답에서 반환 합니다. 또한 메서드는 해당&mdash;`adultScore` `racyScore`속성 `goreScore` &mdash;및 각 범주에 대해 0과 1 사이의 신뢰도 점수를 나타냅니다.

- [빠른 시작: 이미지 분석(.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
- [빠른 시작: 이미지 분석(REST API)](./quickstarts/csharp-analyze.md)
