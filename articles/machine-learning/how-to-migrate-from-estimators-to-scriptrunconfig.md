---
title: 예측 도구에서 ScriptRunConfig로 마이그레이션
titleSuffix: Azure Machine Learning
description: 학습 작업을 구성하기 위해 Estimators에서 ScriptRunConfig로 마이그레이션하기 위한 마이그레이션 가이드입니다.
services: machine-learning
author: mx-iao
ms.author: minxia
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 12/14/2020
ms.topic: how-to
ms.custom: devx-track-python, contperf-fy21q1
ms.openlocfilehash: daf142f6e49a09556d05faf4eea23b31a2cab995
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107888839"
---
# <a name="migrating-from-estimators-to-scriptrunconfig"></a>Estimators에서 ScriptRunConfig로 마이그레이션예측 도구

지금까지 SDK를 통해 Azure Machine Learning에서 학습 작업을 구성하는 메서드는 Estimators, ScriptRunConfig 및 하위 수준 RunConfiguration을 포함하여 여러가지가 있었습니다.   이러한 모호성 및 불일치를 해결하기 위해 Azure ML에서 작업 구성 프로세스를 간소화하고 있습니다.  이제 학습 작업을 구성하는 데 권장되는 옵션으로 ScriptRunConfig를 사용해야 합니다. 

Estimators는 Python SDK 1.19.0 릴리스와 사용하지 않습니다. 또한 일반적으로 RunConfiguration 개체를 직접 인스턴스화하지 말고 ScriptRunConfig 클래스를 사용하여 작업을 구성해야 합니다.

이 문서에서는 Estimators에서 ScriptRunConfig로 마이그레이션할 때 일반적으로 고려하는 사항을 설명합니다.

> [!IMPORTANT]
> Estimators에서 ScriptRunConfig로 마이그레이션하기 위해 Python SDK의 >= 1.15.0를 사용하고 있는지 확인합니다.

## <a name="scriptrunconfig-documentation-and-samples"></a>ScriptRunConfig 설명서 및 샘플
Azure Machine Learning 설명서 및 샘플은 작업 구성 및 제출 시 [ScriptRunConfig](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig)를 사용하도록 업데이트되었습니다.

ScriptRunConfig 사용에 대한 자세한 정보는 설명서를 참조하세요.
* [학습 실행 구성 및 제출](how-to-set-up-training-targets.md)
* [PyTorch 학습 실행 구성](how-to-train-pytorch.md)
* [TensorFlow 학습 실행 구성](how-to-train-tensorflow.md)
* [Scikit-learn 학습 실행 구성](how-to-train-scikit-learn.md)

