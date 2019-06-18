---
title: '의사 결정 포리스트 회귀: 모듈 참조'
titleSuffix: Azure Machine Learning service
description: 의사 결정 트리 앙상블을 기반으로 회귀 모델을 만들려면 Azure Machine Learning 서비스에서 의사 결정 포리스트 회귀 모듈을 사용 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d372adf75d46fdedb7a6f2b17e47822475d1f155
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65442370"
---
# <a name="decision-forest-regression-module"></a>의사 결정 포리스트 회귀 모듈

이 문서에서는 Azure Machine Learning 서비스에 대 한 시각적 인터페이스 (미리 보기)의 모듈을 설명 합니다.

의사 결정 트리 앙상블을 기반으로 회귀 모델을 만들려면이 모듈을 사용 합니다.

모델을 구성한 후에 레이블이 지정 된 데이터 집합을 사용 하 여 모델을 학습 해야 하며 [모델 학습](./train-model.md) 모듈입니다.  학습된 된 모델을 예측 한 다음 사용할 수 있습니다. 

## <a name="how-it-works"></a>작동 방법

의사 결정 트리는 리프 노드 (의사 결정)에 도달할 때까지 이진 트리 데이터 구조를 트래버스 하며 각 인스턴스에 대해 간단한 테스트 시퀀스를 수행 하는 비 파라메트릭 모델입니다.

의사 결정 트리는 경우의이 이점은:

- 이러한는 학습 및 예측 중에 계산과 메모리 사용량을 모두 효율적입니다.

- 비선형 의사 결정 경계를 나타낼 수 있습니다.

- 통합된 기능 선택 및 분류를 수행 하며 잡음 기능이 있는 경우 복원이 됩니다.

이 회귀 모델의 의사 결정 트리 앙상블으로 구성 됩니다. 각 의사 결정 포리스트 회귀 트리에 예측으로 가우스 분포를 출력합니다. 집계 모델의 모든 트리에 대해 결합 된 배포에 가장 가까운 가우스 분포를 찾으려면 트리 앙상블을 통해 수행 됩니다.

이 알고리즘 및 해당 구현에 대 한 이론적 프레임 워크에 대 한 자세한 내용은이 문서를 참조 하세요. [의사 결정 포리스트: 분류, 회귀, 밀도 추정, 매니폴드 학습 및 반 감독된 학습에 대 한 통합된 프레임 워크](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>의사 결정 포리스트 회귀 모델을 구성 하는 방법

1. 추가 된 **의사 결정 포리스트 회귀** 모듈을 실험 합니다. 아래의 인터페이스에 모듈을 찾을 수 있습니다 **Machine Learning**하십시오 **초기화 모델**, 및 **회귀**.

2. 모듈 속성을 열고 한 **메서드를 다시**, 개별 트리를 만드는 데 사용할 메서드를 선택 합니다.  선택할 수 있습니다 **모음** 하거나 **복제**합니다.

    - **모음**: Bagging 라고 *부트스트랩 집계*합니다. 각 트리에 회귀 의사 결정 포리스트를 통해 예측 가우스 분포를 출력합니다. 집계 된 처음 두 분 일치 Gaussians 개별 트리를 반환한 모든 Gaussians를 결합 하 여 지정 된 조합의 분을 가우스를 찾는 것입니다.

         자세한 내용은 참조에 대 한 Wikipedia 항목 [부트스트랩 집계](https://wikipedia.org/wiki/Bootstrap_aggregating)합니다.

    - **복제**: 복제에서는 각 트리는 정확히 동일한 입력된 데이터에 대해 학습 됩니다. 분할의 각 트리 노드에 대 한 조건자는 결정 임의 유지 하 고 트리는 다양 한 됩니다.

         사용 하 여 학습 프로세스에 대 한 자세한 내용은 합니다 **복제** 옵션 [Computer Vision 및 의료 이미지 분석에 대 한 의사 결정 포리스트. Criminisi and J. Shotton. Springer 2013입니다. ](https://research.microsoft.com/projects/decisionforests/).

3. 설정 하 여 학습 모델을 지정 합니다 **트레이너 모드 만들기** 옵션입니다.

    - **단일 매개 변수**

      모델을 구성 하려는 방법를 알고 있으면 특정 값 집합을 인수로 제공할 수 있습니다. 실험을 통해 이러한 값을 알 수 있습니다 또는 알았거나 지침으로 받았을 수 있습니다.



4. 에 대 한 **의사 결정 트리의 수**, 의사 결정 트리 앙상블에서 만들려는 총 수를 나타냅니다. 추가 의사 결정 트리를 만들면 적용 범위가 확대를 잠재적으로 가져올 수 있지만 학습 시간이 증가 됩니다.

    > [!TIP]
    > 이 값에는 또한 학습된 된 모델을 시각화 하는 경우에 표시 되는 트리 수를 제어 합니다. 참조 또는 단일 트리를 인쇄 하려는 경우 값을 1로 설정할 수 있습니다. 단, 즉 해당 하나의 트리만 됩니다 (초기 매개 변수 집합을 사용 하 여 트리)를 생성 한 추가 반복 수행 됩니다.

5. 에 대 한 **의사 결정 트리의 최대 깊이**, 의사 결정 트리의 최대 깊이를 제한할 숫자를 입력 합니다. 트리의 수준을 늘리면 높아지지만 학습 시간이 더 길어지고 과잉 맞춤이 정밀도 높아질 수 있습니다.

6. 에 대 한 **수의 노드당 임의 분할**, 트리의 각 노드를 작성할 때 사용할 분할의 수를 입력 합니다. A *분할* (노드) 트리의 각 수준에서 기능 즉 임의로 구분 됩니다.

7. 에 대 한 **샘플 리프 노드당 최소**, 사례 트리의 터미널 노드 (리프)를 만드는 데 필요한 최소 수를 나타냅니다.

     이 값을 늘려 새 규칙 만들기에 대 한 임계값을 늘려야 합니다. 예를 들어, 단일 사례도 1의 기본값을 사용 하 여 새 규칙을 만드는 발생할 수 있습니다. 값을 5로 늘리면 학습 데이터에 동일한 조건을 만족 하는 사례가 다섯 개 이상 포함 해야 합니다.


9. 레이블이 지정 된 데이터 집합에 연결 하 고 2 개 결과 포함 하는 단일 레이블 열을 선택 하 고 연결할 [모델 학습](./train-model.md)합니다.

    - 설정 하는 경우 **트레이너 모드 만들기** 옵션을 **단일 매개 변수**를 사용 하 여 모델을 학습 합니다 [모델 학습](./train-model.md) 모듈입니다.

   

10. 실험을 실행합니다.

### <a name="results"></a>결과

완료 된 후 교육:

+ 각 반복에 대해 생성 된 트리를 보려면 학습 모듈의 출력을 마우스 오른쪽 단추로 클릭 하 고 선택 **시각화**합니다.

+ 각 노드에 대 한 규칙을 보려면 각 트리를 클릭 하 고 분할으로 드릴 다운 합니다.

+ 학습된 된 모델의 스냅숏으로 저장 하려면 학습 모듈의 출력을 마우스 오른쪽 단추로 클릭 하 고 선택 **학습 된 모델을 저장**합니다. 모델의이 복사본은 실험의 후속 실행에서 업데이트 되지 않습니다. 

## <a name="next-steps"></a>다음 단계

참조를 [사용할 수 있는 모듈 집합](module-reference.md) Azure Machine Learning 서비스입니다. 