---
title: 모델 훈련 - Custom Translator
titleSuffix: Azure Cognitive Services
description: 모델 훈련은 번역 모델을 빌드할 때 중요한 단계입니다. 훈련은 사용자가 훈련에 대해 선택한 문서를 바탕으로 이루어집니다.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: conceptual
ms.openlocfilehash: 2d9c6a9636629d3ad05d50320e00ed4c4d7f0b83
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389272"
---
# <a name="train-a-model"></a>모델 학습

모델 훈련은 번역 모델을 빌드할 때 중요한 단계입니다. 훈련 없이는 모델을 빌드할 수 없습니다. 훈련은 사용자가 훈련에 대해 선택한 문서를 바탕으로 이루어집니다.

모델을 훈련하려면 다음을 수행합니다.

1.  모델을 빌드하려는 프로젝트를 선택합니다.

2.  프로젝트의 [데이터] 탭에 프로젝트 언어 쌍에 대한 관련 문서가 모두 표시됩니다. 모델 훈련에 사용할 문서를 수동으로 선택합니다. 이 화면에서 훈련, 튜닝, 테스트 문서를 선택할 수 있습니다. 또는 훈련 세트만 선택하고 튜닝 세트와 테스트 세트는 Custom Translator가 생성하도록 할 수도 있습니다.

    -  문서 이름: 문서 이름입니다.

    -  페어링: 이 문서가 병렬 문서인지 단일어 문서인지를 나타냅니다. 현재 단일어 문서에는 훈련이 지원되지 않습니다.

    -  문서 유형: 학습, 튜닝, 테스트 또는 사전일 수 있습니다.

    -  언어 쌍: 프로젝트의 원본 언어와 대상 언어를 보여 줍니다.

    -  원본 문장: 원본 파일에서 추출한 문장의 개수를 표시합니다.

    -  대상 문장: 대상 파일에서 추출한 문장의 개수를 표시합니다.

    ![모델 훈련](media/how-to/how-to-train-model.png)

3.  훈련 단추를 클릭합니다.

4.  대화 상자에서 모델의 이름을 지정합니다.

5.  [모델 훈련]을 클릭합니다.

    ![모델 훈련 대화 상자](media/how-to/how-to-train-model-2.png)

6.  Custom Translator가 훈련을 제출하고 [모델] 탭에 훈련의 상태를 표시합니다.

    ![모델 훈련 페이지](media/how-to/how-to-train-model-3.png)

>[!Note]
>사용자 지정 변환기에에서 언제 든 지 작업 영역 내에서 10 개의 동시 학습을 지원합니다.


## <a name="edit-a-model"></a>모델 편집

[모델 세부 사항] 페이지에서 [편집] 링크를 사용하여 모델을 편집할 수 있습니다.

1.  연필 아이콘을 클릭합니다.

    ![모델 편집](media/how-to/how-to-edit-model.png)

2.  대화 상자에서 다음을 변경합니다.

    1.  모델 이름(필수): 모델에 의미 있는 이름을 지정합니다.

        ![추가 편집 대화 상자](media/how-to/how-to-edit-model-dialog.png)

3.  저장을 클릭합니다.


## <a name="next-steps"></a>다음 단계

- [모델 세부 정보를 보는 방법](how-to-view-model-details.md)을 알아보세요.
