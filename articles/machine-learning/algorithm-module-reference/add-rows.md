---
title: '행 추가: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning의 행 추가 모듈을 사용 하 여 두 데이터 집합을 연결 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: a16dc0bfc3fae8852c46435f6407c56149e1b14e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493956"
---
# <a name="add-rows-module"></a>행 추가 모듈

이 문서에서는 Azure Machine Learning designer (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 두 데이터 집합을 연결 합니다. 연결에서 두 번째 데이터 집합의 행은 첫 번째 데이터 집합의 끝에 추가 됩니다.  
  
행 연결은 다음과 같은 시나리오에서 유용 합니다.  
  
+ 일련의 평가 통계를 생성 했으며 보다 쉬운 보고를 위해 이러한 통계를 하나의 테이블로 결합 하려고 합니다.  
  
+ 다른 데이터 집합을 사용 하 고 있고 데이터 집합을 결합 하 여 최종 데이터 집합을 만드는 경우  

## <a name="how-to-use-add-rows"></a>행 추가를 사용 하는 방법  

두 데이터 집합의 행을 연결 하려면 행에 정확히 동일한 스키마가 있어야 합니다. 즉, 열에 동일한 수의 열과 동일한 데이터 형식이 있습니다.

1.  **행 추가** 모듈을 파이프라인으로 끌어 **데이터 변환**의 **조작** 범주에서 찾을 수 있습니다.

2. 두 입력 포트에 데이터 집합을 연결 합니다. 추가 하려는 데이터 집합을 두 번째 (오른쪽) 포트에 연결 해야 합니다. 
  
3.  파이프라인을 실행합니다. 출력 데이터 집합의 행 수는 두 입력 데이터 집합의 행 합계와 같아야 합니다.

    **행 추가** 모듈의 두 입력에 동일한 데이터 집합을 추가 하는 경우 데이터 집합이 중복 됩니다. 

## <a name="next-steps"></a>다음 단계

Azure Machine Learning [사용할 수 있는 모듈 집합](module-reference.md) 을 참조 하세요. 