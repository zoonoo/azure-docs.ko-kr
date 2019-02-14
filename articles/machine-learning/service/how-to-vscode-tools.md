---
title: Visual Studio Code 사용
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
ms.openlocfilehash: e6da35eeb98d4017baae6aa2c8e19fa3f9d5ac1b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55752571"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Visual Studio Code용 Azure Machine Learning 시작

이 문서에서는 **Visual Studio Code용 Azure Machine Learning** 확장을 설치하고 VS Code(Visual Studio Code)에서 Azure Machine Learning Service를 사용하여 첫 번째 실험을 만드는 방법을 알아봅니다.

Visual Studio Code의 Azure Machine Learning 확장을 사용하면 Azure Machine Learning Service를 통해 데이터를 준비하고, 로컬 및 원격 계산 대상에서 기계 학습 모델의 학습과 테스트를 수행하고, 해당 모델을 배포하고, 사용자 지정 메트릭과 실험을 추적할 수 있습니다.

## <a name="prerequisites"></a>필수 조건


+ Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](http://aka.ms/AMLFree)을 지금 사용해 보세요.

+ Visual Studio Code를 설치해야 합니다. 데스크톱에서 실행되는 간단하지만 효율적인 소스 코드 편집기인 VS Code는 Python 등을 기본적으로 지원합니다.  [VS Code 설치 방법에 대해 알아보세요](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Python 3.5 이상을 설치합니다](https://www.anaconda.com/download/).


## <a name="install-the-azure-machine-learning-for-vs-code-extension"></a>VS Code용 Azure Machine Learning 확장 설치

**Azure Machine Learning** 확장을 설치할 때 인터넷에 액세스할 수 있으면 두 개의 확장이 추가로 자동 설치됩니다. 이 두 확장은 [Azure 계정](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) 확장 및 [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) 확장입니다.

Azure Machine Learning을 사용하려면 VS Code를 Python IDE로 전환해야 합니다. [Visual Studio Code에서 Python](https://code.visualstudio.com/docs/languages/python)을 사용하려면 Azure Machine Learning 확장과 함께 자동으로 설치되는 Microsoft Python 확장이 필요합니다. VS Code를 효율적인 IDE로 전환하는 이 확장은 다양한 Python 인터프리터가 포함된 모든 운영 체제에서 작동합니다. 이 확장은 VS Code의 모든 기능을 활용하여 자동 완성과 IntelliSense, Lint, 디버깅, 단위 테스트 기능은 물론 가상 환경과 conda 환경을 비롯한 Python 환경 간을 손쉽게 전환하는 기능도 제공합니다. Python 코드 편집/실행/디버깅 연습을 제공하는 [Python Hello World 자습서](https://code.visualstudio.com/docs/python/python-tutorial)를 참조하세요.

**Azure Machine Learning 확장을 설치하려면**

1. VS Code를 시작합니다.

1. 브라우저에서 [Visual Studio Code용 Azure Machine Learning(미리 보기)](https://aka.ms/vscodetoolsforai) 확장으로 이동합니다.

1. 웹 페이지에서 **설치**를 클릭합니다. 

1. 확장 탭에서 **설치**를 클릭합니다.

1. VS Code에서 확장의 시작 탭이 열리고 작업 막대에 Azure 기호가 추가됩니다.

   ![Visual Studio Code 작업 막대의 Azure 아이콘](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. 대화 상자에서 **로그인**을 클릭하고 화면의 메시지에 따라 Azure에 인증합니다. 
   
   VS Code용 Azure Machine Learning 확장과 함께 설치되는 Azure 계정 확장을 통해 Azure 계정에 인증할 수 있습니다. 명령 목록은 [Azure 계정 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) 페이지를 참조하세요.

> [!Tip] 
> [VS Code용 IntelliCode 확장(미리 보기)](https://go.microsoft.com/fwlink/?linkid=2006060)을 확인해 보세요. IntelliCode는 현재 코드 컨텍스트를 기준으로 하여 가장 관련성이 높은 자동 완성을 유추하는 기능과 같은 Python의 IntelliSense용 AI 지원 기능 집합을 제공합니다.

## <a name="azure-ml-sdk-installation"></a>Azure ML SDK 설치

1. Python 3.5 이상이 설치되어 있으며 VS Code에서 인식되는지 확인합니다. 지금 Python을 설치하는 경우 VS Code를 다시 시작한 후에 https://code.visualstudio.com/docs/python/python-tutorial의 지침에 따라 Python 인터프리터를 선택합니다.

1. 통합 터미널 창에서 사용할 Python 인터프리터를 지정합니다. **Enter** 키를 눌러 기본 Python 인터프리터를 사용할 수도 있습니다.

   ![인터프리터 선택](./media/vscode-tools-for-ai/python.png)

1. 창의 오른쪽 아래 모서리에 Azure ML SDK가 자동으로 설치된다는 알림이 표시됩니다.    Azure Machine Learning 사용을 위한 Visual Studio Code 필수 구성 요소가 포함된 로컬 개인 Python 환경이 생성됩니다.

   ![Python용 Azure Machine Learning SDK 설치](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Azure Machine Learning 시작

VS Code를 사용하여 기계 학습 모델 학습 및 배포를 시작하려면 클라우드에서 모델과 리소스를 포함할 [Azure Machine Learning Service 작업 영역](concept-azure-machine-learning-architecture.md#workspace)을 만들어야 합니다. 작업 영역을 만들고 이 작업 영역에 첫 번째 실험을 만드는 방법은 다음과 같습니다.

1. Visual Studio Code 작업 막대에서 Azure 아이콘을 클릭합니다. Azure: Machine Learning 사이드바가 나타납니다.

   [![설치](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Azure 구독을 마우스 오른쪽 단추로 클릭하고 **작업 영역 만들기**를 선택합니다. 목록이 표시됩니다. 애니메이션 이미지에서 구독 이름은 ‘평가판’이고 작업 영역은 ‘TeamWorkspace’입니다. 

1. 목록에서 기존 리소스 그룹을 선택하거나 명령 팔레트에서 마법사를 사용하여 새 그룹을 만듭니다.

1. 필드에 새 작업 영역의 고유하고 명확한 이름을 입력합니다. 스크린샷에서 작업 영역 이름은 ‘TeamWorkspace’입니다.

1. Enter 키를 누르면 새 작업 영역이 생성됩니다. 이 작업 영역은 트리에서 구독 이름 아래에 표시됩니다.

1. 실험 노드를 마우스 오른쪽 단추로 클릭하고 컨텍스트 메뉴에서 **실험 만들기**를 선택합니다.  실험은 Azure Machine Learning을 사용하는 실행을 추적합니다.

1. 필드에 실험 이름을 입력합니다. 스크린샷에서 실험 이름은 ‘MNIST’입니다.
 
1. Enter 키를 누르면 새 실험이 생성됩니다. 이 실험은 트리에서 작업 영역 이름 아래에 표시됩니다.

1. 작업 영역에서 실험을 마우스 오른쪽 단추로 클릭하고 '활성 실험으로 설정'을 선택할 수 있습니다. **'활성'** 실험은 현재 사용 중인 실험이며, VS Code의 열린 폴더가 클라우드의 이 실험에 연결됩니다. 이 폴더에는 로컬 Python 스크립트가 포함됩니다.

   이제 각 실험이 실행되어 모든 주요 메트릭이 실험 기록에 저장되며, 학습시키는 모델이 Azure Machine Learning에 자동으로 업로드되어 실험 메트릭 및 로그와 함께 저장됩니다.

   [![VS Code에서 폴더 연결](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>바로 가기 키 사용

대다수 VS Code 기능과 마찬가지로 VS Code의 Azure Machine Learning 기능도 키보드에서 액세스할 수 있습니다. 기억해야 할 가장 중요한 키 조합은 명령 팔레트를 표시하는 Ctrl+Shift+P입니다. 명령 팔레트에서는 가장 흔히 수행하는 작업의 바로 가기 키를 포함하여 VS Code의 모든 기능에 액세스할 수 있습니다.

[![VS Code용 Azure Machine Learning의 바로 가기 키](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>다음 단계

이제 Visual Studio Code를 통해 Azure Machine Learning을 사용할 수 있습니다.

[Visual Studio Code에서 계산 대상 만들기 및 모델 학습/배포](how-to-vscode-train-deploy.md)를 수행하는 방법을 알아보세요.
