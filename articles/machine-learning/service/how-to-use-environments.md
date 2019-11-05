---
title: 재사용 가능한 ML 환경 만들기
titleSuffix: Azure Machine Learning
description: 모델 학습 및 배포를 위한 환경을 만들고 관리 합니다. 환경에 대 한 Python 패키지 및 기타 설정을 관리 합니다.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: f733e29fc5fbce764fef9a713747d6793d2ebd43
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489331"
---
# <a name="create-and-manage-reusable-environments-for-training-and-deployment-with-azure-machine-learning"></a>Azure Machine Learning를 사용 하 여 학습 및 배포를 위한 재사용 가능한 환경을 만들고 관리 합니다.
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 프로젝트의 소프트웨어 종속성이 진화 함에 따라 추적 하 고 재현할 수 있도록 Azure Machine Learning [환경을](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) 만들고 관리 하는 방법을 알아봅니다.

소프트웨어 종속성 관리는 개발자를 위한 일반적인 작업입니다. 많은 수동 소프트웨어 구성 없이 빌드를 재현할 수 있도록 하려고 합니다. Pip 및 Conda와 같은 로컬 개발을 위한 솔루션을 사용 하 여 Azure Machine Learning 환경 클래스는 로컬 및 분산 클라우드 개발을 위한 솔루션을 제공 합니다.

이 문서의 예제에서는 다음 작업을 수행 하는 방법을 보여 줍니다.

* 환경 만들기 및 패키지 종속성 지정
* 환경 검색 및 업데이트
* 교육용 환경 사용
* 웹 서비스 배포를 위한 환경 사용

## <a name="what-are-environments"></a>환경 이란?

환경에서는 학습 및 점수 매기기 스크립트와 실행 시간 (Python, Spark 또는 Docker)에 대 한 Python 패키지, 환경 변수 및 소프트웨어 설정을 지정 합니다. 이러한 항목은 다양 한 계산 대상에서 재현 가능 하 고 감사 가능 하며 휴대용 기계 학습 워크플로를 가능 하 게 하는 Azure Machine Learning 작업 영역 내에서 관리 되 고 버전이 지정 된 엔터티입니다

로컬 계산의 환경 개체를 사용 하 여 학습 스크립트를 개발 하 고, 대규모로 모델 학습을 위해 동일한 환경을 Azure Machine Learning 계산에서 재사용 하 고, 동일한 환경에서 모델을 배포할 수 있습니다.

다음은 동일한 환경 개체를 학습을 위한 실행 구성과 웹 서비스 배포에 대 한 유추 및 배포 구성에서 모두 사용할 수 있음을 보여 줍니다.

![Machine learning 워크플로의 환경 다이어그램](./media/how-to-use-environments/ml-environment.png)

### <a name="types-of-environments"></a>환경 유형

환경은 **큐 레이트**, **사용자 관리** 및 **시스템 관리**등의 세 가지 범주로 나눌 수 있습니다.

큐 레이트 환경은 Azure Machine Learning에서 제공 하며 기본적으로 작업 영역에서 사용할 수 있습니다. 여기에는 다양 한 기계 학습 프레임 워크를 시작 하는 데 도움이 되는 Python 패키지 및 설정 컬렉션이 포함 되어 있습니다. 

사용자 관리 환경에서는 사용자가 사용자 환경을 설정하고 컴퓨팅 대상에 학습 스크립트에 필요한 모든 패키지를 설치해야 합니다. Conda에서 자동으로 환경을 확인하지도 않고 아무 것도 설치하지 않습니다. 

[Conda](https://conda.io/docs/) 에서 Python 환경 및 스크립트 종속성을 관리 하려는 경우 시스템 관리 환경이 사용 됩니다. 서비스는 수동으로 구성할 수 없는 원격 계산 대상의 유용성으로 인해 기본적으로 이러한 유형의 환경을 가정 합니다.

## <a name="prerequisites"></a>필수 조건

* Python 용 Azure Machine Learning SDK가 [설치 되어](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)있습니다.
* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)입니다.


## <a name="create-an-environment"></a>환경 만들기

실험에 대 한 환경을 만드는 방법에는 여러 가지가 있습니다.

### <a name="use-curated-environment"></a>큐 레이트 환경 사용

큐 레이트 환경 중 하나를 선택 하 여 시작할 수 있습니다. 

* __AzureML 최소__ 환경에는 실행 추적 및 자산 업로드를 사용 하도록 설정 하는 최소 패키지 집합이 포함 되어 있습니다. 사용자 환경에 대 한 시작 지점으로 사용할 수 있습니다.

