---
title: '.SVD 추천 학습: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning의 학습 .SVD 추천 모듈을 사용 하 여 .SVD 알고리즘을 사용 하 여 Bayesian 추천를 학습 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 417ef716c391126982809574fc16f6681115cac3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "79477411"
---
# <a name="train-svd-recommender"></a>SVD 추천 학습

이 문서에서는 Azure Machine Learning 디자이너 (미리 보기)에서 학습 .SVD 추천 모듈을 사용 하는 방법을 설명 합니다. 이 모듈을 사용 하 여 .SVD (단일 값 분해) 알고리즘을 기반으로 추천 모델을 학습 합니다.  

학습 .SVD 추천 모듈은 사용자-항목-등급 삼중 쌍의 데이터 집합을 읽습니다. 학습 된 .SVD 추천를 반환 합니다. 그런 다음 [점수 .Svd 추천](score-svd-recommender.md) 모듈을 사용 하 여 학습 된 모델을 사용 하 여 등급을 예측 하거나 권장 구성을 생성할 수 있습니다.  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>권장 사항 모델 및 .SVD 추천에 대 한 자세한 정보  

권장 사항 시스템의 주요 목적은 시스템 *사용자* 에 게 하나 이상의 *항목* 을 권장 하는 것입니다. 항목의 예로 영화, 식당, 책 또는 노래가 있을 수 있습니다. 사용자는 개인, 그룹 또는 항목 기본 설정이 있는 다른 엔터티 일 수 있습니다.  

추천 시스템에는 다음과 같은 두 가지 주요 방법이 있습니다. 

+ **내용 기반** 접근 방식에서는 사용자와 항목 모두에 기능을 사용 합니다. 사용자는 나이, 성별 등의 속성으로 설명할 수 있습니다. 항목은 author 및 제조업체와 같은 속성으로 설명할 수 있습니다. 소셜 matchmaking 사이트에서 콘텐츠 기반 추천 시스템의 일반적인 예를 찾을 수 있습니다. 
+ **공동 작업 필터링** 은 사용자 및 항목의 식별자만 사용 합니다. 사용자가 항목에 제공한 등급의 (스파스) 행렬에서 이러한 엔터티에 대 한 암시적 정보를 가져옵니다. 등급을 매긴 항목과 동일한 항목의 등급을 지정한 다른 사용자 로부터 사용자에 대해 알아볼 수 있습니다.  

.SVD 추천는 사용자 및 항목의 식별자와 사용자가 항목에 제공 하는 등급의 매트릭스를 사용 합니다. *공동 작업 추천*. 

.SVD 추천에 대 한 자세한 내용은 관련 연구 백서: [추천 systems에 대 한 Matrix 인수분해 기법](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf)을 참조 하세요.


## <a name="how-to-configure-train-svd-recommender"></a>.SVD 추천 학습을 구성 하는 방법  

### <a name="prepare-data"></a>데이터 준비

모듈을 사용 하기 전에 입력 데이터가 추천 모델에 필요한 형식 이어야 합니다. 사용자-항목-등급 삼중 쌍의 학습 데이터 집합이 필요 합니다.

+ 첫 번째 열에는 사용자 식별자가 포함 됩니다.
+ 두 번째 열에는 항목 식별자가 포함 됩니다.
+ 세 번째 열은 사용자-항목 쌍의 등급을 포함 합니다. 등급 값은 숫자 형식 이어야 합니다.  

Azure Machine Learning 디자이너 ( **데이터 집합** 및 **샘플**)의 **영화 등급** 데이터 집합은 필요한 형식을 보여 줍니다.

![영화 등급](media/module/movie-ratings-dataset.png)

이 샘플에서는 단일 사용자가 여러 영화를 평가한 것을 볼 수 있습니다. 

### <a name="train-the-model"></a>모델 학습

1.  Designer에서 .SVD 추천 모듈을 파이프라인에 추가 하 고 학습 데이터에 연결 합니다.  
   
2.  **요소 수**에 대해 추천에 사용할 요소 수를 지정 합니다.  
    
    각 요소는 사용자가 항목과 관련 된 양을 측정 합니다. 요소 수는 잠재적 요소 공간의 차원 이기도 합니다. 사용자 및 항목 수가 늘어나면 더 많은 요소를 설정 하는 것이 좋습니다. 그러나 숫자가 너무 크면 성능이 저하 될 수 있습니다.
    
3.  **권장 사항 알고리즘 반복 횟수** 알고리즘에서 입력 데이터를 처리 해야 하는 횟수를 나타냅니다. 이 수가 높을수록 예측이 더 정확해 집니다. 그러나 숫자가 높으면 학습 속도가 느려집니다. 기본값은 30입니다.

4.  학습 **률**에 대해 학습을 위한 단계 크기를 정의 하는 0.0 ~ 2.0 사이의 숫자를 입력 합니다.

    학습 속도는 각 반복의 단계 크기를 결정 합니다. 단계 크기가 너무 크면 최적의 솔루션을 과도 하 게 사용할 수 있습니다. 단계 크기가 너무 작은 경우 학습을 통해 최상의 솔루션을 찾는 데 시간이 오래 걸립니다. 
  
5.  파이프라인을 제출합니다.  


## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 
