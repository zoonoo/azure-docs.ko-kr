---
title: 클러스터링 모델을 학습 합니다. 모듈 참조
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스에서 클러스터링 모델 학습 모듈을 사용 하 여 클러스터링 모델을 학습 하는 방법에 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 41cdec1d7f1c3932b17da6f9b1de518071f3f542
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028082"
---
# <a name="train-clustering-model"></a>클러스터링 모델 학습

이 문서에서는 Azure Machine Learning 서비스에 대 한 시각적 인터페이스 (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 클러스터링 모델을 학습 합니다.

모듈에서는 이미 구성한 경우 사용 하 여 학습 되지 않은 클러스터링 모델을 사용 합니다 [K-means 클러스터링](k-means-clustering.md) 모듈 레이블이 지정 된 또는 레이블이 지정 되지 않은 데이터 집합을 사용 하 여 모델을 학습 하 고 합니다. 모듈, 예측 및 학습 데이터의 각 사례에 대 한 클러스터 할당 집합을 사용할 수 있는 두는 학습 된 모델을 만듭니다.

> [!NOTE]
> 클러스터링 모델은 수 사용 하 여 학습 된 [모델 학습](train-model.md) 모듈은 기계 학습 모델을 학습 하는 것에 대 한 제네릭 모듈입니다. 있기 때문입니다 [모델 학습](train-model.md) 감독 학습 알고리즘에만 작동 합니다. K-means 및 클러스터링 알고리즘도 자율된 학습을 허용 알고리즘은 레이블이 지정 되지 않은 데이터에서 자세히 알아볼 수를 의미 합니다.  
  
## <a name="how-to-use-train-clustering-model"></a>클러스터링 모델 학습을 사용 하는 방법  
  
1.  추가 된 **클러스터링 모델 학습** Studio에서 실험에 모듈입니다. 아래에 있는 모듈을 찾을 수 있습니다 **Machine Learning 모듈**를 **학습** 범주입니다.  
  
2. 추가 된 [K-means 클러스터링](k-means-clustering.md) 모듈 또는 사용자 지정 모듈과 호환 클러스터링 만드는 모델 및 클러스터링 모델의 매개 변수를 설정 합니다.  
    
3.  오른쪽 입력 학습 데이터 집합을 연결할 **클러스터링 모델 학습**합니다.
  
5.  **열 집합**, 클러스터를 만드는 데 사용할 데이터 집합에서 열을 선택 합니다. 좋은 기능 하는 열을 선택 해야 합니다: 예를 들어, Id 또는 다른 고유 값이 있는 열 또는 모두 동일한 값을 포함 하는 열을 사용 하지 마십시오.

    레이블을 사용 가능한 경우 기능으로 사용 하거나 그냥 내버려 두면 됩니다.  
  
6. 옵션을 선택 **만 결과 대 한 추가 또는 선택 취소에 대 한 확인**새 클러스터 레이블 함께 학습 데이터를 출력 하려는 경우.

    이 옵션의 선택을 취소 하면 클러스터 할당만 출력 됩니다. 

7. 실험을 실행 하거나 클릭 하 여 **클러스터링 모델 학습** 모듈을 선택 **선택 항목 실행**합니다.  
  
### <a name="results"></a>결과

학습 완료:


+  데이터 집합의 값을 보려면 모듈을 마우스 오른쪽 단추로 클릭, 선택 **결과 데이터 집합**, 클릭 **시각화**합니다.

+ 이후 다시 사용 하기 위해 학습된 된 모델을 저장 하려면 모듈을 마우스 오른쪽 단추로 클릭, 선택 **Trained 모델**, 클릭 **학습 된 모델을 저장**합니다.

+ 모델에서 점수를 생성 하려면 사용 하 여 [클러스터에 데이터 할당](assign-data-to-clusters.md)합니다.



## <a name="next-steps"></a>다음 단계

참조를 [사용할 수 있는 모듈 집합](module-reference.md) Azure Machine Learning 서비스입니다. 