* __Azureml-자습서__ 환경에는 Scikit, Pandas 및 Matplotlib와 같은 일반적인 데이터 과학 패키지와 광범위 한 AzureML sdk 패키지 집합이 포함 되어 있습니다.

큐 레이트 환경은 캐시 된 Docker 이미지에 의해 지원 되므로 실행 준비 비용이 줄어듭니다.

큐 레이트 __메서드를__ 사용 하 여 다음 환경 중 하나를 선택 합니다.

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

다음 코드를 사용 하 여 큐 레이트 환경과 해당 패키지를 나열할 수 있습니다.
```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  _AzureML_ 접두사를 사용 하 여 고유한 환경 이름을 시작 하지 마세요. 큐 레이트 환경용으로 예약 되어 있습니다.

### <a name="instantiate-an-environment-object"></a>환경 개체 인스턴스화

환경을 수동으로 만들려면 SDK에서 Environment 클래스를 가져오고 다음 코드를 사용 하 여 환경 개체를 인스턴스화합니다.

```python
from azureml.core import Environment
Environment(name="myenv")
```

### <a name="conda-and-pip-specification-files"></a>Conda 및 pip 사양 파일

Conda 사양 또는 pip 요구 사항 파일에서 환경을 만들 수도 있습니다.
[From_conda_specification ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) 또는 [from_pip_requirements ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) 메서드를 사용 하 고 메서드 인수에 원하는 파일의 사용자 환경 이름 및 파일 경로를 포함 합니다.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

#From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="existing-conda-environment"></a>기존 Conda 환경

로컬 컴퓨터에 기존 Conda 환경이 있는 경우 서비스는 환경 개체를 만들기 위한 솔루션을 제공 합니다. 이러한 방식으로 원격 실행에서 로컬 대화형 환경을 재사용할 수 있습니다.

다음 코드는 [from_existing_conda_environment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) 메서드를 사용 하 여 `mycondaenv` 기존 Conda 환경에서 환경 개체를 만듭니다.

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="automatically-create-environments"></a>자동으로 환경 만들기

Submit () 메서드를 사용 하 여 학습 실행을 제출 하 여 환경을 자동으로 만듭니다. 학습 실행을 제출할 때 새 환경을 빌드하는 데 필요한 종속성의 크기에 따라 몇 분 정도 걸릴 수 있습니다. 

실행을 제출 하기 전에 실행 구성에서 환경을 지정 하지 않으면 기본 환경이 만들어집니다.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attaches training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Shows each step of run 
run.wait_for_completion(show_output=True)
```

마찬가지로 [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 개체를 학습에 사용 하는 경우 환경을 지정 하지 않고도 평가기 인스턴스를 실행으로 직접 제출할 수 있습니다. `Estimator` 개체는 이미 환경 및 계산 대상을 캡슐화 합니다.


## <a name="add-packages-to-an-environment"></a>환경에 패키지 추가

Conda, pip 또는 private wheel 파일을 사용 하 여 환경에 패키지를 추가 합니다. [CondaDependency 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)를 사용 하 여 각 패키지 종속성을 지정 하 고 환경 PythonSection에 추가 합니다.

### <a name="conda-and-pip-packages"></a>Conda 및 pip 패키지

Conda 패키지 리포지토리에서 패키지를 사용할 수 있는 경우 pip 설치에 Conda를 사용 하는 것이 좋습니다. 그 이유는 Conda 패키지에는 일반적으로 더 안정적인 설치를 수행 하는 미리 빌드된 이진 파일이 포함 되어 있기 때문입니다.

다음 예에서는 `scikit-learn`, 특히 버전 0.21.3 및 `pillow` 패키지를 환경에 추가 하 고 `myenv` [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) 및 [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) 메서드를 각각 추가 합니다.

```python
from azureml.core import Environment
from azureml.core.environment import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs scikit-learn version 0.21.3 conda package
conda_dep.add_conda_package("scikit-learn==0.21.3")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

### <a name="private-wheel-files"></a>개인 휠 파일

정적 [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) 메서드를 사용 하 여 먼저 전용 pip 휠 파일을 작업 영역 저장소에 업로드 한 다음 저장소 url을 캡처하고 URL을 `add_pip_package()` 메서드에 전달 하 여 사용할 수 있습니다.

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>환경 관리

환경을 관리 하 여 계산 대상과 작업 영역의 다른 사용자 간에 업데이트, 추적 및 다시 사용할 수 있습니다.

### <a name="register-environments"></a>환경 등록

실행을 제출 하거나 웹 서비스를 배포할 때 환경이 작업 영역에 자동으로 등록 됩니다. [Register ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) 메서드를 사용 하 여 환경을 수동으로 등록할 수도 있습니다. 이 작업을 수행 하면 환경이 클라우드에서 추적 되 고 버전이 지정 되 고 작업 영역 사용자 간에 공유 될 수 있는 엔터티가 됩니다.

다음 코드는 환경 `myenv``ws`작업 영역에 등록 합니다.

```python
myenv.register(workspace=ws)
```

처음으로 사용 하는 경우 학습 또는 배포에서 환경을 작업 영역에 등록 하 고 작성 한 다음 계산 대상에 배포 합니다. 환경은 서비스에 의해 캐시 됩니다. 캐시 된 환경을 다시 사용 하면 새 서비스 또는 업데이트 된 서비스를 사용 하는 것 보다 훨씬 더 많은 시간이 소요 됩니다.

### <a name="get-existing-environments"></a>기존 환경 가져오기

환경 클래스는 작업 영역에서 이름, 목록 또는 문제 해결 또는 감사를 위한 특정 학습 실행, 재현 가능성 등의 기존 환경을 검색 하는 데 사용할 수 있는 메서드를 제공 합니다.

#### <a name="view-list-of-environments"></a>환경 목록 보기

[`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-)를 사용 하 여 작업 영역에서 환경을 확인 한 다음 다시 사용할 하나를 선택 합니다.

