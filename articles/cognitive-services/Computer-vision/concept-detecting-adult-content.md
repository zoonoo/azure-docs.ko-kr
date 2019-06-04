---
title: 성인 및 외설 콘텐츠 설명 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API를 사용하여 이미지에서 성인 및 외설 콘텐츠를 감지하는 데 관련된 개념입니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 69a4c136e9c210dd40e004b8d5e1c1a2a8fceaa7
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312541"
---
# <a name="detect-adult-and-racy-content"></a>성인 및 외설 콘텐츠 감지

Computer Vision은 개발자가 소프트웨어에서 이러한 이미지의 표시를 제한할 수 있도록 이미지의 성인 자료를 감지할 수 있습니다. 콘텐츠 플래그는 0과 1 사이의 점수로 적용되므로 개발자가 고유한 기본 설정에 따라 결과를 해석할 수 있습니다. 

> [!NOTE]
> 이 기능은 [Azure Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) 서비스를 통해서도 제공됩니다. 텍스트 조정, 사용자 검토 워크플로 등의 좀 더 엄격한 콘텐츠 조정 시나리오에 대한 솔루션으로 이 대안을 참조하세요.

## <a name="content-flag-definitions"></a>콘텐츠 플래그 정의

**성인** 이미지는 본질적으로 음란물이며 주로 노출 또는 성적 행위를 묘사하는 이미지로 정의됩니다. 

**외설** 이미지는 본질적으로 선정적이며 주로 **성인**으로 태그가 지정된 이미지보다 성적으로 덜 노골적인 콘텐츠를 포함하는 이미지로 정의됩니다. 

## <a name="identify-adult-and-racy-content"></a>성인 및 외설 콘텐츠 식별

[분석](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API입니다.

이미지 분석 메서드는 각각 성인 및 외설 콘텐츠를 나타내는 `isAdultContent` 및 `isRacyContent`라는 두 개의 부울 속성을 메서드의 JSON 응답으로 반환합니다. 또한 메서드는 각각 성인 및 외설 콘텐츠를 식별하는 신뢰도 점수를 나타내는 `adultScore` 및 `racyScore`라는 두 개의 속성을 반환합니다.

## <a name="next-steps"></a>다음 단계

[도메인 특정 콘텐츠 감지](concept-detecting-domain-content.md) 및 [얼굴 감지](concept-detecting-faces.md)에 대한 개념을 알아봅니다.
