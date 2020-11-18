---
title: '자습서: Jupyter Notebook에서 시작(Python)'
titleSuffix: Azure Machine Learning
description: Jupyter Notebook을 설정하는 방법에 대한 자습서입니다. 작업 영역을 만들고, Notebooks를 작업 영역에 복제하고, Notebooks를 실행하는 컴퓨팅 인스턴스를 만듭니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: devx-track-python
ms.openlocfilehash: 6eb76c9d8cc8447755e045972a7aceddefd1604a
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94532973"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-jupyter-notebooks"></a>자습서: Jupyter Notebook에서 Azure Machine Learning 시작

이 자습서에서는 [관리형 클라우드 기반 워크스테이션(컴퓨팅 인스턴스)](concept-compute-instance.md)에서 Jupyter Notebook을 사용하여 Azure Machine Learning을 시작하는 단계를 완료합니다. 이 자습서는 다른 모든 Jupyter Notebook 자습서에 대한 기반입니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 다른 Jupyter Notebook 자습서에서 사용할 [Azure Machine Learning 작업 영역](concept-workspace.md)을 만듭니다.
> * 자습서 Notebook을 작업 영역의 사용자 폴더로 복제합니다.
> * Azure Machine Learning Python SDK가 설치되고 미리 구성된 클라우드 기반 컴퓨팅 인스턴스를 만듭니다.

Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

## <a name="create-a-workspace"></a>작업 영역 만들기

Azure Machine Learning 작업 영역은 기계 학습 모델을 실험하고, 학습시키고, 배포하는 데 사용하는 클라우드의 기본 리소스입니다. Azure 구독 및 리소스 그룹을 서비스에서 사용하기 쉬운 개체에 연결합니다.

Azure Machine Learning 작업 영역이 이미 있으면 [Notebook 폴더 복제](#clone)로 건너뜁니다.  

[작업 영역을 만드는 방법](how-to-manage-workspace.md)에는 여러 가지가 있습니다.  이 자습서에서는 Azure 리소스를 관리하기 위한 웹 기반 콘솔인 Azure Portal을 통해 작업 영역을 만듭니다.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT]
> *작업 영역* 및 *구독* 을 적어 둡니다. 올바른 장소에서 실험을 만들려면 이 정보가 필요합니다.

## <a name="run-a-notebook-in-your-workspace"></a><a name="azure"></a>작업 영역에서 Notebook 실행

Azure Machine Learning은 설치할 필요가 없는 미리 구성된 환경을 위해 클라우드 Notebook 서버를 작업 영역에 포함시킵니다. 환경, 패키지 및 종속성을 제어하려면 [사용자 고유의 환경](tutorial-1st-experiment-sdk-setup-local.md)을 사용합니다.

 다음 비디오를 따르거나 자세한 단계를 사용하여 작업 영역에서 자습서 Notebook을 복제하고 실행합니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

### <a name="clone-a-notebook-folder"></a><a name="clone"></a> Notebook 폴더 복제

Azure Machine Learning 스튜디오에서 다음 실험 설정 및 실행 단계를 완료합니다. 이 통합 인터페이스에는 모든 기술 수준의 데이터 과학 전문가를 위한 데이터 과학 시나리오를 수행하는 기계 학습 도구가 포함되어 있습니다.

