---
title: '변환 적용: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure 기계 학습에서 변환 적용 모듈을 사용하여 이전에 계산된 변환을 기반으로 입력 데이터 집합을 수정하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/05/2020
ms.openlocfilehash: ccf9d0c3eef50c7dfd838f1929e52506e8984879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78395268"
---
# <a name="apply-transformation-module"></a>변환 모듈 적용

이 문서에서는 Azure 기계 학습 디자이너(미리 보기)의 모듈에 대해 설명합니다.

이 모듈을 사용하여 이전에 계산된 변환을 기반으로 입력 데이터 집합을 수정합니다.

예를 들어 데이터 **정규화** 모듈을 사용하여 교육 데이터를 정규화하기 위해 z 점수를 사용한 경우 채점 단계에서 학습을 위해 계산된 z 점수 값을 사용하려고 합니다. Azure 기계 학습에서 정규화 메서드를 변환으로 저장한 다음 **변환 적용을** 사용하여 점수 매기기 전에 입력 데이터에 z 점수를 적용할 수 있습니다.

## <a name="how-to-save-transformations"></a>변환을 저장하는 방법

디자이너를 사용하면 데이터 변환을 **데이터 집합으로** 저장하여 다른 파이프라인에서 사용할 수 있습니다.

1. 성공적으로 실행된 데이터 변환 모듈을 선택합니다.

1. 출력 **+ 로그 탭을 선택합니다.**

1. **결과 변환을**저장하려면 **저장 아이콘을** 선택합니다.

## <a name="how-to-use-apply-transformation"></a>변환 적용 사용 방법  
  
1. 파이프라인에 **변환 적용** 모듈을 추가합니다. 이 모듈은 모듈 팔레트의 평가 & **모델** 섹션에서 찾을 수 있습니다. 
  
1. 모듈 팔레트의 **데이터 집합** > **내 데이터 집합에서** 사용할 저장된 변환을 찾습니다.

1. 저장된 변환의 출력을 **변환 적용** 모듈의 왼쪽 입력 포트에 연결합니다.

    데이터 집합에는 변환이 처음 디자인된 데이터 집합과 동일한 스키마(열 수, 열 이름, 데이터 형식)가 있어야 합니다.  
  
1. 원하는 모듈의 데이터 집합 출력을 **변환 적용** 모듈의 오른쪽 입력 포트에 연결합니다.
  
1. 새 데이터 집합에 변환을 적용하려면 파이프라인을 실행합니다.  

## <a name="next-steps"></a>다음 단계

Azure 기계 학습에 사용할 수 있는 [모듈 집합을](module-reference.md) 참조하십시오. 