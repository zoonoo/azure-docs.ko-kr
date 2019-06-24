---
title: Machine learning 용 Visual Studio Code 사용
titleSuffix: Azure Machine Learning service
description: Visual Studio Code용 Azure Machine Learning을 설치하고 Azure Machine Learning에서 간단한 실험을 만드는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 70f9c34957b977aff9fc6211bf79415ed9abe255
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66016518"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Visual Studio Code용 Azure Machine Learning 시작

이 문서에서는 Visual Studio Code에 대 한 Azure Machine Learning에 대 한 확장을 사용 하 여 학습 및 기계 학습 및 딥 러닝 모델을 배포 하는 방법에 알아봅니다.

합니다 [Azure Machine Learning 서비스](overview-what-is-azure-ml.md) 로컬 및 원격에서 실행 하는 실험 계산 대상에 대 한 지원을 제공 합니다. 모든 실험에서 여러 다른 기법, 하이퍼 매개 변수 등을 반복적으로 시도하기 위해 여러 실행을 추적할 수 있습니다. Azure Machine Learning을 사용하여 사용자 지정 메트릭 및 실험 실행을 추적하여 데이터 과학을 재현하고 감사하도록 할 수 있습니다.

또한 테스트 및 프로덕션 요구에 맞게 이러한 모델을 배포할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

+ Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 시도 된 [무료 또는 유료 버전의 Azure Machine Learning 서비스](https://aka.ms/AMLFree)합니다.

+ Visual Studio Code를 설치해야 합니다. Visual Studio Code는 데스크톱에서 실행 되는 간단 하지만 강력한 소스 코드 편집기입니다. Python 및 다른 프로그래밍 언어에 대 한 기본 제공 지원이 제공 됩니다. Visual Studio Code를 아직 설치 하지 않은 경우 [확인해](https://code.visualstudio.com/docs/setup/setup-overview)합니다.

+ [Python 3.5 또는 이후 설치](https://www.anaconda.com/download/)합니다.


## <a name="install-the-extension-for-azure-machine-learning-for-visual-studio-code"></a>Azure Machine Learning 용 Visual Studio Code 용 확장을 설치

Azure Machine Learning 확장을 설치 하는 경우 (있는 경우 인터넷에 액세스) 두 개 이상의 확장 프로그램은 자동으로 설치 됩니다. 하는 [Azure 계정 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) 하며 [Microsoft Python 확장](https://marketplace.visualstudio.com/items?itemName=ms-python.python)합니다.

Azure Machine Learning을 사용 하려면 Visual Studio Code Python 통합 개발 환경 (IDE)으로 설정 해야 합니다. Microsoft Python 확장을 사용 해야 [Visual Studio Code에서 Python](https://code.visualstudio.com/docs/languages/python)합니다. 이 확장은 Azure Machine Learning 확장명이 자동으로 설치 됩니다. 확장 하면 Visual Studio Code는 훌륭한 IDE 및 다양 한 Python 인터프리터를 사용 하 여 모든 운영 체제에서 작동 합니다. Microsoft Python 확장 자동 완성, IntelliSense, linting, 디버깅 및 단위 테스트를 위해 모든 Visual Studio Code의 power를 사용 합니다. 또한 확장을 사용 하면 Python 환경에 포함 하 여 가상 및 conda 환경 간을 쉽게 전환할 수 있습니다. 편집 하는 방법에 대 한 자세한 정보를 실행 하 고 Python 코드 디버깅, 참조를 [Python hello world 자습서](https://code.visualstudio.com/docs/python/python-tutorial)합니다.

Azure Machine Learning 확장을 설치 합니다.

1. Visual Studio Code를 엽니다.

1. 웹 브라우저에서로 이동 [Visual Studio Code 확장 (미리 보기)에 대 한 Azure Machine Learning](https://aka.ms/vscodetoolsforai)합니다.

1. 해당 웹 페이지에서 선택 **설치**합니다. 

1. 확장 탭에서 선택 **설치**합니다.

1. Visual Studio Code에서 확장에 대 한 시작 탭이 열리고 (다음 스크린 샷에서 빨간색 윤곽선이 있는) Azure 기호 작업 표시줄에 추가 됩니다.

   ![Visual Studio Code 작업 표시줄에서 azure 아이콘](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. 대화 상자에서 선택 **로그인** 지시에 따라 Azure를 사용 하 여 인증 하 고 있습니다. 
   
   Visual Studio Code 확장에 대 한 Azure Machine Learning과 함께 설치 된 Azure 계정 확장을 사용 하면 Azure 계정으로 인증할 수 있습니다. 명령의 목록을 페이지를 참조 하십시오.는 [Azure 계정 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)합니다.

> [!Tip] 
> 체크 아웃 합니다 [Visual Studio Code (미리 보기)에 대 한 확장 IntelliCode](https://go.microsoft.com/fwlink/?linkid=2006060)합니다. IntelliCode 현재 코드 컨텍스트를 기반으로 가장 관련성이 높은 autocompletions 유추와 같은 Python의 IntelliSense에 대 한 AI 지원 기능의 집합을 제공 합니다.

## <a name="install-the-azure-machine-learning-sdk"></a>Azure Machine Learning SDK 설치

1. Python 3.5 또는 이후가 설치 되어 Visual Studio 코드에서 인식할 수 있는지 확인 합니다. 지금 설치 하는 경우 Visual Studio Code를 다시 시작 하 고 [Python 인터프리터 선택](https://code.visualstudio.com/docs/python/python-tutorial)합니다.

1. 통합된 터미널 창에서 사용 하는 Python 인터프리터를 지정 합니다. 또는 기본 Python 인터프리터를 사용 하려면 enter 키를 선택 합니다.

   ![인터프리터 선택](./media/vscode-tools-for-ai/python.png)

1. 창의 오른쪽 아래 구석에 알림이 표시 됩니다, 나타내는 합니다 [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 자동으로 설치 됩니다. 새로 생성된 된 Python 환경에 로컬 이며 개인 있고 Visual Studio Code 필수 구성 요소는 Azure Machine Learning 서비스를 사용 합니다.

   ![Python용 Azure Machine Learning SDK 설치](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Azure Machine Learning 시작

교육을 시작 하 고 생성 해야 Visual Studio Code에서 기계 학습 모델을 배포 하기 전에 [Azure Machine Learning 서비스 작업 영역](concept-workspace.md) 클라우드에서 합니다. 이 작업 영역에서 모델 및 리소스에 포함 됩니다. 

작업 영역을 만들고 첫 번째 실험을 추가 합니다.

1. Visual Studio Code 작업 표시줄에서 Azure 아이콘을 선택 합니다. Azure: Machine Learning 사이드바가 나타납니다.

   [![작업 영역 만들기](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Azure 구독을 마우스 오른쪽 단추로 클릭하고 **작업 영역 만들기**를 선택합니다. 목록이 표시됩니다. 예제에서는 애니메이션된 이미지에서 구독 이름은 **무료 평가판**, 및 작업 영역이 **TeamWorkspace**합니다. 

1. 목록에서 리소스 그룹을 선택 하거나 명령 팔레트에서 마법사를 사용 하 여 새로 만듭니다.

1. 필드에 새 작업 영역의 고유하고 명확한 이름을 입력합니다. 예제 이미지에서 작업 영역 이름은 **TeamWorkspace**합니다.

1. 새 작업 영역을 만들려면 Enter를 선택 합니다. 구독 이름 아래에 있는 트리를 표시 됩니다.

1. 마우스 오른쪽 단추로 클릭 합니다 **실험** 노드를 선택 하 고 **실험 만들기** 상황에 맞는 메뉴에서.  실험은 Azure Machine Learning을 사용하는 실행을 추적합니다.

1. 필드에 실험에 대 한 이름을 입력 합니다. 실험 이름은 예제 스크린샷에 **MNIST**합니다.
 
1. 새 실험을 만들려면 Enter를 선택 합니다. 실험 트리를 작업 영역 이름 아래에 나타납니다.

1. 실험을 설정 하는 것을 작업 영역에서 단추로 합니다 **활성** 실험 합니다. 합니다 **활성** 실험은 현재 실험 합니다. Visual Studio Code에 열린 폴더 클라우드에서이 실험에 연결 됩니다. 이 폴더에는 로컬 Python 스크립트가 포함됩니다.

이제 실험 기록 내에서 주요 메트릭을 저장 됩니다. 마찬가지로, 학습할 모델은 자동으로 Azure Machine Learning에 업로드 되며 실험 메트릭 및 로그와 함께 저장 합니다. 

[![Visual Studio Code에서 폴더를 연결 합니다.](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>계산 대상 만들기 및 관리

Visual Studio Code에 대 한 Azure Machine Learning을 사용 하 여 데이터를 준비 하, 모델을 학습 및 로컬 및 원격 계산 대상에 배포할 수 있습니다.

확장은 Azure Machine Learning에 대 한 몇 가지 원격 계산 대상을 지원합니다. 자세한 내용은 전체 목록은 참조 하세요. 지원 되는 [Azure Machine Learning에 대 한 계산 대상](how-to-set-up-training-targets.md)합니다.

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Visual Studio Code에서 Azure Machine Learning에 대 한 계산 대상 만들기

계산 대상을 만들려면:

1. Visual Studio Code 작업 표시줄에서 Azure 아이콘을 선택 합니다. Azure: Machine Learning 사이드바가 나타납니다.

2. 트리 보기에서 Azure 구독 및 Azure Machine Learning 서비스 작업 영역을 확장합니다. 다음 예제 이미지에서 구독 이름은 **무료 평가판**, 및 작업 영역이 **TeamWorkspace**합니다. 

3. 작업 영역 노드에서 **계산** 노드를 마우스 오른쪽 단추로 클릭하고 **계산 만들기**를 선택합니다.

4. 목록에서 계산 대상 유형을 선택합니다. 

5. 명령 팔레트에서 가상 머신 크기를 선택 합니다.

6. 명령 팔레트에서 필드의 계산 대상에 대 한 이름을 입력 합니다. 

7. 새 탭에는 JSON 구성 파일에서 모든 고급 속성을 지정 합니다. 최대 노드 수와 같은 속성을 지정할 수 있습니다.

8. 선택 창의 오른쪽 아래 모서리에서 계산 대상, 구성를 마치면 **제출**합니다.

Azure Machine Learning compute (AMLCompute)를 만드는 방법의 예는 다음과 같습니다.

[![Visual Studio Code에서 AML 계산 만들기](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>실행된 구성 파일

Visual Studio Code 확장을 자동으로 로컬 컴퓨터에 로컬 계산 대상 및 로컬 및 docker 환경에 대 한 실행된 구성을 만듭니다. 연결 된 계산 대상 노드에서 실행된 구성 파일을 찾을 수 있습니다. 

## <a name="train-and-tune-models"></a>모델 학습 및 조정

Visual Studio Code (미리 보기)에 대 한 Azure Machine Learning을 사용 하 여 신속 하 게 코드에서 반복을 단계별로 실행 및 디버깅 및 소스 코드 제어에 대 한 솔루션을 사용 하 합니다. 

Azure Machine Learning을 사용 하 여 로컬로 실험을 실행 합니다.

1. Visual Studio Code 작업 표시줄에서 Azure 아이콘을 선택 합니다. Azure: Machine Learning 사이드바가 나타납니다.

1. 트리 보기에서 Azure 구독 및 Azure Machine Learning 서비스 작업 영역을 확장합니다. 

1. 작업 영역 노드를 확장 합니다 **계산** 노드에서 마우스 오른쪽 단추로 클릭은 **구성 실행** 사용 하려는 계산 합니다. 

1. **실험 실행**을 선택합니다.

1. 파일 탐색기에서 실행 하려는 스크립트를 선택 합니다. 

1. 선택 **보기 실험 실행** 보려면 통합 된 Azure Machine Learning 포털을 실행 하 고 모니터링 하 고 학습 된 모델을 참조 하세요.

실험을 로컬로 실행 하는 방법의 예는 다음과 같습니다.

[![실험을 로컬로 실행](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-visual-studio-code"></a>Visual Studio Code에서 실험에 대 한 원격 계산 사용

원격 계산 대상에 학습을 사용 하려면 실행된 구성 파일을 만들 필요가 있습니다. 이 파일은 Azure Machine Learning에 실험 실행 위치뿐만 아니라 환경 준비 방법도 알려줍니다.

#### <a name="the-conda-dependencies-file"></a>Conda 종속성 파일

기본적으로 새 conda 환경, 만들어지고 설치 종속성 관리 됩니다. 그러나, 종속성 및 해당 버전에 지정 해야 합니다 *aml_config/conda_dependencies.yml* 파일입니다. 

다음 코드 조각은 기본값과에서 *aml_config/conda_dependencies.yml* 지정 `tensorflow=1.12.0`합니다. 종속성의 버전을 지정 하지 않으면 최신 버전이 사용 됩니다. 구성 파일에서 추가 종속성을 추가할 수 있습니다.

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2
- tensorflow=1.12.0

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

실행 하려면 원격 컴퓨터의 Azure Machine Learning을 사용 하 여 실험 계산 대상:

1. Visual Studio Code 작업 표시줄에서 Azure 아이콘을 선택 합니다. Azure: Machine Learning 사이드바가 나타납니다.

1. 트리 보기에서 Azure 구독 및 Azure Machine Learning 서비스 작업 영역을 확장합니다. 

1. 편집기 창에서 Python 스크립트를 마우스 오른쪽 단추로 클릭 하 고 선택 **AML: Azure에서 실험으로 실행**을 선택합니다. 

1. 명령 팔레트에서 계산 대상을 선택 합니다. 

1. 명령 팔레트에서 필드에 실행된 구성 이름을 입력 합니다. 

1. 편집 된 *conda_dependencies.yml* 실험의 런타임 종속성을 지정 하는 파일입니다. 창의 오른쪽 아래 모퉁이에서 선택한 **제출**합니다. 

1. 선택 **보기 실험 실행** 보려면 통합 된 Azure Machine Learning 포털을 실행 하 고 모니터링 하 고 학습 된 모델을 참조 하세요.

원격 계산 대상에 실험을 실행 하는 방법의 예는 다음과 같습니다.

[![원격 대상에 실험 실행](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>모델 배포 및 관리
Azure Machine Learning에 배포 하 고 기계 학습 모델 클라우드 및에 지에서 관리 수 있습니다. 

### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Visual Studio Code에서 Azure Machine learning 모델을 등록

모델을 학습 했으므로 작업 영역에서 등록할 수 있습니다. 추적 하 고 등록 된 모델을 배포할 수 있습니다.

모델을 등록 합니다.

1. Visual Studio Code 작업 표시줄에서 Azure 아이콘을 선택 합니다. Azure: Machine Learning 사이드바가 나타납니다.

1. 트리 보기에서 Azure 구독 및 Azure Machine Learning 서비스 작업 영역을 확장합니다.

1. 작업 영역 노드에서 **모델**을 마우스 오른쪽 단추로 클릭하고 **모델 등록**을 선택합니다.

1. 명령 팔레트를 필드에 모델 이름을 입력 합니다. 

1. 목록에서 업로드 하도록 선택할을 **모델 파일** (단일 모델용) 또는 **모델 폴더** (TensorFlow와 같은 여러 파일을 사용 하 여 모델용). 

1. 폴더 또는 파일을 선택합니다.

1. 선택 창의 오른쪽 아래 모서리에 있는 모델 속성을 구성를 마치면 **제출**합니다. 

Azure Machine learning 모델을 등록 하는 방법의 예는 다음과 같습니다.

[![AML에 모델 등록](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-visual-studio-code"></a>Visual Studio Code에서 서비스 배포

Visual Studio Code에서 웹 서비스를 배포할 수 있습니다.
+ Azure ACI (Container Instances) 테스트 합니다.
+ Azure Kubernetes Service (AKS) 프로덕션에 대 한 합니다.

ACI 컨테이너 즉석에서 생성 되므로 미리 테스트 하는 ACI 컨테이너를 만들 필요가 없습니다. 그러나 미리 AKS 클러스터를 구성 하려면 수행 해야 합니다. 자세한 내용은 [Azure Machine Learning 서비스를 사용 하 여 모델 배포](how-to-deploy-and-where.md)합니다.

웹 서비스를 배포 합니다.

1. Visual Studio Code 작업 표시줄에서 Azure 아이콘을 선택 합니다. Azure: Machine Learning 사이드바가 나타납니다.

1. 트리 보기에서 Azure 구독 및 Azure Machine Learning 서비스 작업 영역을 확장합니다.

1. 작업 영역 노드에서 **모델** 노드를 확장합니다.

1. 배포 하 고 선택 하려는 모델을 마우스 오른쪽 단추로 클릭 **등록 된 모델에서 서비스 배포** 상황에 맞는 메뉴입니다.

1. 명령 팔레트에서 배포 하려는 계산 대상을 선택 합니다. 

1. 명령 팔레트를 필드에이 서비스의 이름을 입력 합니다.  

1. 명령 팔레트에서 찾아보기 및 스크립트 파일을 선택 하려면 키보드에서 Enter 키를 선택 합니다.

1. 명령 팔레트에서 찾아 conda 종속성 파일을 선택 하려면 키보드에서 Enter 키를 선택 합니다.

1. 선택 창의 오른쪽 아래 모서리에 있는 서비스 속성을 구성를 마치면 **제출** 배포 합니다. 서비스 속성 파일의 로컬 docker 파일 또는 schema.json 파일을 지정할 수 있습니다.

이제 웹 서비스가 배포됩니다.

웹 서비스를 배포 하는 방법의 예는 다음과 같습니다.

[![웹 서비스 배포](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>바로 가기 키 사용

Visual Studio Code에서 Azure Machine Learning 기능에 액세스 하려면 키보드를 사용할 수 있습니다. 알고 가장 중요 한 바로 가기 키 Ctrl + Shift + P를 명령 팔레트를 표시 하는 경우 명령 팔레트에서 가장 일반적인 작업에 대 한 바로 가기 키를 포함 하 여 Visual Studio Code의 기능을 모두에 액세스할을 수 있습니다.

[![Visual Studio Code에 대 한 Azure Machine Learning에 대 한 바로 가기 키](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>다음 단계

* Visual Studio Code 외부에서 Azure Machine Learning을 사용 하 여 학습 하는 방법의 연습을 참조 하세요. [자습서: Azure Machine Learning을 사용하여 모델 학습](tutorial-train-models-with-aml.md)을 참조하세요.
* 편집, 실행 및 코드를 로컬로 디버그 하는 방법의 연습에 대해서는 [Python hello world 자습서](https://code.visualstudio.com/docs/python/python-tutorial)합니다.
