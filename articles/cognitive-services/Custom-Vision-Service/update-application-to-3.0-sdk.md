---
title: 프로젝트를 3.0 API로 업데이트하는 방법
titleSuffix: Azure Cognitive Services
description: Custom Vision 프로젝트를 이전 버전의 API에서 3.0 API로 업데이트하는 방법을 알아봅니다.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: c134f30b124113a23df0e73cd1bbc8209e335183
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "73647494"
---
# <a name="update-to-the-30-api"></a>3\.0 API로 업데이트

Custom Vision은 이제 일반 공급되며 API 업데이트를 거쳤습니다.
이 업데이트에는 몇 가지 새로운 기능과 호환성이 손상되는 몇 가지 중요한 변경 사항이 포함되어 있습니다.

* 예측 API는 이제 프로젝트 형식을 기준으로 두 개로 분할됩니다.
* VAIDK(Vision AI Developer Kit) 내보내기 옵션을 사용하려면 특정 방식으로 프로젝트를 만들어야 합니다.
* 명명된 반복의 게시/게시 취소를 위해 기본 반복이 제거되었습니다.

이 가이드에서는 새 API 버전을 사용하도록 프로젝트를 업데이트하는 방법을 보여 줍니다. 변경 내용에 대한 전체 목록은 [릴리스 정보](release-notes.md)를 참조하세요.

## <a name="use-the-updated-prediction-api"></a>업데이트된 예측 API 사용

2\.x API는 이미지 분류자 및 개체 감지기 프로젝트 모두에 대해 동일한 예측 호출을 사용했습니다. 두 프로젝트 형식 모두 **PredictImage** 및 **PredictImageUrl** 호출에 사용할 수 있었습니다. 3\.0부터는 이 API를 분할하여 프로젝트 형식을 호출에 일치시켜야 합니다.

* **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** 및 **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** 을 사용하여 이미지 분류 프로젝트에 대한 예측을 가져옵니다.
* **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** 및 **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** 을 사용하여 개체 감지 프로젝트에 대한 예측을 가져옵니다.

## <a name="use-the-new-iteration-publishing-workflow"></a>새로운 반복 게시 워크플로 사용

2\.x API는 예측에 사용할 반복을 선택하려면 기본 반복 또는 지정된 반복 ID를 사용했습니다. 3\.0부터는 학습 API에서 지정된 이름으로 반복을 먼저 게시하는 게시 흐름을 채택했습니다. 그런 다음 이름을 예측 메서드에 전달하여 사용할 반복을 지정합니다.

> [!IMPORTANT]
> 3\.0 API는 기본 반복 기능을 사용하지 않습니다. 이전 API가 사용 중단될 때까지 계속해서 2.x API를 사용하여 반복을 기본값으로 전환할 수 있습니다. 이러한 API는 일정 기간 동안 유지 관리되고 **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** 메서드를 호출하여 반복을 기본값으로 표시할 수 있습니다.

### <a name="publish-an-iteration"></a>반복 게시

반복이 학습되고 나면 **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** 메서드를 사용하여 예측에 사용할 수 있도록 설정할 수 있습니다. 반복을 게시하려면 CustomVision 웹 사이트의 설정 페이지에서 사용할 수 있는 예측 리소스 ID가 필요합니다.

![예측 리소스 ID가 설명된 Custom Vision 웹 사이트 설정 페이지입니다.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Custom Vision 예측 리소스로 이동하고 **속성** 을 선택하여 [Azure Portal](https://portal.azure.com)에서 이 정보를 가져올 수도 있습니다.

반복이 게시되고 나면 앱은 예측 API 호출에서 이름을 지정하여 이를 예측에 사용할 수 있습니다. 예측 호출에 대해 반복을 사용할 수 없게 하려면 **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** API를 사용합니다.

## <a name="next-steps"></a>다음 단계

* [학습 API 참조 설명서(REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [예측 API 참조 설명서(REST)](https://go.microsoft.com/fwlink/?linkid=865445)