---
title: 사용자 지정 모델-양식 인식기에 대 한 학습 데이터 집합을 작성 하는 방법
titleSuffix: Azure Cognitive Services
description: 학습 데이터 집합이 양식 인식기 모델 학습을 위해 최적화 되어 있는지 확인 하는 방법에 대해 알아봅니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 71ad7c5dd3ad74082da552cd3c45142bc0c2d624
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "75380629"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>사용자 지정 모델에 대 한 학습 데이터 집합 작성

폼 인식기 사용자 지정 모델을 사용 하는 경우 모델에서 산업별 양식으로 교육을 할 수 있도록 고유한 학습 데이터를 제공 합니다. 5 개의 채워진 폼 또는 빈 양식 (파일 이름에 "empty" 라는 단어를 포함 해야 함)을 사용 하 여 모델을 학습 하 고 두 개의 채워진 폼을 사용할 수 있습니다. 학습을 위해 채워진 폼이 충분 한 경우에도 학습 데이터 집합에 빈 양식을 추가 하면 모델의 정확도를 향상 시킬 수 있습니다.

수동으로 레이블이 지정 된 학습 데이터를 사용 하려는 경우에는 동일한 형식의 5 개 이상의 양식으로 시작 해야 합니다. 동일한 데이터 집합에서 레이블이 없는 폼과 빈 폼을 계속 사용할 수 있습니다.

## <a name="training-data-tips"></a>학습 데이터 팁

학습에 최적화 된 데이터 집합을 사용 하는 것이 중요 합니다. 다음 팁을 사용 하 여 [사용자 지정 모델 학습](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) 작업에서 최상의 결과를 얻을 수 있도록 합니다.

* 가능 하면 이미지 기반 문서 대신 텍스트 기반 PDF 문서를 사용 합니다. 스캔 한 Pdf는 이미지로 처리 됩니다.
* 채워진 폼의 경우 모든 필드가 채워진 예제를 사용 합니다.
* 각 필드에 서로 다른 값이 있는 양식을 사용합니다.
* 폼 이미지의 품질이 낮은 경우 더 큰 데이터 집합 (예: 10-15 이미지)을 사용 합니다.
* 학습 데이터 집합의 총 크기는 최대 500 페이지가 될 수 있습니다.

## <a name="general-input-requirements"></a>일반 입력 요구 사항

학습 데이터 집합도 모든 폼 인식기 콘텐츠에 대 한 입력 요구 사항을 준수 하는지 확인 합니다. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>학습 데이터 업로드

학습에 사용할 양식 문서 집합을 함께 배치한 경우 Azure blob storage 컨테이너에 업로드 해야 합니다. 컨테이너를 사용 하 여 Azure storage 계정을 만드는 방법을 모를 경우 [에는 Azure Portal에 대 한 Azure Storage 빠른](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)시작을 수행 합니다.

### <a name="organize-your-data-in-subfolders-optional"></a>하위 폴더의 데이터 구성 (선택 사항)

기본적으로 [학습 사용자 지정 모델](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) API는 저장소 컨테이너의 루트에 있는 양식 문서만 사용 합니다. 그러나 API 호출에서 지정 하는 경우 하위 폴더의 데이터로 학습을 수행할 수 있습니다. 일반적으로 [사용자 지정 모델](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) 호출의 본문에는 다음과 같은 형식이 있습니다. 여기서 `<SAS URL>` 은 컨테이너의 공유 액세스 서명 URL입니다.

```json
{
  "source":"<SAS URL>"
}
```

요청 본문에 다음 콘텐츠를 추가 하면 API가 하위 폴더에 있는 문서를 학습 합니다. 필드 `"prefix"` 는 선택 사항이 며, 경로가 지정 된 문자열로 시작 하는 파일에 대 한 학습 데이터 집합을 제한 합니다. 예를 들어 값 `"Test"`을 사용 하면 API가 "Test" 라는 단어로 시작 되는 파일이 나 폴더만 볼 수 있습니다.

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

학습 데이터 집합을 작성 하는 방법을 배웠으므로 이제 빠른 시작을 따라 사용자 지정 양식 인식기 모델을 학습 하 고 폼에서 사용 하기 시작 합니다.

* [퀵 스타트: 모델 학습 및 말아 넘기기를 사용 하 여 양식 데이터 추출](./quickstarts/curl-train-extract.md)
* [빠른 시작: Python을 사용 하 여 모델을 학습 하 고 양식 데이터를 추출 합니다. REST API](./quickstarts/python-train-extract.md)
* [REST API 및 Python을 사용하여 레이블로 학습](./quickstarts/python-labeled-data.md)