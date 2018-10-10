---
title: Azure Machine Learning으로 기계 학습 모델 학습
description: Azure Machine Learning 서비스를 사용하여 일반적인 기계 학습 및 심층 학습 모델의 단일 노드 분산 학습을 수행하는 방법을 알아봅니다.
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 50b808b5769f2160d765ecdb431d71856e651b33
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46983585"
---
# <a name="how-to-train-models-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 모델을 학습하는 방법

기계 학습 모델, 특히 심층 신경망을 학습하는 것은 종종 시간 및 계산 집약적 작업입니다. 학습 스크립트를 작성하고 로컬 머신의 작은 데이터 하위 집합에서 실행을 마친 후에는 워크로드를 강화하려고 할 수도 있습니다.

학습을 용이하게 하기 위해 Azure Machine Learning Python SDK는 사용자가 Azure 에코시스템에서 모델을 쉽게 학습할 수 있게 하는 높은 수준의 추상화인 Estimator(추정기) 클래스를 제공합니다. Estimator 개체를 만들고 사용하여 GPO 클러스터 전체에서 단일 노드 실행이든 분산 학습이든 관계없이 원격 계산에서 실행하려는 모든 학습 코드를 제출할 수 있습니다. PyTorch 및 TensorFlow 작업의 경우 Azure Machine Learning은 이러한 프레임워크를 쉽게 사용할 수 있도록 각각의 사용자 지정 PyTorch 및 TensorFlow 추정기를 제공합니다.

## <a name="train-with-an-estimator"></a>추정기를 사용하여 학습

[작업 영역](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspace)을 만들고 [개발 환경](how-to-configure-environment.md)을 설정했으면 Azure Machine Learning에서 모델을 학습하려면 다음 단계를 수행해야 합니다.  
1. [원격 계산 대상 만들기](how-to-set-up-training-targets.md)
2. [학습 데이터 업로드](how-to-access-data.md)(선택 사항)
3. 학습 스크립트 만들기
4. Estimator 개체 만들기
5. 학습 작업 제출

이 문서에서는 4-5단계를 중점적으로 다룹니다. 1-3단계는 [이 자습서](tutorial-train-models-with-aml.md)를 참조하세요.

### <a name="single-node-training"></a>단일 노드 학습

