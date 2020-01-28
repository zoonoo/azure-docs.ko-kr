---
title: 빅 데이터에서 일괄 예측 실행
titleSuffix: Azure Machine Learning
description: Azure Machine Learning의 ParallelRunStep을 사용하여 대량의 데이터에 대한 비동기 유추를 수행하는 방법을 알아봅니다. ParallelRunStep은 기본적으로 병렬 처리 기능을 제공하며 빅 데이터 사용 사례를 위한 높은 처리량의 fire-and-forget(실행 후 망각) 유추에 맞게 최적화됩니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: vaidyas
author: vaidya-s
ms.date: 01/15/2020
ms.custom: Ignite2019
ms.openlocfilehash: ff366468c994d8ba151dd476a5bcccc52bb7309f
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122836"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Azure Machine Learning을 사용하여 대량의 데이터에 대한 일괄 처리 유추 실행
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning을 사용하여 대량의 데이터를 비동기 병렬 방식으로 처리하는 방법을 알아봅니다. 여기서 설명하는 ParallelRunStep은 공개 미리 보기로 제공됩니다. 유추 및 처리 데이터를 생성하는 성능과 처리량이 뛰어난 방법입니다. 비동기 기능을 기본 제공합니다.

ParallelRunStep을 사용하면 간단하게 오프라인 유추를 수 테라바이트에 달하는 프로덕션 데이터의 대규모 머신 클러스터로 확장하여 생산성을 향상하고 비용을 최적화할 수 있습니다.

이 문서에서는 다음 작업에 대해 알아봅니다.

