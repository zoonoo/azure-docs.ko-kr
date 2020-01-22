---
title: 데이터 요약
titleSuffix: Azure Machine Learning
description: Azure Machine Learning의 데이터 요약 모듈을 사용 하 여 데이터 집합의 열에 대 한 기본 설명 통계 보고서를 생성 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: a037c959b44a6216724c285f932250b1b05cb509
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76313749"
---
# <a name="summarize-data"></a>데이터 요약

이 문서에서는 Azure Machine Learning 디자이너의 모듈을 설명 합니다.

데이터 요약 모듈을 사용 하 여 입력 테이블의 각 열을 설명 하는 표준 통계 측정값 집합을 만들 수 있습니다.

요약 통계는 전체 데이터 집합의 특성을 이해 하려는 경우에 유용 합니다. 예를 들어, 다음을 알아야 합니다.

- 각 열에는 몇 개의 누락 값이 있나요?
- 기능 열에 있는 고유한 값은 몇 개입니까?
- 각 열에 대 한 평균과 표준 편차는 무엇 인가요?

모듈은 각 열에 대 한 중요 점수를 계산 하 고 입력으로 제공 된 각 변수 (데이터 열)에 대 한 요약 통계 행을 반환 합니다.

## <a name="how-to-configure-summarize-data"></a>요약 데이터를 구성 하는 방법  

1. 파이프라인에 **데이터 요약** 모듈을 추가 합니다. 이 모듈은 디자이너의 **통계 함수** 범주에서 찾을 수 있습니다.

1. 보고서를 생성 하려는 데이터 집합을 연결 합니다.

    일부 열에 대해서만 보고 하려면 [데이터 집합에서 열 선택](select-columns-in-dataset.md) 모듈을 사용 하 여 작업할 열의 하위 집합을 프로젝션 합니다.

1. 추가 매개 변수는 필요 하지 않습니다. 기본적으로 모듈은 입력으로 제공 된 모든 열을 분석 하 고, 열의 값 유형에 따라 [결과](#results) 섹션에 설명 된 대로 관련 된 통계 집합을 출력 합니다.

1. 파이프라인을 실행합니다.

## <a name="results"></a>결과

모듈의 보고서에는 다음 통계가 포함 될 수 있습니다. 

|열 이름|Description|
|------|------|  
|**기능**|열의 이름입니다.|
|**Count**|모든 행의 개수|
|**고유 값 수**|열의 고유 값 수|
|**누락 값 개수**|열의 고유 값 수|
|**Min**|열에서 가장 낮은 값|  
|**Max**|열에서 가장 높은 값|
|**평균값**|모든 열 값의 평균|
|**평균 편차**|열 값의 평균 편차|
|**1 분 분**|처음 사분 위 값|
|**Median**|중앙값 열 값|
|**3 분 분**|3 분 분의 값|
|**모드**|열 값 모드|
|**Range**|최대값과 최 댓 값 사이의 값 수를 나타내는 정수입니다.|
|**표본 분산**|열에 대 한 가변성 참고 참조|
|**샘플 표준 편차**|열에 대 한 표준 편차 참고 참조|
|**샘플 왜곡도**|열에 대 한 왜곡도 참고 참조|
|**샘플 첨도**|열에 대 한 첨도 참고 참조|
|**P 0.5**|0.5% 백분위수|
|**P1**|1% 백분위 수|
|**P5**|5% 백분위 수|
|**P95**|95% 백분위 수|
|**P 99.5**|99.5% 백분위 수 |

## <a name="next-steps"></a>다음 단계

Azure Machine Learning [사용할 수 있는 모듈 집합](module-reference.md) 을 참조 하세요.  
