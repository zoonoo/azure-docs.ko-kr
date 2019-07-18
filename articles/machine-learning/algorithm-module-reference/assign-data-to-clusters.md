---
title: 클러스터에 데이터를 할당 합니다. 모듈 참조
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스에서 할당 데이터를 클러스터 모듈을 사용 하 여 클러스터링 모델 점수 매기기 방법에 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1c2d2a02ecfb617551dd9174b87f363d57b151a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65467210"
---
# <a name="module-assign-data-to-clusters"></a>모듈: 클러스터에 데이터 할당

이 문서에서는 사용 하는 방법을 설명 합니다 *클러스터에 데이터 할당* Azure Machine Learning 시각적 인터페이스에는 모듈입니다. 모듈을 사용 하 여 학습 된 클러스터링 모델을 통해 예측을 생성 합니다 *k-means 클러스터링* 알고리즘입니다.

할당 데이터를 클러스터 모듈 각 새 데이터 요소에 대 한 예상 할당을 포함 하는 데이터 집합을 반환 합니다. 


## <a name="how-to-use-assign-data-to-clusters"></a>클러스터에 데이터 할당을 사용 하는 방법
  
1. Azure Machine Learning 시각적 인터페이스에서 이전에 학습된 된 클러스터링 모델을 찾습니다. 수 만들고 다음 방법 중 하나를 사용 하 여 클러스터링 모델을 학습 합니다.  
  
    - K-means 클러스터링 알고리즘을 사용 하 여 구성 합니다 [K-means 클러스터링](k-means-clustering.md) 모듈 및 데이터 집합 및 클러스터링 모델 학습 모듈 (이 문서)를 사용 하 여 모델을 학습 합니다.  
  
    - 기존의 학습 된 클러스터링 모델을 추가할 수도 있습니다는 **모델 저장** 그룹 작업 영역에 있습니다.

2. 학습된 된 모델의 왼쪽된 입력된 포트에 연결 **클러스터에 데이터 할당**합니다.  

3. 새 데이터 집합을 입력으로 연결 합니다. 

   이 데이터 집합에 레이블은 선택적입니다. 일반적으로 클러스터링는 자율된 학습 메서드입니다. 범주를 미리 알고 되지 있습니다. 그러나 입력된 열에는 클러스터링 모델 또는 오류가 발생 하는 학습에 사용 된 열과 동일 하 여야 합니다.

    > [!TIP]
    > 사용 하 여 클러스터 예측에서 인터페이스에 기록 되는 열의 수를 줄이려면 [데이터 집합의 열 선택](select-columns-in-dataset.md), 열의 하위 집합을 선택 하 고 합니다. 
    
4. 유지 된 **만 결과 대 한 추가 또는 선택 취소에 대 한 확인** 확인란을 선택한 결과 전체 입력된 데이터 집합 (클러스터 할당) 결과 표시 하는 열을 포함 하 여 포함 하려는 경우.
  
    이 확인란의 선택을 취소 하면 결과만 반환 됩니다. 웹 서비스의 일부로 예측을 만들 때이 옵션이 유용할 수 있습니다.
  
5.  실험을 실행합니다.  
  
### <a name="results"></a>결과

+  데이터 집합의 값을 보려면 모듈을 마우스 오른쪽 단추로 클릭, 선택 **결과 데이터 집합**를 선택한 후 **시각화**합니다.

