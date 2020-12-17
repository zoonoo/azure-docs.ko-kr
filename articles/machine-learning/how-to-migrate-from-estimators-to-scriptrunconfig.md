---
title: 추정에서 ScriptRunConfig로 마이그레이션
titleSuffix: Azure Machine Learning
description: 학습 작업을 구성 하기 위해 추정에서 ScriptRunConfig로 마이그레이션하기 위한 마이그레이션 가이드입니다.
services: machine-learning
author: mx-iao
ms.author: minxia
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 12/14/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: 64c03b1c9fc18a4e78af9914b893599683069ced
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97633015"
---
# <a name="migrating-from-estimators-to-scriptrunconfig"></a>추정에서 ScriptRunConfig로 마이그레이션

지금까지 추정, ScriptRunConfig 및 하위 수준 RunConfiguration을 포함 하 여 SDK를 통해 Azure Machine Learning에서 학습 작업을 구성 하는 여러 메서드가 있습니다.   이러한 모호성 및 불일치를 해결 하기 위해 Azure ML에서 작업 구성 프로세스를 간소화 합니다.  이제 학습 작업을 구성 하는 데 권장 되는 옵션으로 ScriptRunConfig를 사용 해야 합니다. 

추정는 Python SDK의 1.19.0 릴리스와 함께 사용 되지 않습니다. 또한 일반적으로 RunConfiguration 개체를 직접 인스턴스화하지 말고 ScriptRunConfig 클래스를 사용 하 여 작업을 구성 해야 합니다.

이 문서에서는 추정에서 ScriptRunConfig로 마이그레이션할 때 일반적인 고려 사항을 설명 합니다.

> [!IMPORTANT]
> 추정에서 ScriptRunConfig로 마이그레이션하려면 Python SDK의 >= 1.15.0를 사용 하 고 있는지 확인 합니다.

## <a name="scriptrunconfig-documentation-and-samples"></a>ScriptRunConfig 설명서 및 샘플
Azure Machine Learning 설명서 및 샘플은 작업 구성 및 제출에 [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py&preserve-view=true) 를 사용 하도록 업데이트 되었습니다.

ScriptRunConfig 사용에 대 한 자세한 내용은 다음 설명서를 참조 하세요.
* [학습 실행 구성 및 제출](how-to-set-up-training-targets.md)
* [PyTorch 학습 실행 구성](how-to-train-pytorch.md)
* [TensorFlow 학습 실행 구성](how-to-train-tensorflow.md)
* [Scikit 구성-학습 실행 학습](how-to-train-scikit-learn.md)

또한 다음 샘플 & 자습서를 참조 하세요.
* [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)
* [Azure/azureml-examples](https://github.com/Azure/azureml-examples)

## <a name="defining-the-training-environment"></a>학습 환경 정의
다양 한 프레임 워크 추정는 Docker 이미지에 의해 지원 되는 환경을 미리 구성 하는 반면 이러한 이미지에 대 한 Dockerfiles는 전용입니다.  따라서 이러한 환경에는 많은 투명도가 포함 되어 있지 않습니다. 또한 추정는 환경 관련 구성을 `pip_packages` 해당 생성자의 개별 매개 변수 (예:)로 사용 합니다 `custom_docker_image` .

ScriptRunConfig를 사용 하는 경우 모든 환경 관련 구성은 `Environment` `environment` ScriptRunConfig 생성자의 매개 변수로 전달 되는 개체에 캡슐화 됩니다. 학습 작업을 구성 하려면 학습 스크립트에 필요한 모든 종속성을 포함 하는 환경을 제공 합니다. 환경을 제공 하지 않는 경우 Azure ML은 Azure ML 기본 이미지 중 하나 (특히에 의해 정의 된 기본 환경)를 사용 합니다 `azureml.core.environment.DEFAULT_CPU_IMAGE` . 환경을 제공 하는 방법에는 두 가지가 있습니다.

* [큐 레이트 환경 사용](how-to-use-environments.md#use-a-curated-environment) -큐 레이트 환경은 기본적으로 작업 영역에서 사용할 수 있는 미리 정의 된 환경입니다. 각 프레임 워크 평가기을 지 원하는 미리 구성 된 각 프레임 워크/버전 Docker 이미지에 해당 하는 큐 레이트 환경이 있습니다.
* [사용자 고유의 사용자 지정 환경 정의](how-to-use-environments.md)

큐 레이트 PyTorch 1.6 환경을 학습에 사용 하는 예제는 다음과 같습니다.

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

학습 스크립트가 실행 되는 프로세스에 설정 되는 **환경 변수** 를 지정 하려면 환경 개체를 사용 합니다.
```
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

Azure ML 환경을 구성 하 고 관리 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.
* [환경 사용 방식](how-to-use-environments.md)
* [큐레이팅 환경](resource-curated-environments.md)
* [사용자 지정 Docker 이미지를 사용 하 여 학습](how-to-train-with-custom-image.md)

## <a name="using-data-for-training"></a>학습에 데이터 사용
### <a name="datasets"></a>데이터 세트
학습에 Azure ML 데이터 집합을 사용 하는 경우 매개 변수를 사용 하 여 데이터 집합을 스크립트에 인수로 전달 합니다 `arguments` . 이렇게 하면 인수를 통해 학습 스크립트에서 데이터 경로 (탑재 지점 또는 다운로드 경로)를 얻게 됩니다.

다음 예제에서는 FileDataset가 원격 계산에 탑재 되는 학습 작업을 구성 합니다 `mnist_ds` .
```python
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', mnist_ds.as_mount()], # or mnist_ds.as_download() to download
                      compute_target=compute_target,
                      environment=pytorch_env)
```

### <a name="datareference-old"></a>DataReference (이전)
이전 DataReference 방식에 대해 Azure ML 데이터 집합을 사용 하는 것이 좋지만 어떤 이유로 든 여전히 Datareference를 사용 하는 경우 다음과 같이 작업을 구성 해야 합니다.
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

학습에 데이터를 사용 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.
* [Azure ML의 데이터 집합으로 학습](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)

## <a name="distributed-training"></a>분산 학습
학습을 위해 분산 작업을 구성 해야 하는 경우 `distributed_job_config` ScriptRunConfig 생성자에 매개 변수를 지정 하 여이 작업을 수행 합니다. 각 유형의 분산 된 작업에 대해 [MpiConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py&preserve-view=true), [PyTorchConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?view=azure-ml-py&preserve-view=true)또는 [TensorflowConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py&preserve-view=true) 를 전달 합니다.

다음 예에서는 MPI/Horovod로 분산 된 학습을 사용 하도록 PyTorch 교육 작업을 구성 합니다.
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
* [TensorFlow를 사용 하 여 분산 교육](how-to-train-tensorflow.md#distributed-training)

## <a name="miscellaneous"></a>기타
어떤 이유로 든 ScriptRunConfig의 기본 RunConfiguration 개체에 액세스 해야 하는 경우 다음과 같이 할 수 있습니다.
```
src.run_config
```

## <a name="next-steps"></a>다음 단계

* [학습 실행 구성 및 제출](how-to-set-up-training-targets.md)
