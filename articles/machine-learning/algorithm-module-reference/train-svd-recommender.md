---
title: 'SVD 추천 학습: 모듈 참조'
titleSuffix: Azure Machine Learning
description: SVD 알고리즘을 사용하여 베이지언 추천을 학습하기 위해 Azure Machine Learning에서 SVD 추천 학습 모듈을 사용하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/17/2021
ms.openlocfilehash: 77407f253bb347160ea331bd7384d8085f21b040
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654461"
---
# <a name="train-svd-recommender"></a>SVD 추천 학습

이 문서에서는 Azure Machine Learning 디자이너에서 SVD 추천 학습 모듈을 사용하는 방법을 설명합니다. 이 모듈을 사용하여 SVD(Single Value Decomposition) 알고리즘에 기반하여 추천 모델을 학습합니다.  

SVD 추천 학습 모듈은 사용자-항목-등급의 삼중 쌍으로 이루어진 데이터 세트를 읽습니다. 이는 학습된 SVD 추천을 반환합니다. 그런 다음 학습된 모델을 사용하여 [SVD 추천 점수](score-svd-recommender.md) 모듈을 연결하여 등급을 예상하거나 추천을 생성합니다.  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>추천 모델 및 SVD 추천에 대한 자세한 내용  

추천 시스템의 주요 목표는 하나 이상의 *항목* 을 시스템의 *사용자* 에게 추천하는 것입니다. 항목은 영화, 식당, 책 또는 노래 등을 예로 들 수 있습니다. 사용자는 개인, 그룹 또는 항목 기본 설정이 있는 다른 엔터티일 수 있습니다.  

추천 시스템에는 두 가지 기본 접근 방식이 있습니다. 

+ **콘텐츠 기반** 접근 방식은 사용자와 항목 모두에 대한 기능을 사용합니다. 사용자는 나이, 성별 등의 속성으로 설명할 수 있습니다. 항목은 작성자, 제조업체 등의 속성으로 설명할 수 있습니다. 콘텐츠 기반 추천 시스템의 일반적인 예는 소셜 매치메이킹 사이트에서 확인할 수 있습니다. 
+ **공동 작업 필터링** 은 사용자 및 항목의 식별자만 사용합니다. 사용자가 항목에 제공한 등급의 (스파스) 매트릭스에서 이러한 엔터티에 대한 암시적 정보를 가져옵니다. 사용자가 평가한 항목과 동일한 항목의 등급을 매긴 다른 사용자를 통해 사용자에 대해 파악할 수 있습니다.  

SVD 추천은 사용자 및 항목의 식별자와 사용자가 항목에 부여하는 등급의 매트릭스를 사용합니다. 이는 *공동 작업 추천* 입니다. 

SVD 추천에 대한 자세한 내용은 관련 연구 논문: [Matrix factorization techniques for recommender systems](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf)를 참조하세요.


## <a name="how-to-configure-train-svd-recommender"></a>SVD 추천 학습을 구성하는 방법  

### <a name="prepare-data"></a>데이터 준비

모듈을 사용하기 전에 입력 데이터는 추천 모델이 예상하는 형식이어야 합니다. 사용자-항목-등급의 삼중 쌍으로 이루어진 학습 데이터 집합이 필요합니다.

+ 첫 번째 열은 사용자 식별자를 포함합니다.
+ 두 번째 열은 항목 식별자를 포함합니다.
+ 세 번째 열은 사용자-항목 쌍의 등급을 포함합니다. 등급 값은 숫자 형식이어야 합니다.  

Azure Machine Learning 디자이너(**데이터 세트** 및 **샘플** 선택)의 **영화 등급** 데이터 세트는 필요한 형식을 보여 줍니다.

![영화 등급](media/module/movie-ratings-dataset.png)

이 샘플에서는 단일 사용자가 여러 영화를 평가한 것을 볼 수 있습니다. 

### <a name="train-the-model"></a>모델 학습

1.  디자이너에서 SVD 추천 학습 모듈을 파이프라인에 추가하고 학습 데이터에 연결합니다.  
   
2.  **요소의 수** 는 추천에 사용할 요소의 수를 지정합니다.  
    
    각 요소는 사용자가 항목과 관련된 정도를 측정합니다. 요소의 수는 잠재 요소 공간의 차원이기도 합니다. 사용자 및 항목 수가 늘어나면 요소를 더 많이 설정하는 것이 좋습니다. 하지만 숫자가 너무 크면 성능이 저하될 수 있습니다.
    
3.  **추천 알고리즘 반복 횟수** 는 알고리즘에서 입력 데이터를 처리해야 하는 횟수를 나타냅니다. 이 수가 높을수록 예측이 더 정확해 집니다. 하지만 숫자가 높으면 학습 속도가 느리다는 뜻입니다. 기본값은 30입니다.

4.  **학습률** 은 학습의 단계 크기를 정의하는 0.0에서 2.0 사이의 숫자를 입력합니다.

    학습 속도는 각 반복의 단계 크기를 결정합니다. 단계 크기가 너무 크면 최적 솔루션을 초과할 수 있습니다. 단계 크기가 너무 작으면 학습이 최적의 솔루션을 찾는 데 걸리는 시간이 길어집니다. 
  
5.  파이프라인을 제출합니다.  

## <a name="results"></a>결과

파이프라인 실행이 완료된 후 점수 매기기에 모델을 사용하려면 [SVD 추천 학습](train-svd-recommender.md)을 [SVD 추천 점수](score-svd-recommender.md)에 연결하여 새 입력 예제에 대한 값을 예측합니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 
