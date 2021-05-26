---
title: 사용자 지정 모델 - Form Recognizer
titleSuffix: Azure Applied AI Services
description: Form Recognizer API 사용자 지정 모델 - 사용량 및 제한 사항과 관련된 개념에 대해 알아봅니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: lajanuar
ms.openlocfilehash: 329176e40b1a8cf187a4b2154a26b1adbbca4233
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110374735"
---
# <a name="form-recognizer-custom-models"></a>Form Recognizer 사용자 지정 모델

Form Recognizer는 고급 기계 학습 기술을 사용하여 양식과 문서에서 데이터를 분석하고 추출합니다. Form Recognizer 모델은 특정 콘텐츠를 분석하기 위한 참조로 사용되는 추출된 데이터의 표현입니다. Form Recognizer 모델에는 두 가지 유형이 있습니다.

* **사용자 지정 모델**. Form Recognizer 사용자 지정 모델은 비즈니스 관련 _양식_ 에서 추출된 데이터를 나타냅니다. 사용자 지정 모델은 고유한 양식 데이터를 분석하도록 학습되어야 합니다.

* **미리 빌드된 모델**. Form Recognizer는 현재 _영수증, 명함, 신분증_ 및 _송장_ 에 대한 미리 빌드된 모델을 지원합니다. 미리 빌드된 모델은 문서 이미지에서 정보를 검색하여 추출하고, 추출된 데이터를 구조적 JSON 출력으로 반환합니다.

## <a name="what-does-a-custom-model-do"></a>사용자 지정 모델에서 수행하는 작업

Form Recognizer를 사용하면 사용 사례와 관련된 양식에서 정보를 추출하는 모델을 학습시킬 수 있습니다. 시작하려면 동일한 양식 유형의 5개 예제만 있으면 됩니다. 사용자 지정 모델은 레이블이 지정된 데이터 세트를 사용하거나 사용하지 않고 학습될 수 있습니다.

## <a name="create-use-and-manage-your-custom-model"></a>사용자 지정 모델 만들기, 사용 및 관리

사용자 지정 모델을 개략적인 수준에서 빌드, 학습 및 사용하는 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
>[&#120783;. 학습 데이터 세트 조합](build-training-data-set.md#custom-model-input-requirements)

사용자 지정 모델 빌드는 학습 데이터 세트를 설정하는 것으로 시작합니다. 샘플 데이터 세트에 대해 동일한 유형으로 완성된 5개 이상의 양식이 필요합니다. 파일 형식이 다를 수 있으며, 텍스트와 필기를 모두 포함할 수 있습니다. 양식은 동일한 형식의 문서여야 하며 Form Recognizer에 대한 [입력 요구 사항](build-training-data-set.md#custom-model-input-requirements)을 따라야 합니다.  

> [!div class="nextstepaction"]
> [&#120784;. 학습 데이터 세트 업로드](build-training-data-set.md#upload-your-training-data)

학습 데이터를 Azure Blob 스토리지 컨테이너에 업로드해야 합니다. 컨테이너를 사용하여 Azure 스토리지 계정을 만드는 방법을 모르는 경우 [Azure Portal의 Azure Storage 빠른 시작](../../storage/blobs/storage-quickstart-blobs-portal.md)을 *참조하세요*. 체험 가격 책정 계층(F0)을 사용하여 서비스를 사용해 보고, 나중에 프로덕션을 위한 유료 계층으로 업그레이드합니다.  

> [!div class="nextstepaction"]
>[&#120785;. 사용자 지정 모델 학습](quickstarts/client-library.md#train-a-custom-model)

레이블이 지정된 데이터 세트를 사용하거나 사용하지 않고 [모델을 학습시킬 수](quickstarts/client-library.md#train-a-custom-model) 있습니다. 레이블이 지정되지 않은 데이터 세트는 사용자 입력을 추가하지 않고 키 정보를 검색하여 식별하는 레이아웃 API만 사용합니다. 레이블이 지정된 데이터 세트도 레이아웃 API를 사용하지만 사용자의 특정 레이블 및 필드 위치와 같은 추가 사용자 입력이 포함됩니다. 레이블이 지정된 데이터와 레이블이 지정되지 않은 데이터를 모두 사용하려면 레이블이 지정된 학습 데이터에 대해 동일한 유형으로 완성된 5개 이상의 양식으로 시작한 다음, 레이블이 지정되지 않은 데이터를 필요한 데이터 세트에 추가합니다.  

>[!div class="nextstepaction"]
>[&#120786;. 사용자 지정 모델을 사용하여 문서 분석](quickstarts/client-library.md#analyze-forms-with-a-custom-model)

학습 데이터 세트의 일부가 아닌 양식을 사용하여 새로 학습된 모델을 테스트합니다. 더 많은 학습을 계속 수행하여 사용자 지정 모델의 성능을 향상시킬 수 있습니다.  

> [!div class="nextstepaction"]
>[&#120787;. 사용자 지정 모델 관리](quickstarts/client-library.md#manage-custom-models)

언제든지 구독 중인 모든 사용자 지정 모델의 목록을 보거나, 특정 사용자 지정 모델에 대한 정보를 검색하거나, 계정에서 사용자 지정 모델을 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

API 참조 문서를 검색하여 Form Recognizer 클라이언트 라이브러리에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Form Recognizer API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)
>
