---
title: 자동화된 Machine Learning이란 - Azure Machine Learning
description: 이 문서에서는 자동화된 Machine Learning에 대해 파악할 수 있습니다. Azure Machine Learning 서비스는 알고리즘을 자동으로 선택하고 해당 알고리즘에서 모델을 생성할 수 있습니다. 자동화된 Machine Learning에서는 입력한 매개 변수와 기준을 사용하여 모델에 가장 적합한 알고리즘을 선택함으로써 시간을 절약할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: krishnan
author: krishnaanumalasetty
ms.date: 9/24/2018
ms.openlocfilehash: 2a9c05b68d05102fab80b2aa8fb1c1dad8a367ea
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960042"
---
# <a name="what-is-automated-machine-learning"></a>자동화된 Machine Learning이란?

이 문서에서는 자동화된 Machine Learning에 대해 파악할 수 있습니다. Azure Machine Learning 서비스는 알고리즘을 자동으로 선택하고 해당 알고리즘에서 모델을 생성할 수 있습니다. 자동화된 Machine Learning에서는 입력한 매개 변수와 기준을 사용하여 모델에 가장 적합한 알고리즘을 선택함으로써 시간을 절약할 수 있습니다.

## <a name="how-it-works"></a>작동 방법

1. 해결하려는 Machine Learning 문제의 유형을 구성합니다. 지원되는 감독 방식 학습의 두 가지 범주는 다음과 같습니다.
   + 분류
   + 회귀

   학습 시에 Azure Machine Learning이 사용해 볼 수 있는 [모델 목록](how-to-configure-auto-train.md#select-your-experiment-type)을 참조하세요.

1. 학습 데이터의 원본과 형식을 지정합니다. 데이터는 레이블이 지정되어 있어야 하며 개발 환경이나 Azure Blob Storage에 저장할 수 있습니다. 개발 환경에 데이터를 저장하는 경우에는 학습 스크립트와 같은 디렉터리에 저장해야 합니다. 이 디렉터리는 학습용으로 선택하는 계산 대상에 복사됩니다.

    학습 스크립트에서는 데이터를 Numpy 배열이나 Pandas 데이터 프레임으로 읽어들일 수 있습니다.

    학습 및 유효성 검사 데이터 선택을 위한 분할 옵션을 구성할 수도 있고, 별도의 학습 및 유효성 검사 데이터 집합을 지정할 수도 있습니다.

1. 모델을 학습시키는 데 사용되는 [계산 대상](how-to-set-up-training-targets.md)을 구성합니다.

1. 자동화된 Machine Learning을 구성합니다. 이 구성 과정에서는 Azure Machine Learning이 여러 모델을 반복 학습하는 과정에서 사용되는 매개 변수, 하이퍼 매개 변수 설정, 그리고 최적 모델 결정 시에 확인할 메트릭을 제어합니다. 

1. 학습 실행을 제출합니다.

Azure Machine Learning 서비스는 학습 중에 각기 다른 알고리즘과 매개 변수 사용을 시도하는 여러 파이프라인을 만듭니다. 그리고 사용자가 입력한 반복 횟수 제한에 도달하거나, 사용자가 지정한 메트릭의 대상 값에 도달하면 학습을 중지합니다.

[ ![자동화된 Machine Learning](./media/how-to-automated-ml/automated-machine-learning.png) ](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

실행 중에 수집된 메트릭을 포함하는 기록된 실행 정보를 검사할 수 있습니다. 학습 실행에서는 모델 및 데이터 전처리를 포함하는 Python 직렬화 개체(.pkl 파일)도 생성됩니다.

## <a name="next-steps"></a>다음 단계

예제를 통해 자동화된 Machine Learning을 사용하는 모델을 작성하는 방법을 알아봅니다.

+ [자습서: Azure 자동화된 Machine Learning을 사용하여 자동으로 분류 모델 학습시키기](tutorial-auto-train-models.md)

+ [자동 학습용 설정을 구성하는 방법](how-to-configure-auto-train.md)

+ [원격 리소스에 대한 자동 학습을 사용하는 방법](how-to-auto-train-remote.md) 