> * 원격 컴퓨팅 리소스를 만듭니다.
> * 사용자 지정 유추 스크립트를 작성합니다.
> * [MNIST](https://publicdataset.azurewebsites.net/dataDetail/mnist/) 데이터 세트를 기반으로 미리 학습된 이미지 분류 모델을 등록하는 [기계 학습 파이프라인](concept-ml-pipelines.md)을 만듭니다. 
> * 모델을 사용하여 Azure Blob 스토리지 계정에서 사용할 수 있는 샘플 이미지에 대한 일괄 처리 유추를 실행합니다. 

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning 무료 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* 안내형 빠른 시작의 경우 Azure Machine Learning 작업 영역 또는 Notebook 가상 머신이 아직 없으면 [설정 자습서](tutorial-1st-experiment-sdk-setup.md)를 완료하세요. 

* 사용자 고유의 환경 및 종속성을 관리하려면 사용자 고유의 환경을 구성하는 방법에 대한 [방법 가이드](how-to-configure-environment.md)를 참조하세요. 환경에서 `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps`를 실행하여 필요한 종속성을 다운로드합니다.

## <a name="set-up-machine-learning-resources"></a>기계 학습 리소스 설정

다음은 일괄 처리 유추 파이프라인을 실행하는 데 필요한 리소스를 설정하는 작업입니다.

- 유추할 이미지가 들어 있는 Blob 컨테이너를 가리키는 데이터 저장소를 만듭니다.
- 일괄 처리 유추 파이프라인 단계의 입력 및 출력으로 데이터 참조를 설정합니다.
- 일괄 처리 유추 단계를 실행하도록 컴퓨팅 클러스터를 설정합니다.

### <a name="create-a-datastore-with-sample-images"></a>샘플 이미지로 데이터 저장소 만들기

`pipelinedata` 계정의 공용 Blob 컨테이너 `sampledata`에서 MNIST 평가 세트를 가져옵니다. 이 컨테이너를 가리키는 이름인 `mnist_datastore`를 사용하여 데이터 저장소를 만듭니다. 이어지는 `register_azure_blob_container` 호출에서, `overwrite` 플래그를 `True`로 설정하면 이전에 만든 데이터 저장소를 해당 이름으로 덮어씁니다. 

`datastore_name`, `container_name` 및 `account_name`에 대한 고유한 값을 입력하여 Blob 컨테이너를 가리키도록 이 단계를 변경할 수 있습니다.

```python
from azureml.core import Datastore
from azureml.core import Workspace

# Load workspace authorization details from config.json
ws = Workspace.from_config()

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

다음으로, 작업 영역 기본 데이터 저장소를 출력 데이터 저장소로 설정합니다. 유추 출력에 사용할 것입니다.

작업 영역을 만들 때 [Azure File](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)  및 [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) 는 기본적으로 작업 영역에 연결됩니다. Azure Files는 작업 영역의 기본 데이터 저장소이지만, Blob Storage를 데이터 저장소로 사용할 수도 있습니다. 자세한 내용은 [Azure 스토리지 옵션](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)을 참조하세요.

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-inputs-and-outputs"></a>데이터 입력 및 출력 구성

이제 다음을 포함한 데이터 입력 및 출력을 구성해야 합니다.

- 입력 이미지가 포함되는 디렉터리입니다.
- 미리 학습된 모델이 저장되는 디렉터리
- 레이블을 포함하는 디렉터리
- 출력에 대한 디렉터리

`Dataset`는 Azure Machine Learning에서 데이터를 탐색, 변환 및 관리하는 데 사용되는 클래스입니다. 이 클래스에는 `TabularDataset` 및 `FileDataset`의 두 가지 형식이 있습니다. 이 예제에서는 일괄 처리 유추 파이프라인 단계의 입력으로 `FileDataset`를 사용합니다. 

> [!NOTE] 
> 현재는 일괄 처리 유추의 `FileDataset` 지원 범위가 Azure Blob 스토리지로 제한됩니다. 

사용자 지정 유추 스크립트의 다른 데이터 세트를 참조할 수도 있습니다. 예를 들어 `Dataset.register` 및 `Dataset.get_by_name`을 사용하여 이미지 레이블 지정용 스크립트의 레이블에 액세스할 수 있습니다.

Azure Machine Learning 데이터 세트에 대한 자세한 내용은 [데이터 세트 만들기 및 액세스(미리 보기)](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)를 참조하세요.

`PipelineData` 개체는 파이프라인 단계 간에 중간 데이터를 전송하는 데 사용됩니다. 이 예제에서는 유추 출력에 사용합니다.

```python
from azureml.core.dataset import Dataset

mnist_ds_name = 'mnist_sample_data'

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
registered_mnist_ds = input_mnist_ds.register(ws, mnist_ds_name, create_new_version=True)
named_mnist_ds = registered_mnist_ds.as_named_input(mnist_ds_name)

output_dir = PipelineData(name="inferences", 
                          datastore=def_data_store, 
                          output_path_on_compute="mnist/results")
```

### <a name="set-up-a-compute-target"></a>컴퓨팅 대상 설정

Azure Machine Learning에서 *컴퓨팅*(또는 *컴퓨팅* 대상)은 기계 학습 파이프라인에서 계산 단계를 수행하는 머신 또는 클러스터를 가리킵니다. 다음 코드를 실행하여 [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) 대상을 기반으로 CPU를 만듭니다.

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

## <a name="prepare-the-model"></a>모델 준비

[미리 학습된 이미지 분류 모델을 다운로드](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz)한 다음, `models` 디렉터리로 추출합니다.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

그런 다음, 원격 컴퓨팅 리소스에 사용할 수 있도록 모델을 작업 영역에 등록합니다.

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>유추 스크립트 작성

>[!Warning]
>다음 코드는 [샘플 Notebook](https://aka.ms/batch-inference-notebooks)에서 사용하는 샘플일 뿐입니다. 각자 시나리오에 맞는 고유의 스크립트를 만들어야 합니다.

스크립트에 다음 두 함수가 *포함되어야 합니다*.
- `init()`: 이후 유추를 위해 비용이 많이 드는 준비 또는 일반적인 준비에 이 함수를 사용합니다. 예를 들어 모델을 글로벌 개체에 로드하는 데 사용합니다. 이 함수는 프로세스를 시작할 때 한 번만 호출됩니다.
-  `run(mini_batch)`: 이 함수는 각 `mini_batch` 인스턴스에 대해 실행됩니다.
    -  `mini_batch`: 병렬 실행 단계에서는 run 메서드를 호출하고 목록 또는 Pandas DataFrame을 메서드에 인수로 전달합니다. 입력이 FileDataset이면 min_batch의 각 항목이 파일 경로이고, 입력이 TabularDataset이면 Pandas 데이터 프레임입니다.
    -  `response`: run() 메서드는 Pandas 데이터 프레임 또는 배열을 반환해야 합니다. append_row output_action의 경우 반환되는 요소가 공통 출력 파일에 추가됩니다. summary_only의 경우 요소의 내용이 무시됩니다. 모든 출력 작업의 경우 반환되는 각 출력 요소는 입력 미니 일괄 처리의 입력 요소에 대한 성공적인 실행 하나를 나타냅니다. 사용자는 입력을 실행 결과에 매핑하기에 충분한 데이터가 실행에 포함되어 있는지 확인해야 합니다. 실행 출력은 출력 파일에 기록되지만 순서대로 기록된다는 보장은 없으므로, 사용자는 출력의 키를 사용하여 입력에 매핑해야 합니다.

```python
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://aka.ms/batch-inference-notebooks)
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

### <a name="how-to-access-other-files-in-source-directory-in-entry_script"></a>entry_script의 원본 디렉터리에 있는 다른 파일에 액세스하는 방법

입력 스크립트와 동일한 디렉터리에 다른 파일 또는 폴더가 있는 경우 현재 작업 디렉터리를 찾아 참조할 수 있습니다.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>ParallelRunStep을 포함하는 파이프라인 빌드 및 실행

이제 파이프라인을 빌드하는 데 필요한 모든 준비가 끝났습니다.

### <a name="prepare-the-run-environment"></a>실행 환경 준비

먼저 스크립트의 종속성을 지정합니다. 나중에 파이프라인 단계를 만들 때 이 개체가 사용됩니다.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.13.1", "pillow"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
batch_env.spark.precache_packages = False
```

### <a name="specify-the-parameters-for-your-batch-inference-pipeline-step"></a>일괄 처리 유추 파이프라인 단계에 대한 매개 변수를 지정합니다.

`ParallelRunConfig`는 Azure Machine Learning 파이프라인 내에서 새로 도입된 일괄 처리 유추 `ParallelRunStep` 인스턴스에 대한 주요 구성입니다. 이를 사용하여 스크립트를 래핑하고 다음 매개 변수를 모두 포함하여 필요한 매개 변수를 구성합니다.
- `entry_script`: 여러 노드에서 병렬로 실행되는 로컬 파일 경로인 사용자 스크립트입니다. `source_directory`가 있으면 상대 경로를 사용합니다. 없으면 머신에서 액세스할 수 있는 아무 경로를 사용합니다.
- `mini_batch_size`: 단일 `run()` 호출에 전달된 미니 일괄 처리의 크기입니다. (선택 사항입니다. 기본값은 FileDataset의 경우 `10` 파일이고, TabularDataset의 경우 `1MB`입니다.)
    - `FileDataset`의 경우 최솟값이 `1`인 파일 수입니다. 여러 파일을 한 미니 일괄 처리로 결합할 수 있습니다.
    - `TabularDataset`의 경우 데이터의 크기입니다. 예제 값은 `1024`, `1024KB`, `10MB` 및 `1GB`입니다. 권장 값은 `1MB`입니다. `TabularDataset`의 미니 일괄 처리는 파일 경계를 넘지 않습니다. 예를 들어 다양한 크기의 .csv 파일이 있는 경우 가장 작은 파일은 100KB이고 가장 큰 파일은 10MB입니다. `mini_batch_size = 1MB`를 설정하는 경우 크기가 1MB보다 작은 파일은 하나의 미니 일괄 처리로 취급됩니다. 크기가 1MB보다 큰 파일은 여러 개의 미니 일괄 처리로 분할됩니다.
- `error_threshold`: 처리 중에 무시해야 하는 `FileDataset`에 대한 `TabularDataset` 및 파일 오류에 대한 레코드 실패 횟수입니다. 전체 입력의 오류 횟수가 이 값을 초과하면 작업이 중단됩니다. 오류 임계값은 `run()` 메서드로 전송되는 개별 미니 일괄 처리가 아닌 전체 입력에 적용됩니다. 범위는 `[-1, int.max]`입니다. `-1` 부분은 처리 중에 발생하는 모든 오류를 무시한다는 뜻입니다.
- `output_action`: 다음 값 중 하나는 출력을 구성하는 방법을 나타냅니다.
    - `summary_only`: 사용자 스크립트는 출력을 저장합니다. `ParallelRunStep`은 출력을 오류 임계값 계산에만 사용합니다.
    - `append_row`: 모든 입력 파일의 경우 출력 폴더에 줄로 구분된 모든 출력을 추가하는 하나의 파일만 생성됩니다. 이 파일 이름은 `parallel_run_step.txt`입니다.
- `source_directory`: 컴퓨팅 대상에서 실행할 모든 파일이 포함된 폴더 경로입니다(선택 사항).
- `compute_target`: `AmlCompute`만 지원됩니다.
- `node_count`: 사용자 스크립트를 실행하는 데 사용되는 컴퓨팅 노드의 수입니다.
- `process_count_per_node`: 노드당 프로세스 수입니다.
- `environment`: Python 환경 정의입니다. 기존 Python 환경을 사용하거나 실험을 위한 임시 환경을 설정하도록 구성할 수 있습니다. 이 정의는 필요한 애플리케이션 종속성을 설정하는 역할도 담당합니다(선택 사항).
- `logging_level`: 로그의 자세한 정도입니다. 값의 자세한 정도는 `WARNING`, `INFO`, `DEBUG` 순서로 높아집니다. (선택 사항이며 기본값은 `INFO`)
- `run_invocation_timeout`: `run()` 메서드 호출 시간 제한(초)입니다. (선택 사항이며 기본값은 `60`)

```python
from azureml.contrib.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory=scripts_folder,
    entry_script="digit_identification.py",
    mini_batch_size="5",
    error_threshold=10,
    output_action="append_row",
    environment=batch_env,
    compute_target=compute_target,
    node_count=4)
