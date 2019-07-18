---
title: 사용자 지정 모델-폼 인식기에 대 한 학습 데이터 집합을 빌드하는 방법
titleSuffix: Azure Cognitive Services
description: 폼 인식기 모델 학습에 최적화 된 학습 데이터 집합을 확인 하는 방법에 알아봅니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 643f0d6dd3ee073bd19f8697346689523032ad9f
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592637"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>학습 데이터 집합을 사용자 지정 모델 빌드

인식기 양식 사용자 지정 모델을 사용 하는 경우 산업별 forms에 모델을 교육할 수 있으므로 고유한 성향 습득 데이터 제공 합니다. 두 가지 입력 형식 및 5 채워진 forms 또는 빈 폼 (파일 이름에 "빈" 단어 포함 해야 함)를 사용 하 여 모델을 학습 시킬 수입니다. 충분 한 채운 양식을 사용 하 여 학습에 하는 경우에 학습 데이터 집합에 빈 양식을 추가 모델의 정확도 개선할 수 있습니다.

## <a name="training-data-tips"></a>학습 데이터 팁

학습에 최적화 된 데이터 집합을 사용 하는 것이 반드시 합니다. 다음 팁을 했는지에서 최상의 결과 얻는 사용 합니다 [모델 학습](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) 작업:

* 가능한 경우 이미지 기반 문서 대신 텍스트를 기반으로 PDF 문서를 사용 합니다. 스캔 한 Pdf 이미지로 처리 됩니다.
* 사용할 수 있는 경우 하나의 빈 양식 및 양식 채워진 두를 사용 합니다.
* 채워진 forms에 대 한 모든 입력 필드에 있는 예제를 사용 합니다.
* 각 필드에 다른 값을 사용 하 여 forms를 사용 합니다.
* 폼 이미지 품질이 경우 큰 데이터 집합 (예: 10 ~ 15 이미지)를 사용 합니다.

## <a name="general-input-requirements"></a>일반 입력된 요구 사항

학습 데이터 집합에는 또한 모든 폼 인식기 콘텐츠에 대 한 입력된 요구 사항을 따르는 있는지 확인 합니다. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>학습 데이터를 업로드 합니다.

학습에 사용 하는 폼 문서 집합과 함께 배치 했으므로, Azure blob storage 컨테이너에 업로드 해야 합니다. 컨테이너를 사용 하 여 Azure storage 계정을 만드는 방법, 모르는 경우 다음 합니다 [Azure portal의 Azure Storage 빠른 시작](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)합니다.

### <a name="organize-your-data-in-subfolders-optional"></a>하위 폴더 (선택 사항) 데이터 구성

기본적으로 [모델 학습](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) API만 저장소 컨테이너의 루트에 있는 형식 문서를 사용 합니다. 그러나 API 호출에서 지정 하는 경우 하위 폴더에 있는 데이터로 학습 시킬 수 있습니다. 일반적으로 본문을 [모델 학습](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) 호출에는 다음 폼에 있는 `<SAS URL>` 컨테이너의 공유 액세스 서명 URL은:

```json
{
  "source":"<SAS URL>"
}
```

요청 본문에 다음 콘텐츠를 추가 하는 경우 하위 폴더에 있는 문서를 사용 하 여 API를 학습할 수 있습니다. `"prefix"` 필드는 선택 사항이 며 경로가 지정 된 문자열로 시작 하는 파일에 학습 데이터 집합을 제한 합니다. 따라서 값 `"Test"`, 예를 들어, 파일 또는 폴더 "Test" 라는 단어로 시작 하는 확인 하는 API를 발생 합니다.

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  }
}
```

## <a name="next-steps"></a>다음 단계

이제 학습 데이터 집합을 빌드하는 방법을 배웠으므로 사용자 지정 양식 인식기 모델을 학습 하 고 폼의 사용을 시작 하는 빠른 시작을 수행 합니다.

* [빠른 시작: 모델을 학습 하 고 cURL을 사용 하 여 폼 데이터를 추출 합니다.](./quickstarts/curl-train-extract.md)
* [빠른 시작: 모델을 학습 하 고 Python을 사용 하 여 REST API를 사용 하는 양식 데이터를 추출 합니다.](./quickstarts/python-train-extract.md)

