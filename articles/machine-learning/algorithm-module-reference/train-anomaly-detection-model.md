---
title: '변칙 검색 모델 학습: 모듈 참조'
titleSuffix: Azure Machine Learning
description: 학습 변칙 검색 모델 모듈을 사용하여 학습된 변칙 검색 모델을 만드는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 78ed2d85729cce94e8dfa579545f558d2cfe4651
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502972"
---
# <a name="train-anomaly-detection-model"></a>이상 탐지 모델 학습

이 문서에서는 Azure 기계 학습 디자이너(미리 보기)에서 **변칙 검색 모델 학습** 모듈을 사용하여 학습된 변칙 검색 모델을 만드는 방법에 대해 설명합니다.

모듈은 변칙 검색 모델및 레이블이 지정되지 않은 데이터 집합에 대한 모델 매개 변수 집합을 입력으로 사용합니다. 학습된 변칙 검색 모델과 학습 데이터에 대한 레이블 집합을 반환합니다.  

디자이너에서 제공하는 변칙 검색 알고리즘에 대한 자세한 내용은 다음 항목을 참조하십시오. 

+ [PCA 기반 변칙 검색](pca-based-anomaly-detection.md)  

## <a name="how-to-configure-train-anomaly-detection-model"></a>길수 변칙 검색 모델을 구성하는 방법 

1.  디자이너의 파이프라인에 **학습 변칙 검색 모델** 모듈을 추가합니다. 이 모듈은 **변칙 검색** 범주에서 찾을 수 있습니다.

2. PCA 기반 변칙 감지와 같은 변칙 검색을 위해 설계된 모듈 중 하나를 [연결합니다.](pca-based-anomaly-detection.md)

    다른 유형의 모델은 지원되지 않습니다. 파이프라인을 실행하면 오류가 발생합니다: 모든 모델에 동일한 학습자 유형이 있어야 합니다.  

3.  레이블 열을 선택하고 알고리즘과 관련된 다른 매개 변수를 설정하여 변칙 검색 모듈을 구성합니다.  

4.  학습 데이터 집합을 **학습 변칙 검색 모델의**오른쪽 입력에 연결합니다.  

5.  파이프라인을 제출합니다.  

## <a name="results"></a>결과

교육이 완료된 후:

+ 모델의 매개 변수를 보려면 모듈을 마우스 오른쪽 단추로 클릭하고 **시각화를**선택합니다. 

+ 예측을 만들려면 새 입력 데이터와 함께 [점수 모델을](score-model.md) 사용합니다.

+ 학습된 모델의 스냅샷을 저장하려면 모듈을 선택하고 오른쪽 패널의 **Outputs+logs** 탭 에서 **데이터 집합 등록** 아이콘을 클릭합니다.   

 
## <a name="next-steps"></a>다음 단계

Azure 기계 학습에 사용할 수 있는 [모듈 집합을](module-reference.md) 참조하십시오. 

디자이너 모듈과 관련된 오류 목록은 [디자이너(미리 보기)의 예외 및 오류 코드를](designer-error-codes.md) 참조하십시오.
'