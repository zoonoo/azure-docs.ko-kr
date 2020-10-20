---
title: ML 파이프라인의 디버그 & 문제 해결
titleSuffix: Azure Machine Learning
description: Python에서 Azure Machine Learning 파이프라인을 디버깅 합니다. 파이프라인 개발에 대 한 일반적인 문제 및 원격 실행 전후에 스크립트를 디버그 하는 데 도움이 되는 팁에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: troubleshooting, devx-track-python
ms.openlocfilehash: d369aafa7fdade93df1fe1706aa90c5135c75e79
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216966"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>기계 학습 파이프라인 디버그 및 문제 해결

이 문서에서는 [AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) 및 [Azure Machine Learning designer](https://docs.microsoft.com/azure/machine-learning/concept-designer)에서 [machine learning 파이프라인](concept-ml-pipelines.md) 을 디버그 하 고 문제를 해결 하는 방법에 대해 알아봅니다.

## <a name="troubleshooting-tips"></a>문제 해결 팁

다음 표에는 잠재적인 솔루션을 포함 하는 파이프라인 개발 중에 일반적인 문제가 포함 되어 있습니다.

| 문제 | 가능한 해결 방법 |
|--|--|
| 디렉터리에 데이터를 전달할 수 없습니다. `PipelineData` | 파이프라인이 단계 출력 데이터를 기대 하는 위치에 해당 하는 디렉터리를 스크립트에 만들었는지 확인 합니다. 대부분의 경우 입력 인수는 출력 디렉터리를 정의 하 고 디렉터리를 명시적으로 만듭니다. `os.makedirs(args.output_dir, exist_ok=True)`를 사용 하 여 출력 디렉터리를 만듭니다. 이 디자인 패턴을 보여 주는 점수 매기기 스크립트 예제에 대 한 [자습서](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) 를 참조 하세요. |
| 종속성 버그 | 원격 파이프라인에서 로컬 테스트할 때 발생 하지 않은 종속성 오류가 표시 되 면 원격 환경 종속성 및 버전이 테스트 환경의 종속성과 일치 하는지 확인 합니다. [환경 빌드, 캐싱 및 재사용](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse) 을 참조 하세요.|
| 계산 대상의 모호한 오류 | 계산 대상을 삭제 하 고 다시 만들어 보세요. 계산 대상을 다시 만드는 과정은 빠르게 수행할 수 있으며 일부 일시적인 문제를 해결할 수 있습니다. |
| 파이프라인이 단계를 다시 사용 하지 않음 | 단계 다시 사용은 기본적으로 사용 되지만 파이프라인 단계에서 사용 하지 않도록 설정 하지 않았는지 확인 합니다. 다시 사용 하지 않도록 설정 된 경우 `allow_reuse` 단계의 매개 변수는로 설정 됩니다 `False` . |
| 파이프라인이 불필요 하 게 다시 실행 되 고 있습니다. | 기본 데이터 또는 스크립트가 변경 될 때만 단계가 다시 실행 되도록 하려면 각 단계에 대 한 소스 코드 디렉터리를 분리 합니다. 여러 단계에 동일한 원본 디렉터리를 사용 하는 경우 불필요 한 다시 실행이 발생할 수 있습니다. `source_directory`파이프라인 단계 개체에서 매개 변수를 사용 하 여 해당 단계에 대 한 격리 된 디렉터리를 가리키고 여러 단계에 대해 동일한 경로를 사용 하지 않는지 확인 `source_directory` 합니다. |
| 학습 epoch 또는 기타 루핑 동작을 초과 하는 단계 속도 저하 | 로깅을 비롯 한 파일 쓰기를에서로 전환 해 `as_mount()` 보세요 `as_upload()` . **탑재** 모드는 원격 가상화 된 파일 시스템을 사용 하 고 추가 될 때마다 전체 파일을 업로드 합니다. |

## <a name="troubleshooting-parallelrunstep"></a>슈팅이 `ParallelRunStep` 

에 대 한 스크립트는 `ParallelRunStep` 다음 두 가지 함수를 *포함 해야 합니다* .
- `init()`: 이후 유추를 위해 비용이 많이 드는 준비 또는 일반적인 준비에 이 함수를 사용합니다. 예를 들어 모델을 글로벌 개체에 로드하는 데 사용합니다. 이 함수는 프로세스를 시작할 때 한 번만 호출됩니다.
-  `run(mini_batch)`: 이 함수는 각 `mini_batch` 인스턴스에 대해 실행됩니다.
    -  `mini_batch`: `ParallelRunStep`은(는) run 메서드를 호출하고 목록 또는 pandas `DataFrame`을(를) 메서드에 인수로 전달합니다. 입력이 `FileDataset`이면 mini_batch의 각 항목이 파일 경로가 되고 입력이 `TabularDataset`이면 Pandas `DataFrame`입니다.
    -  `response`: run() 메서드는 Pandas `DataFrame` 또는 배열을 반환해야 합니다. append_row output_action의 경우 반환되는 요소가 공통 출력 파일에 추가됩니다. summary_only의 경우 요소의 내용이 무시됩니다. 모든 출력 작업의 경우 반환되는 각 출력 요소는 입력 미니 일괄 처리의 입력 요소에 대한 성공적인 실행 하나를 나타냅니다. 입력을 실행 출력 결과에 매핑하기에 충분한 데이터가 실행 결과에 포함되어 있는지 확인합니다. 실행 출력은 출력 파일에 기록되지만 순서대로 기록된다는 보장은 없으므로, 사용자는 출력의 키를 사용하여 입력에 매핑해야 합니다.

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

다른 파일 또는 폴더가 유추 스크립트와 동일한 디렉터리에 있는 경우 현재 작업 디렉터리를 찾아 참조할 수 있습니다.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

### <a name="parameters-for-parallelrunconfig"></a>ParallelRunConfig에 대 한 매개 변수

`ParallelRunConfig`는 Azure Machine Learning 파이프라인 내에서 `ParallelRunStep` 인스턴스에 대한 주요 구성입니다. 스크립트를 래핑하고 다음 항목을 포함하여 필요한 매개 변수를 구성하는 데 사용됩니다.
- `entry_script`: 여러 노드에서 병렬로 실행되는 로컬 파일 경로인 사용자 스크립트입니다. `source_directory`가 있으면 상대 경로를 사용합니다. 없으면 머신에서 액세스할 수 있는 아무 경로를 사용합니다.
- `mini_batch_size`: 단일 `run()` 호출에 전달된 미니 일괄 처리의 크기입니다. (선택 사항이며 기본값은 `FileDataset`의 경우 `10` 파일이고 `TabularDataset`의 경우 `1MB`임)
    - `FileDataset`의 경우 최솟값이 `1`인 파일 수입니다. 여러 파일을 한 미니 일괄 처리로 결합할 수 있습니다.
    - `TabularDataset`의 경우 데이터의 크기입니다. 예제 값은 `1024`, `1024KB`, `10MB` 및 `1GB`입니다. 권장 값은 `1MB`입니다. `TabularDataset`의 미니 일괄 처리는 파일 경계를 넘지 않습니다. 예를 들어 다양한 크기의 .csv 파일이 있는 경우 가장 작은 파일은 100KB이고 가장 큰 파일은 10MB입니다. `mini_batch_size = 1MB`를 설정하는 경우 크기가 1MB보다 작은 파일은 하나의 미니 일괄 처리로 취급됩니다. 크기가 1MB보다 큰 파일은 여러 개의 미니 일괄 처리로 분할됩니다.
- `error_threshold`: 처리 중에 무시해야 하는 `FileDataset`에 대한 `TabularDataset` 및 파일 오류에 대한 레코드 실패 횟수입니다. 전체 입력의 오류 횟수가 이 값을 초과하면 작업이 중단됩니다. 오류 임계값은 `run()` 메서드로 전송되는 개별 미니 일괄 처리가 아닌 전체 입력에 적용됩니다. 범위는 `[-1, int.max]`입니다. `-1` 부분은 처리 중에 발생하는 모든 오류를 무시한다는 뜻입니다.
- `output_action`: 다음 값 중 하나는 출력을 구성하는 방법을 나타냅니다.
    - `summary_only`: 사용자 스크립트는 출력을 저장합니다. `ParallelRunStep`은 출력을 오류 임계값 계산에만 사용합니다.
    - `append_row`: 모든 입력에서, 출력 폴더에 줄로 구분된 모든 출력을 추가하는 하나의 파일만 생성됩니다.
- `append_row_file_name`: append_row output_action의 출력 파일 이름을 사용자 지정하는 방법은 다음과 같습니다(선택 사항이며 기본값은 `parallel_run_step.txt`).
- `source_directory`: 컴퓨팅 대상에서 실행할 모든 파일이 포함된 폴더 경로입니다(선택 사항).
- `compute_target`: `AmlCompute`만 지원됩니다.
- `node_count`: 사용자 스크립트를 실행하는 데 사용되는 컴퓨팅 노드의 수입니다.
- `process_count_per_node`: 노드당 프로세스 수입니다. 모범 사례는 한 노드에 있는 GPU 또는 CPU 수로 설정하는 것입니다(선택 사항이며 기본값은 `1`).
- `environment`: Python 환경 정의입니다. 기존 Python 환경을 사용하도록 구성할 수도 있고 임시 환경을 설정하도록 구성할 수도 있습니다. 이 정의는 필요한 애플리케이션 종속성을 설정하는 역할도 담당합니다(선택 사항).
- `logging_level`: 로그의 자세한 정도입니다. 값의 자세한 정도는 `WARNING`, `INFO`, `DEBUG` 순서로 높아집니다. (선택 사항이며 기본값은 `INFO`)
- `run_invocation_timeout`: `run()` 메서드 호출 시간 제한(초)입니다. (선택 사항이며 기본값은 `60`)
- `run_max_try`: 미니 일괄 처리의 최대 `run()` 시도 횟수입니다. 예외가 throw되거나 `run_invocation_timeout`에 도달할 때 아무 것도 반환되지 않으면 `run()`이 실패합니다(선택 사항이며 기본값은 `3`). 

`mini_batch_size`, `node_count`, `process_count_per_node`, `logging_level`, `run_invocation_timeout` 및 `run_max_try`를 `PipelineParameter`로 지정할 수 있습니다. 그러면 파이프라인 실행을 다시 제출할 때 매개 변수 값을 세밀하게 조정할 수 있습니다. 이 예제에서는 `mini_batch_size` 및 `Process_count_per_node`에 `PipelineParameter`을(를) 사용하며 나중에 실행을 다시 제출할 때 이 값을 변경할 것입니다. 

### <a name="parameters-for-creating-the-parallelrunstep"></a>ParallelRunStep 만들기에 대 한 매개 변수

스크립트, 환경 구성 및 매개 변수를 사용하여 ParallelRunStep을 만듭니다. 유추 스크립트의 실행 대상으로 작업 영역에 이미 연결한 컴퓨팅 대상을 지정합니다. `ParallelRunStep`을 사용하여 다음 매개 변수를 모두 사용하는 일괄 처리 유추 파이프라인 단계를 만듭니다.
- `name`: 단계의 이름이며, 고유한 3-32자 문자와 regex ^\[a-z\]([-a-z0-9]*[a-z0-9])?$로 구성되어야 한다는 명명 제한이 있습니다.
- `parallel_run_config`: 이전에 정의된 `ParallelRunConfig` 개체입니다.
- `inputs`: 병렬 처리를 위해 분할하려는 하나 이상의 단일 형식 Azure Machine Learning 데이터 세트입니다.
- `side_inputs`: 분할할 필요 없이 측면 입력으로 사용되는 하나 이상의 참조 데이터 또는 데이터 세트입니다.
- `output`: 출력 디렉터리에 해당하는 `PipelineData` 개체입니다.
- `arguments`: 사용자 스크립트에 전달된 인수 목록입니다. 항목 스크립트에서 인수를 검색하려면 unknown_args를 사용합니다(선택 사항).
- `allow_reuse`: 동일한 설정/입력으로 실행할 때 이전 결과를 단계에 다시 사용할 것인지 여부를 지정합니다. 이 매개 변수가 `False`이면 파이프라인 실행 중에 이 단계에 대해 항상 새로운 실행이 생성됩니다. (선택 사항이며 기본값은 `True`)

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```

## <a name="debugging-techniques"></a>디버깅 기술

파이프라인 디버깅에는 세 가지 주요 기술이 있습니다. 

* 로컬 컴퓨터에서 개별 파이프라인 단계 디버그
* 로깅 및 Application Insights를 사용 하 여 문제의 원인을 격리 하 고 진단 합니다.
* Azure에서 실행 되는 파이프라인에 원격 디버거 연결

### <a name="debug-scripts-locally"></a>로컬에서 스크립트 디버그

파이프라인에서 가장 일반적으로 발생 하는 오류 중 하나는 도메인 스크립트가 의도 한 대로 실행 되지 않거나 디버깅 하기 어려운 원격 계산 컨텍스트에서 런타임 오류를 포함 하는 것입니다.

파이프라인 자체를 로컬로 실행할 수는 없지만 로컬 컴퓨터에서 격리 된 스크립트를 실행 하면 계산 및 환경 빌드 프로세스를 기다릴 필요가 없기 때문에 더 빠르게 디버그할 수 있습니다. 이 작업을 수행 하려면 몇 가지 개발 작업이 필요 합니다.

* 데이터가 클라우드 데이터 저장소에 있는 경우 데이터를 다운로드 하 여 스크립트에 사용할 수 있도록 해야 합니다. 데이터의 작은 샘플을 사용 하는 것은 런타임에 빠르게 중단 하 고 스크립트 동작에 대 한 피드백을 빠르게 얻는 좋은 방법입니다.
* 중간 파이프라인 단계를 시뮬레이션 하려는 경우 이전 단계에서 특정 스크립트가 예상 하는 개체 유형을 수동으로 빌드해야 할 수 있습니다.
* 또한 사용자 고유의 환경을 정의 하 고 원격 계산 환경에 정의 된 종속성을 복제 해야 합니다.

로컬 환경에서 스크립트를 설치 하 고 나면 다음과 같은 디버깅 작업을 수행 하는 것이 훨씬 쉽습니다.

* 사용자 지정 디버그 구성 연결
* 실행 일시 중지 및 개체 상태 검사
* 런타임이 될 때까지 노출 되지 않는 형식 또는 논리 오류를 catch 합니다.

> [!TIP] 
> 스크립트가 예상 대로 실행 되는지 확인 한 후에는 여러 단계로 이루어진 파이프라인에서 실행을 시도 하기 전에 단일 단계 파이프라인에서 스크립트를 실행 하는 것이 좋습니다.

## <a name="configure-write-to-and-review-pipeline-logs"></a>파이프라인 로그 구성, 쓰기 및 검토

스크립트를 로컬로 테스트 하는 것은 파이프라인 빌드를 시작 하기 전에 주요 코드 조각과 복잡 한 논리를 디버깅 하는 좋은 방법 이지만, 특정 시점에는 특히 파이프라인 단계 간의 상호 작용 중에 발생 하는 동작을 진단할 때 실제 파이프라인 실행 중에 스크립트를 디버그 해야 할 수 있습니다. `print()`JavaScript 코드를 디버그 하는 방법과 비슷하게 원격 실행 중에 개체 상태와 예상 값을 볼 수 있도록 단계 스크립트에서 문을 매우 사용 하는 것이 좋습니다.

### <a name="logging-options-and-behavior"></a>로깅 옵션 및 동작

다음 표에서는 파이프라인에 대 한 다양 한 디버그 옵션에 대 한 정보를 제공 합니다. 여기에 표시 된 Azure Machine Learning, Python 및 OpenCensus 뿐 아니라 다른 옵션도 존재 하므로 완전 한 목록은 아닙니다.

| 라이브러리                    | Type   | 예제                                                          | 대상                                  | 리소스                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | 메트릭 | `run.log(name, val)`                                             | Azure Machine Learning 포털 UI             | [실험을 추적 하는 방법](how-to-track-experiments.md)<br>[azureml 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true)                                                                                                                                                 |
| Python 인쇄/로깅    | 로그    | `print(val)`<br>`logging.info(message)`                          | 드라이버 로그, Azure Machine Learning 디자이너 | [실험을 추적 하는 방법](how-to-track-experiments.md)<br><br>[Python 로깅](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | 로그    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights 추적                | [Application Insights에서 파이프라인 디버깅](how-to-debug-pipelines-application-insights.md)<br><br>[OpenCensus Azure Monitor Exporters](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Python 로깅 cookbook](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>로깅 옵션 예제

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning 디자이너

디자이너에서 만든 파이프라인의 경우 제작 페이지나 파이프라인 실행 세부 정보 페이지에서 **70_driver_log** 파일을 찾을 수 있습니다.

### <a name="enable-logging-for-real-time-endpoints"></a>실시간 끝점에 대 한 로깅 사용

디자이너에서 실시간 끝점의 문제를 해결 하 고 디버그 하려면 SDK를 사용 하 여 응용 프로그램 정보 로깅을 사용 하도록 설정 해야 합니다. 로깅을 사용 하면 모델 배포 및 사용 문제를 해결 하 고 디버그할 수 있습니다. 자세한 내용은 [배포 된 모델에 대 한 로깅](how-to-enable-logging.md#logging-for-deployed-models)을 참조 하세요. 

### <a name="get-logs-from-the-authoring-page"></a>제작 페이지에서 로그 가져오기

파이프라인 실행을 제출 하 고 제작 페이지에 유지 하는 경우 각 모듈의 실행이 완료 되 면 각 모듈에 대해 생성 되는 로그 파일을 찾을 수 있습니다.

1. 작성 캔버스에서 실행이 완료 된 모듈을 선택 합니다.
1. 모듈의 오른쪽 창에서  **출력 + 로그** 탭으로 이동 합니다.
1. 오른쪽 창을 확장 하 고 **70_driver_log.txt** 를 선택 하 여 브라우저에서 파일을 봅니다. 로그를 로컬로 다운로드할 수도 있습니다.

    ![디자이너의 확장 된 출력 창](./media/how-to-debug-pipelines/designer-logs.png)? view = azure-ml-py&preserve-view = true)? view = azure-ml-py&preserve-view = true)

### <a name="get-logs-from-pipeline-runs"></a>파이프라인 실행에서 로그 가져오기

파이프라인 실행 세부 정보 페이지에서 특정 실행에 대 한 로그 파일을 찾을 수도 있습니다 .이 페이지는 스튜디오의 **파이프라인** 또는 **실험** 섹션에서 찾을 수 있습니다.

1. 디자이너에서 만든 파이프라인 실행을 선택 합니다.

    ![파이프라인 실행 페이지](./media/how-to-debug-pipelines/designer-pipelines.png)

1. 미리 보기 창에서 모듈을 선택 합니다.
1. 모듈의 오른쪽 창에서  **출력 + 로그** 탭으로 이동 합니다.
1. 오른쪽 창을 확장 하 여 브라우저에서 **70_driver_log.txt** 파일을 보거나 파일을 선택 하 여 로그를 로컬로 다운로드 합니다.

> [!IMPORTANT]
> 파이프라인 실행 정보 페이지에서 파이프라인을 업데이트 하려면 파이프라인 실행을 새 파이프라인 초안으로 **복제** 해야 합니다. 파이프라인 실행은 파이프라인의 스냅숏입니다. 로그 파일과 비슷하며 변경할 수 없습니다. 

## <a name="application-insights"></a>Application Insights
이러한 방식으로 OpenCensus Python 라이브러리를 사용 하는 방법에 대 한 자세한 내용은이 가이드: [Application Insights machine learning 파이프라인 디버그 및 문제 해결](how-to-debug-pipelines-application-insights.md) 을 참조 하세요.

## <a name="interactive-debugging-with-visual-studio-code"></a>Visual Studio Code를 사용한 대화형 디버깅

ML 파이프라인에서 사용 되는 Python 코드를 대화형으로 디버깅 해야 하는 경우도 있습니다. Visual Studio Code (VS Code) 및 debugpy를 사용 하 여 학습 환경에서 실행 되는 코드에 연결할 수 있습니다. 자세한 내용은 [VS Code의 대화형 디버깅 가이드](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* 을 사용 하는 방법에 대 한 전체 자습서는 `ParallelRunStep` [자습서: 일괄 처리 점수 매기기를 위한 Azure Machine Learning 파이프라인 빌드](tutorial-pipeline-batch-scoring-classification.md)를 참조 하세요.

* ML 파이프라인에서 자동화 된 기계 학습을 보여 주는 전체 예제는 [Python의 Azure Machine Learning 파이프라인에서 자동화 된 ML 사용](how-to-use-automlstep-in-pipelines.md)을 참조 하세요.

* [Azureml-파이프라인-코어](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py&preserve-view=true) 패키지 및 [azureml 파이프라인 단계](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py&preserve-view=true) 패키지에 대 한 도움말은 SDK 참조를 참조 하세요.

* [디자이너 예외 및 오류 코드](algorithm-module-reference/designer-error-codes.md)의 목록을 참조 하세요.
