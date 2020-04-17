---
title: 재사용 가능한 ML 환경 만들기
titleSuffix: Azure Machine Learning
description: 모델 교육 및 배포를 위한 환경을 만들고 관리합니다. 환경에 대한 파이썬 패키지 및 기타 설정을 관리합니다.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: dc30318027962247f7504b734385d7642550ec87
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536355"
---
# <a name="reuse-environments-for-training-and-deployment-by-using-azure-machine-learning"></a>Azure 기계 학습을 사용하여 교육 및 배포를 위한 환경 재사용
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure 기계 학습 [환경을](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)만들고 관리하는 방법을 알아봅니다. 환경을 사용하여 프로젝트의 소프트웨어 종속성이 발전함에 따라 추적하고 재현할 수 있습니다.

소프트웨어 종속성 관리는 개발자에게 일반적인 작업입니다. 광범위한 수동 소프트웨어 구성 없이 빌드를 재현할 수 있도록 해야 합니다. Azure Machine `Environment` Learning 클래스는 pip 및 Conda와 같은 로컬 개발 솔루션을 담당하며 로컬 및 분산 클라우드 개발을 위한 솔루션을 제공합니다.

이 문서의 예제에서는 다음 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주시면 됩니다.

* 환경을 만들고 패키지 종속성을 지정합니다.
* 환경을 검색하고 업데이트합니다.
* 교육을 위해 환경을 사용합니다.
* 웹 서비스 배포에 환경을 사용합니다.

Azure 기계 학습에서 환경이 작동하는 방식에 대한 자세한 내용은 [ML 환경이란 무엇입니까?](concept-environments.md)

## <a name="prerequisites"></a>사전 요구 사항

