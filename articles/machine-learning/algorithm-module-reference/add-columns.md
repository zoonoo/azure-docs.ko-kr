---
title: '열 추가: 모듈 참조'
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스에서 열 추가 모듈을 사용 하 여 두 데이터 집합을 연결 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 630f8f7d4561ed6e7e9f943f4b3b123daeee4d67
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129032"
---
# <a name="add-columns-module"></a>열 추가 모듈

이 문서에서는 Azure Machine Learning 서비스에 대 한 시각적 인터페이스 (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 두 데이터 집합을 연결 합니다. 지정 하는 두 데이터 집합의 모든 열을 입력으로 결합 하 여 단일 데이터 집합을 만듭니다. 세 개 이상의 데이터 집합을 연결 해야 하는 경우 **열 추가**의 여러 인스턴스를 사용 합니다.



## <a name="how-to-configure-add-columns"></a>열 추가를 구성 하는 방법
1. **열 추가** 모듈을 실험에 추가 합니다.

2. 연결 하려는 두 데이터 집합을 연결 합니다. 세 개 이상의 데이터 집합을 결합 하려는 경우에는 여러 개의 **열**을 조합 하 여 함께 사용할 수 있습니다.

    - 행 수가 다른 두 개의 열을 결합할 수 있습니다. 출력 데이터 집합은 작은 원본 열의 각 행에 대해 누락 된 값으로 채워집니다.

    - 추가할 개별 열을 선택할 수 없습니다. **열 추가**를 사용 하는 경우 각 데이터 집합의 모든 열이 연결 됩니다. 따라서 열의 하위 집합만 추가 하려면 데이터 집합의 열 선택을 사용 하 여 원하는 열이 포함 된 데이터 집합을 만듭니다.

3. 실험을 실행합니다.

### <a name="results"></a>결과
실험을 실행 한 후:

- 새 데이터 집합의 첫 번째 행을 보려면 **열 추가** 의 출력을 마우스 오른쪽 단추로 클릭 하 고 시각화를 선택 합니다.

새 데이터 집합의 열 수는 두 입력 데이터 집합의 열 합계와 같습니다.

입력 데이터 집합에 이름이 같은 열이 두 개 있으면 열 이름에 숫자 접미사가 추가 됩니다. 예를 들어 TargetOutcome 이라는 열이 두 개 있는 경우 왼쪽 열 이름이 TargetOutcome_1로 바뀌고 오른쪽 열의 이름이 TargetOutcome_2로 바뀝니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning 서비스에 [사용할 수 있는 모듈 집합](module-reference.md) 을 참조 하세요. 