#### <a name="get-environment-by-name"></a>이름을 기준으로 환경 가져오기

이름 및 버전으로 특정 환경을 가져올 수도 있습니다.
다음 코드는 [get ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) 메서드를 사용 하 여 `ws` 작업 영역에서 `myenv` 환경의 버전 `1`를 검색 합니다.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="training-run-specific-environment"></a>학습 실행 특정 환경

학습을 완료 한 후 특정 실행에 사용 되는 환경을 가져오려면 Run 클래스에서 [get_environment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) 메서드를 사용 합니다.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>기존 환경 업데이트

기존 환경 (예: Python 패키지 추가)을 변경 하는 경우 실행을 제출 하거나 모델을 배포 하거나 환경을 수동으로 등록할 때 환경의 새 버전이 만들어집니다. 버전 관리를 사용 하면 시간에 따라 환경에 대 한 변경 내용을 볼 수 있습니다.

기존 환경의 Python 패키지 버전을 업데이트 하려면 해당 패키지에 대 한 정확한 버전 번호를 지정 합니다. 그렇지 않으면 Azure Machine Learning는 환경을 만들었을 때의 패키지 버전과 기존 환경을 다시 사용 합니다.

### <a name="debug-the-image-build"></a>이미지 빌드 디버그

이 예제에서는 [build ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace-) 메서드를 사용 하 여 수동으로 Docker 이미지로 환경을 만들고 [wait_for_completion ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)를 사용 하 여 이미지 빌드에서 출력 로그를 모니터링 합니다. 그러면 빌드된 이미지가 디버깅에 도움이 되는 Azure Container Registry 작업 영역 아래에 나타납니다.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="docker-and-environments"></a>Docker 및 환경

 Azure Machine Learning `Environments` 클래스의 [Dockersection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) 을 사용 하면 학습 실행이 실행 되는 게스트 운영 체제에 대 한 세부 정보를 사용자 지정 하 고 제어할 수 있습니다.

Docker를 `enable` 하는 경우 서비스는 Docker 이미지를 빌드하고 해당 Docker 컨테이너 내에서 사양을 사용 하 여 Python 환경을 만듭니다. 이렇게 하면 학습 실행에 대 한 추가 격리 및 재현 가능성 제공 됩니다.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

빌드된 후에는 기본적으로 작업 영역과 연결 된 Azure Container Registry에 Docker 이미지가 표시 됩니다.  리포지토리 이름의 형식은 *azureml/azureml_\<uuid\>* 입니다. 고유 식별자 (*uuid*) 부분은 환경 구성에서 계산 된 해시에 해당 합니다. 이렇게 하면 서비스에서 지정 된 환경에 해당 하는 이미지가 다시 사용 하기 위해 이미 있는지 여부를 확인할 수 있습니다.

