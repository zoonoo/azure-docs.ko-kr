---
title: '변칙 검색 모델 학습: 모듈 참조'
titleSuffix: Azure Machine Learning
description: 변칙 검색 모델 학습 모듈을 사용 하 여 학습 된 변칙 검색 모델을 만드는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 55c5b89eb23581543d57187ee4d7899d97eafe66
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925957"
---
# <a name="train-anomaly-detection-model"></a>이상 탐지 모델 학습

이 문서에서는 Azure Machine Learning designer (미리 보기)의 **변칙 검색 모델 학습** 모듈을 사용 하 여 학습 된 변칙 검색 모델을 만드는 방법을 설명 합니다.

모듈은 변칙 검색 모델 및 레이블이 없는 데이터 집합에 대 한 모델 매개 변수 집합을 입력으로 사용 합니다. 학습 된 변칙 검색 모델을 학습 데이터에 대 한 레이블 집합과 함께 반환 합니다.  

디자이너에서 제공 하는 변칙 검색 알고리즘에 대 한 자세한 내용은 다음 항목을 참조 하세요. 

+ [PCA 기반 변칙 검색](pca-based-anomaly-detection.md)  

## <a name="how-to-configure-train-anomaly-detection-model"></a>변칙 검색 모델을 구성 하는 방법 

1.  디자이너에서 파이프라인에 **변칙 검색 모델 학습** 모듈을 추가 합니다. **변칙 검색** 범주에서이 모듈을 찾을 수 있습니다.

2. 변칙 검색을 위해 설계 된 모듈 중 하나 (예: [PCA 기반 변칙 검색)](pca-based-anomaly-detection.md) 를 연결 합니다.

    다른 모델 유형은 지원 되지 않습니다. 파이프라인을 실행 하는 동안 오류가 발생 합니다. 모든 모델의 학습자 형식이 동일 해야 합니다.  

3.  레이블 열을 선택 하 고 알고리즘과 관련 된 다른 매개 변수를 설정 하 여 변칙 검색 모듈을 구성 합니다.  

4.  학습 **변칙 검색 모델**의 오른쪽 입력에 학습 데이터 집합을 연결 합니다.  

5.  파이프라인을 실행합니다.  

## <a name="results"></a>결과

학습 완료 후:

+ 모델의 매개 변수를 보려면 모듈을 마우스 오른쪽 단추로 클릭 하 고 **시각화**를 선택 합니다. 

+ 예측을 만들려면 새 입력 데이터를 사용 하 여 [모델 점수 매기기](score-model.md) 를 사용 합니다.

+ 학습 된 모델의 스냅숏을 저장 하려면 모듈을 선택 하 고 오른쪽 패널의 **출력 + 로그** 탭에서 **데이터 집합 등록** 아이콘을 클릭 합니다.   

 
## <a name="next-steps"></a>다음 단계

Azure Machine Learning [사용할 수 있는 모듈 집합](module-reference.md) 을 참조 하세요. 

디자이너 모듈에 특정 한 오류 목록은 [디자이너 (미리 보기)에 대 한 예외 및 오류 코드](designer-error-codes.md) 를 참조 하세요.
