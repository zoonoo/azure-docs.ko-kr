---
title: 성인 및 외설 콘텐츠 설명 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API를 사용하여 이미지에서 성인 및 외설 콘텐츠를 검색하는 데 관련된 개념입니다.
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: b1ba8e7556b6ba134624548142bf73e84d875c6a
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984525"
---
# <a name="detecting-adult-and-racy-content"></a>성인 및 외설 콘텐츠 검색

다양한 시각적 범주 중 성인/외설 그룹을 사용하면 성인용 자료를 검색하고 성적인 콘텐츠를 포함하는 이미지 표시를 제한할 수 있습니다. 사용자의 기본 설정에 맞게 슬라이딩 눈금에 성인/외설 콘텐츠 검색 필터를 설정할 수 있습니다.

## <a name="defining-adult-and-racy-content"></a>성인 및 외설 콘텐츠 정의

[분석 이미지 메서드](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)에서 다룬 다양한 시각적 기능 중에서 성인 시작적 기능을 사용하면 성인 및 외설 이미지를 검색하도록 설정할 수 있습니다. "성인" 이미지는 본질 상 공격적이며 노출 및 성적 행위를 묘사하는 이미지로 정의됩니다. "외설" 이미지는 본질 상 성적으로 선정적이며 "성인"으로 태그가 지정된 이미지보다 적게 성적으로 노골적인 콘텐츠를 포함하는 이미지로 정의됩니다. 성인 시각적 기능 형식은 성적으로 선정적이고 노골적인 성적 콘텐츠를 포함하는 이미지를 표시하지 않도록 제한하는 데 일반적으로 사용됩니다.

## <a name="identifying-adult-and-racy-content"></a>성인 및 외설 콘텐츠 식별

이미지 분석 메서드는 메서드의 JSON 응답에서 각각 성인 및 외설 콘텐츠를 나타내는 `isAdultContent` 및 `isRacyContent`라는 두 개의 속성을 반환합니다. 두 속성은 모두 부울 값, true 또는 false를 반환합니다. 또한 메서드는 각각 성인 및 외설 콘텐츠를 식별하는 신뢰도 점수를 나타내는 `adultScore` 및 `racyScore`라는 두 개의 속성을 반환합니다. 성인 및 외설 콘텐츠 검색에 대한 신뢰도 필터는 특정 시나리오에 따라 기본 설정에 맞게 슬라이딩 눈금에서 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[도메인별 콘텐츠 검색](concept-detecting-domain-content.md) 및 [얼굴 감지](concept-detecting-faces.md)에 대한 개념을 알아봅니다.