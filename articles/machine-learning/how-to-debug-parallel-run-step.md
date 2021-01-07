---
title: ParallelRunStep 문제 해결
titleSuffix: Azure Machine Learning
description: Machine learning 파이프라인에서 ParallelRunStep를 사용 하 여 오류가 발생 하는 경우 문제를 해결 하는 방법에 대 한 팁입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.custom: troubleshooting
ms.reviewer: jmartens, larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 09/23/2020
ms.openlocfilehash: 468af45f88c097e6f792a28df61ea0f1aea0d1ef
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740286"
---
# <a name="troubleshooting-the-parallelrunstep"></a>ParallelRunStep 문제 해결

이 문서에서는 [AZURE MACHINE LEARNING SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)에서 [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?preserve-view=true&view=azure-ml-py) 클래스를 사용 하 여 오류가 발생 하는 경우 문제를 해결 하는 방법에 대해 알아봅니다.

파이프라인 문제 해결에 대 한 일반적인 팁은 [machine learning 파이프라인 문제 해결](how-to-debug-pipelines.md)을 참조 하세요.

## <a name="testing-scripts-locally"></a>로컬에서 스크립트 테스트

 ParallelRunStep는 ML 파이프라인에서 한 단계로 실행 됩니다. 첫 번째 단계로 [스크립트를 로컬로 테스트할](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines) 수 있습니다.

##  <a name="script-requirements"></a>스크립트 요구 사항

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

## <a name="debugging-scripts-from-remote-context"></a>원격 컨텍스트에서 스크립트 디버그

채점 스크립트의 로컬 디버깅을 실제 파이프라인에서의 디버깅으로 전환하는 것은 어려운 작업일 수 있습니다. 포털에서 로그를 찾는 방법에 대 한 자세한 내용은  [원격 컨텍스트에서 스크립트 디버깅에 대 한 machine learning 파이프라인 섹션](how-to-debug-pipelines.md)을 참조 하세요. 해당 섹션의 정보는 ParallelRunStep에도 적용됩니다.

예를 들어 로그 파일 `70_driver_log.txt`에는 ParallelRunStep 코드를 시작하는 컨트롤러의 정보가 포함됩니다.

ParallelRunStep 작업의 분산 특성으로 인해 여러 원본의 로그가 있습니다. 그러나 다음과 같이 높은 수준의 정보를 제공하는 두 통합 파일이 생성됩니다.

- `~/logs/job_progress_overview.txt`: 이 파일은 지금까지 생성된 미니 일괄 처리(작업)의 수와 지금까지 처리된 미니 일괄 처리 수에 대한 높은 수준의 정보를 제공합니다. 이 끝에서는 작업의 결과를 보여 줍니다. 작업이 실패할 경우 오류 메시지와, 문제 해결을 시작할 지점이 표시됩니다.

- `~/logs/sys/master_role.txt`:이 파일은 실행 중인 작업의 주 노드 (orchestrator 라고도 함)를 제공 합니다. 작업 만들기, 진행률 모니터링, 실행 결과가 포함됩니다.

EntryScript 도우미 및 print 문을 사용하여 항목 스크립트에서 생성된 로그는 다음 파일에 있습니다.

- `~/logs/user/entry_script_log/<ip_address>/<process_name>.log.txt`: 이러한 파일은 EntryScript 도우미를 사용 하 여 entry_script에서 작성 한 로그입니다.

- `~/logs/user/stdout/<ip_address>/<process_name>.stdout.txt`: 이러한 파일은 entry_script의 stdout (예: print 문)의 로그입니다.

- `~/logs/user/stderr/<ip_address>/<process_name>.stderr.txt`: 이러한 파일은 stderr의 entry_script에 대 한 로그입니다.

스크립트의 오류에 대한 간략한 해석은 다음과 같습니다.

- `~/logs/user/error.txt`: 이 파일은 스크립트의 오류를 요약하려 합니다.

스크립트의 오류에 대한 자세한 내용은 다음과 같습니다.

- `~/logs/user/error/`: 항목 스크립트를 로드 하 고 실행 하는 동안 throw 된 예외에 대 한 전체 스택 추적을 포함 합니다.

각 노드가 점수 스크립트를 실행하는 방법을 완전히 해석하려면 각 노드에 대한 개별 프로세스 로그를 확인합니다. 프로세스 로그는 작업자 노드에서 그룹화한 `sys/node` 폴더에 있습니다.