* [파이썬을위한 Azure 기계 학습 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Azure 기계 학습 작업 영역](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>환경 만들기

다음 섹션에서는 실험에 대한 환경을 만들 수 있는 여러 가지 방법을 살펴봅니다.

### <a name="use-a-curated-environment"></a>선별된 환경 사용

먼저 선별된 환경 중 하나를 선택할 수 있습니다. 

* _AzureML-최소_ 환경에는 실행 추적 및 자산 업로드를 활성화하는 최소 패키지 집합이 포함되어 있습니다. 사용자 고유의 환경에 대한 시작점으로 사용할 수 있습니다.

* _AzureML-자습서_ 환경에는 일반적인 데이터 과학 패키지가 포함되어 있습니다. 이 패키지에는 Scikit-Learn, 팬더, Matplotlib 및 더 큰 azureml-sdk 패키지 세트가 포함됩니다.

선별된 환경은 캐시된 Docker 이미지로 뒷받침됩니다. 이렇게 하면 실행 준비 비용이 줄어듭니다.

이 `Environment.get` 방법을 사용하여 선별된 환경 중 하나를 선택합니다.

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

다음 코드를 사용하여 선별된 환경과 패키지를 나열할 수 있습니다.

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  _AzureML_ 접두사로 사용자 고유의 환경 이름을 시작하지 마십시오. 이 접두사는 선별된 환경에 대해 예약되어 있습니다.

### <a name="instantiate-an-environment-object"></a>환경 개체 인스턴스화

환경을 수동으로 만들려면 SDK에서 클래스를 `Environment` 가져옵니다. 그런 다음 다음 코드를 사용하여 환경 개체를 인스턴스화합니다.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-conda-and-pip-specification-files"></a>콘다 및 핍 사양 파일 사용

Conda 사양 또는 pip 요구 사항 파일에서 환경을 만들 수도 있습니다. [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) 메서드 또는 메서드를 [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) 사용합니다. 메서드 인수에 환경 이름과 원하는 파일의 파일 경로를 포함합니다.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="use-existing-conda-environments"></a>기존 Conda 환경 사용

로컬 컴퓨터에 기존 Conda 환경이 있는 경우 서비스를 사용하여 환경 개체를 만들 수 있습니다. 이 전략을 사용하면 원격 실행시 로컬 대화형 환경을 다시 사용할 수 있습니다.

다음 코드는 기존 Conda 환경에서 `mycondaenv`환경 개체를 만듭니다. 메서드를 [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) 사용합니다.

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="create-environments-automatically"></a>자동으로 환경 만들기

교육 실행을 제출하여 환경을 자동으로 만듭니다. 메서드를 사용하여 실행을 `submit()` 제출합니다. 교육 실행을 제출하면 새 환경 빌드에 몇 분 정도 걸릴 수 있습니다. 빌드 기간은 필요한 종속성의 크기에 따라 다릅니다. 

실행을 제출하기 전에 실행 구성에서 환경을 지정하지 않으면 기본 환경이 만들어집니다.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attach training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Show each step of run 
run.wait_for_completion(show_output=True)
```

마찬가지로 학습에 개체를 [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 사용하는 경우 환경을 지정하지 않고 추정기 인스턴스를 실행으로 직접 제출할 수 있습니다. 개체는 `Estimator` 이미 환경 및 계산 대상을 캡슐화합니다.

## <a name="add-packages-to-an-environment"></a>환경에 패키지 추가

Conda, pip 또는 개인 휠 파일을 사용하여 환경에 패키지를 추가합니다. [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) 클래스를 사용하여 각 패키지 종속성을 지정합니다. 환경의 `PythonSection`에 추가합니다.

### <a name="conda-and-pip-packages"></a>콘다 및 핍 패키지

Conda 패키지 리포지토리에서 패키지를 사용할 수 있는 경우 pip 설치 대신 Conda 설치를 사용하는 것이 좋습니다. Conda 패키지에는 일반적으로 설치의 신뢰성을 높이는 사전 제작된 바이너리가 함께 제공됩니다.

다음 예제는 환경에 추가합니다. 의 버전 1.17.0을 `numpy`추가합니다. 또한 `pillow` 패키지를 `myenv`추가합니다. 이 예제에서는 [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) 메서드와 [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) 메서드를 각각 사용합니다.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs numpy version 1.17.0 conda package
conda_dep.add_conda_package("numpy==1.17.0")

# Installs pillow package
conda_dep.add_pip_package("pillow")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

>[!IMPORTANT]
> 다른 실행에 동일한 환경 정의를 사용하는 경우 Azure Machine Learning 서비스는 사용자의 캐시된 이미지를 다시 사용합니다. 고정되지 않은 패키지 종속성이 있는 환경을 만드는 경우(예: ```numpy```해당 환경은 환경 생성 _시_설치된 패키지 버전을 계속 사용합니다. 또한 일치하는 정의가 있는 이후 환경은 이전 버전을 계속 사용하게 됩니다. 자세한 내용은 [환경 건물, 캐싱 및 재사용을 참조하십시오.](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)

### <a name="private-wheel-files"></a>개인 휠 파일

먼저 작업 영역 저장소에 업로드하여 개인 핍 휠 파일을 사용할 수 있습니다. 정적 [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) 메서드를 사용하여 업로드합니다. 그런 다음 저장소 URL을 캡처하고 `add_pip_package()` URL을 메서드에 전달합니다.

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>환경 관리

환경을 관리하여 계산 대상 및 작업 영역의 다른 사용자와 함께 환경을 업데이트, 추적 및 재사용할 수 있습니다.

### <a name="register-environments"></a>환경 등록

실행을 제출하거나 웹 서비스를 배포할 때 환경이 작업 영역에 자동으로 등록됩니다. [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) 메서드를 사용하여 환경을 수동으로 등록할 수도 있습니다. 이 작업을 수행하면 환경을 클라우드에서 추적하고 버전이 되는 엔터티로 변환됩니다. 엔터티는 작업 영역 사용자 간에 공유할 수 있습니다.

다음 코드는 `myenv` `ws` 환경을 작업 영역에 등록합니다.

```python
myenv.register(workspace=ws)
```

교육 또는 배포에서 환경을 처음 사용하면 작업 영역에 등록됩니다. 그런 다음 계산 대상에 빌드되고 배포됩니다. 서비스는 환경을 캐시합니다. 캐시된 환경을 다시 사용하는 것은 새 서비스 나 업데이트된 환경을 사용하는 것보다 훨씬 적은 시간이 소요됩니다.

### <a name="get-existing-environments"></a>기존 환경 얻기

클래스는 `Environment` 작업 영역에서 기존 환경을 검색할 수 있는 메서드를 제공합니다. 이름을 지정하거나 목록으로 또는 특정 학습 실행으로 환경을 검색할 수 있습니다. 이 정보는 문제 해결, 감사 및 재현성에 유용합니다.

#### <a name="view-a-list-of-environments"></a>환경 목록 보기

클래스를 사용하여 작업 공간의 환경을 [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-) 봅니다. 그런 다음 재사용할 환경을 선택합니다.

#### <a name="get-an-environment-by-name"></a>이름으로 환경 받기

이름과 버전으로 특정 환경을 얻을 수도 있습니다. 다음 코드는 [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) 메서드를 사용하여 `1` 작업 `myenv` 영역에서 `ws` 환경 버전을 검색합니다.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>실행 관련 환경 교육

교육이 완료된 후 특정 실행에 사용된 환경을 얻으려면 [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) 클래스에서 `Run` 메서드를 사용합니다.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>기존 환경 업데이트

예를 들어 Python 패키지를 추가하여 기존 환경을 변경한다고 가정해 보겠습니다. 그런 다음 실행을 제출하거나 모델을 배포하거나 환경을 수동으로 등록할 때 새 버전의 환경이 만들어집니다. 버전 전환을 사용하면 시간이 지남에 따라 환경의 변경 내용을 볼 수 있습니다.

기존 환경에서 Python 패키지 버전을 업데이트하려면 해당 패키지의 버전 번호를 지정합니다. 정확한 버전 번호를 사용하지 않는 경우 Azure Machine Learning은 기존 환경을 원래 패키지 버전으로 다시 사용합니다.

### <a name="debug-the-image-build"></a>이미지 빌드 디버깅

다음 예제에서는 [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace--image-build-compute-none-) 메서드를 사용하여 환경을 Docker 이미지로 수동으로 만듭니다. 을 사용하여 [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)이미지 빌드의 출력 로그를 모니터링합니다. 그러면 빌드된 이미지가 작업 영역의 Azure 컨테이너 레지스트리 인스턴스에 나타납니다. 이 정보는 디버깅에 유용합니다.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="enable-docker"></a>도커 사용

 Azure [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) Machine Learning `Environment` 클래스를 사용하면 교육을 실행하는 게스트 운영 체제를 세밀하게 사용자 지정하고 제어할 수 있습니다.

Docker를 사용하도록 설정하면 서비스가 Docker 이미지를 빌드합니다. 또한 Docker 컨테이너 내에서 사양을 사용하는 Python 환경을 만듭니다. 이 기능은 교육 실행에 추가적인 격리 및 재현성을 제공합니다.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

기본적으로 새로 빌드된 Docker 이미지는 작업 영역과 연결된 컨테이너 레지스트리에 나타납니다.  저장소 이름에는 *azureml/azureml_\<\>uuid*양식이 있습니다. 이름의 고유*식별자(uuid)* 부분은 환경 구성에서 계산된 해시에 해당합니다. 이 서신을 통해 서비스는 지정된 환경에 대한 이미지가 재사용을 위해 이미 존재하는지 여부를 확인할 수 있습니다.

또한, 서비스는 자동으로 우분투 리눅스 기반 [기본 이미지](https://github.com/Azure/AzureML-Containers)중 하나를 사용. 지정된 파이썬 패키지를 설치합니다. 기본 이미지에는 CPU 버전과 GPU 버전이 있습니다. Azure 기계 학습은 사용할 버전을 자동으로 검색합니다.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

사용자 지정 Dockerfile을 지정할 수도 있습니다. Docker ```FROM``` 명령을 사용하여 Azure 기계 학습 기본 이미지 중 하나에서 시작한 다음 사용자 지정 단계를 추가하는 것이 가장 간단합니다. 비 파이썬 패키지를 종속성으로 설치해야 하는 경우 이 방법을 사용합니다.

```python
# Specify docker steps as a string. Alternatively, load the string from a file.
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
```

### <a name="use-user-managed-dependencies"></a>사용자 관리 종속성 사용

경우에 따라 사용자 지정 기본 이미지에 사용 하려는 패키지가 있는 Python 환경이 이미 포함될 수 있습니다.

기본적으로 Azure Machine Learning 서비스는 지정한 종속성을 가진 Conda 환경을 빌드하고 기본 이미지에 설치한 Python 라이브러리를 사용하는 대신 해당 환경에서 실행을 실행합니다. 

설치된 패키지를 직접 사용하려면 매개 `Environment.python.user_managed_dependencies = True`변수를 설정합니다. 기본 이미지에 Python 인터프리터가 포함되어 있는지 확인하고 교육 스크립트에 필요한 패키지가 있는지 확인합니다.

예를 들어 NumPy 패키지가 설치된 기본 Miniconda 환경에서 실행하려면 먼저 패키지를 설치하는 단계가 있는 Dockerfile을 지정합니다. 그런 다음 사용자 관리 종속성을 `True`로 설정합니다. 

변수를 설정하여 이미지 내에서 특정 파이썬 인터프리터에 대한 경로를 지정할 `Environment.python.interpreter_path` 수도 있습니다.

```python
dockerfile = """
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN conda install numpy
"""

myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
myenv.python.user_managed_dependencies=True
myenv.python.interpreter_path = "/opt/miniconda/bin/python"
```

## <a name="use-environments-for-training"></a>교육을 위한 환경 사용

교육 실행을 제출하려면 환경을 결합하고 [대상을 계산하며](concept-compute-target.md)교육 파이썬 스크립트를 실행 구성으로 결합해야 합니다. 이 구성은 실행을 제출하는 데 사용되는 래퍼 개체입니다.

교육 실행을 제출하면 새 환경을 구축하는 데 몇 분 정도 걸릴 수 있습니다. 기간은 필요한 종속성의 크기에 따라 다릅니다. 환경은 서비스에 의해 캐시됩니다. 환경 정의가 변경되지 않는 한 전체 설정 시간이 한 번만 발생합니다.

다음 로컬 스크립트 실행 예제에서는 [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) 래퍼 개체로 사용할 위치를 보여 줍니다.

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.environment import Environment

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
> 실행 기록을 비활성화하거나 스냅샷을 실행하려면 아래 `ScriptRunConfig.run_config.history`설정을 사용합니다.

실행 구성에서 환경을 지정하지 않으면 실행을 제출할 때 서비스가 기본 환경을 만듭니다.

### <a name="use-an-estimator-for-training"></a>교육에 대한 추정기 사용

교육에 [추정기](how-to-train-ml-models.md) 사용 하는 경우 추정기 인스턴스를 직접 제출할 수 있습니다. 이미 환경과 계산 대상을 캡슐화합니다.

다음 코드는 단일 노드 학습 실행에 대한 추정값을 사용합니다. 모델에 대한 원격 계산에서 `scikit-learn` 실행됩니다. 이전에 계산 대상 개체 및 `compute_target`datastore 개체를 만들었다고 `ds`가정합니다.

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

## <a name="use-environments-for-web-service-deployment"></a>웹 서비스 배포에 환경 사용

모델을 웹 서비스로 배포할 때 환경을 사용할 수 있습니다. 이 기능을 사용하면 재현 가능한 연결된 워크플로우를 사용할 수 있습니다. 이 워크플로에서는 학습 계산과 추론 계산 모두에서 동일한 라이브러리를 사용하여 모델을 학습, 테스트 및 배포할 수 있습니다.

웹 서비스를 배포하려면 배포 개체에 환경, 추론 계산, 점수 매기기 스크립트 [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)및 등록된 모델을 결합합니다. 자세한 내용은 [모델을 배포하는 방법 및 위치를](how-to-deploy-and-where.md)참조하십시오.

이 예제에서는 교육 실행을 완료했다고 가정합니다. 이제 해당 모델을 Azure 컨테이너 인스턴스에 배포하려고 합니다. 웹 서비스를 빌드하면 모델 및 채점 파일이 이미지에 탑재되고 Azure 기계 학습 추론 스택이 이미지에 추가됩니다.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference, & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>노트북 예제

이 [예제 전자 필기장에서는](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) 이 문서에서 보여 준 개념 및 메서드를 확장합니다.

[사용자 지정 Docker 기본 이미지를 사용하여 모델을 배포하는](how-to-deploy-custom-docker-image.md) 경우 사용자 지정 Docker 기본 이미지를 사용하여 모델을 배포하는 방법을 보여 줍니다.

이 [예제 전자 필기에서는](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) Spark 모델을 웹 서비스로 배포하는 방법을 보여 줍니다.

## <a name="create-and-manage-environments-with-the-cli"></a>CLI를 사용하여 환경 생성 및 관리

[Azure 기계 학습 CLI는](reference-azure-machine-learning-cli.md) Python SDK의 대부분의 기능을 미러링합니다. 환경을 만들고 관리하는 데 사용할 수 있습니다. 이 섹션에서 설명하는 명령은 기본 기능을 보여 줍니다.

다음 명령은 지정된 디렉터리에서 기본 환경 정의에 대한 파일을 스캐폴드합니다. 이 파일은 JSON 파일입니다. SDK의 해당 클래스처럼 작동합니다. 파일을 사용하여 사용자 지정 설정이 있는 새 환경을 만들 수 있습니다. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

다음 명령을 실행하여 지정된 디렉터리에서 환경을 등록합니다.

```azurecli-interactive
az ml environment register -d myenvdir
```

다음 명령을 실행하여 등록된 모든 환경을 나열합니다.

```azurecli-interactive
az ml environment list
```

다음 명령을 사용하여 등록된 환경을 다운로드합니다.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>다음 단계

* 관리되는 계산 대상을 사용하여 모델을 학습하려면 [자습서: 모델 학습](tutorial-train-models-with-aml.md)을 참조하십시오.
* 학습된 모델을 가진 후 [모델을 배포하는 방법과 위치를](how-to-deploy-and-where.md)알아봅니다.
* 클래스 [ `Environment` SDK 참조를](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)봅니다.
* 이 문서에서 설명하는 개념 및 방법에 대한 자세한 내용은 [예제 전자 필기장을](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments)참조하십시오.
