---
title: '변형 적용: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 변환 적용 모듈을 사용 하 여 이전에 계산 된 변환을 기반으로 입력 데이터 집합을 수정 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/05/2020
ms.openlocfilehash: ccf9d0c3eef50c7dfd838f1929e52506e8984879
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395268"
---
# <a name="apply-transformation-module"></a>변환 모듈 적용

이 문서에서는 Azure Machine Learning designer (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 이전에 계산 된 변환을 기반으로 입력 데이터 집합을 수정 합니다.

예를 들어 **데이터 정규화** 모듈을 사용 하 여 z 점수를 사용 하 여 학습 데이터를 정규화 한 경우 점수 매기기 단계 중에 학습에 대해 계산 된 z 점수 값을 사용 하는 것이 좋습니다. Azure Machine Learning에서는 정규화 방법을 변환으로 저장 한 다음 **변환 적용** 을 사용 하 여 점수 매기기 전에 입력 데이터에 z 점수를 적용할 수 있습니다.

## <a name="how-to-save-transformations"></a>변환 저장 방법

디자이너를 사용 하면 데이터 변환을 다른 파이프라인에서 사용할 수 있도록 데이터 **집합** 으로 저장할 수 있습니다.

1. 성공적으로 실행 된 데이터 변환 모듈을 선택 합니다.

1. **출력 + 로그** 탭을 선택 합니다.

1. **저장 아이콘** 을 선택 하 여 **결과 변환을**저장 합니다.

## <a name="how-to-use-apply-transformation"></a>변형 적용 사용 방법  
  
1. 파이프라인에 **변환 적용** 모듈을 추가 합니다. 모듈 팔레트의 **모델 점수 매기기 & 평가** 섹션에서이 모듈을 찾을 수 있습니다. 
  
1. 모듈 팔레트에서 **내 데이터** 집합 > **데이터 집합** 에서 사용 하려는 저장 된 변환을 찾습니다.

1. 저장 된 변환의 출력을 **변환 적용** 모듈의 왼쪽 입력 포트에 연결 합니다.

    데이터 집합은 변환의 첫 번째 스키마 (열 수, 열 이름, 데이터 형식)가 정확히 일치 해야 합니다.  
  
1. 원하는 모듈의 데이터 집합 출력을 **변환 적용** 모듈의 오른쪽 입력 포트에 연결 합니다.
  
1. 새 데이터 집합에 변환을 적용 하려면 파이프라인을 실행 합니다.  

## <a name="next-steps"></a>다음 단계

Azure Machine Learning [사용할 수 있는 모듈 집합](module-reference.md) 을 참조 하세요. 