1. [Azure Machine Learning Studio](https://ml.azure.com/)에 로그인합니다.

1. 해당 구독과 직접 만든 작업 영역을 선택합니다.

1. 왼쪽에서 **Notebook** 을 선택합니다.

1. 위쪽에서 **샘플** 탭을 선택합니다.

1. **Python** 폴더를 엽니다.

1. 버전 번호가 있는 폴더를 엽니다. 이 번호는 Python SDK의 현재 릴리스를 나타냅니다.

1. **tutorials** 폴더의 오른쪽에 있는 **...** 단추를 선택한 다음, **복제** 를 선택합니다.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="tutorials 폴더 복제를 보여 주는 스크린샷":::

1. 작업 영역에 액세스하는 각 사용자가 폴더 목록에 표시됩니다. **tutorials** 폴더를 복제할 폴더를 선택합니다.

### <a name="open-the-cloned-notebook"></a><a name="open"></a>복제된 Notebook 열기

1. **사용자 파일** 섹션에서 닫혀 있던 **tutorials** 폴더를 엽니다.

    > [!IMPORTANT]
    > **samples** 폴더에서는 Notebook을 볼 수 있지만 실행할 수는 없습니다. Notebook을 실행하려면 **사용자 파일** 섹션에서 복제된 버전의 Notebook을 열어야 합니다.
    
1. **tutorials/image-classification-mnist-data** 폴더에서 **tutorial-1st-experiment-sdk-train.ipynb** 파일을 선택합니다.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="tutorials 폴더 열기를 보여 주는 스크린샷":::

1. 위쪽 바에서 Notebook을 실행하는 데 사용할 컴퓨팅 인스턴스를 선택합니다. 이러한 VM(가상 머신)은 [Azure Machine Learning을 실행하는 데 필요한 모든 항목](concept-compute-instance.md#contents)을 사용하여 미리 구성됩니다.

1. VM을 찾을 수 없는 경우 **+ 추가** 를 선택하여 컴퓨팅 인스턴스 VM을 만듭니다.

    1. VM을 만들 경우 다음 규칙을 따릅니다.
 
        + 이름은 필수이며, 필드를 비워 둘 수 없습니다.
        + 이름은 작업 영역 또는 컴퓨팅 인스턴스의 Azure 지역에 있는 모든 기존 컴퓨팅 인스턴스에서 고유해야 합니다(대/소문자 구분 안 함). 선택한 이름이 고유하지 않으면 경고를 받게 됩니다.
        + 유효한 문자는 대문자, 소문자, 숫자(0~9) 및 대시(-) 문자입니다.
        + 이름은 3~24자여야 합니다.
        + 이름은 숫자 또는 대시 문자가 아닌 문자로 시작해야 합니다.
        + 대시 문자를 사용하는 경우 대시 뒤에 하나 이상의 문자가 있어야 합니다. 예를 들어 Test-, test-0, test-01은 유효하지 않지만 test-a0, test-0a는 유효한 인스턴스입니다.

    1. 사용 가능한 선택 항목에서 VM 크기를 선택합니다. 이 자습서에서는 기본 VM을 선택하는 것이 좋습니다.

    1. 그런 다음 **만들기** 를 선택합니다. VM을 설정하는 데 약 5분 정도 걸릴 수 있습니다.

1. VM을 사용할 수 있는 경우 해당 VM이 위쪽 도구 모음에 표시됩니다. 이제 도구 모음에서 **모두 실행** 을 사용하거나 Notebook의 코드 셀에서 **Shift+Enter** 를 사용하여 Notebook을 실행할 수 있습니다.

사용자 지정 위젯이 있거나 Jupyter 또는 JupyterLab을 사용하려는 경우 맨 오른쪽에 있는 **Jupyter** 드롭다운 목록을 선택합니다. 그런 다음, **Jupyter** 또는 **JupyterLab** 을 선택합니다. 새 브라우저 창이 열립니다.

## <a name="next-steps"></a>다음 단계

이제 개발 환경이 설정되었으므로 Jupyter Notebook의 모델 학습으로 계속 진행합니다.

> [!div class="nextstepaction"]
> [자습서: MNIST 데이터와 scikit-learn을 사용하여 이미지 분류 모델 학습](tutorial-train-models-with-aml.md)

<a name="stop-compute-instance"></a> 지금 다른 자습서를 수행하지 않으려면 클라우드 Notebook 서버 VM을 사용하지 않을 때 중지하여 비용을 줄입니다.

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]