또한, 다음 샘플 및 자습서를 참조하세요.
* [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)
* [Azure/azureml-examples](https://github.com/Azure/azureml-examples)

## <a name="defining-the-training-environment"></a>학습 환경 정의
다양한 프레임워크 예측 도구는 Docker 이미지가 지원하는 환경을 미리 구성 한 반면 이러한 이미지에 대한 Dockerfiles는 비공개입니다.  따라서 이러한 환경에 포함된 것에 대해 투명성이 높지 않습니다. 또한 예측 도구는 환경 관련 구성을 각 생성자의 개별 매개 변수(예: `pip_packages`, `custom_docker_image`)로 사용합니다.

ScriptRunConfig를 사용하는 경우 모든 환경 관련 구성은 ScriptRunConfig 생성자의 `environment` 매개 변수로 전달되는 `Environment` 개체에 캡슐화됩니다. 학습 작업을 구성하려면 학습 스크립트에 필요한 모든 종속성을 포함하는 환경을 제공합니다. 환경을 제공하지 않는 경우 Azure ML은 Azure ML 베이스 이미지 중 하나(특히 `azureml.core.environment.DEFAULT_CPU_IMAGE`에 의해 정의된 이미지)를 기본 환경으로 사용합니다. 환경을 만드는 몇 가지 방법이 있습니다.

* [큐레이팅된 환경 사용](how-to-use-environments.md#use-a-curated-environment) - 큐레이팅된 환경은 기본값으로 작업 영역에서 사용할 수 있는 미리 정의된 환경입니다. 각 프레임워크 예측 도구를 지원하고 미리 구성된 각 프레임워크/버전 Docker 이미지에 해당하는 큐레이팅된 환경이 있습니다.
* [사용자 고유의 환경 정의](how-to-use-environments.md)

큐레이팅된 PyTorch 1.6 환경을 학습에 사용하는 예제는 다음과 같습니다.

```python
from azureml.core import Workspace, ScriptRunConfig, Environment

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)

compute_target = ws.compute_targets['my-cluster']
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env)
```

학습 스크립트가 실행되는 프로세스에 설정될 **환경 변수** 를 지정하려면 환경 개체를 사용합니다.
```
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

Azure ML 환경을 구성하고 관리하는 방법에 대한 자세한 정보는 다음을 참조하세요.
* [환경 사용 방식](how-to-use-environments.md)
* [큐레이팅 환경](resource-curated-environments.md)
* [사용자 지정 Docker 이미지를 사용하여 학습](how-to-train-with-custom-image.md)

## <a name="using-data-for-training"></a>학습에 데이터를 사용하기
### <a name="datasets"></a>데이터 세트
학습에 Azure ML 데이터 세트를 사용하는 경우 `arguments` 매개 변수를 사용하여 데이터 세트를 스크립트에 인수로 전달합니다. 이렇게 하면 인수를 통해 학습 스크립트에서 데이터 경로(탑재 지점 또는 다운로드 경로)를 얻게 됩니다.

다음 예제에서는 FileDataset, `mnist_ds`가 원격 컴퓨팅에 탑재되는 학습 작업을 구성합니다.
```python
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', mnist_ds.as_mount()], # or mnist_ds.as_download() to download
                      compute_target=compute_target,
                      environment=pytorch_env)
```

### <a name="datareference-old"></a>DataReference(이전)
이전 DataReference 방식에서는 Azure ML 데이터 세트를 사용하는 것이 좋지만 어떤 이유로든 아직 DataReferences를 사용하는 경우 다음과 같이 작업을 구성해야 합니다.
```python
# if you want to pass a DataReference object, such as the below:
datastore = ws.get_default_datastore()
data_ref = datastore.path('./foo').as_mount()

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', str(data_ref)], # cast the DataReference object to str
                      compute_target=compute_target,
                      environment=pytorch_env)
src.run_config.data_references = {data_ref.data_reference_name: data_ref.to_config()} # set a dict of the DataReference(s) you want to the `data_references` attribute of the ScriptRunConfig's underlying RunConfiguration object.
```

학습에 데이터를 사용하는 방법에 대한 자세한 정보는 다음을 참조하세요.
* [Azure ML에서 데이터 세트로 학습](./how-to-train-with-datasets.md)

## <a name="distributed-training"></a>분산 학습
학습을 위해 분산 작업을 구성해야 하는 경우 ScriptRunConfig 생성자에서 `distributed_job_config` 매개 변수를 지정하여 이 작업을 수행합니다. 각 유형의 분산 작업에 대해 [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration), [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) 또는 [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration)를 전달합니다.

다음 예에서는 MPI/Horovod로 분산 학습을 사용하도록 PyTorch 학습 작업을 구성합니다.
```python
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2, process_count_per_node=2))
```

자세한 내용은 다음을 참조하세요.
* [PyTorch를 사용하여 분산 학습](how-to-train-pytorch.md#distributed-training)
* [TensorFlow를 사용한 분산 학습](how-to-train-tensorflow.md#distributed-training)

## <a name="miscellaneous"></a>기타
어떤 이유로든 ScriptRunConfig의 기본 RunConfiguration 개체에 액세스해야 하는 경우 다음과 같이 할 수 있습니다.
```
src.run_config
```

## <a name="next-steps"></a>다음 단계

* [학습 실행 구성 및 제출](how-to-set-up-training-targets.md)