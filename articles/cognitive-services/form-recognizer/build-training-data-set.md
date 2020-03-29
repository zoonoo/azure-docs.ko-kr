---
title: 사용자 지정 모델에 대한 학습 데이터 집합을 빌드하는 방법 - 양식 인식기
titleSuffix: Azure Cognitive Services
description: 양식 인식기 모델 교육에 교육 데이터 집합이 최적화되었는지 확인하는 방법을 알아봅니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 71ad7c5dd3ad74082da552cd3c45142bc0c2d624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75380629"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>사용자 지정 모델에 대한 학습 데이터 세트 빌드

양식 인식기 사용자 지정 모델을 사용하는 경우 모델이 산업별 양식으로 학습할 수 있도록 사용자 고유의 학습 데이터를 제공합니다. 5개의 채워진 양식 또는 빈 양식(파일 이름에 "empty"이라는 단어를 포함해야 합니다)과 두 개의 채워진 양식으로 모델을 학습할 수 있습니다. 입력 양식을 충분히 사용하여 학습하더라도 학습 데이터 집합에 빈 양식을 추가하면 모델의 정확도가 향상될 수 있습니다.

수동으로 레이블이 지정된 학습 데이터를 사용하려면 동일한 형식의 5개 이상의 형태로 시작해야 합니다. 레이블이 지정되지 않은 양식과 빈 양식을 동일한 데이터 집합에서 계속 사용할 수 있습니다.

## <a name="training-data-tips"></a>교육 데이터 팁

학습에 최적화된 데이터 집합을 사용하는 것이 중요합니다. 다음 팁을 사용하여 [Train 사용자 지정 모델](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) 작업에서 최상의 결과를 얻을 수 있습니다.

* 가능하면 이미지 기반 문서 대신 텍스트 기반 PDF 문서를 사용합니다. 스캔한 PDF는 이미지로 처리됩니다.
* 채워진 양식의 경우 모든 필드가 채워진 예제를 사용합니다.
* 각 필드에 서로 다른 값이 있는 양식을 사용합니다.
* 양식 이미지의 품질이 낮은 경우 더 큰 데이터 집합(예: 10~15개 이미지)을 사용합니다.
* 학습 데이터 집합의 총 크기는 최대 500페이지입니다.

## <a name="general-input-requirements"></a>일반 입력 요구 사항

교육 데이터 집합이 모든 Form 인식기 콘텐츠에 대한 입력 요구 사항도 준수하는지 확인합니다. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>교육 데이터 업로드

학습에 사용할 양식 문서 집합을 함께 넣은 경우 Azure Blob 저장소 컨테이너에 업로드해야 합니다. 컨테이너를 사용하여 Azure 저장소 계정을 만드는 방법을 모르는 경우 [Azure Portal에 대한 Azure 저장소 빠른 시작에](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)따라 .

### <a name="organize-your-data-in-subfolders-optional"></a>하위 폴더에서 데이터 구성(선택 사항)

기본적으로 Train [사용자 지정 모델](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) API는 저장소 컨테이너의 루트에 있는 양식 문서만 사용합니다. 그러나 API 호출에서 데이터를 지정하는 경우 하위 폴더의 데이터로 학습할 수 있습니다. 일반적으로 Train 사용자 [지정 모델](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) 호출의 `<SAS URL>` 본문에는 컨테이너의 공유 액세스 서명 URL인 다음 양식이 있습니다.

```json
{
  "source":"<SAS URL>"
}
```

요청 본문에 다음 콘텐츠를 추가하면 API는 하위 폴더에 있는 문서로 학습합니다. 필드는 `"prefix"` 선택 사항이며 지정된 문자열로 경로가 시작되는 파일로 학습 데이터 집합을 제한합니다. 예를 들어 `"Test"`의 값으로 인해 API는 "Test"라는 단어로 시작하는 파일이나 폴더만 보게 됩니다.

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>다음 단계

이제 학습 데이터 집합을 빌드하는 방법을 배웠으니 빠른 시작을 따라 사용자 지정 양식 인식기 모델을 학습하고 양식에서 사용하기 시작합니다.

* [빠른 시작: cURL을 사용하여 모델 학습 및 양식 데이터 추출](./quickstarts/curl-train-extract.md)
* [빠른 시작: 파이썬을 사용하여 REST API를 사용하여 모델을 학습하고 양식 데이터를 추출합니다.](./quickstarts/python-train-extract.md)
* [REST API 및 Python을 사용하여 레이블로 학습](./quickstarts/python-labeled-data.md)