또한 서비스는 Ubuntu Linux 기반 [기본 이미지](https://github.com/Azure/AzureML-Containers)중 하나를 자동으로 사용 하 고 지정 된 Python 패키지를 설치 합니다. 기본 이미지에는 CPU 및 GPU 버전이 있습니다. Azure Machine Learning는 사용할 버전을 자동으로 검색 합니다.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

> [!NOTE]
> 사용자 지정 Docker 이미지를 사용 하는 동안 `environment.python.user_managed_dependencies=False`을 지정 하는 경우 서비스는 이미지 내에서 Conda 환경을 빌드하고 기본 이미지에 설치 했을 수 있는 Python 라이브러리를 사용 하는 대신 해당 환경에서 실행을 실행 합니다. 매개 변수를 `True`으로 설정 하 여 설치 된 패키지를 사용 합니다.

## <a name="using-environments-for-training"></a>교육 환경 사용

학습 실행을 제출 하려면 환경, [계산 대상](concept-compute-target.md) 및 학습 Python 스크립트를 실행 구성으로 결합 해야 합니다. 실행을 전송 하는 데 사용 되는 래퍼 개체입니다.

학습 실행을 제출할 때 새 환경을 빌드하는 데 필요한 종속성의 크기에 따라 몇 분 정도 걸릴 수 있습니다. 환경 정의가 변경 되지 않은 상태로 유지 되는 한, 환경 정의가 서비스에 의해 캐시 되므로 전체 설치 시간은 한 번만 발생 합니다.

다음 로컬 스크립트 실행 예제에서는 [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) 를 래퍼 개체로 사용 하는 경우를 보여 줍니다.

```python
from azureml.core import Environment, ScriptRunConfig, Experiment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Add training script to run config
runconfig = ScriptRunConfig(source_directory=".", script="train.py")

# Attach compute target to run config
runconfig.run_config.target = "local"

# Attach environment to run config
runconfig.run_config.environment = myenv

# Submit run 
run = exp.submit(runconfig)
```

> [!NOTE]
> 실행 기록을 사용 하지 않도록 설정 하거나 스냅숏을 실행 하려면 `ScriptRunConfig.run_config.history`에서 설정을 사용 합니다.

실행 구성에서 환경을 지정 하지 않으면 실행을 제출할 때 서비스에서 기본 환경을 만듭니다.

### <a name="train-with-an-estimator"></a>추정기를 사용하여 학습

학습에 [평가기](how-to-train-ml-models.md) 를 사용 하는 경우 이미 환경 및 계산 대상을 캡슐화 하므로 평가기 인스턴스를 직접 제출할 수 있습니다.

다음 코드는 scikit 학습 모델에 대 한 원격 계산에서 단일 노드 학습 실행에 대해 평가기를 사용 하 고 이전에 생성 된 계산 대상 개체인 `compute_target` 및 데이터 저장소 개체 `ds`로 가정 합니다.

```python
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

# Submit the run 
run = experiment.submit(sk_est)
```

## <a name="using-environments-for-web-service-deployment"></a>웹 서비스 배포를 위한 환경 사용

모델을 웹 서비스로 배포할 때 환경을 사용할 수 있습니다. 이렇게 하면 학습 및 유추 계산 모두에서 정확히 동일한 라이브러리를 사용 하 여 모델을 학습, 테스트 및 배포할 수 있는 재현 가능한 연결 워크플로를 사용할 수 있습니다.

웹 서비스를 배포 하려면 배포 개체, [배포 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)에 환경, 유추 계산, 점수 매기기 스크립트 및 등록 된 모델을 결합 합니다. [웹 서비스 배포](how-to-deploy-and-where.md)에 대해 자세히 알아보세요.

이 예제에서는 학습 실행을 완료 하 고 ACI (Azure Container Instance)에 해당 모델을 배포 하려는 경우를 가정 합니다. 웹 서비스를 빌드할 때 모델 및 점수 매기기 파일이 이미지에 탑재 되 고 Azure Machine Learning 유추 스택이 이미지에 추가 됩니다.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>노트북 예제

이 [예제 전자 필기장은](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) 이 문서에 설명 되어 있는 개념과 메서드를 확장 합니다.

## <a name="next-steps"></a>다음 단계

* [자습서: 모델 학습](tutorial-train-models-with-aml.md) 은 관리 되는 계산 대상을 사용 하 여 모델을 학습 합니다.
* 모델을 학습했으면 [모델을 배포하는 방법 및 위치](how-to-deploy-and-where.md)를 알아봅니다.
* [환경 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) SDK 참조를 봅니다.
