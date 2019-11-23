---
title: 성인, 외설, 범주 콘텐츠-Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision APi를 사용 하 여 이미지에서 성인 콘텐츠를 검색 하는 것과 관련 된 개념입니다.
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
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718525"
---
# <a name="detect-adult-content"></a>성인 콘텐츠 검색

Computer Vision는 개발자가 소프트웨어에서 이러한 이미지의 표시를 제한할 수 있도록 이미지에서 성인 자료를 검색할 수 있습니다. 콘텐츠 플래그는 0과 1 사이의 점수로 적용되므로 개발자가 고유한 기본 설정에 따라 결과를 해석할 수 있습니다.

> [!NOTE]
> 이러한 기능의 대부분은 [Azure Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) 서비스에서 제공 합니다. 텍스트 조정, 사용자 검토 워크플로 등의 좀 더 엄격한 콘텐츠 조정 시나리오에 대한 솔루션으로 이 대안을 참조하세요.

## <a name="content-flag-definitions"></a>콘텐츠 플래그 정의

"성인" 분류 내에는 다음과 같은 다양 한 범주가 있습니다.

- **성인** 이미지는 기본적으로 명확 하 게 성적을 나타내는 것으로 정의 되며 종종 노출 및 성적 행위를 나타냅니다.
- **외설** 이미지는 본질적으로 선정적이며 주로 **성인**으로 태그가 지정된 이미지보다 성적으로 덜 노골적인 콘텐츠를 포함하는 이미지로 정의됩니다.
- **범주** 이미지는 피를 나타내는 것으로 정의 됩니다.

## <a name="use-the-api"></a>API 사용

[분석 이미지](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API를 사용 하 여 성인 콘텐츠를 검색할 수 있습니다. `Adult` 값을 **Visualfeatures** 쿼리 매개 변수에 추가 하는 경우 API는 JSON 응답에서 `isAdultContent`, `isRacyContent`및 `isGoryContent`&mdash;&mdash;세 가지 부울 속성을 반환 합니다. 또한이 메서드는&mdash;`adultScore`, `racyScore`, `goreScore`&mdash;해당 하는 속성을 반환 합니다 .이 속성은 각각의 각 범주에 대해 0과 1 사이의 신뢰도 점수를 나타냅니다.

- [빠른 시작: 이미지 분석 (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
- [빠른 시작: 이미지 분석 (REST API)](./quickstarts/csharp-analyze.md)
