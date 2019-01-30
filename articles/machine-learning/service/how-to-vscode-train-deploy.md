---
title: VS Code에서 모델 학습 및 배포
titleSuffix: Azure Machine Learning service
description: Visual Studio Code용 Azure Machine Learning에 대해 알아보고 Visual Studio Code를 사용하여 Azure Machine Learning 서비스에서 기계 학습 및 딥 러닝 모델을 학습 및 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: e7df9086fa5ffc6273a6cb063bdee3cfdfa73e34
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54013318"
---
# <a name="use-visual-studio-code-to-train-and-deploy-machine-learning-models"></a>Visual Studio Code를 사용하여 기계 학습 모델 학습 및 배포

이 문서에서는 **Visual Studio Code용 Azure Machine Learning** 확장을 사용하여 VS Code(Visual Studio Code)에서 Azure Machine Learning 서비스로 기계 학습 및 딥 러닝 모델을 학습하고 배포하는 방법을 알아봅니다.

Azure Machine Learning은 로컬로 및 원격 계산 대상에 대해 실험을 실행하도록 지원합니다. 모든 실험에서 여러 다른 기법, 하이퍼 매개 변수 등을 반복적으로 시도하기 위해 여러 실행을 추적할 수 있습니다. Azure Machine Learning을 사용하여 사용자 지정 메트릭 및 실험 실행을 추적하여 데이터 과학을 재현하고 감사하도록 할 수 있습니다.

또한 테스트 및 프로덕션 요구에 맞게 이러한 모델을 배포할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

+ Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](http://aka.ms/AMLFree)을 지금 사용해 보세요.

+ [VS Code용 Azure Machine Learning](how-to-vscode-tools.md) 확장을 설치합니다.

+ VS Code와 함께 [Azure Machine Learning SDK for Python을 설치](how-to-vscode-tools.md)합니다.

## <a name="create-and-manage-compute-targets"></a>계산 대상 만들기 및 관리

VS Code용 Azure Machine Learning을 사용하여 데이터를 준비하고, 모델을 학습하고, 로컬 및 원격 컴퓨팅 대상에 배포할 수 있습니다.

이 확장은 Azure Machine Learning을 위한 몇 가지 다른 원격 계산 대상을 지원합니다. Azure Machine Learning에 대해 [지원되는 전체 계산 대상 목록](how-to-set-up-training-targets.md)을 참조하세요.

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>VS Code에서 Azure Machine Learning의 계산 대상 만들기

**계산 대상을 만들려면**

1. Visual Studio Code 작업 막대에서 Azure 아이콘을 클릭합니다. Azure: Machine Learning 사이드바가 나타납니다.

2. 트리 보기에서 Azure 구독 및 Azure Machine Learning 서비스 작업 영역을 확장합니다. 애니메이션 이미지에서 구독 이름은 'Free Trial'이고 작업 영역은 'TeamWorkspace'입니다. 

3. 작업 영역 노드에서 **계산** 노드를 마우스 오른쪽 단추로 클릭하고 **계산 만들기**를 선택합니다.

4. 목록에서 계산 대상 유형을 선택합니다. 

5. 명령 팔레트에서 Virtual Machine 크기를 선택합니다.

6. 명령 팔레트에서 필드에 컴퓨팅 대상의 이름을 입력합니다. 

7. 새 탭에서 열리는 JSON 구성 파일의 고급 속성을 지정합니다. 최대 노드 수와 같은 속성을 지정할 수 있습니다.

8. 컴퓨팅 대상 구성을 마치면 화면의 오른쪽 하단 모서리에 있는 **제출**을 클릭합니다.

Azure Machine Learning Compute(AMLCompute)를 만드는 예제는 다음과 같습니다. [![VS Code에서 AML 컴퓨팅 만들기](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>‘실행 구성’ 파일

VS Code 확장은 로컬 컴퓨터의 **로컬** 및 **docker** 환경의 로컬 컴퓨팅 대상 및 실행 구성을 자동으로 만듭니다. 실행 구성 파일은 연결된 컴퓨팅 대상에서 찾을 수 있습니다. 

이는 기본 로컬 실행 구성 파일의 한 조각입니다. 기본적으로 `userManagedDependencies: True`이므로 모든 라이브러리/종속 항목을 직접 설치해야 합니다. 그러면 로컬 실험을 실행하는 데 VS Code Python 확장이 지정한 기본 Python 환경이 사용됩니다.

```yaml
# user_managed_dependencies = True indicates that the environment will be user managed. False indicates that AzureML will manage the user environment.
    userManagedDependencies: True
# The python interpreter path
    interpreterPath: python
# Path to the conda dependencies file to use for this run. If a project
# contains multiple programs with different sets of dependencies, it may be
# convenient to manage those environments with separate files.
    condaDependenciesFile: aml_config/conda_dependencies.yml
# Docker details
    docker:
# Set True to perform this run inside a Docker container.
    enabled: false
```

## <a name="train-and-tune-models"></a>모델 학습 및 조정

VS Code용 Azure Machine Learning(미리 보기)을 사용하여 코드를 신속하게 반복하고, 단계별로 실행 및 디버그하고, 선택한 소스 코드 제어 솔루션을 사용할 수 있습니다. 

**Azure Machine Learning을 사용하여 실험을 로컬에서 실행하려면**

1. Visual Studio Code 작업 막대에서 Azure 아이콘을 클릭합니다. Azure: Machine Learning 사이드바가 나타납니다.

1. 트리 보기에서 Azure 구독 및 Azure Machine Learning 서비스 작업 영역을 확장합니다. 

1. 작업 영역 노드에서 **컴퓨팅** 노드를 확장하고 사용하려는 컴퓨팅의 **실행 구성**을 마우스 오른쪽 단추로 클릭합니다. 

1. **실험 실행**을 선택합니다.

1. 파일 탐색기에서 실행할 스크립트를 선택합니다. 

1. **실험 실행 보기**를 클릭하여 통합된 Azure Machine Learning 포털에서 실행을 모니터링하고 학습된 모델을 확인합니다.

로컬에서 실험을 실행하는 예제는 다음과 같습니다. [![로컬에서 실험 실행](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>VS Code에서 실험을 위해 원격 계산 사용

학습 시 원격 계산 대상을 사용하려면 실행 구성 파일을 만들어야 합니다. 이 파일은 Azure Machine Learning에 실험 실행 위치뿐만 아니라 환경 준비 방법도 알려줍니다.

#### <a name="the-conda-dependencies-file"></a>Conda 종속성 파일

기본적으로 새 conda 환경이 만들어지고, 설치 종속성이 관리됩니다. 그러나 `aml_config/conda_dependencies.yml` 파일에 종속성을 지정해야 합니다.

다음은 기본 ‘aml_config/conda_dependencies.yml’의 일부입니다.
구성 파일에서 추가 종속성을 추가할 수 있습니다.

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2
- tensorflow

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

**원격 컴퓨팅 대상에서 Azure Machine Learning을 사용하여 실험을 실행하려면**

1. Visual Studio Code 작업 막대에서 Azure 아이콘을 클릭합니다. Azure: Machine Learning 사이드바가 나타납니다.

1. 트리 보기에서 Azure 구독 및 Azure Machine Learning 서비스 작업 영역을 확장합니다. 

1. 편집기 창에서 Python 스크립트를 마우스 오른쪽 단추로 클릭하고 **AML: Azure에서 실험으로 실행**을 선택합니다. 

1. 명령 팔레트에서 컴퓨팅 대상을 선택합니다. 

1. 명령 팔레트에서 필드에 실행 구성 이름을 입력합니다. 

1. conda_dependencies.yml 파일을 편집하여 실험의 런타임 종속 항목을 지정한 후 화면의 오른쪽 하단 모서리에서 **제출**을 클릭합니다. 

1. **실험 실행 보기**를 클릭하여 통합된 Azure Machine Learning 포털에서 실행을 모니터링하고 학습된 모델을 확인합니다.

원격 컴퓨팅 대상에서 실험을 실행하는 예제는 다음과 같습니다. [![원격 대상에서 실험 실행](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>모델 배포 및 관리
Azure Machine Learning을 사용하여 클라우드 및 에지에서 Machine Learning 모델을 배포 및 관리할 수 있습니다. 

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>VS Code에서 Azure Machine Learning에 모델 등록

모델을 학습했으므로 작업 영역에서 등록할 수 있습니다.
등록한 모델을 추적 및 배포할 수 있습니다.

**모델을 등록하려면**

1. Visual Studio Code 작업 막대에서 Azure 아이콘을 클릭합니다. Azure: Machine Learning 사이드바가 나타납니다.

1. 트리 보기에서 Azure 구독 및 Azure Machine Learning 서비스 작업 영역을 확장합니다.

1. 작업 영역 노드에서 **모델**을 마우스 오른쪽 단추로 클릭하고 **모델 등록**을 선택합니다.

1. 명령 팔레트에서 필드에 모델 이름을 입력합니다. 

1. 목록에서 **모델 파일**(단일 모델용) 및 **모델 폴더**(Tensorflow와 같은 여러 파일을 포함하는 모델용) 중 어떤 항목을 업로드할지 선택합니다. 

1. 폴더 또는 파일을 선택합니다.

1. 모델 속성 구성을 마치면 화면의 오른쪽 하단 모서리에 있는 **제출**을 클릭합니다. 

AML에 모델을 등록하는 예제는 다음과 같습니다. [![AML에 모델 등록](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-vs-code"></a>VS Code에서 서비스 배포

VS Code를 사용하여 웹 서비스를 다음에 배포할 수 있습니다.
+ ACI(Azure Container Instance): 테스트용
+ AKS(azure Kubernetes Service): 프로덕션용 

ACI 컨테이너가 즉석에서 만들어지므로 테스트할 컨테이너를 미리 만들 필요가 없습니다. 그러나 AKS 클러스터는 미리 구성해야 합니다. 

일반적인 [Azure Machine Learning 웹 서비스 배포](how-to-deploy-and-where.md)에 대해 자세히 알아봅니다.

**웹 서비스를 배포하려면**

1. Visual Studio Code 작업 막대에서 Azure 아이콘을 클릭합니다. Azure: Machine Learning 사이드바가 나타납니다.

1. 트리 보기에서 Azure 구독 및 Azure Machine Learning 서비스 작업 영역을 확장합니다.

1. 작업 영역 노드에서 **모델** 노드를 확장합니다.

1. 배포하려는 모델을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **등록된 모델에서 서비스 배포** 명령을 선택합니다.

1. 명령 팔레트에서 배포하려는 컴퓨팅 대상을 목록에서 선택합니다. 

1. 명령 팔레트에서 필드에 이 서비스의 이름을 입력합니다.  

1. 명령 팔레트에서 키보드의 Enter 키를 눌러 스크립트 파일을 찾아 선택합니다.

1. 명령 팔레트에서 키보드의 Enter 키를 눌러 conda 종속성 파일을 찾아 선택합니다.

1. 서비스 속성 구성을 마치면 화면의 오른쪽 하단 모서리에 있는 **제출**을 클릭하여 배포합니다. 이 서비스 속성 파일에서 사용하려는 로컬 Docker 파일 또는 schema.json 파일을 지정할 수 있습니다.

이제 웹 서비스가 배포됩니다.

웹 서비스를 배포하는 예제는 다음과 같습니다. [![웹 서비스 배포](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

## <a name="next-steps"></a>다음 단계

VS Code 외부에서 Machine Learning을 사용한 학습을 연습하려면 [자습서: Azure Machine Learning을 사용하여 모델 학습](tutorial-train-models-with-aml.md)을 참조하세요.

로컬로 코드를 편집, 실행 및 디버그하는 방법을 연습하려면 [Python Hello World 자습서](https://code.visualstudio.com/docs/python/python-tutorial)를 참조하세요.
