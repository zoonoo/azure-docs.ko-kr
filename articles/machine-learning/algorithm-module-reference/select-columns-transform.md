---
title: '열 변환 선택: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 디자이너에서 열 변환 선택 모듈을 사용하여 선택 변환을 수행하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: f03840e55366d7f105ca4b57bd60061c82833e72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420719"
---
# <a name="select-columns-transform"></a>열 변환 선택

이 문서에서는 Azure Machine Learning 디자이너에서 열 변환 선택 모듈을 사용하는 방법을 설명합니다. 열 변환 선택 모듈의 목적은 다운스트리밍 기계 학습 작업에서 예측 가능하고 일관된 열 집합이 사용되도록 하는 것입니다.

이 모듈은 특정 열이 필요한 점수 매기기와 같은 작업에 유용합니다. 사용 가능한 열을 변경하면 파이프라인이 중단되거나 결과가 변경될 수 있습니다.

열 변환 선택을 사용하여 열 집합을 만들고 저장합니다. 그런 다음 [변환 적용](apply-transformation.md) 모듈을 사용하여 이러한 선택 사항을 새 데이터에 적용합니다.

## <a name="how-to-use-select-columns-transform"></a>열 변환 선택을 사용하는 방법

이 시나리오에서는 기능 선택을 사용하여 모델 학습에 사용할 동적 열 집합을 생성한다고 가정합니다. 점수 매기기 프로세스에서 열 선택이 동일하도록 하려면 열 변환 선택 모듈을 사용하여 열 선택을 캡처하고 파이프라인의 다른 곳에 적용합니다.

1. 디자이너에서 파이프라인에 입력 데이터 세트를 추가합니다.

2. [필터 기반 기능 선택](filter-based-feature-selection.md) 인스턴스를 추가합니다.

3. 모듈을 연결하고 기능 선택 모듈을 구성하여 입력 데이터 세트에서 수많은 최상의 기능을 자동으로 찾도록 합니다.

4. [모델 학습](train-model.md) 인스턴스를 추가하고 [필터 기반 기능 선택](filter-based-feature-selection.md)의 출력을 학습 입력으로 사용합니다.

    > [!IMPORTANT]
    > 기능 중요도는 열의 값을 기반으로 하므로 [모델 학습](train-model.md)에 입력할 수 있는 열을 미리 알 수 없습니다.  
5. 열 변환 선택 모듈의 인스턴스를 연결합니다. 

    이 단계에서는 다른 데이터 세트에 저장하거나 적용할 수 있는 변환으로 열 선택을 생성합니다. 이 단계에서는 기능 선택에서 식별된 열이 다른 모듈이 재사용할 수 있도록 저장되었는지 확인합니다.

6. [모델 점수 매기기](score-model.md) 모듈을 추가합니다. 

   *입력 데이터 세트를 연결하지 마세요.* 대신 [변환 적용](apply-transformation.md) 모듈을 추가하고 기능 선택 변환의 출력을 연결합니다.

   파이프라인 구조는 다음과 같아야 합니다.

   > [!div class="mx-imgBorder"]
   > ![샘플 파이프라인](media/module/filter-based-feature-selection-score.png)

   > [!IMPORTANT]
   > 점수 매기기 데이터 세트에 [필터 기반 기능 선택](filter-based-feature-selection.md)을 적용하고 동일한 결과를 얻을 수는 없습니다. 기능 선택은 값을 기반으로 하기 때문에 다른 열 집합을 선택하여 점수 매기기 작업이 실패할 수 있습니다.
    
7. 파이프라인을 제출합니다.

열 선택을 저장한 다음 적용하는 이 프로세스를 통해 동일한 데이터 스키마를 학습 및 점수 매기기에 사용할 수 있습니다.


## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 