다음 코드는 scikit-learn 모델을 위해 Azure의 원격 계산에서 실행되는 단일 노드 학습을 안내합니다. `compute_target` [계산 대상](how-to-set-up-training-targets.md#batch) 개체와 `ds` [데이터 저장소](how-to-access-data.md) 개체는 이미 만들어져 있어야 합니다.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

위의 코드 조각에서 Estimator 생성자에 지정한 매개 변수는 다음과 같습니다.
* `source_directory`: 학습 작업에 필요한 모든 코드가 포함되어 있는 로컬 디렉터리입니다. 이 폴더는 로컬 머신에서 원격 계산으로 복사됩니다. 
* `script_params`: `entry_script` 학습 스크립트에 대한 명령줄 인수를 <명령줄 인수, 값> 쌍 형식으로 지정하는 사전입니다.
* `compute_target`: 학습 스크립트가 실행될 원격 계산(여기서는 [Batch AI](how-to-set-up-training-targets.md#batch) 클러스터)입니다.
* `entry_script`: 원격 계산에서 실행될 학습 스크립트의 파일 경로(`source_directory` 기준)입니다. 이 파일 및 이 파일이 종속되는 모든 추가 파일이 이 폴더에 있어야 합니다.
* `conda_packages`: 학습 스크립트에 필요한 conda를 통해 설치할 Python 패키지의 목록입니다.  
생성자에는 필요한 모든 pip 패키지에 사용할 수 있는 `pip_packages`라는 다른 매개 변수도 있습니다.

Estimator 개체를 만들었으므로 `experiment` [실험](concept-azure-machine-learning-architecture.md#experiment) 개체의 `submit` 함수 호출을 통해 원격 계산에서 실행할 학습 작업을 제출할 수 있습니다. 

```Python
run = experiment.submit(sk_est)
print(run.get_details().status)
```

> [!IMPORTANT]
> 두 개의 **특수 폴더**인 *outputs* 및 *logs*는 Azure Machine Learning에서 별도로 처리합니다. 학습하는 동안 루트 디렉터리(각각 `./outputs` 및 `./logs`) 기준의 *outputs* 및 *logs*라는 폴더에 파일을 쓰면, 이러한 파일이 실행 기록에 자동으로 업로드되므로 실행이 완료되면 해당 파일에 액세스할 수 있습니다. 
>
> 모델 파일, 검사점, 데이터 파일 또는 그려진 이미지와 같이 학습 중에 만들어진 아티팩트에 액세스하려면 이러한 파일을 `./outputs` 폴더에 씁니다.
>
> 마찬가지로, 학습 실행의 로그는 `./logs` 폴더에 쓸 수 있습니다. Azure Machine Learning의 [TensorBoard 통합](https://aka.ms/aml-notebook-tb)을 활용하려면 TensorBoard 로그를 이 폴더에 기록해야 합니다. 실행이 진행되는 동안 TensorBoard를 시작하고 이러한 로그를 스트림할 수 있습니다.  나중에 이전 실행의 로그를 복원할 수도 있습니다.
>
> 예를 들어 원격 학습을 실행한 후 *outputs* 폴더에 기록된 파일을 로컬 머신으로 다운로드하는 코드는 `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`입니다.

### <a name="distributed-training-and-custom-docker-images"></a>분산 학습 및 사용자 지정 Docker 이미지

Estimator를 사용하여 수행할 수 있는 두 가지 추가 학습 시나리오는 다음과 같습니다.
1. 사용자 지정 Docker 이미지 사용
2. 다중 노드 클러스터에서 분산 학습

다음 코드에서는 CNTK 모델에 대한 분산 학습을 수행하는 방법을 보여 줍니다. 또한 기본 Azure Machine Learning 이미지를 사용하는 대신, 학습에 사용할 사용자 고유의 사용자 지정 Docker 이미지가 있다고 가정합니다.

[계산 대상](how-to-set-up-training-targets.md#batch) 개체 `compute_target`는 이미 만들어져 있어야 합니다. 추정기는 다음과 같이 만들 수 있습니다.

```Python
from azureml.train.estimator import Estimator

estimator = Estimator(source_directory='./my-cntk-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',     
                      pip_packages=['cntk==2.5.1'],
                      custom_docker_base_image='microsoft/mmlspark:0.12')
```

위의 코드에서 노출하는 Estimator 생성자에 대한 새 매개 변수는 다음과 같습니다.
* `custom_docker_base_image`: 사용하려는 이미지의 이름입니다. 공용 Docker 리포지토리(여기서는 Docker 허브)에서 사용할 수 있는 이미지만 제공할 수 있습니다. 개인 Docker 리포지토리의 이미지를 사용하려면 생성자의 `environment_definition` 매개 변수를 대신 사용합니다.
* `node_count`: 학습 작업에 사용할 노드의 수입니다. 이 인수의 기본값은 `1`입니다.
* `process_count_per_node`: 각 노드에서 실행할 프로세스("작업자")의 수입니다. 여기서는 각 노드에서 사용할 수 있는 `2` GPU를 사용합니다. 이 인수의 기본값은 `1`입니다.
* `distributed_backend`: 분산 학습을 시작하기 위한 백 엔드이며, Estimator에서 MPI를 통해 제공합니다. 이 인수의 기본값은 `None`입니다. 병렬 또는 분산 학습을 수행하려면(예: `node_count`>1, `process_count_per_node`>1 또는 둘 다) `distributed_backend='mpi'`를 설정합니다. AML에서 사용되는 MPI 구현은 [Open MPI](https://www.open-mpi.org/)입니다.

마지막으로, 학습 작업을 제출합니다.
```Python
run = experiment.submit(cntk_est)
```

## <a name="examples"></a>예
sklearn 모델 학습에 대한 자습서는 다음을 참조하세요.
* `tutorials/01.train-models.ipynb`

사용자 지정 Docker를 사용하는 분산 CNTK에 대한 자습서는 다음을 참조하세요.
* `training/06.distributed-cntk-with-custom-docker/06.distributed-cntk-with-custom-docker.ipynb`

이러한 노트북을 다운로드하려면 다음을 수행합니다.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

* [학습 중에 실행 메트릭 추적](how-to-track-experiments.md)
* [PyTorch 모델 학습](how-to-train-pytorch.md)
* [TensorFlow 모델 학습](how-to-train-tensorflow.md)
* [하이퍼 매개 변수 튜닝](how-to-tune-hyperparameters.md)
* [학습된 모델 배포](how-to-deploy-and-where.md)