```

### <a name="create-the-pipeline-step"></a>파이프라인 단계 만들기

스크립트, 환경 구성 및 매개 변수를 사용하여 파이프라인 단계를 만듭니다. 스크립트의 실행 대상으로 작업 영역에 이미 연결한 컴퓨팅 대상을 지정합니다. `ParallelRunStep`을 사용하여 다음 매개 변수를 모두 사용하는 일괄 처리 유추 파이프라인 단계를 만듭니다.
- `name`: 단계의 이름이며, 고유한 3-32자 문자와 regex ^\[a-z\]([-a-z0-9]*[a-z0-9])?$로 구성되어야 한다는 명명 제한이 있습니다.
- `models`: Azure Machine Learning 모델 레지스트리에 이미 등록된 모델 이름이 0개 이상 있습니다.
- `parallel_run_config`: 이전에 정의된 `ParallelRunConfig` 개체입니다.
- `inputs`: 하나 이상의 단일 형식 Azure Machine Learning 데이터 세트입니다.
- `output`: 출력 디렉터리에 해당하는 `PipelineData` 개체입니다.
- `arguments`: 사용자 스크립트에 전달된 인수 목록입니다(선택 사항).
- `allow_reuse`: 동일한 설정/입력으로 실행할 때 이전 결과를 단계에 다시 사용할 것인지 여부를 지정합니다. 이 매개 변수가 `False`이면 파이프라인 실행 중에 이 단계에 대해 항상 새로운 실행이 생성됩니다. (선택 사항이며 기본값은 `True`)

```python
from azureml.contrib.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="batch-mnist",
    models=[model],
    parallel_run_config=parallel_run_config,
    inputs=[named_mnist_ds],
    output=output_dir,
    arguments=[],
    allow_reuse=True
)
```

>[!Note]
> 위의 단계는 [필수 구성 요소](#prerequisites)에 설명된 대로 `azureml-contrib-pipeline-steps`에 따라 달라집니다. 

### <a name="run-the-pipeline"></a>파이프라인 실행

이제 파이프라인을 실행합니다. 먼저, 이전에 만든 작업 영역 참조와 파이프라인 단계를 사용하여 `Pipeline` 개체를 만듭니다. `steps` 매개 변수는 단계의 배열입니다. 여기서는 하나의 일괄 처리 채점 단계만 있습니다. 여러 단계가 있는 파이프라인을 빌드하려면 이 배열에서 단계를 순서대로 배치합니다.

다음으로, `Experiment.submit()` 함수를 사용하여 실행할 파이프라인을 제출합니다.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
pipeline_run = Experiment(ws, 'digit_identification').submit(pipeline)
```

## <a name="monitor-the-parallel-run-job"></a>병렬 실행 작업 모니터링

일괄 처리 유추 작업을 완료하는 데 시간이 오래 걸릴 수 있습니다. 이 예제에서는 Jupyter 위젯을 사용하여 진행률을 모니터링합니다. 다음을 사용하여 작업의 진행률을 관리할 수도 있습니다.

* Azure Machine Learning Studio 
* [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py) 개체의 콘솔 출력

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="next-steps"></a>다음 단계

이 프로세스가 작동하는 모습을 처음부터 끝까지 보려면 [일괄 처리 유추 Notebook](https://aka.ms/batch-inference-notebooks)을 사용해 보세요. 

ParallelRunStep에 대한 디버깅 및 문제 해결 지침은 [이 방법 가이드](how-to-debug-parallel-run-step.md)를 참조하세요.

파이프라인의 디버깅 및 문제 해결 지침은 [방법 가이드](how-to-debug-pipelines.md)를 참조하세요.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

