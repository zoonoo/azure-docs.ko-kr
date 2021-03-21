---
title: 사용자 지정 모델-양식 인식기에 대 한 학습 데이터 집합을 작성 하는 방법
titleSuffix: Azure Cognitive Services
description: 학습 데이터 집합이 양식 인식기 모델 학습을 위해 최적화 되어 있는지 확인 하는 방법에 대해 알아봅니다.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: lajanuar
ms.openlocfilehash: b33ac3cb710a2d2a9d92efadf14dc829cb5da6e8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467496"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>사용자 지정 모델에 대 한 학습 데이터 집합 작성

폼 인식기 사용자 지정 모델을 사용 하는 경우 [사용자 지정 모델](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync) 학습 작업에 고유한 학습 데이터를 제공 하 여 모델에서 산업별 양식으로 학습할 수 있습니다. 이 가이드에 따라 데이터를 수집 하 고 준비 하 여 모델을 효과적으로 학습 하는 방법을 알아보세요.

동일한 형식의 채워진 폼이 5 개 이상 있어야 합니다.

수동으로 레이블이 지정 된 학습 데이터를 사용 하려는 경우에는 동일한 형식의 5 개 이상의 채워진 양식으로 시작 해야 합니다. 필요한 데이터 집합 외에도 레이블이 없는 폼을 계속 사용할 수 있습니다.

## <a name="custom-model-input-requirements"></a>사용자 지정 모델 입력 요구 사항

먼저 학습 데이터 집합이 폼 인식기의 입력 요구 사항을 준수 하는지 확인 합니다.

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="training-data-tips"></a>학습 데이터 팁

학습을 위해 데이터 집합을 추가로 최적화 하려면 다음 추가 팁을 따르세요.

* 가능 하면 이미지 기반 문서 대신 텍스트 기반 PDF 문서를 사용 합니다. 스캔 한 Pdf는 이미지로 처리 됩니다.
* 채워진 폼의 경우 모든 필드가 채워진 예제를 사용 합니다.
* 각 필드에 서로 다른 값이 있는 양식을 사용합니다.
* 폼 이미지의 품질이 낮은 경우 더 큰 데이터 집합 (예: 10-15 이미지)을 사용 합니다.

## <a name="upload-your-training-data"></a>학습 데이터 업로드

학습에 사용할 양식 문서 집합을 함께 배치한 경우 Azure blob storage 컨테이너에 업로드 해야 합니다. 컨테이너를 사용 하 여 Azure storage 계정을 만드는 방법을 모를 경우 [에는 Azure Portal에 대 한 Azure Storage 빠른](../../storage/blobs/storage-quickstart-blobs-portal.md)시작을 수행 합니다. 표준 성능 계층을 사용 합니다.

수동으로 레이블이 지정 된 데이터를 사용 하려는 경우 학습 문서에 해당 하는 파일에 *대 한.labels.js* 및 *.ocr.js* 를 업로드 해야 합니다. [샘플 레이블 도구](./quickstarts/label-tool.md) (또는 사용자 고유의 UI)를 사용 하 여 이러한 파일을 생성할 수 있습니다.

### <a name="organize-your-data-in-subfolders-optional"></a>하위 폴더의 데이터 구성 (선택 사항)

기본적으로 [학습 사용자 지정 모델](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync) API는 저장소 컨테이너의 루트에 있는 양식 문서만 사용 합니다. 그러나 API 호출에서 지정 하는 경우 하위 폴더의 데이터로 학습을 수행할 수 있습니다. 일반적으로 [사용자 지정 모델](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync) 호출의 본문에는 다음과 같은 형식이 있습니다. 여기서 `<SAS URL>` 은 컨테이너의 공유 액세스 서명 URL입니다.

```json
{
  "source":"<SAS URL>"
}
```

요청 본문에 다음 콘텐츠를 추가 하면 API가 하위 폴더에 있는 문서를 학습 합니다. `"prefix"`필드는 선택 사항이 며, 경로가 지정 된 문자열로 시작 하는 파일에 대 한 학습 데이터 집합을 제한 합니다. 예를 들어 값을 `"Test"` 사용 하면 API가 "Test" 라는 단어로 시작 되는 파일이 나 폴더만 볼 수 있습니다.

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

* [클라이언트 라이브러리나 REST API 사용 하 여 모델을 학습 하 고 양식 데이터를 추출 합니다.](./quickstarts/client-library.md)
* [샘플 레이블 도구를 사용 하 여 레이블로 학습](./quickstarts/label-tool.md)

## <a name="see-also"></a>참조

* [Form Recognizer란?](./overview.md)