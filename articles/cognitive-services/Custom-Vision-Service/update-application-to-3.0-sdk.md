---
title: 3.0 프로젝트를 마이그레이션하는 방법 API
titlesuffix: Azure Cognitive Services
description: 3.0 이전 버전의 API에서 사용자 지정 비전 프로젝트를 마이그레이션하는 방법을 알아봅니다 API.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: 9dd473aadd7123cafc27209f5c34322fdbcffb71
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044009"
---
# <a name="migrate-to-the-30-api"></a>3.0 마이그레이션 API

Custom Vision 이제 일반 공급에 도달 하 고에 대 한 API 업데이트 되었습니다.
이 업데이트는 몇 가지 새로운 기능 및 중요 한 점은 몇 가지 주요 변경 내용에 포함합니다.

* 예측 API는 이제을 프로젝트 유형을 기반으로 두 개로 분할 됩니다.
* 비전 AI 개발자 키트 (VAIDK) 내보내기 옵션에서는 특정 방식으로 프로젝트를 만들어야 합니다.
* 기본 반복 게시를 위해 제거한/반복을 명명 된 게시를 취소 합니다.

이 가이드에서는 새로운 API 버전을 사용 하 여 프로젝트를 업데이트 하는 방법을 보여 줍니다. 참조를 [릴리스 정보](release-notes.md) 변경 내용의 전체 목록입니다.

## <a name="use-the-updated-prediction-api"></a>업데이트 된 예측 API 사용

이미지 분류자 및 개체 탐지기 프로젝트 모두에 대 한 동일한 예측 호출을 사용 하는 Api 2.x입니다. 두 프로젝트 형식에 따라 허용 된 합니다 **PredictImage** 하 고 **PredictImageUrl** 호출 합니다. 3.0 부터는에서는 분할이 API 호출에 프로젝트 형식과 일치 해야 있도록:

* 사용 하 여 **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** 하 고 **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** 이미지 분류 프로젝트에 대 한 예측을 가져오려고 합니다.
* 사용 하 여 **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** 하 고 **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** 개체 검색 프로젝트에 대 한 예측을 가져오려고 합니다.

## <a name="use-the-new-iteration-publishing-workflow"></a>새 반복 게시 워크플로 사용 합니다.

Api 2.x 하는 데 기본 반복 또는 지정 된 반복 ID 예측에 사용할 반복을 선택 합니다. 3.0 부터는 여기서 처음 게시할 학습 API에서 지정 된 이름으로 반복 하는 게시 흐름을 채택 했습니다 했습니다. 그런 다음 이름을 사용 하는 반복을 지정 하려면 예측 메서드에 전달 합니다.

> [!IMPORTANT]
> 3.0 Api 기본 반복 기능을 사용 하지 마십시오. 이전 Api의 사용을 중단 했습니다 될 때까지 Api 2.x는 기본적으로 반복을 설정/해제 하는 데 계속 수 있습니다. 이러한 Api의 시간 동안 유지 되 고 호출할 수 있습니다 합니다 **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** 기본적으로 반복을 표시 하는 방법입니다.

### <a name="publish-an-iteration"></a>반복에 게시

반복 학습 되 면 하 사용할 수 있도록 예측 사용에 대 한 합니다 **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** 메서드. 반복에 게시 하려면 CustomVision 웹 사이트의 설정 페이지에서 사용할 수 있는 예측 리소스 ID를 사용 해야 합니다.

![설명 된 예측 리소스 ID 사용 하 여 사용자 지정 비전 웹 사이트 설정 페이지입니다.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> 이 정보를 가져올 수도 있습니다는 [Azure Portal](https://portal.azure.com) 사용자 지정 비전 예측 리소스를 이동 하 고 선택 하 여 **속성**합니다.

반복 게시 되 면 앱 사용할 수 예측에 대 한 해당 예측 API 호출의 이름을 지정 합니다. 반복 예측 호출에 사용할 수 없는 사용 합니다 **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** API.

## <a name="additional-export-options"></a>추가 내보내기 옵션

3.0에서는 두 개의 추가 공개 된 Api 내보내기 대상: ARM 아키텍처와 비전 AI 개발자 키트입니다.

* ARM을 사용 하려면 하면 다음 DockerFile을 선택 및 내보내기 옵션으로 다음 ARM Compact 도메인을 선택 합니다.
* 비전 AI 개발 키트용 프로젝트도 만들어야 합니다는 __(Compact) 일반__ VAIDK 대상 지정 뿐만 아니라 도메인 내보내기 플랫폼 인수입니다.

## <a name="next-steps"></a>다음 단계

* [교육 API 참조 설명서 (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [예측 API 참조 설명서 (REST)](https://go.microsoft.com/fwlink/?linkid=865445)