- `~/logs/sys/node/<ip_address>/<process_name>.txt`:이 파일은 작업자에서 선택 하거나 완료할 때 각 미니 일괄 처리에 대 한 자세한 정보를 제공 합니다. 각 미니 일괄 처리에 대해 이 파일에는 다음이 포함됩니다.

    - 작업자 프로세스의 IP 주소 및 PID 
    - 전체 항목 수, 성공적으로 처리된 항목 수, 실패한 항목 수
    - 시작 시간, 기간, 처리 시간 및 실행 메서드 시간.

각 작업자에 대한 프로세스의 리소스 사용 정보도 확인할 수 있습니다. 이 정보는 CSV 형식이며 `~/logs/sys/perf/<ip_address>/node_resource_usage.csv`에 있습니다. 각 프로세스에 대 한 정보는에서 확인할 수 있습니다 `~logs/sys/perf/<ip_address>/processes_resource_usage.csv` .

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>원격 컨텍스트의 내 사용자 스크립트는 어떻게 기록하나요?

ParallelRunStep는 process_count_per_node 기반으로 한 노드에서 여러 프로세스를 실행할 수 있습니다. 노드의 각 프로세스에서 로그를 구성 하 고 print 및 log 문을 결합 하려면 아래와 같이 ParallelRunStep로 거를 사용 하는 것이 좋습니다. EntryScript에서로 거를 가져오고 로그가 포털의 **로그/사용자** 폴더에 표시 되도록 합니다.

**로거를 사용 하는 샘플 항목 스크립트:**
```python
from azureml_user.parallel_run import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>조회 테이블을 포함하는 하나 이상의 파일 등, 측면 입력을 모든 작업자에게 전달하려면 어떻게 해야 하나요?

사용자는 ParalleRunStep의 side_inputs 매개 변수를 사용 하 여 스크립트에 참조 데이터를 전달할 수 있습니다. Side_inputs로 제공 되는 모든 데이터 집합은 각 작업자 노드에 탑재 됩니다. 사용자는 인수를 전달 하 여 탑재 위치를 가져올 수 있습니다.

참조 데이터를 포함 하는 데이터 [집합](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py) 을 생성 하 고 작업 영역에 등록 합니다. `ParallelRunStep`의 `side_inputs` 매개 변수에 전달합니다. 또한 섹션에 해당 경로를 추가 하 여 `arguments` 탑재 된 경로에 쉽게 액세스할 수 있습니다.

```python
label_config = label_ds.as_named_input("labels_input")
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

그런 다음, 다음과 같이 추론 스크립트(예: init () 메서드)에서 액세스할 수 있습니다.

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

### <a name="how-to-use-input-datasets-with-service-principal-authentication"></a>서비스 주체 인증과 함께 입력 데이터 집합을 사용 하는 방법

사용자는 작업 영역에서 사용 되는 서비스 주체 인증으로 입력 데이터 집합을 전달할 수 있습니다. ParallelRunStep에서 이러한 데이터 집합을 사용 하려면 ParallelRunStep 구성을 생성 하기 위해 해당 데이터 집합을 등록 해야 합니다.

```python
service_principal = ServicePrincipalAuthentication(
    tenant_id="**_",
    service_principal_id="_*_",
    service_principal_password="_*_")
 
ws = Workspace(
    subscription_id="_*_",
    resource_group="_*_",
    workspace_name="_*_",
    auth=service_principal
    )
 
default_blob_store = ws.get_default_datastore() # or Datastore(ws, '_*_datastore-name_*_') 
ds = Dataset.File.from_files(default_blob_store, '_*path**_')
registered_ds = ds.register(ws, '_*_dataset-name_*_', create_new_version=True)
```

## <a name="next-steps"></a>다음 단계

_ [Azure Machine Learning 파이프라인을 보여 주는 이러한 Jupyter 노트북](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines) 참조

* [Azureml-파이프라인 단계](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py) 패키지에 대 한 도움말은 SDK 참조를 참조 하세요. ParallelRunStep 클래스에 대 한 참조 [설명서](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?preserve-view=true&view=azure-ml-py) 를 봅니다.

* ParallelRunStep와 함께 파이프라인 사용에 대 한 [고급 자습서](tutorial-pipeline-batch-scoring-classification.md) 를 따르세요. 이 자습서에서는 다른 파일을 측면 입력으로 전달 하는 방법을 보여 줍니다.