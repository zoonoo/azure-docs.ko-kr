---
title: 프로젝트를 3.0 API로 업데이트하는 방법
titleSuffix: Azure Cognitive Services
description: 이전 버전의 API에서 3.0 API로 사용자 지정 비전 프로젝트를 업데이트하는 방법을 알아봅니다.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: c134f30b124113a23df0e73cd1bbc8209e335183
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647494"
---
# <a name="update-to-the-30-api"></a>3.0 API로 업데이트

사용자 지정 비전이 이제 일반 공급에 도달했으며 API 업데이트를 거쳤습니다.
이 업데이트에는 몇 가지 새로운 기능과 중요한 몇 가지 주요 변경 사항이 포함되어 있습니다.

* 이제 예측 API는 프로젝트 유형에 따라 두 개로 분할됩니다.
* 비전 AI 개발자 키트(VAIDK) 내보내기 옵션을 사용하려면 특정 방식으로 프로젝트를 만들어야 합니다.
* 기본 반복은 명명된 반복을 게시/게시 취소하기 위해 제거되었습니다.

이 가이드에서는 새 API 버전으로 작동하도록 프로젝트를 업데이트하는 방법을 보여 줍니다. 변경 내용의 전체 목록은 [릴리스 참조](release-notes.md) 를 참조하십시오.

## <a name="use-the-updated-prediction-api"></a>업데이트된 예측 API 사용

2.x API는 이미지 분류기와 개체 감지기 프로젝트 모두에 대해 동일한 예측 호출을 사용했습니다. 두 프로젝트 유형 모두 **예측 이미지** 및 **PredictImageUrl** 호출에 허용되었습니다. 3.0부터 는 프로젝트 유형을 호출에 일치시켜야 하므로 이 API를 분할했습니다.

* **[분류이미지](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** 및 **[분류ImageUrl을](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** 사용하여 이미지 분류 프로젝트에 대한 예측을 가져옵니다.
* **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** 및 **[DetectImageUrl을](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** 사용하여 개체 검색 프로젝트에 대한 예측을 가져옵니다.

## <a name="use-the-new-iteration-publishing-workflow"></a>새 반복 게시 워크플로 사용

2.x API는 기본 반복 또는 지정된 반복 ID를 사용하여 예측에 사용할 이터레이션을 선택했습니다. 3.0부터 는 교육 API에서 지정된 이름으로 반복을 먼저 게시하는 게시 흐름을 채택했습니다. 그런 다음 이름을 예측 메서드에 전달하여 사용할 반복을 지정합니다.

> [!IMPORTANT]
> 3.0 API는 기본 반복 기능을 사용하지 않습니다. 이전 API를 더 이상 사용하지 않으면 2.x API를 계속 사용하여 반복을 기본값으로 전환할 수 있습니다. 이러한 API는 잠시 동안 유지 관리되며 **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** 메서드를 호출하여 반복을 기본값으로 표시할 수 있습니다.

### <a name="publish-an-iteration"></a>반복 게시

반복이 학습되면 **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** 메서드를 사용하여 예측에 사용할 수 있도록 할 수 있습니다. 반복을 게시하려면 CustomVision 웹 사이트의 설정 페이지에서 사용할 수 있는 예측 리소스 ID가 필요합니다.

![예측 리소스 ID가 설명된 사용자 지정 비전 웹 사이트 설정 페이지입니다.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> 사용자 지정 비전 예측 리소스로 이동하여 **속성을**선택하여 [Azure Portal에서](https://portal.azure.com) 이 정보를 얻을 수도 있습니다.

반복이 게시되면 앱은 예측 API 호출에 이름을 지정하여 예측에 사용할 수 있습니다. 예측 호출에 대해 반복을 사용할 수 없게 하려면 **[게시 취소 API를](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** 사용합니다.

## <a name="next-steps"></a>다음 단계

* [교육 API 참조 문서(REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [예측 API 참조 문서(REST)](https://go.microsoft.com/fwlink/?linkid=865445)