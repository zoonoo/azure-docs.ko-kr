---
title: 기계 학습에 Visual Studio Code 사용
titleSuffix: Azure Machine Learning service
description: Visual Studio Code용 Azure Machine Learning을 설치하고 Azure Machine Learning에서 간단한 실험을 만드는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 7/12/2019
ms.custom: seodec18
ms.openlocfilehash: 3b49def1af7f13e38c3a9daea32d56bf3c633261
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871760"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Visual Studio Code용 Azure Machine Learning 시작

이 문서에서는 Visual Studio Code에 대 한 Azure Machine Learning 확장을 사용 하 여 기계 학습 및 심층 학습 모델을 학습 하 고 배포 하는 방법을 알아봅니다.

[Azure Machine Learning 서비스](overview-what-is-azure-ml.md) 는 로컬 및 원격 계산 대상에서 실행 하는 실험에 대 한 지원을 제공 합니다. 모든 실험에서 여러 다른 기법, 하이퍼 매개 변수 등을 반복적으로 시도하기 위해 여러 실행을 추적할 수 있습니다. Azure Machine Learning을 사용하여 사용자 지정 메트릭 및 실험 실행을 추적하여 데이터 과학을 재현하고 감사하도록 할 수 있습니다.

테스트 및 프로덕션 요구에 맞게 이러한 모델을 배포할 수도 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

+ Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [무료 또는 유료 버전의 Azure Machine Learning 서비스](https://aka.ms/AMLFree)를 사용해 보세요.

+ Visual Studio Code를 설치해야 합니다. Visual Studio Code은 데스크톱에서 실행 되는 간단 하지만 강력한 소스 코드 편집기입니다. Python 및 기타 프로그래밍 언어에 대 한 기본 제공 지원이 제공 됩니다. Visual Studio Code를 아직 설치 하지 않은 경우 [방법을 확인](https://code.visualstudio.com/docs/setup/setup-overview)하세요.

+ [Python 3.5 이상을 설치](https://www.anaconda.com/download/)합니다.


## <a name="install-the-extension-for-azure-machine-learning-for-visual-studio-code"></a>Visual Studio Code에 대 한 Azure Machine Learning 확장을 설치 합니다.

Azure Machine Learning 확장을 설치 하면 인터넷에 액세스할 수 있는 경우 두 개 이상의 확장이 자동으로 설치 됩니다. [Azure 계정 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) 및 [Microsoft Python 확장](https://marketplace.visualstudio.com/items?itemName=ms-python.python)입니다.

Azure Machine Learning을 사용 하려면 Visual Studio Code를 Python IDE (통합 개발 환경)로 전환 해야 합니다. [Visual Studio Code에서 python](https://code.visualstudio.com/docs/languages/python)을 사용 하려면 Microsoft python 확장이 필요 합니다. 이 확장은 Azure Machine Learning 확장을 사용 하 여 자동으로 설치 됩니다. 확장은 뛰어난 IDE를 Visual Studio Code 하 고 다양 한 Python 인터프리터를 사용 하는 모든 운영 체제에서 작동 합니다. Microsoft Python 확장은 Visual Studio Code의 모든 기능을 사용 하 여 자동 완성, IntelliSense, lint, 디버깅 및 단위 테스트를 제공 합니다. 또한 확장을 사용 하 여 가상 및 conda 환경을 비롯 한 Python 환경 간을 쉽게 전환할 수 있습니다. Python 코드를 편집, 실행 및 디버깅 하는 방법에 대 한 자세한 내용은 [python hello-세계 자습서](https://code.visualstudio.com/docs/python/python-tutorial)를 참조 하세요.

Azure Machine Learning 확장을 설치 하려면:

1. Visual Studio Code를 엽니다.

1. 웹 브라우저에서 [Visual Studio Code 확장 (미리 보기)에 대 한 Azure Machine Learning](https://aka.ms/vscodetoolsforai)로 이동 합니다.

1. 해당 웹 페이지에서 **설치**를 선택 합니다. 

1. 확장 탭에서 **설치**를 선택 합니다.

1. 확장의 시작 탭이 Visual Studio Code에 열리고, Azure 기호 (다음 스크린샷에서 빨간색으로 표시 됨)가 활동 표시줄에 추가 됩니다.

   ![Visual Studio Code 활동 표시줄의 Azure 아이콘](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. 대화 상자에서 **로그인** 을 선택 하 고 프롬프트에 따라 Azure를 사용 하 여 인증 합니다. 
   
   Visual Studio Code 확장에 대 한 Azure Machine Learning와 함께 설치 되는 Azure 계정 확장은 Azure 계정으로 인증 하는 데 도움이 됩니다. 명령 목록은 [Azure 계정 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)에 대 한 페이지를 참조 하세요.

> [!Tip] 
> [Visual Studio Code (미리 보기)에 대 한 IntelliCode 확장](https://go.microsoft.com/fwlink/?linkid=2006060)을 확인 하세요. IntelliCode는 현재 코드 컨텍스트를 기반으로 가장 관련성이 높은 autocompletions를 유추 하는 것과 같이 Python의 IntelliSense에 대 한 AI 지원 기능 집합을 제공 합니다.

## <a name="install-the-azure-machine-learning-sdk"></a>Azure Machine Learning SDK 설치

1. Python 3.5 이상이 설치 되어 Visual Studio Code에서 인식 되는지 확인 합니다. 지금 설치 하는 경우 Visual Studio Code를 다시 시작 하 고 [Python 인터프리터를 선택](https://code.visualstudio.com/docs/python/python-tutorial)합니다.

1. 통합 터미널 창에서 사용할 Python 인터프리터를 지정 합니다. 또는 Enter 키를 선택 하 여 기본 Python 인터프리터를 사용 합니다.

   ![인터프리터 선택](./media/vscode-tools-for-ai/python.png)

1. 창의 오른쪽 아래 모서리에 [AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 가 자동으로 설치 되 고 있음을 나타내는 알림이 표시 됩니다. 새로 만든 Python 환경은 로컬 및 개인 환경 이며 Azure Machine Learning 서비스를 사용 하기 위한 Visual Studio Code 필수 구성 요소가 있습니다.

   ![Python용 Azure Machine Learning SDK 설치](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Azure Machine Learning 시작

Visual Studio Code에서 기계 학습 모델을 학습 하 고 배포 하기 전에 클라우드에서 [Azure Machine Learning 서비스 작업 영역](concept-workspace.md) 을 만들어야 합니다. 이 작업 영역에는 모델과 리소스가 포함 됩니다. 

작업 영역을 만들고 첫 번째 실험을 추가 하려면 다음을 수행 합니다.

1. Visual Studio Code 활동 표시줄에서 Azure 아이콘을 선택 합니다. Azure: Machine Learning 사이드바가 나타납니다.

   [![작업 영역 만들기](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Azure 구독을 마우스 오른쪽 단추로 클릭하고 **작업 영역 만들기**를 선택합니다. 목록이 표시됩니다. 예제 애니메이션 이미지에서 구독 이름은 **무료 평가판**이며 작업 영역은 **teamworkspace**입니다. 

1. 목록에서 리소스 그룹을 선택 하거나 명령 팔레트에서 마법사를 사용 하 여 새 리소스 그룹을 만듭니다.

1. 필드에 새 작업 영역의 고유하고 명확한 이름을 입력합니다. 예제 이미지에서 작업 영역 이름은 **Teamworkspace**입니다.

1. Enter 키를 선택 하 여 새 작업 영역을 만듭니다. 이 파일은 트리에서 구독 이름 아래에 나타납니다.

1. **실험** 노드를 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 **실험 만들기** 를 선택 합니다.  실험은 Azure Machine Learning을 사용하는 실행을 추적합니다.

1. 필드에 실험의 이름을 입력 합니다. 예제 스크린샷에서 실험의 이름은 **Mnist**입니다.
 
1. Enter 키를 선택 하 여 새 실험을 만듭니다. 실험은 트리에서 작업 영역 이름 아래에 나타납니다.

1. 작업 영역에서 실험을 마우스 오른쪽 단추로 클릭 하 여 **활성** 실험으로 설정할 수 있습니다. **활성** 실험은 현재 실험입니다. Visual Studio Code에서 열린 폴더는 클라우드에서이 실험에 연결 됩니다. 이 폴더에는 로컬 Python 스크립트가 포함됩니다.

이제 키 메트릭이 실험 기록 내에 저장 됩니다. 마찬가지로 학습 하는 모델은 Azure Machine Learning 자동으로 업로드 되어 실험 메트릭과 로그와 함께 저장 됩니다. 

[![Visual Studio Code에 폴더 연결](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>계산 대상 만들기 및 관리

Visual Studio Code에 대 한 Azure Machine Learning를 사용 하 여 데이터를 준비 하 고, 모델을 학습 하 고, 로컬 및 원격 계산 대상 모두에 배포할 수 있습니다.

확장은 Azure Machine Learning의 여러 원격 계산 대상을 지원 합니다. 자세한 내용은 [Azure Machine Learning에 대해](how-to-set-up-training-targets.md)지원 되는 계산 대상의 전체 목록을 참조 하세요.

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Visual Studio Code에서 Azure Machine Learning에 대 한 계산 대상 만들기

계산 대상을 만들려면 다음을 수행 합니다.

1. Visual Studio Code 활동 표시줄에서 Azure 아이콘을 선택 합니다. Azure: Machine Learning 사이드바가 나타납니다.

2. 트리 보기에서 Azure 구독 및 Azure Machine Learning 서비스 작업 영역을 확장합니다. 다음 예제 이미지에서 구독 이름은 **무료 평가판**이며 작업 영역은 **teamworkspace**입니다. 

3. 작업 영역 노드에서 **계산** 노드를 마우스 오른쪽 단추로 클릭하고 **계산 만들기**를 선택합니다.

4. 목록에서 계산 대상 유형을 선택합니다. 

5. 명령 팔레트에서 가상 머신 크기를 선택 합니다.

6. 명령 팔레트의 필드에 계산 대상의 이름을 입력 합니다. 

7. 새 탭에서 열리는 JSON 구성 파일에서 고급 속성을 지정 합니다. 최대 노드 수와 같은 속성을 지정할 수 있습니다.

8. 계산 대상 구성을 마치면 창의 오른쪽 아래에 있는 **제출**을 선택 합니다.

다음은 Azure Machine Learning 계산 (AMLCompute)을 만드는 방법의 예입니다.

[![Visual Studio Code에서 AML 계산 만들기](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>실행 구성 파일

Visual Studio Code 확장은 로컬 컴퓨터의 로컬 및 docker 환경에 대 한 로컬 계산 대상과 실행 구성을 자동으로 만듭니다. 연결 된 계산 대상 노드 아래에서 실행 구성 파일을 찾을 수 있습니다. 

## <a name="train-and-tune-models"></a>모델 학습 및 조정

Visual Studio Code (미리 보기)에 대 한 Azure Machine Learning를 사용 하 여 코드를 빠르게 반복 하 고, 단계별로 실행 하 고, 디버그 하 고, 소스 코드 제어를 위해 솔루션을 사용 합니다. 

Azure Machine Learning를 사용 하 여 로컬로 실험을 실행 하려면 다음을 수행 합니다.

1. Visual Studio Code 활동 표시줄에서 Azure 아이콘을 선택 합니다. Azure: Machine Learning 사이드바가 나타납니다.

1. 트리 보기에서 Azure 구독 및 Azure Machine Learning 서비스 작업 영역을 확장합니다. 

1. 작업 영역 노드에서 **계산** 노드를 확장 하 고 사용 하려는 계산의 **실행 구성** 을 마우스 오른쪽 단추로 클릭 합니다. 

1. **실험 실행**을 선택합니다.

1. 파일 탐색기에서 실행 하려는 스크립트를 선택 합니다. 

1. 실행을 모니터링 하 고 학습 된 모델을 보려면 통합 Azure Machine Learning 포털을 보려면 **실험 실행 보기** 를 선택 합니다.

실험을 로컬로 실행 하는 방법의 예는 다음과 같습니다.

[![로컬로 실험 실행](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-visual-studio-code"></a>Visual Studio Code의 실험에 원격 계산 사용

학습에 원격 계산 대상을 사용 하려면 실행 구성 파일을 만들어야 합니다. 이 파일은 Azure Machine Learning에 실험 실행 위치뿐만 아니라 환경 준비 방법도 알려줍니다.

#### <a name="the-conda-dependencies-file"></a>Conda 종속성 파일

기본적으로 새 conda 환경이 만들어지고 설치 종속성이 관리 됩니다. 그러나 *aml_config/conda_dependencies* 파일에서 종속성 및 해당 버전을 지정 해야 합니다. 

기본 *aml_config/conda_dependencies* 의 다음 코드 조각은를 지정 `tensorflow=1.12.0`합니다. 종속성의 버전을 지정 하지 않으면 최신 버전이 사용 됩니다. 구성 파일에서 추가 종속성을 추가할 수 있습니다.

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

원격 계산 대상에서 Azure Machine Learning를 사용 하 여 실험을 실행 하려면 다음을 수행 합니다.

1. Visual Studio Code 활동 표시줄에서 Azure 아이콘을 선택 합니다. Azure: Machine Learning 사이드바가 나타납니다.

1. 트리 보기에서 Azure 구독 및 Azure Machine Learning 서비스 작업 영역을 확장합니다. 

1. 편집기 창에서 Python 스크립트를 마우스 오른쪽 단추로 클릭 하 고 AML: **를 선택 합니다. Azure에서 실험으로 실행**을 선택합니다. 

1. 명령 팔레트에서 계산 대상을 선택 합니다. 

1. 명령 팔레트의 필드에 실행 구성 이름을 입력 합니다. 

1. *Conda_dependencies* 파일을 편집 하 여 실험의 런타임 종속성을 지정 합니다. 그런 다음 창의 오른쪽 아래에 있는 **제출**을 선택 합니다. 

1. 실행을 모니터링 하 고 학습 된 모델을 보려면 통합 Azure Machine Learning 포털을 보려면 **실험 실행 보기** 를 선택 합니다.

원격 계산 대상에서 실험을 실행 하는 방법의 예는 다음과 같습니다.

[![원격 대상에서 실험 실행](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>모델 배포 및 관리
Azure Machine Learning에서는 클라우드와에 지에서 기계 학습 모델을 배포 하 고 관리할 수 있습니다. 

### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>모델을 등록 하 여 Azure Machine Learning Visual Studio Code

이제 모델을 학습 했으므로 작업 영역에 등록할 수 있습니다. 등록 된 모델을 추적 하 고 배포할 수 있습니다.

모델을 등록 하려면:

1. Visual Studio Code 활동 표시줄에서 Azure 아이콘을 선택 합니다. Azure: Machine Learning 사이드바가 나타납니다.

1. 트리 보기에서 Azure 구독 및 Azure Machine Learning 서비스 작업 영역을 확장합니다.

1. 작업 영역 노드에서 **모델**을 마우스 오른쪽 단추로 클릭하고 **모델 등록**을 선택합니다.

1. 명령 팔레트의 필드에 모델 이름을 입력 합니다. 

1. 목록에서 **모델 파일** (단일 모델의 경우) 또는 **모델 폴더** (TensorFlow와 같은 여러 파일이 있는 모델의 경우)를 업로드할 지 여부를 선택 합니다. 

1. 폴더 또는 파일을 선택합니다.

1. 모델 속성 구성을 마치면 창의 오른쪽 아래에 있는 **제출**을 선택 합니다. 

Azure Machine Learning에 모델을 등록 하는 방법에 대 한 예제는 다음과 같습니다.

[![AML에 모델 등록](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-visual-studio-code"></a>Visual Studio Code에서 서비스 배포

Visual Studio Code에서 웹 서비스를 배포할 수 있습니다.
+ 테스트를 위한 ACI (Azure Container Instances).
+ 프로덕션을 위한 AKS (Azure Kubernetes Service)

ACI 컨테이너는 즉석에서 만들어지기 때문에 미리 테스트할 ACI 컨테이너를 만들 필요가 없습니다. 그러나 AKS 클러스터를 미리 구성 해야 합니다. 자세한 내용은 [Azure Machine Learning 서비스를 사용 하 여 모델 배포](how-to-deploy-and-where.md)를 참조 하세요.

웹 서비스를 배포 하려면:

1. Visual Studio Code 활동 표시줄에서 Azure 아이콘을 선택 합니다. Azure: Machine Learning 사이드바가 나타납니다.

1. 트리 보기에서 Azure 구독 및 Azure Machine Learning 서비스 작업 영역을 확장합니다.

1. 작업 영역 노드에서 **모델** 노드를 확장합니다.

1. 배포 하려는 모델을 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 **등록 된 모델에서 서비스 배포** 를 선택 합니다.

1. 명령 팔레트에서 배포 하려는 계산 대상을 선택 합니다. 

1. 명령 팔레트의 필드에이 서비스의 이름을 입력 합니다.  

1. 명령 팔레트에서 키보드의 Enter 키를 선택 하 여 스크립트 파일을 찾아 선택 합니다.

1. 명령 팔레트에서 키보드의 Enter 키를 선택 하 여 conda 종속성 파일을 찾아 선택 합니다.

1. 서비스 속성 구성을 마치면 창의 오른쪽 아래에 있는 배포에 **제출** 을 선택 합니다. 서비스 속성 파일에서 로컬 docker 파일 또는 schema. json 파일을 지정할 수 있습니다.

이제 웹 서비스가 배포됩니다.

웹 서비스를 배포 하는 방법의 예는 다음과 같습니다.

[![웹 서비스 배포](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>바로 가기 키 사용

키보드를 사용 하 여 Visual Studio Code Azure Machine Learning 기능에 액세스할 수 있습니다. 가장 중요 한 바로 가기 키는 Ctrl + Shift + P 이며 명령 팔레트를 표시 합니다. 명령 팔레트에서 가장 일반적인 작업에 대 한 바로 가기 키를 포함 하 여 Visual Studio Code의 모든 기능에 액세스할 수 있습니다.

[![Visual Studio Code Azure Machine Learning에 대 한 바로 가기 키](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>다음 단계

* Visual Studio Code 외부 Azure Machine Learning를 학습 하는 방법에 대 한 연습은 [자습서: Azure Machine Learning을 사용하여 모델 학습](tutorial-train-models-with-aml.md)을 참조하세요.
* 코드를 로컬로 편집, 실행 및 디버그 하는 방법에 대 한 연습은 [Python hello-세계 자습서](https://code.visualstudio.com/docs/python/python-tutorial)를 참조 하세요.
