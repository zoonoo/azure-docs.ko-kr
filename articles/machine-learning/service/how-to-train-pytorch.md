---
title: PyTorch 모델 학습 및 등록
titleSuffix: Azure Machine Learning service
description: 이 문서에서는 학습 Azure Machine Learning 서비스를 사용 하 여 PyTorch 모델을 등록 하는 방법을 보여 줍니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 06/18/2019
ms.custom: seodec18
ms.openlocfilehash: d9c953eeecedf14a8f3fae43c5d4713252d58b4c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840094"
---
# <a name="train-and-register-pytorch-models-at-scale-with-azure-machine-learning-service"></a>학습 및 Azure Machine Learning 서비스를 사용 하 여 대규모로 PyTorch 모델 등록

이 문서에서는 학습 Azure Machine Learning 서비스를 사용 하 여 PyTorch 모델을 등록 하는 방법을 보여 줍니다. 기반 [학습 자습서 PyTorch의 전송](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html) 닭 및 turkeys의 이미지에 대 한 심층 신경망 (DNN) 분류자를 작성 하는 합니다.

[PyTorch](https://pytorch.org/) deep neural network DNN ()를 만드는 데 일반적으로 오픈 소스 계산 프레임 워크입니다. Azure Machine Learning 서비스를 사용 하 여 탄력적인 클라우드 계산 리소스를 사용 하 여 오픈 소스 학습 작업 규모를 신속 하 게 확장할 수 있습니다. 교육 실행, 버전 모델을 통해 추적할 수 있습니다 모델 등을 배포 합니다.

PyTorch부터 모델을 개발 하 든 기존 모델을 클라우드로 가져오는 Azure Machine Learning 서비스 도움이 될 수 있습니다 프로덕션이 준비 된 모델을 작성 합니다.

## <a name="prerequisites"></a>전제 조건

이러한 환경 중 하나에서이 코드를 실행 합니다.

 - Azure Machine Learning Notebook VM-다운로드 나 설치 필요 없이

    - 완료 합니다 [클라우드 기반 notebook 퀵 스타트](quickstart-run-cloud-notebook.md) SDK 및 샘플 리포지토리를 사용 하 여 미리 로드 전용된 노트북 서버를 만들려면.
    - Notebook 서버에 샘플 폴더에서이 디렉터리로 이동 하 여 완료 되 고 확장 된 notebook을 찾습니다: **방법으로-사용-azureml > 학습 된 심층 학습 > train-hyperparameter-tune-deploy-with-pytorch** 폴더입니다. 
 
 - 사용자 고유의 Jupyter Notebook 서버

    - [Azure Python SDK 학습 컴퓨터 설치](setup-create-workspace.md#sdk)
    - [작업 영역 구성 파일 만들기](setup-create-workspace.md#write-a-configuration-file)
    - [샘플 스크립트 파일 다운로드](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch) `pytorch_train.py`
     
    완료 된 찾을 수도 있습니다 [Jupyter Notebook 버전](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) GitHub 샘플 페이지에이 가이드의 합니다. Notebook 지능형 하이퍼 매개 변수 튜닝, 모델 배포 및 notebook 위젯 확장된 섹션을 포함 합니다.

## <a name="set-up-the-experiment"></a>실험을 설정

이 섹션에서는 필요한 python 패키지를 로드, 초기화 작업 영역, 실험, 만들기 및 학습 데이터와 학습 스크립트를 업로드 하 여 학습 실험을 설정 합니다.

### <a name="import-packages"></a>패키지 가져오기

먼저, 필요한 Python 라이브러리를 가져와야 합니다.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import PyTorch
```

### <a name="initialize-a-workspace"></a>작업 영역 초기화

[Azure Machine Learning 서비스 작업 영역](concept-workspace.md) 서비스에 대 한 최상위 리소스입니다. 만든 모든 아티팩트를 작업할 수 있는 중앙된 위치를 사용 하 여 제공 합니다. Python SDK에서 작업 영역 아티팩트를 만들어 액세스할 수 있습니다는 [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) 개체입니다.

작업 영역 개체를 만들어야 합니다 `config.json` 에서 만든 파일을 [전제 조건 섹션](#prerequisites)합니다.

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>실험 만들기

실험 및 학습 스크립트를 보관할 폴더를 만듭니다. 이 예제에서는 "pytorch-새"를 호출 하는 실험을 만듭니다.

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>데이터 가져오기

데이터 집합에 대 한 120 학습 이미지의 각 turkeys 및 닭, 각 클래스에 대 한 유효성 검사 이미지 100 개를 사용 하 여 구성 됩니다. 다운로드 하 고 데이터 집합 학습 스크립트의 일부로 추출 `pytorch_train.py`합니다. 이미지의 하위 집합은는 [v5 데이터 집합 열기 이미지](https://storage.googleapis.com/openimages/web/index.html)합니다.

### <a name="prepare-training-scripts"></a>학습 스크립트를 준비 합니다.

이 자습서에서는 학습 스크립트 `pytorch_train.py`을 이미 제공 됩니다. 실제로 이므로 모든 사용자 지정 학습 스크립트를 Azure Machine Learning 서비스를 사용 하 여 실행 합니다.

Pytorch 학습 스크립트를 업로드 `pytorch_train.py`합니다.

```Python
shutil.copy('pytorch_train.py', project_folder)
```

그러나 Azure Machine Learning 서비스 추적 및 메트릭 기능을 사용 하려는 경우에 교육 스크립트 내에서 약간 코드를 추가 해야 합니다. 메트릭 추적의 예제에서 확인할 수 있습니다 `pytorch_train.py`합니다.

## <a name="create-a-compute-target"></a>계산 대상 만들기

실행 되도록 PyTorch 작업에 대 한 계산 대상을 만듭니다. 이 예제에서는 Azure Machine Learning GPU 가능 계산 클러스터를 만듭니다.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

계산 대상에 대 한 자세한 내용은 참조는 [계산 대상을 란](concept-compute-target.md) 문서.

## <a name="create-a-pytorch-estimator"></a>PyTorch 예측 만들기

합니다 [PyTorch 스 티 메이 터](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) 계산 대상에서 PyTorch 교육 작업을 시작 하는 간단한 방법을 제공 합니다.

PyTorch 스 티 메이 터 제네릭 통해 구현 됩니다 [ `estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 클래스에서 임의 프레임 워크를 지 원하는 데 사용할 수 있습니다. 제네릭 평가기를 사용 하 여 모델을 학습 하는 방법에 대 한 자세한 내용은 참조 하세요. [평가기를 사용 하 여 Azure Machine Learning을 사용 하 여 모델을 학습 합니다.](how-to-train-ml-models.md)

통해 해당 이름을 전달 하 여 결과 docker 이미지에 설치 된 패키지 있습니다 학습 스크립트 추가 pip 또는 conda 패키지 실행에 필요한 경우는 `pip_packages` 고 `conda_packages` 인수입니다.

```Python
script_params = {
    '--num_epochs': 30,
    '--output_dir': './outputs'
}

estimator = PyTorch(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='pytorch_train.py',
                    use_gpu=True,
                    pip_packages=['pillow==5.4.1'])
```

## <a name="submit-a-run"></a>실행을 제출 합니다.

합니다 [개체 실행](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) 작업이 실행 되 고 완료 된 후 실행된 기록에 인터페이스를 제공 합니다.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

실행을 실행 하는 다음 단계를 통해 이동 합니다.

- **준비**: PyTorch 스 티 메이 터에 따라 docker 이미지를 생성 됩니다. 이미지 작업 공간의 container registry에 업로드 이며 이후 실행에 대 한 캐시 됩니다. 로그는 실행된 기록에도 스트리밍되고 진행 상황을 모니터링 볼 수 있습니다.

- **크기 조정**: 클러스터는 Batch AI 클러스터 노드를 현재 사용할 수 있는 것 보다는 실행을 실행 해야 하는 경우 확장 하려고 합니다.

- **Running**: 스크립트 폴더에 있는 모든 스크립트가 계산 대상에 업로드 됩니다, 데이터 저장소는 탑재 하거나 복사할 수 고를 entry_script 실행 됩니다. Stdout에서 출력 및. / logs 폴더 실행된 기록에 스트리밍됩니다 및 실행을 모니터링 할 수 있습니다.

- **후 처리 중**: 합니다. / 출력 폴더 실행의 실행된 기록에 복사 됩니다.

## <a name="register-or-download-a-model"></a>등록 또는 모델을 다운로드 합니다.

모델을 학습 한 후 작업 영역에 등록할 수 있습니다. 모델 등록 하면 저장소와 버전 간소화 하기 위해 작업 영역에서 모델 [모델 관리 및 배포](concept-model-management-and-deployment.md)합니다.

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

또한 실행 개체를 사용 하 여 모델의 로컬 복사본을 다운로드할 수 있습니다. 학습 스크립트에서 `pytorch_train.py`, 개체를 저장할 PyTorch 계산 대상 (로컬)를 로컬 폴더에 모델을 유지 합니다. 복사본을 다운로드 하려면 Run 개체를 사용할 수 있습니다.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>분산 학습

합니다 [ `PyTorch` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) 스 티 메이 터 CPU 및 GPU 클러스터 간에 분산된 교육을도 지원 합니다. 분산된 PyTorch 작업을 쉽게 실행할 수 있습니다 하 고 Azure Machine Learning 서비스를 오케스트레이션 관리 됩니다.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) 는 오픈 소스 분산된 교육 Uber 개발한에 대 한 모든 프레임 워크를 줄일 합니다. 분산된 된 GPU PyTorch 작업 하는 쉬운 경로 제공합니다.

Horovod를 사용 하려면 지정는 [ `MpiConfiguration` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) 에 대 한 개체는 `distributed_training` PyTorch 생성자에 매개 변수입니다. 이 매개 변수 Horovod 라이브러리에 학습 스크립트에서 사용 하 여 설치 되어 있는지 확인 합니다.


```Python
from azureml.train.dnn import PyTorch

estimator= PyTorch(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```
Horovod 및 해당 종속성을 설치할를 학습 스크립트에서 가져올 수 있도록 `train.py` 다음과 같습니다.

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>ONNX 내보내려면

사용 하 여 유추를 최적화 하는 [ONNX 런타임](concept-onnx.md), PyTorch 학습 된 모델을 ONNX 형식으로 변환 합니다. 모델 점수 매기기 또는 유추 하는 단계 프로덕션 데이터에 가장 일반적으로 예측에 대 한 배포 된 모델이 사용 되는 위치입니다. 참조 된 [자습서](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) 예입니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 학습 하 고 Azure Machine Learning 서비스에서 PyTorch 모델을 등록 합니다. 모델을 배포 하는 방법에 알아보려면 모델 배포 문서를 진행 합니다.

> [!div class="nextstepaction"]
> [방법 및 모델을 배포할 수 있는 위치](how-to-deploy-and-where.md)
* [학습 중에 실행 메트릭 추적](how-to-track-experiments.md)
* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)
* [학습된 모델 배포](how-to-deploy-and-where.md)
