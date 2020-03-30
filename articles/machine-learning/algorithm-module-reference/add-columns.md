---
title: '열 추가: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure 기계 학습에서 열 추가 모듈을 사용하여 두 데이터 집합을 통합하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: f2e067f76d6ed7d89a38e9b8920c407f161969a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456781"
---
# <a name="add-columns-module"></a>열 추가 모듈

이 문서에서는 Azure 기계 학습 디자이너(미리 보기)의 모듈에 대해 설명합니다.

이 모듈을 사용하여 두 데이터 집합을 연결합니다. 입력으로 지정한 두 데이터 집합의 모든 열을 결합하여 단일 데이터 집합을 만듭니다. 두 개 이상의 데이터 집합을 연결해야 하는 경우 **열 추가의**여러 인스턴스를 사용합니다.



## <a name="how-to-configure-add-columns"></a>열 추가를 구성하는 방법
1. **파이프라인에 열 추가 모듈을** 추가합니다.

2. 연결할 두 데이터 집합을 연결합니다. 두 개 이상의 데이터 집합을 결합하려는 경우 **열 추가의**여러 조합을 함께 연결할 수 있습니다.

    - 행 수가 다른 두 열을 결합할 수 있습니다. 출력 데이터 집합은 더 작은 소스 열의 각 행에 대해 누락된 값으로 패딩됩니다.

    - 추가할 개별 열을 선택할 수 없습니다. 열 추가를 사용할 때 각 데이터 집합의 모든 **열이 연결됩니다.** 따라서 열의 하위 집합만 추가하려면 데이터 집합에서 열 선택을 사용하여 원하는 열이 있는 데이터 집합을 만듭니다.

3. 파이프라인을 제출합니다.

### <a name="results"></a>결과
파이프라인이 실행된 후:

- 새 데이터 집합의 첫 번째 행을 보려면 열 추가 모듈을 마우스 오른쪽 단추로 클릭하고 **시각화를 선택합니다.** 또는 모듈을 선택하고 오른쪽 패널의 **출력** 탭으로 전환하고 포트 출력의 히스토그램 아이콘을 클릭하여 결과를 **시각화합니다.**

새 데이터 집합의 열 수는 두 입력 데이터 집합의 열 합과 같습니다.

입력 데이터 집합에 이름이 같은 두 개의 열이 있는 경우 숫자 접미사가 열 이름에 추가됩니다. 예를 들어 TargetResult라는 열의 인스턴스가 두 개 있는 경우 왼쪽 열의 이름이 TargetOutcome_1 이름이 바뀌고 오른쪽 열의 이름이 TargetOutcome_2 바뀝니다.

## <a name="next-steps"></a>다음 단계

Azure 기계 학습에 사용할 수 있는 [모듈 집합을](module-reference.md) 참조하십시오. 