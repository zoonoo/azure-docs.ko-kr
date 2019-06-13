---
title: 프로젝트를 3.0 API로 마이그레이션하는 방법
titlesuffix: Azure Cognitive Services
description: 3.0 이전 버전의 API에서 Custom Vision 프로젝트를 마이그레이션하는 방법을 알아봅니다.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: 9dd473aadd7123cafc27209f5c34322fdbcffb71
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59044009"
---
# <a name="migrate-to-the-30-api"></a>3.0 API로 마이그레이션 

Custom Vision은 이제 일반 공급이 되었고 API 업데이트가 완료되었습니다.
이 업데이트는 몇 가지 새로운 기능을 포함하며, 중요한 점은 다음과 같은 몇 가지 호환성이 손상되는 변경을 포함한다는 것입니다.

* 예측 API는 이제 프로젝트 유형을 기반으로 두 개로 분할됩니다.
* 비전 AI 개발자 키트 (VAIDK) 내보내기 옵션에서는 특정 방식으로 프로젝트를 만들어야 합니다.
* 기본 반복은 제거되고 명명된 반복의 게시/게시 취소가 사용됩니다. 

이 가이드에서는 새로운 API 버전을 사용할 수 있도록 프로젝트를 업데이트하는 방법을 보여 줍니다. 변경 내용의 전체 목록은 [릴리스 정보](release-notes.md)를 참조합니다.

## <a name="use-the-updated-prediction-api"></a>업데이트된 예측 API 사용

2.x API는 이미지 분류자 및 개체 탐지기 프로젝트 모두에 대해 동일한 예측 호출을 사용했습니다. 두 프로젝트 유형 모두 **PredictImage**와 **PredictImageUrl** 호출에 허용되었습니다. 3.0부터는 이 API가 분할되어 호출에 프로젝트 형식이 일치하도록 해야 합니다.

* 이미지 분류 프로젝트에 대한 예측을 가져오기 위해서는 **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)**와 **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)**을 사용합니다.
* 개체 감지 프로젝트에 대한 예측을 가져오기 위해서는 **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)**와 **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)**을 사용합니다.

## <a name="use-the-new-iteration-publishing-workflow"></a>새 반복 게시 워크플로 사용

2.x API는 예측에 사용할 반복을 선택하기 위해 기본 반복 또는 지정된 반복 ID를 사용합니다. 3.0부터는 학습 API에서 지정된 이름으로 반복을 먼저 게시하는 게시 흐름을 채택했습니다. 그런 다음 사용할 반복을 지정하기 위해 이름을 예측 메서드에 전달합니다.

> [!IMPORTANT]
> 3.0 API는 기본 반복 기능을 사용하지 않습니다. 이전 API의 사용이 중단될 때까지, 2.x API는 반복을 기본값으로 전환하기 위해 계속 사용할 수 있습니다. 이러한 API는 일정 기간 동안 유지되며 **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** 메서드를 호출하여 반복을 기본값으로 표시할 수 있습니다.

### <a name="publish-an-iteration"></a>반복 게시

반복이 학습되면, **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** 메서드를 사용하여 예측에 사용 가능하게 할 수 있습니다. 반복을 게시하려면 Custom Vision 웹 사이트의 설정 페이지에서 사용할 수 있는 예측 리소스 ID를 사용해야 합니다.

![설명 된 예측 리소스 ID 사용 하 여 사용자 지정 비전 웹 사이트 설정 페이지입니다.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> [Azure Portal](https://portal.azure.com)의 Custom Vision 예측 리소스로 이동하고 **속성**을 선택하여 이 정보를 가져올 수도 있습니다.

반복이 게시되면, 앱은 예측 API 호출에서 해당 이름을 지정하여 예측에 사용할 수 있습니다. 예측 호출에 반복을 사용할 수 없도록 하려면, **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** API를 사용합니다.

## <a name="additional-export-options"></a>추가 내보내기 옵션

3.0 API에서는 두 개의 추가 내보내기 대상이 공개됩니다: ARM 아키텍처와 Vision AI 개발자 키트

* ARM을 사용하려면, 소형 도메인을 선택한 다음 DockerFile을 선택하고 내보내기 옵션으로 ARM을 선택합니다.
* 비전 AI 개발 키트를 위해서는 프로젝트는 대상 내보내기 플랫폼 인수로 VAIDK를 지정하는 것 뿐만 아니라 __일반(소형)__ 도메인으로 생성해야 합니다. 

## <a name="next-steps"></a>다음 단계

* [교육 API 참조 설명서 (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [예측 API 참조 설명서 (REST)](https://go.microsoft.com/fwlink/?linkid=865445)
