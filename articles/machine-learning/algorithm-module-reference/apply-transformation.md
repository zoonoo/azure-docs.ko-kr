---
title: 변환에 적용 됩니다. 모듈 참조
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스에 변환 적용 모듈로 사용 하 여 이전에 계산 된 변환에 따라 입력된 데이터 집합을 수정 하는 방법에 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 810f375642af49814049589cb83ad17fea578b13
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028712"
---
# <a name="apply-transformation-module"></a>변환 모듈 적용

이 문서에서는 Azure Machine Learning 서비스에 대 한 시각적 인터페이스 (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 이전에 계산 된 변환에 따라 입력된 데이터 집합을 수정 합니다.  
  
예를 들어, z 점수를 사용 하 여 학습 데이터를 정규화를 사용 하는 경우는 **Normalize-data** 점수 매기기 단계도 교육에 대 한 계산 된 z 점수 값을 사용 하려는 모듈입니다. Azure Machine Learning에서 정규화 방법으로 변환 후 사용 하 여 저장할 수 있습니다 **변환 적용** 평가 전에 입력된 데이터에는 z 점수를 적용 합니다.
  
Azure Machine Learning 만들기 및 다양 한 사용자 지정 변환 적용 한 다음에 대 한 지원을 제공 합니다. 예를 들어 다음 저장 하 고 다음 변환을 다시 사용할 수는 것이 좋습니다.  
  
- 제거 하거나 누락 값을 사용 하 여 대체 **누락 데이터 정리**
- 사용 하 여 데이터를 정규화 **데이터 정규화**
  

## <a name="how-to-use-apply-transformation"></a>변환 적용을 사용 하는 방법  
  
1. 추가 된 **변환 적용** 모듈을 실험 합니다. 이 모듈에서 찾을 수 있습니다 **Machine Learning**를 **점수** 범주입니다. 
  
2. 입력으로 사용할 기존 변환을 찾습니다.  이전에 저장 된 변환에서 찾을 수 있습니다 합니다 **변환** 왼쪽된 탐색 창에서 그룹입니다.  
  
   
  
3. 변환 하려는 데이터 집합을 연결 합니다. 데이터 집합에는 정확히 동일한 스키마 (열, 열 이름, 데이터 형식 수) 변환 먼저 디자인 된 대상 데이터 집합으로 있어야 합니다.  
  
4. 다른 매개 변수 없이 변환을 정의할 때 모든 사용자 지정은 수행 되므로 설정 해야 합니다.  
  
5. 새 데이터 집합에 변환을 적용 하려면 실험을 실행 합니다.  

## <a name="next-steps"></a>다음 단계

참조를 [사용할 수 있는 모듈 집합](module-reference.md) Azure Machine Learning 서비스입니다. 