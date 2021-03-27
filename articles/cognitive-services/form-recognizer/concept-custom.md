---
title: 사용자 지정 모델-폼 인식기
titleSuffix: Azure Cognitive Services
description: 양식 인식기 API 사용자 지정 모델-사용량 및 제한과 관련 된 개념을 알아봅니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: lajanuar
ms.openlocfilehash: 74ced2ecadb5ccfe5cdb7966550e469ae4f8ab31
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612265"
---
# <a name="form-recognizer-custom-models"></a>폼 인식기 사용자 지정 모델

양식 인식기는 고급 기계 학습 기술을 사용 하 여 양식과 문서에서 데이터를 분석 하 고 추출 합니다. 양식 인식기 모델은 추출 된 데이터를 표현 하는 것으로, 특정 콘텐츠를 분석 하기 위한 참조로 사용 됩니다. 폼 인식기 모델에는 다음과 같은 두 가지 유형이 있습니다.

* **사용자 지정 모델**. 양식 인식기 사용자 지정 모델은 사용자의 비즈니스와 관련 된 _양식_ 에서 추출 된 데이터를 나타냅니다. 고유한 양식 데이터를 분석 하려면 사용자 지정 모델을 학습 해야 합니다.

* **미리 빌드된 모델** 양식 인식기는 현재 _수신, 비즈니스 카드, 식별 카드_ 및 _청구서_ 에 대해 미리 작성 한 모델을 지원 합니다. 미리 작성 된 모델은 문서 이미지에서 정보를 검색 및 추출 하 고 추출 된 데이터를 구조적 JSON 출력으로 반환 합니다.

## <a name="what-does-a-custom-model-do"></a>사용자 지정 모델은 무엇을 하나요?

폼 인식기를 사용 하 여 사용 사례와 관련 된 양식에서 정보를 추출 하는 모델을 학습할 수 있습니다. 시작 하려면 동일한 양식 형식에 대 한 5 개의 예제가 필요 합니다. 레이블 데이터 집합을 사용 하거나 사용 하지 않고 사용자 지정 모델을 학습 시킬 수 있습니다.

## <a name="create-use-and-manage-your-custom-model"></a>사용자 지정 모델 만들기, 사용 및 관리

개략적인 수준에서 사용자 지정 모델을 작성, 학습 및 사용 하는 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [1. 학습 데이터 집합을 어셈블합니다.](build-training-data-set.md#custom-model-input-requirements)

사용자 지정 모델 작성은 학습 데이터 집합을 설정 하는 것부터 시작 됩니다. 샘플 데이터 집합에 대해 동일한 형식의 완성 된 최소 5 개 양식이 필요 합니다. 이러한 파일의 형식은 서로 다를 수 있으며 텍스트와 필기를 모두 포함할 수 있습니다. 폼의 문서 형식은 동일 해야 하며 폼 인식기의 [입력 요구 사항을](build-training-data-set.md#custom-model-input-requirements) 따라야 합니다.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;

> [!div class="nextstepaction"]
> [2. 학습 데이터 집합 업로드](build-training-data-set.md#upload-your-training-data)

Azure blob storage 컨테이너에 학습 데이터를 업로드 해야 합니다. 컨테이너를 사용 하 여 Azure storage 계정을 만드는 방법을 모를 경우 [Azure Portal Azure Storage 빠른](../../storage/blobs/storage-quickstart-blobs-portal.md)시작을 *참조* 하세요. 무료 가격 책정 계층 (F0)을 사용 하 여 서비스를 시도 하 고 나중에 프로덕션을 위한 유료 계층으로 업그레이드 합니다.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;
> [!div class="nextstepaction"]
> [3. 사용자 지정 모델 학습](quickstarts/client-library.md#train-a-custom-model)

레이블 데이터 집합 [을 사용](quickstarts/client-library.md#train-a-model-with-labels) [하지 않고](quickstarts/client-library.md#train-a-model-without-labels) 모델을 학습 시킬 수 있습니다. 레이블이 지정 되지 않은 데이터 집합은 사용자 입력을 추가 하지 않고 키 정보를 검색 하 고 식별 하는 레이아웃 API에만 의존 합니다. 또한 레이블이 지정 된 데이터 집합은 레이아웃 API를 사용 하지만 사용자의 특정 레이블 및 필드 위치와 같은 보조 사용자 입력을 포함 합니다. 레이블이 지정 된 데이터와 레이블이 지정 되지 않은 데이터를 모두 사용 하려면 레이블이 지정 된 학습 데이터에 대해 동일한 형식의 5 개 이상의 완성 된 형태를 시작한 후 레이블이 지정 되지 않은 데이터를 필요한 데이터 집합에 추가 합니다.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;  

>[!div class="nextstepaction"]
> [4. 사용자 지정 모델을 사용 하 여 문서 분석](quickstarts/client-library.md#analyze-forms-with-a-custom-model)

학습 데이터 집합의 일부가 아닌 폼을 사용 하 여 새로 학습 된 모델을 테스트 합니다. 사용자 지정 모델의 성능을 향상 시키기 위해 계속 해 서 더 많은 교육을 수행할 수 있습니다.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;

> [!div class="nextstepaction"]
> [5. 사용자 지정 모델 관리](quickstarts/client-library.md#manage-custom-models)

언제 든 지 구독에서 모든 사용자 지정 모델의 목록을 보거나, 특정 사용자 지정 모델에 대 한 정보를 검색 하거나, 사용자 지정 모델을 계정에서 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

API 참조 설명서를 탐색 하 여 양식 인식기 클라이언트 라이브러리에 대해 자세히 알아보세요.
> [!div class="nextstepaction"]
> [양식 인식기 API 참조](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291)
>
