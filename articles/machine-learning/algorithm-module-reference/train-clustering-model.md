---
title: '클러스터링 모델 학습: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 클러스터링 모델 학습 모듈을 사용하여 클러스터링 모델을 학습하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/17/2021
ms.openlocfilehash: ea6673a04bf9f5f568c660658e51036f2d2712e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654733"
---
# <a name="train-clustering-model"></a>클러스터링 모델 학습

이 문서에서는 Azure Machine Learning 디자이너의 모듈에 대해 설명합니다.

이 모듈을 사용하여 클러스터링 모델을 학습합니다.

이 모듈은 이미 [K-Means Clustering](k-means-clustering.md) 모듈을 사용하여 구성한 학습되지 않은 클러스터링 모델을 사용하며 레이블이 지정되거나 지정되지 않은 데이터 세트를 사용하여 모델을 학습합니다. 이 모듈은 예측에 사용할 수 있는 학습된 모델과 학습 데이터의 각 사례에 대한 클러스터 할당 집합을 만듭니다.

> [!NOTE]
> 기계 학습 모델 학습을 위한 일반 모듈인 [모델 학습](train-model.md) 모듈을 사용하여 클러스터링 모델을 학습시킬 수 없습니다. [모델 학습](train-model.md)은 감독된 학습 알고리즘에서만 작동하기 때문입니다. K-Means 및 다른 클러스터링 알고리즘을 사용하는 경우 자율 학습을 허용합니다. 즉, 알고리즘이 레이블이 지정되지 않은 데이터를 통해 학습할 수 있습니다.  
  
## <a name="how-to-use-train-clustering-model"></a>클러스터링 모델 학습을 사용하는 방법  

1.  디자이너에서 파이프라인에 **클러스터링 모델 학습** 모듈을 추가합니다. **학습** 범주의 **기계 학습 모듈** 아래에서 모듈을 찾을 수 있습니다.  
  
2. [K-Means Clustering](k-means-clustering.md) 모듈이나 호환되는 클러스터링 모델을 만들고 클러스터링 모델의 매개 변수를 설정하는 다른 사용자 지정 모듈을 추가합니다.  
    
3.  **클러스터링 모델 학습** 의 오른쪽 입력에 학습 데이터 세트를 연결합니다.
  
5.  **열 집합** 에서 클러스터를 작성하는 데 사용할 데이터 세트의 열을 선택합니다. 올바른 기능을 제공하는 열을 선택해야 합니다. 예를 들어 ID 또는 고유한 값이 있는 다른 열을 사용하거나 모든 값이 같은 열을 사용하지 마십시오.

    레이블을 사용할 수 있는 경우 해당 레이블을 기능으로 사용하거나 그대로 둘 수 있습니다.  
  
6. 새 클러스터 레이블과 함께 학습 데이터를 출력하려면 **추가하려면 선택/결과만 출력하려면 선택 취소** 옵션을 선택합니다.

    이 옵션의 선택을 취소하면 클러스터 할당만 출력됩니다. 

7. 파이프라인을 제출하거나 **클러스터링 모델 학습** 모듈을 클릭하고 **선택한 항목 실행** 을 선택합니다.  
  
### <a name="results"></a>결과

학습을 완료한 후:

+ 학습된 모델의 스냅샷을 저장하려면 **모델 학습** 모듈의 오른쪽 패널에서 **출력** 탭을 선택합니다. **데이터 세트 등록** 아이콘을 선택하여 모델을 재사용 가능한 모듈로 저장합니다.

+ 모델에서 점수를 생성하려면 [클러스터에 데이터 할당](assign-data-to-clusters.md)을 사용합니다.

> [!NOTE]
> 디자이너에서 학습된 모델을 배포해야 하는 경우 **모델 점수 매기기** 대신 [클러스터에 데이터 할당](assign-data-to-clusters.md)이 유추 파이프라인에서 [웹 서비스 출력 모듈](web-service-input-output.md)의 입력에 연결되어 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 