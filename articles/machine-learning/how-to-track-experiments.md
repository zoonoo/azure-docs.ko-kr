---
title: Log ML 실험 & 메트릭
titleSuffix: Azure Machine Learning
description: Azure ML 실험과 실행 메트릭을 모니터링하여 모델 생성 프로세스를 향상시킵니다. run.log, Run.start_logging, 또는 ScriptRunConfig를 사용하여 로깅을 학습 스크립트에 추가합니다.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 7eaa2fbe6033f801a252f6f2c7afa5eb726bce2f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318248"
---
# <a name="enable-logging-in-azure-ml-training-runs"></a>Azure ML 학습 실행에서 로깅 사용


Azure Machine Learning Python SDK를 사용하면 기본 Python 로깅 패키지와 SDK 관련 기능을 모두 사용하여 실시간 정보를 기록할 수 있습니다. 로컬로 로그인하고 로그를 포털의 작업 영역에 보낼 수 있습니다.

로그는 오류 및 경고를 진단하거나 매개 변수 및 모델 성능과 같은 성능 메트릭을 추적하는 데 도움이 됩니다. 이 문서에서는 다음 시나리오에서 로깅을 사용하도록 설정하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 대화형 학습 세션
> * ScriptRunConfig를 사용하여 학습 작업 제출
> * Python 네이티브 `logging` 설정
> * 추가 원본에서 로깅


> [!TIP]
> 이 문서에서는 모델 학습 프로세스를 모니터링하는 방법을 보여 줍니다. 할당량, 완료된 학습 실행 또는 완료된 모델 배포와 같이 Azure Machine Learning의 리소스 사용량 및 이벤트를 모니터링하는 데 관심이 있는 경우 [Azure Machine Learning 모니터링](monitor-azure-machine-learning.md)을 참조하세요.

## <a name="data-types"></a>데이터 형식

스칼라 값, 목록, 테이블, 이미지, 디렉터리 등을 포함한 여러 데이터 형식을 기록할 수 있습니다. 다양한 데이터 형식에 대한 자세한 내용과 Python 코드 예제는 [Run 클래스 참조 페이지](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true)를 참조하세요.

## <a name="interactive-logging-session"></a>대화형 로깅 세션

대화형 로깅 세션은 일반적으로 Notebook 환경에서 사용됩니다. [Experiment.start_logging()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truestart-logging--args----kwargs-) 메서드는 대화형 로깅 세션을 시작합니다. 세션 중에 기록된 모든 메트릭은 실험의 실행 기록에 추가됩니다. [run.complete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truecomplete--set-status-true-) 메서드는 세션을 종료하고 실행을 완료됨으로 표시합니다.

## <a name="scriptrun-logs"></a>ScriptRun 로그

이 섹션에서는 ScriptRunConfig로 구성할 때 생성된 실행 내부에 로깅 코드를 추가하는 방법을 알아봅니다. [**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) 클래스를 사용하여 반복 가능한 실행에 대한 스크립트 및 환경을 캡슐화할 수 있습니다. 또한 이 옵션을 사용하여 모니터링을 위한 시각적 Jupyter Notebook 위젯을 표시할 수 있습니다.

다음 예제에서는 [run.log()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truelog-name--value--description----) 메서드를 사용하여 알파 값에 대한 매개 변수 스윕을 수행하고 결과를 캡처합니다.

1. 로깅 논리가 포함된 학습 스크립트(`train.py`)를 만듭니다.

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. 사용자 관리 환경에서 실행되도록 ```train.py``` 스크립트를 제출합니다. 학습을 위해 전체 스크립트 폴더가 제출됩니다.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)] [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

    `show_output` 매개 변수는 자세한 정보 로깅을 설정합니다. 그러면 학습 프로세스의 세부 정보와 원격 리소스 또는 컴퓨팅 대상에 대한 정보를 확인할 수 있습니다. 실험을 제출할 때 자세한 정보 로깅을 설정하려면 다음 코드를 사용합니다.

```python
run = exp.submit(src, show_output=True)
```

결과 실행의 `wait_for_completion` 함수에서도 동일한 매개 변수를 사용할 수 있습니다.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>네이티브 Python 로깅

SDK의 일부 로그에는 로깅 수준을 DEBUG로 설정하도록 지시하는 오류가 포함될 수 있습니다. 로깅 수준을 설정하려면 스크립트에 다음 코드를 추가합니다.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>추가 로깅 원본

Azure Machine Learning은 학습 중에 자동화된 Machine Learning 실행 또는 작업을 실행하는 Docker 컨테이너와 같은 다른 원본의 정보를 기록할 수도 있습니다. 이러한 로그는 문서화되어 있지 않지만 문제가 발생하여 Microsoft 지원에 문의하는 경우 문제 해결 중에 이러한 로그를 사용할 수 있습니다.

Azure Machine Learning 디자이너의 메트릭 로깅에 대한 자세한 내용은 [디자이너에서 메트릭을 기록하는 방법](how-to-track-designer-experiments.md)을 참조하세요.

## <a name="example-notebooks"></a>노트북 예제

이 문서의 개념을 보여 주는 노트북은 다음과 같습니다.
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

Azure Machine Learning을 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Machine Learning 디자이너에서 메트릭을 기록하는 방법](how-to-track-designer-experiments.md)을 알아봅니다.

* 최상의 모델을 등록하고 배포하는 방법에 대한 예제는 [Azure Machine Learning으로 이미지 분류 모델 학습](tutorial-train-models-with-aml.md) 자습서를 참조하세요.
