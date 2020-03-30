---
title: '클러스터링 모델 학습: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure 기계 학습에서 클러스터링 모델 학습 을 사용하여 클러스터링 모델을 학습하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c29baf3cdda998a2ab78c84f3311b84d37086bcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477428"
---
# <a name="train-clustering-model"></a>클러스터링 모델 학습

이 문서에서는 Azure 기계 학습 디자이너(미리 보기)의 모듈에 대해 설명합니다.

이 모듈을 사용하여 클러스터링 모델을 학습합니다.

이 모듈은 [K-Means 클러스터링](k-means-clustering.md) 모듈을 사용하여 이미 구성한 학습되지 않은 클러스터링 모델을 사용하고 레이블이 지정되거나 레이블이 지정되지 않은 데이터 집합을 사용하여 모델을 학습합니다. 이 모듈은 예측에 사용할 수 있는 학습된 모델과 학습 데이터의 각 사례에 대한 클러스터 할당 집합을 모두 만듭니다.

> [!NOTE]
> 클러스터링 모델은 기계 학습 모델 학습을 위한 일반 모듈인 [Train Model](train-model.md) 모듈을 사용하여 학습할 수 없습니다. [기차 모델은](train-model.md) 감독 학습 알고리즘에서만 작동하기 때문입니다. K-means 및 기타 클러스터링 알고리즘을 사용하면 자율 학습이 허용되므로 알고리즘이 레이블이 지정되지 않은 데이터에서 학습할 수 있습니다.  
  
## <a name="how-to-use-train-clustering-model"></a>기차 클러스터링 모델 사용 방법  

1.  디자이너의 파이프라인에 **클러스터링 모델 학습** 도구를 추가합니다. **기계** 학습 모듈 에서 모듈을 찾을 수 **있습니다.**  
  
2. [K-평균 클러스터링](k-means-clustering.md) 모듈 또는 호환되는 클러스터링 모델을 만드는 다른 사용자 지정 모듈을 추가하고 클러스터링 모델의 매개 변수를 설정합니다.  
    
3.  학습 데이터 집합을 **학습 클러스터링 모델의**오른쪽 입력에 연결합니다.
  
5.  **열 집합에서**클러스터 작성에 사용할 데이터 집합의 열을 선택합니다. 좋은 기능을 만드는 열(예: 고유 값이 있는 아이디 또는 다른 열또는 값이 모두 같은 열)을 선택해야 합니다.

    레이블을 사용할 수 있는 경우 피쳐로 사용하거나 그대로 둘 수 있습니다.  
  
6. 옵션을 **선택하거나, 부속을 확인하거나,** 새 클러스터 레이블과 함께 학습 데이터를 출력하려는 경우에만 결과를 선택 취소합니다.

    이 옵션을 선택 취소하면 클러스터 할당만 출력됩니다. 

7. 파이프라인을 제출하거나 **클러스터링 모델 학습** 모듈을 클릭하고 **선택된 실행을**선택합니다.  
  
### <a name="results"></a>결과

교육이 완료된 후:

+ 학습된 모델의 스냅샷을 저장하려면 **학습 모델** 모듈의 오른쪽 패널에서 **출력** 탭을 선택합니다. 모델을 재사용 가능한 모듈로 저장하려면 **데이터 집합 등록** 아이콘을 선택합니다.

+ 모델에서 점수를 생성하려면 [클러스터에 데이터 할당을](assign-data-to-clusters.md)사용합니다.

## <a name="next-steps"></a>다음 단계

Azure 기계 학습에 사용할 수 있는 [모듈 집합을](module-reference.md) 참조하십시오. 