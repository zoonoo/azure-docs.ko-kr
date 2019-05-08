---
title: 열을 추가 합니다. 모듈 참조
titleSuffix: Azure Machine Learning service
description: 두 데이터 집합을 연결할 Azure Machine Learning 서비스에서 열 추가 모듈을 사용 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f1e087e97007c6ba271651a9791c7c3b38a9b9b7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029357"
---
# <a name="add-columns-module"></a>열 모듈 추가

이 문서에서는 Azure Machine Learning 서비스에 대 한 시각적 인터페이스 (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 두 개의 데이터 집합을 연결 합니다. 단일 데이터 집합 만들기에 대 한 입력으로 지정 하는 두 데이터 집합의 모든 열을 결합 합니다. 두 개 이상의 데이터 집합을 연결 해야 하는 경우의 여러 인스턴스를 사용 **열 추가**합니다.



## <a name="how-to-configure-add-columns"></a>추가 열을 구성 하는 방법
1. 추가 된 **열 추가** 모듈을 실험 합니다.

2. 연결 하려는 두 데이터 집합을 연결 합니다. 두 개 이상의 데이터 집합을 결합 하려는 경우 함께 연결할 수 있습니다 여러 조합을 **열 추가**합니다.

    - 행 수가 다른 두 개의 열을 결합 하는 것이 가능 합니다. 출력 데이터 집합은 비교적 작은 원본 열에서 각 행에 대해 누락 된 값으로 채워집니다.

    - 추가할 개별 열을 선택할 수 없습니다. 각 데이터 집합의 모든 열이 사용 하는 경우 연결 됩니다 **열 추가**합니다. 따라서 열의 하위 집합만 추가 하려는 경우 사용할 열 선택 데이터 집합의 열을 사용 하 여 데이터 집합을 만들려면.

3. 실험을 실행합니다.

### <a name="results"></a>결과
실험 후에 실행 합니다.

- 새 데이터 집합의 첫 번째 행을 보려면 출력을 마우스 오른쪽 단추로 클릭 **열 추가** 시각화를 선택 합니다.

새 데이터 집합의 열 수가 두 입력된 데이터 집합의 열 합을 같습니다.

입력된 데이터 집합에서 동일한 이름 가진 두 개의 열이 있는 경우 열 이름에 숫자 접미사가 추가 됩니다. 예를 들어 두 인스턴스의 TargetOutcome 라는 열 경우 왼쪽된 열은 이름을 바꿀 TargetOutcome_1 및 오른쪽 열 TargetOutcome_2 이름을 변경 합니다.

## <a name="next-steps"></a>다음 단계

참조를 [사용할 수 있는 모듈 집합](module-reference.md) Azure Machine Learning 서비스입니다. 