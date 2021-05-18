---
title: 모델 훈련 - Custom Translator
titleSuffix: Azure Cognitive Services
description: 모델 훈련은 번역 모델을 빌드할 때 중요한 단계입니다. 훈련은 사용자가 훈련에 대해 선택한 문서를 바탕으로 이루어집니다.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 5c17f842b93fb99a6a06b94f84ae26126794b776
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98896429"
---
# <a name="train-a-model"></a>모델 학습

모델 학습은 번역 모델을 빌드하기 위한 첫 번째 단계이자 가장 중요한 단계입니다. 학습 없이는 모델을 빌드할 수 없습니다. 훈련은 사용자가 훈련에 대해 선택한 문서를 바탕으로 이루어집니다. "학습" 문서 유형의 문서를 선택할 때 최소 요구 사항이 병렬 문장 10,000개라는 점에 유의하세요. 문서를 선택하면 총 학습 문장 수가 표시됩니다. 모델 학습을 위해 사전 문서 유형의 문서만 선택하는 경우에는 이 요구 사항이 적용되지 않습니다.

모델을 훈련하려면 다음을 수행합니다.

1. 모델을 빌드하려는 프로젝트를 선택합니다.

2. 프로젝트의 [데이터] 탭에 프로젝트 언어 쌍에 대한 관련 문서가 모두 표시됩니다. 모델 훈련에 사용할 문서를 수동으로 선택합니다. 이 화면에서 훈련, 튜닝, 테스트 문서를 선택할 수 있습니다. 또는 훈련 세트만 선택하고 튜닝 세트와 테스트 세트는 Custom Translator가 생성하도록 할 수도 있습니다.

    - 문서 이름: 문서의 이름입니다.

    - 페어링: 병렬 문서인지 단일어 문서인지를 나타냅니다. 현재 단일어 문서에는 훈련이 지원되지 않습니다.

    - 문서 유형: 훈련, 튜닝, 테스트 또는 사전일 수 있습니다.

    - 언어 쌍: 프로젝트의 원본 언어와 대상 언어를 보여 줍니다.

    - 원본 문장: 원본 파일에서 추출한 문장의 개수를 표시합니다.

    - 대상 문장: 대상 파일에서 추출한 문장의 개수를 표시합니다.

    ![모델 학습](media/how-to/how-to-train-model.png)

3. "모델 만들기" 단추를 클릭합니다.

4. 대화 상자에서 모델의 이름을 지정합니다. 기본적으로 "모델 만들기" 단추를 클릭하면 학습 파이프라인을 시작할 수 있도록 "즉시 학습"이 선택됩니다. "초안으로 저장"을 선택하면 모델 메타데이터를 만들고 모델을 초안 상태로 만들 수 있지만 모델 학습은 시작되지 않습니다. 나중에 초안 상태의 모델을 수동으로 선택하여 학습시켜야 합니다.

5. "모델 만들기" 단추를 클릭합니다.

    ![모델 훈련 대화 상자](media/how-to/how-to-train-model-2.png)

6. Custom Translator가 훈련을 제출하고 모델 탭에 훈련의 상태를 표시합니다.

    ![모델 훈련 페이지](media/how-to/how-to-train-model-3.png)

>[!Note]
>Custom Translator는 언제든지 작업 영역 내에서 10개의 동시 학습을 지원합니다.

## <a name="modify-a-model-name"></a>모델 이름 수정

모델 세부 정보 페이지에서 모델 이름을 수정할 수 있습니다.

1. 프로젝트 페이지에서 모델이 있는 프로젝트 이름을 클릭합니다.
2. 모델 탭을 클릭합니다.
3. 모델 이름을 클릭하면 모델 세부 정보가 표시됩니다.
4. 연필 아이콘을 클릭합니다.

    ![모델 편집](media/how-to/how-to-edit-model.png)

5. 대화 상자에서 모델 이름을 변경하고 모델에 의미 있는 이름을 지정합니다.

    ![추가 편집 대화 상자](media/how-to/how-to-edit-model-dialog.png)

6. 저장을 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [모델 세부 정보를 보는 방법](how-to-view-model-details.md)을 알아보세요.
