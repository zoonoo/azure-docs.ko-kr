---
title: 기계 학습에 Visual Studio Code 사용
titleSuffix: Azure Machine Learning
description: Visual Studio Code Azure Machine Learning를 설치 하 고 Azure Machine Learning에서 실험을 만드는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jimgries
author: greazer
ms.date: 09/20/2019
ms.custom: seodec18
ms.openlocfilehash: a93c71a97cdb1f6296919a248cf7ef545f7b307f
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71269248"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Visual Studio Code용 Azure Machine Learning 시작

이 문서에서는 **Visual Studio Code 확장에 대해 Azure Machine Learning** 를 사용 하 여 기계 학습 모델을 학습 하 고 배포 하는 방법을 알아봅니다.

[Azure Machine Learning 서비스](overview-what-is-azure-ml.md) 는 기계 학습 모델의 빌드, 학습 및 배포를 간소화 합니다.
+ 학습을 위해 로컬 또는 원격으로 실험 실행을 지원 합니다. 모든 실험에 대해 여러 실행의 사용자 지정 메트릭을 기록 하 여 하이퍼 매개 변수를 세밀 하 게 조정할 수 있습니다.
+ 또한 Azure Machine Learning 서비스를 사용 하 여 테스트 및 프로덕션 요구에 맞게 Machine Learning 모델을 쉽게 배포할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

+ Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning의 무료 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

+ Windows, Mac 및 Linux에서 실행 되는 간단한 코드 편집기 인 [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview)를 설치 합니다.

