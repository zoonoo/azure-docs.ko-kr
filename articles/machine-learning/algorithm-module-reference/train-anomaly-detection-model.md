---
title: '변칙 검색 모델 학습: 모듈 참조'
titleSuffix: Azure Machine Learning
description: 변칙 검색 모델 학습 모듈을 사용하여 학습된 변칙 검색 모델을 만드는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: edf35fada4233fbe43bc7f859c2414bfb8130714
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "90905729"
---
# <a name="train-anomaly-detection-model-module"></a>변칙 검색 모델 학습 모듈

이 문서에서는 Azure Machine Learning 디자이너에서 변칙 검색 모델 학습 모듈을 사용하여 학습된 변칙 검색 모델을 만드는 방법을 설명합니다.

모듈은 변칙 검색 모델 및 레이블이 없는 데이터 세트에 대한 매개 변수 세트를 입력으로 사용합니다. 학습 데이터에 대한 레이블 세트와 함께 학습된 변칙 검색 모델을 반환합니다.  

디자이너에서 제공하는 변칙 검색 알고리즘에 대한 자세한 내용은 [PCA 기반 변칙 검색](pca-based-anomaly-detection.md)을 참조하세요.  

## <a name="how-to-configure-train-anomaly-detection-model"></a>변칙 검색 모델 학습을 구성하는 방법 

1.  디자이너의 파이프라인에 **변칙 검색 모델 학습** 모듈을 추가합니다. 이 모듈은 **변칙 검색** 범주에서 찾을 수 있습니다.

2. [PCA 기반 변칙 검색](pca-based-anomaly-detection.md)과 같이 변칙 검색을 위해 설계된 모듈 중 하나를 연결합니다.

    다른 유형의 모델은 지원되지 않습니다. 파이프라인을 실행하면 "모든 모델의 학습자 유형이 동일해야 합니다."라는 오류가 표시됩니다.  

3.  레이블 열을 선택하고 알고리즘과 관련된 다른 매개 변수를 설정하여 변칙 검색 모듈을 구성합니다.  

4.  **변칙 검색 모델 학습** 의 오른쪽 입력에 학습 데이터 세트를 연결합니다.  

5.  파이프라인을 제출합니다.  

## <a name="results"></a>결과

학습 완료 후:

+ 모델의 매개 변수를 보려면 모듈을 마우스 오른쪽 단추로 클릭하고 **시각화** 를 선택합니다. 

+ 예측을 만들려면 새 입력 데이터로 [모델 점수 매기기](score-model.md) 모듈을 사용합니다.

+ 학습된 모델의 스냅샷을 저장하려면 모듈을 선택합니다. 그런 다음, 오른쪽 패널의 **출력 + 로그** 탭 아래에서 **데이터 세트 등록** 아이콘을 선택합니다.   

 
## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 

디자이너 모듈과 관련된 오류 목록은 [디자이너의 예외 및 오류 코드](designer-error-codes.md)를 참조하세요.
'