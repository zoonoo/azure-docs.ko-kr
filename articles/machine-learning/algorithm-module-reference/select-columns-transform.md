---
title: '열 변환 선택: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning의 열 변형 선택 모듈을 사용 하 여 지정 된 데이터 집합에서와 동일한 열의 하위 집합을 선택 하는 변환을 만드는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: b3a0b904d65e6597c058ccf05ec837696e9ca20e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90893632"
---
# <a name="select-columns-transform"></a>열 변환 선택

이 문서에서는 Azure Machine Learning 디자이너에서 열 변환 선택 모듈을 사용 하는 방법을 설명 합니다. 열 변환 선택 모듈의 목적은 다운스트림 기계 학습 작업에서 예측 가능 하 고 일관성 있는 열 집합이 사용 되도록 하는 것입니다.

이 모듈은 특정 열이 필요한 점수 매기기 등의 작업에 유용 합니다. 사용 가능한 열을 변경 하면 파이프라인이 손상 되거나 결과가 변경 될 수 있습니다.

열 선택 변환을 사용 하 여 열 집합을 만들고 저장할 수 있습니다. 그런 다음 [변환 적용](apply-transformation.md) 모듈을 사용 하 여 이러한 선택 항목을 새 데이터에 적용 합니다.

## <a name="how-to-use-select-columns-transform"></a>열 선택 변환 사용 방법

이 시나리오에서는 기능 선택을 사용 하 여 모델을 학습 하는 데 사용 되는 동적 열 집합을 생성 하 려 한다고 가정 합니다. 점수 매기기 프로세스에서 열 선택이 동일한 지 확인 하려면 열 변환 선택 모듈을 사용 하 여 열 선택 항목을 캡처하고 파이프라인의 다른 위치에 적용 합니다.

1. 디자이너에서 파이프라인에 입력 데이터 집합을 추가 합니다.

2. [필터 기반 기능 선택](filter-based-feature-selection.md)의 인스턴스를 추가 합니다.

3. 모듈을 연결 하 고 기능 선택 모듈을 구성 하 여 입력 데이터 집합에서 여러 가지 최상의 기능을 자동으로 찾습니다.

4. 학습 [모델](train-model.md) 의 인스턴스를 추가 하 고 [필터 기반 기능 선택](filter-based-feature-selection.md) 의 출력을 학습의 입력으로 사용 합니다.

    > [!IMPORTANT]
    > 기능 중요도는 열의 값을 기반으로 하기 때문에 [모델 학습](train-model.md)을 위해 입력에 사용할 수 있는 열을 미리 알 수 없습니다.  
5. 열 선택 변환 모듈의 인스턴스를 연결 합니다. 

    이 단계에서는 다른 데이터 집합에 저장 하거나 적용할 수 있는 변환으로 열 선택 항목을 생성 합니다. 이 단계를 수행 하면 기능 선택에서 식별 된 열이 다른 모듈에서 다시 사용할 수 있도록 저장 됩니다.

6. [모델 점수 매기기](score-model.md) 모듈을 추가 합니다. 

   *입력 데이터 집합을 연결 하지 마십시오.* 대신, [변환 적용](apply-transformation.md) 모듈을 추가 하 고 기능 선택 변환의 출력을 연결 합니다.

   > [!IMPORTANT]
   > [필터 기반 기능 선택](filter-based-feature-selection.md) 을 점수 매기기 데이터 집합에 적용 하 고 동일한 결과를 얻을 수는 없습니다. 기능 선택은 값을 기반으로 하기 때문에 다른 열 집합을 선택할 수 있으며,이로 인해 점수 매기기 작업이 실패 합니다.
7. 파이프라인을 제출합니다.

열 선택을 저장 하 고 적용 하면 동일한 데이터 스키마를 학습 및 평가에 사용할 수 있습니다.


## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 
