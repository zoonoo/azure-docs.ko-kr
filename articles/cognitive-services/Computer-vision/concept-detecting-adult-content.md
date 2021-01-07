---
title: 성인, 외설, 범주 콘텐츠-Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API를 사용 하 여 이미지에서 성인 콘텐츠를 검색 하는 것과 관련 된 개념입니다.
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
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532621"
---
# <a name="detect-adult-content"></a>성인 콘텐츠 검색

Computer Vision는 개발자가 소프트웨어에서 이러한 이미지의 표시를 제한할 수 있도록 이미지에서 성인 자료를 검색할 수 있습니다. 콘텐츠 플래그는 0과 1 사이의 점수와 함께 적용 되므로 개발자는 자신의 기본 설정에 따라 결과를 해석할 수 있습니다.

> [!NOTE]
> 이러한 기능의 대부분은 [Azure Content Moderator](../content-moderator/overview.md) 서비스에서 제공 합니다. 텍스트 조정, 사용자 검토 워크플로 등의 좀 더 엄격한 콘텐츠 조정 시나리오에 대한 솔루션으로 이 대안을 참조하세요.

## <a name="content-flag-definitions"></a>콘텐츠 플래그 정의

"성인" 분류에는 다음과 같은 다양 한 범주가 포함 되어 있습니다.

- **성인** 이미지는 기본적으로 명확 하 게 표시 되며 노출 및 성적 행위를 표시 하는 경우가 많습니다.
- **외설** 이미지는 기본적으로 성적 추천 **성인** 으로 태그가 지정 된 이미지 보다 성적 명시적인 콘텐츠를 포함 하는 경우가 많습니다.
- **범주** 이미지는 피를 표시 합니다.

## <a name="use-the-api"></a>API 사용

[분석 이미지](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API를 사용 하 여 성인 콘텐츠를 검색할 수 있습니다. 의 값을 `Adult` **visualfeatures** 쿼리 매개 변수에 추가 하는 경우 API는 &mdash; `isAdultContent` `isRacyContent` `isGoryContent` &mdash; JSON 응답에서, 및 라는 세 개의 부울 속성을 반환 합니다. 또한 메서드는 해당 하는 속성 &mdash; `adultScore` , 및를 반환 `racyScore` `goreScore` &mdash; 합니다 .이 속성은 각각의 각 범주에 대해 0과 1 사이의 신뢰 점수를 나타냅니다.

- [빠른 시작: Computer Vision REST API 또는 클라이언트 라이브러리](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