+ [Python 3.5 이상을 설치](https://www.anaconda.com/download/)합니다.


## <a name="install-the-extension"></a>확장 설치

Azure Machine Learning 확장을 설치 하면 두 개의 확장을 자동으로 설치 합니다. [Azure 계정 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) 및 [Microsoft Python 확장](https://marketplace.visualstudio.com/items?itemName=ms-Python.Python)입니다. Python 코드를 편집, 실행 및 디버그 하는 데 Python 확장을 사용 하는 방법에 대 한 자세한 내용은 [python hello-세계 자습서](https://code.visualstudio.com/docs/Python/Python-tutorial)를 참조 하세요.

Azure Machine Learning 확장을 설치 하려면:

1. Visual Studio Code를 엽니다.

1. 확장 탭으로 전환 하 고 "Azure Machine Learning"를 검색 합니다.

1. 확장 탭에서 **설치**를 선택 합니다.

1. 확장의 시작 탭이 Visual Studio Code에 열리고 Azure 기호 (다음 스크린샷에 빨간색으로 강조 표시 됨)가 활동 표시줄에 추가 됩니다.

   ![Visual Studio Code 활동 표시줄의 Azure 아이콘](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. 대화 상자에서 **로그인** 을 선택 하 고 프롬프트에 따라 Azure를 사용 하 여 인증 합니다.

   Visual Studio Code 확장에 대 한 Azure Machine Learning와 함께 설치 되는 Azure 계정 확장은 Azure 계정으로 인증 하는 데 도움이 됩니다. 명령 목록은 [Azure 계정 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)에 대 한 페이지를 참조 하세요.

> [!TIP]
> [Visual Studio Code 확장 (미리 보기)에 대 한 Azure Machine Learning](https://aka.ms/vscodetoolsforai)에서 직접 확장 설치 관리자를 다운로드할 수도 있습니다.

## <a name="quickstart-with-azure-machine-learning"></a>Azure Machine Learning 빠른 시작
Azure Machine Learning 서비스를 사용 하 여 학습 스크립트를 실행 하는 방법에는 여러 가지가 있습니다. 방금 시작한 경우 먼저 Azure에서 실행 하는 학습 스크립트를 신속 하 게 제출 하는 방법을 살펴보겠습니다.

Azure Machine Learning 개념을 이미 잘 알고 있고 확장을 사용 하 여 관리 하 고 사용 하는 방법에 대 한 자세한 내용을 보려면 아래 VS Code를 [사용 하 여 Azure Machine Learning](./how-to-vscode-tools.md#azure-machine-learning-in-depth-with-vs-code) 을 참조 하세요.

## <a name="run-an-existing-python-training-script-in-azure"></a>Azure에서 기존 Python 학습 스크립트 실행
기존 학습 스크립트가 있는 경우 VS Code에 대 한 Azure Machine Learning 확장은 뛰어난 편집, 디버깅 및 소스 관리 환경을 제공할 뿐만 아니라 Azure에서 스크립트에 대 한 메트릭을 쉽게 실행 하 고 저장할 수 있습니다.

이제 시작하겠습니다. 준비 된 사용자 고유의 학습 스크립트를 사용 하거나 샘플 [vscode-도구-ai 리포지토리](https://github.com/microsoft/vscode-tools-for-ai)를 복제할 수 있습니다. 이는이 확장에 대 한 문제를 해결 하기 위한 공용 리포지토리입니다. 또한이 예제에 사용할 작은 **mnist** 샘플 폴더도 포함 되어 있습니다.

1. VS Code에서 **mnist** 폴더를 엽니다.

1. 선호 하는 가상 환경 패키지 또는 Anaconda를 사용 하 여 새 Python 환경을 만들고 TensorFlow 및 numpy 패키지를 설치 합니다.

1. VS Code 상태 표시줄의 왼쪽 아래 모서리에서 Python 인터프리터로 만든 새 환경을 선택 합니다.

1. **Train.py** 를 열고 디버거를 열고 실행 단추를 누르거나 F5 키를 누릅니다.

   [![MNIST 교육 실행](./media/vscode-tools-for-ai/run-mnist.gif)](./media/vscode-tools-for-ai/run-mnist.gif#lightbox)

모든 항목이 제대로 설치 되 면 스크립트가 실행 되 고 outputs 폴더에 TensorFlow 모델이 생성 됩니다.

[![TensorFlow 모델 표시](./media/vscode-tools-for-ai/show-tensorflow-model.gif)](./media/vscode-tools-for-ai/show-tensorflow-model.gif#lightbox)

이제 스크립트가 제대로 실행 되는 것을 확인 했으므로 Azure에서 실행 하겠습니다.

**Train.py**을 추가로 수정 하지 않고 쉽게 수행할 수 있습니다. 그러나 몇 가지 간단한 변경 만으로 Azure Machine Learning를 사용 하 여 각 학습 실행에 대해 선택한 중요 한 메트릭을 자동으로 추적할 수 있습니다.

### <a name="make-azure-aware-of-your-run-metrics"></a>Azure에서 실행 메트릭을 인식 하도록 설정
Azure에서 실행 중인 중요 한 정보를 인식할 수 있도록 프로젝트를 수정 하려면 다음을 수행 합니다.

1. **Train.py** 와 동일한 폴더에 **amlrun.py** 라는 파일을 만듭니다.

    ```Python
    import azureml
    from azureml.core import Run

    # access the Azure ML run
    # init run param to check if running within AML
    def get_AMLRun():
        try:
            run = Run.get_submitted_run()
            return run
        except Exception as e:
            print("Caught = {}".format(e.message))
            return None
    ```

2. **Train.py** 에서 amlrun 파일 가져오기

    ```Python
    ...
    from utils import prepare_data
    from amlrun import get_AMLRun
    ...
    ```
3. **Train.py** 에서 실행 개체를 초기화 합니다.

    ```Python
    ...
    init = tf.global_variables_initializer()
    saver = tf.train.Saver()
    run = get_AMLRun()
    ...
    ```
4. 실행 .log () 함수를 사용 하 여 Azure에 메트릭을 기록 합니다.

    ```Python
    ...
            acc_val = acc_op.eval(feed_dict = {X: X_test, y: y_test})

            # log accuracies to AML logger if using AML
            if run != None:
                run.log('Validation Accuracy', np.float(acc_val))
                run.log('Training Accuracy', np.float(acc_train))

            print(epoch, '-- Training accuracy:', acc_train, '\b Validation
    ...
    ```
### <a name="run-the-script-in-azure"></a>Azure에서 스크립트 실행
정말 간단하죠. 이제는 확장을 사용 하 여 클라우드에서 스크립트를 실행 하면 됩니다. 다음 연습 비디오는 새로운 Azure ML 작업 영역을 만드는 데 걸리는 시간과 학습 스크립트를 실행 하는 데 걸리는 시간을 압축 하는 liberty을 가져옵니다.

   [![Azure ML 실험 시작](./media/vscode-tools-for-ai/start-golden-path.gif)](./media/vscode-tools-for-ai/start-golden-path.gif#lightbox)

실험 실행 단추를 클릭 한 후 다음과 같이 프롬프트에 응답 합니다.

1. Azure 구독 선택
1. *새* Azure ML 작업 영역 만들기를 선택 합니다.
1. 미리 구성 된 템플릿 집합에서 선택 하 여 실행에 대 한 Python 환경을 초기화 합니다. 템플릿은 다음에 대 한 시작 지점 및 포함 설정을 제공 합니다.
    1. **PyTorch**, **TensorFlow**또는 **Scikit-배우기**
    1. **단일** 또는 **분산** 계산 교육
    1. 사용자 지정 환경의 **일반**
1. 템플릿에 포함 되지 않은 패키지를 추가 하 여 스크립트에 대해 pip 및 conda 패키지 목록이 완전 한지 확인 합니다.
1. 실험 실행에 대 한 기본 이름 및 사양을 검토 하 고 json 파일의 **실험 제출** 링크를 클릭 합니다. 제출 하기 전에 실험 설정을 검토 하거나 변경할 수 있는 것 처럼 json 파일은 저장 되지 않습니다.
1. 확장을 통해 모든 항목을 설정 하 고 스크립트를 실행 하는 동안 잠시 후 완화 하세요.

    [![클라우드에서 학습](./media/vscode-tools-for-ai/run-golden-path.gif)](./media/vscode-tools-for-ai/run-golden-path.gif#lightbox)

몇 초 후에 실험이 Azure에 제출 되었다는 알림이 표시 됩니다 .이 경우 Azure Portal에는 VS Code 알림에서 **실험 실행 보기** 링크를 클릭 하거나 새로 고침을 통해 VS Code 내에서 진행 상황을 볼 수 있습니다. 단추를 클릭 합니다.

현재 Azure Portal 에서만 실행 메트릭을 볼 수 있습니다. 위에서 언급 한 **실험 실행 링크 보기** 는 기록한 메트릭이 표시 되는 실행으로 이동 합니다.
[![포털에서 실험 실행](./media/vscode-tools-for-ai/experiment-run-on-portal.PNG)](./media/vscode-tools-for-ai/experiment-run-on-portal.PNG#lightbox)

## <a name="azure-machine-learning-in-depth-with-vs-code"></a>VS Code를 사용 하 여 심층 Azure Machine Learning

위의 연습에서는 가장 쉬운 경로를 따라 실험을 제출 했습니다. 이를 통해 확장에서 실험을 실행 하기 위해 관리 해야 하는 단계를 최소화할 수 있습니다. 이 섹션에서는 최대 제어를 제공 하 여 모든 Azure Machine Learning 개념을 개별적으로 관리 하는 방법을 설명 합니다.

Visual Studio Code에서 기계 학습 모델을 학습 하 고 배포 하기 전에 클라우드에서 [Azure Machine Learning 작업 영역](concept-workspace.md) 을 만들어야 합니다. 이 작업 영역에는 모델과 리소스가 포함 됩니다.

### <a name="create-a-workspace"></a>작업 영역 만들기

1. Visual Studio Code 활동 표시줄에서 Azure 아이콘을 선택 합니다. Azure: Machine Learning 사이드바가 나타납니다.

    [![작업 영역 만들기](./media/vscode-tools-for-ai/create-workspace.gif)](./media/vscode-tools-for-ai/create-workspace.gif#lightbox)


1. Azure 구독을 마우스 오른쪽 단추로 클릭하고 **작업 영역 만들기**를 선택합니다. 기본적으로 생성 된 날짜와 시간을 포함 하는 이름이 생성 됩니다. 이름을 **Teamworkspace** 로 변경 하 고 enter 키를 누릅니다.

1. 목록에서 리소스 그룹을 선택 하거나 새로 만들 리소스 그룹을 선택 합니다. 새 항목을 만드는 경우 모델을 배포 하려는 위치와 가장 가까운 위치를 선택 합니다. 이 경우 **미국 서 부 2**를 선택 했습니다.

1. Enter 키를 누르면 작업 영역을 만들기 위한 요청을 받게 됩니다 Azure Machine Learning. Visual Studio Code 알림 영역에서 프로세스에 대 한 알림이 표시 됩니다.

1. 구독 노드를 확장 하 여 새로 만든 작업 영역을 찾습니다.

### <a name="create-an-experiment"></a>실험 만들기
작업 영역에서 하나 이상의 실험을 만들어 개별 모델 학습 실행을 추적 하 고 분석할 수 있습니다. 실행은 Azure 클라우드 또는 로컬 컴퓨터에서 수행할 수 있습니다.

1. **Teamworkspace** 작업 영역을 확장 합니다. **실험** 노드를 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 **실험 만들기** 를 선택 합니다.

1. 프롬프트에 실험의 이름을 입력 합니다. 예제 스크린샷에서 실험의 이름은 **Mnist**입니다.

1. Enter 키를 선택 하 여 새 실험을 만듭니다. 새 실험은 **실험** 노드의 자식으로 트리에 나타납니다.

1. 작업 영역에서 실험을 마우스 오른쪽 단추로 클릭 하 여 **활성** 실험으로 설정할 수 있습니다. **활성** 실험은 클라우드에서 현재 열려 있는 폴더에 대해 클라우드에서 실험을 연결 하는 링크를 Visual Studio Code 합니다. 이 폴더에는 로컬 Python 스크립트가 포함됩니다. 활성 실험을 설정 하 여 모든 학습 실행에 대 한 주요 메트릭은 실행 위치에 관계 없이 실험 내에 저장 됩니다.

    [![실험 만들기](./media/vscode-tools-for-ai/create-experiment.gif)](./media/vscode-tools-for-ai/create-experiment.gif#lightbox)


### <a name="create-and-manage-compute-targets"></a>컴퓨팅 대상 만들기 및 관리

Visual Studio Code에 대 한 Azure Machine Learning를 사용 하 여 데이터를 준비 하 고, 모델을 학습 하 고, 로컬 및 원격 계산 대상 모두에 배포할 수 있습니다.

확장은 Azure Machine Learning의 여러 원격 계산 대상을 지원 합니다. 자세한 내용은 [Azure Machine Learning에 대해](how-to-set-up-training-targets.md)지원 되는 계산 대상의 전체 목록을 참조 하세요.

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Visual Studio Code에서 Azure Machine Learning에 대 한 계산 대상 만들기

계산 대상을 만들려면 다음을 수행 합니다.

1. Visual Studio Code 활동 표시줄에서 Azure 아이콘을 선택 합니다. Azure: Machine Learning 사이드바가 나타납니다.

1. 트리 뷰에서 Azure 구독 및 Azure Machine Learning 작업 영역을 확장 합니다.

1. 작업 영역 노드에서 **컴퓨팅** 노드를 마우스 오른쪽 단추로 클릭하고 **컴퓨팅 만들기**를 선택합니다.

1. 목록에서 컴퓨팅 대상 유형을 선택합니다.

1. 명령 팔레트 프롬프트에서 가상 머신 크기를 선택 합니다. "Gpu"와 같은 텍스트를 사용 하 여 계산을 필터링 할 수 있습니다.

1. 명령 팔레트 프롬프트에서 계산 대상의 이름을 입력 합니다.

1. 이름을 입력 한 후에는 기본 매개 변수를 사용 하 여 계산이 생성 됩니다. 매개 변수를 변경 하려면 새 계산을 마우스 오른쪽 단추로 클릭 하 고 **계산 편집**을 선택 합니다.

1. 표시 되는 json에서 원하는 변경을 수행 하 고 "저장 후 계속" CodeLens를 클릭 합니다. 키보드를 사용 하면 **ctrl + shift + p** 를 눌러 명령 팔레트를 호출 하 고 **Azure ML을 실행할 수 있습니다. 저장 및 계속** 명령)

다음은 Azure Machine Learning 계산 (AMLCompute)을 만들고 편집 하는 방법의 예입니다.

[![Visual Studio Code에서 AML 계산 만들기](./media/vscode-tools-for-ai/create-remote-compute.gif)](./media/vscode-tools-for-ai/create-remote-compute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>실행 구성 파일

계산에서 Azure Machine Learning 실험을 실행 하려면 해당 계산을 적절 하 게 구성 해야 합니다. 실행 구성 파일은이 환경을 지정 하는 메커니즘입니다.

위에서 만든 AmlCompute에 대 한 실행 구성을 만드는 방법에 대 한 예제는 다음과 같습니다.

[![계산에 대 한 실행 구성 만들기](./media/vscode-tools-for-ai/create-runconfig.gif)](./media/vscode-tools-for-ai/create-runconfig.gif#lightbox)

로컬 컴퓨터에서 Azure ML 실험을 실행 하려면 여전히 실행 구성 파일이 필요 합니다. 로컬 실행 구성을 만들 때 사용 되는 Python 환경에는 VS Code 내에서 설정 된 인터프리터의 경로가 기본값으로 사용 됩니다.

### <a name="train-and-tune-models"></a>모델 학습 및 조정

VS Code에 대 한 Azure ML 확장을 사용 하 여 실험에서 학습 스크립트를 실행 하는 여러 가지 방법이 있습니다.

1. 학습 스크립트를 마우스 오른쪽 단추로 클릭 하 **고 Azure ML을 선택 합니다. Azure에서 실험으로 실행**
1. 실험 실행 도구 모음 아이콘을 클릭 합니다.
1. 실행 구성 노드를 마우스 오른쪽 단추로 클릭 합니다.
1. VS Code 명령 팔레트를 사용 하 여 **Azure ML을 실행 합니다. 실험 실행**

Azure Machine Learning 실험을 실행 하려면 다음을 수행 합니다.

1. Visual Studio Code 활동 표시줄에서 Azure 아이콘을 선택 합니다.

1. 트리 뷰에서 Azure 구독 및 Azure Machine Learning 작업 영역을 확장 합니다.

1. 작업 영역 노드에서 **실험** 노드를 확장 하 고 실행 하려는 실험을 마우스 오른쪽 단추로 클릭 합니다.

1. **실험 실행**을 선택합니다.

1. 모델을 학습 하기 위해 실행 하려는 Python 파일의 이름을 선택 하 고 enter 키를 눌러 실행을 제출 합니다. 참고: 선택한 파일은 VS Code에서 현재 열려 있는 폴더에 상주해 야 합니다.

1. 실행이 제출 되 면 선택한 실험 아래에 **실행 노드가** 표시 됩니다. 이 노드를 사용 하 여 실행 상태를 모니터링할 수 있습니다. 참고: 최신 상태를 확인 하려면 정기적으로 창을 새로 고쳐야 할 수도 있습니다.

이전에 만든 계산에서 실험을 실행 하는 방법의 예는 다음과 같습니다.

[![로컬로 실험 실행](./media/vscode-tools-for-ai/run-experiment.gif)](./media/vscode-tools-for-ai/run-experiment.gif#lightbox)

### <a name="deploy-and-manage-models"></a>모델 배포 및 관리
Azure Machine Learning에서는 클라우드와에 지에서 기계 학습 모델을 배포 하 고 관리할 수 있습니다.

#### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>모델을 등록 하 여 Azure Machine Learning Visual Studio Code

이제 모델을 학습 했으므로 작업 영역에 등록할 수 있습니다. 등록 된 모델을 추적 하 고 배포할 수 있습니다.

모델을 등록 하려면:

1. Visual Studio Code 활동 표시줄에서 Azure 아이콘을 선택 합니다. Azure: Machine Learning 사이드바가 나타납니다.

1. 트리 뷰에서 Azure 구독 및 Azure Machine Learning 작업 영역을 확장 합니다.

1. 작업 영역 노드에서 **모델**을 마우스 오른쪽 단추로 클릭하고 **모델 등록**을 선택합니다.

1. 명령 팔레트의 필드에 모델 이름을 입력 합니다.

1. 목록에서 **모델 파일** (단일 모델의 경우) 또는 **모델 폴더** (TensorFlow와 같은 여러 파일이 있는 모델의 경우)를 업로드할 지 여부를 선택 합니다.

1. 폴더 또는 파일을 선택합니다.

1. 모델 속성 구성을 마치면 창의 오른쪽 아래에 있는 **제출**을 선택 합니다.

Azure Machine Learning에 모델을 등록 하는 방법에 대 한 예제는 다음과 같습니다.

[![AML에 모델 등록](./media/vscode-tools-for-ai/register-model.gif)](./media/vscode-tools-for-ai/register-model.gif#lightbox)


#### <a name="deploy-your-service-from-visual-studio-code"></a>Visual Studio Code에서 서비스 배포

Visual Studio Code에서 웹 서비스를 배포할 수 있습니다.
+ 테스트를 위한 ACI (Azure Container Instances).
+ 프로덕션을 위한 AKS (Azure Kubernetes Service)

ACI 컨테이너는 필요에 따라 만들어지므로 미리 테스트할 ACI 컨테이너를 만들 필요가 없습니다. 그러나 AKS 클러스터를 미리 구성 해야 합니다. 자세한 내용은 [Azure Machine Learning를 사용 하 여 모델 배포](how-to-deploy-and-where.md)를 참조 하세요.

웹 서비스를 배포 하려면:

1. Visual Studio Code 활동 표시줄에서 Azure 아이콘을 선택 합니다. Azure: Machine Learning 사이드바가 나타납니다.

1. 트리 뷰에서 Azure 구독과 Azure Machine Learning 작업 영역을 확장 합니다.

1. 작업 영역 노드에서 **모델** 노드를 확장합니다.

1. 배포 하려는 모델을 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 **등록 된 모델에서 서비스 배포** 를 선택 합니다.

1. 명령 팔레트에서 배포 하려는 계산 대상을 선택 합니다.

1. 명령 팔레트의 필드에이 서비스의 이름을 입력 합니다.

1. 명령 팔레트에서 키보드의 Enter 키를 선택 하 여 스크립트 파일을 찾아 선택 합니다.

1. 명령 팔레트에서 키보드의 Enter 키를 선택 하 여 conda 종속성 파일을 찾아 선택 합니다.

1. 서비스 속성 구성을 마치면 창의 오른쪽 아래에 있는 배포에 **제출** 을 선택 합니다. 서비스 속성 파일에서 로컬 docker 파일 또는 schema. json 파일을 지정할 수 있습니다.

이제 웹 서비스가 배포됩니다.

웹 서비스를 배포 하는 방법의 예는 다음과 같습니다.

[![웹 서비스 배포](./media/vscode-tools-for-ai/create-image.gif)](./media/vscode-tools-for-ai/create-image.gif#lightbox)

### <a name="experiment-with-additional-features"></a>추가 기능 실험

명령 팔레트를 사용 하 여 Visual Studio Code의 많은 Azure Machine Learning 기능에 액세스할 수 있습니다. 명령 팔레트를 호출 하려면 Ctrl + Shift + P를 입력 합니다. 여기에서 확장의 추가 Azure ML 기능을 검색할 수 있습니다.

[![Visual Studio Code Azure Machine Learning에 대 한 바로 가기 키](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>다음 단계

* Visual Studio Code 외부 Azure Machine Learning를 학습 하는 방법에 대 한 연습은 [자습서: Azure Machine Learning을 사용하여 모델 학습](tutorial-train-models-with-aml.md)을 참조하세요.
* 코드를 로컬로 편집, 실행 및 디버그 하는 방법에 대 한 연습은 [Python hello-세계 자습서](https://code.visualstudio.com/docs/Python/Python-tutorial)를 참조 하세요.
