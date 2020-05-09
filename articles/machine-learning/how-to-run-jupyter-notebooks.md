---
title: 작업 영역에서 Jupyter 노트북을 실행 하는 방법
titleSuffix: Azure Machine Learning
description: Azure Machine Learning studio에서 작업 영역을 벗어나지 않고 Jupyter Notebook를 실행 하는 방법을 알아봅니다.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 67da2cb31d59838bb3ad2b964530d85d8be9be4c
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783657"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>작업 영역에서 Jupyter 노트북을 실행 하는 방법
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning studio의 작업 영역에서 직접 Jupyter 노트북을 실행 하는 방법을 알아봅니다. [Jupyter](https://jupyter.org/) 또는 [Jupyterlab](https://jupyterlab.readthedocs.io)을 시작할 수 있지만 작업 영역을 떠나지 않고도 노트북을 편집 하 고 실행할 수도 있습니다.

자세한 내용은 다음을 참조 하세요.

* 작업 영역에서 Jupyter 노트북 만들기
* 노트북에서 실험 실행
* 노트북 환경 변경
* 노트북을 실행 하는 데 사용 된 계산 인스턴스의 세부 정보 찾기

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://aka.ms/AMLFree)을 만듭니다.
* Machine Learning 작업 영역. [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

## <a name="create-notebooks"></a><a name="create"></a>노트북 만들기

Azure Machine Learning 작업 영역에서 새 Jupyter 노트북을 만들고 작업을 시작 합니다. 새로 만든 노트북은 기본 작업 영역 저장소에 저장 됩니다. 이 노트북은 작업 영역에 액세스할 수 있는 사용자와 공유할 수 있습니다. 

새 노트북을 만들려면 다음을 수행 합니다. 

1. [Azure Machine Learning studio](https://ml.azure.com)에서 작업 영역을 엽니다.
1. 왼쪽에서 **노트북**을 선택 합니다. 
1. **내 파일** 섹션에서 **사용자 파일** 목록 위의 **새 파일 만들기** 아이콘을 선택 합니다.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="새 파일 만들기":::

1. 파일의 이름을로 합니다. 
1. Jupyter Notebook 파일의 경우 파일 형식으로 **Python 노트북** 을 선택 합니다.
1. 파일 디렉터리를 선택 합니다.
1. **만들기**를 선택합니다.

> [!TIP]
> 텍스트 파일도 만들 수 있습니다.  파일 형식으로 **텍스트** 를 선택 하 고 이름에 확장명을 추가 합니다 (예: myfile.py 또는 myfile).  

노트북 페이지의 맨 위에 있는 도구를 사용 하 여 전자 필기장을 포함 한 폴더 및 파일을 업로드할 수도 있습니다.  노트북 및 대부분의 텍스트 파일 형식은 미리 보기 섹션에 표시 됩니다.  대부분의 다른 파일 형식에는 미리 보기를 사용할 수 없습니다.

### <a name="clone-samples"></a>샘플 복제

작업 영역에는 SDK를 탐색 하 고 사용자의 machine learning 프로젝트에 대 한 예제로 제공 되는 노트북이 포함 된 **Samples** 폴더가 있습니다.  작업 영역 저장소 컨테이너의 고유한 폴더로 이러한 노트북을 복제할 수 있습니다.  

예제는 [자습서: 첫 번째 ML 실험 만들기](tutorial-1st-experiment-sdk-setup.md#azure)를 참조 하세요.

### <a name="a-nameterminal-use-files-from-git-and-version-my-files"></a><a name="terminal">Git 및 버전 내 파일의 파일 사용

터미널 창을 사용 하 여 모든 Git 작업에 액세스할 수 있습니다. 모든 Git 파일 및 폴더는 작업 영역 파일 시스템에 저장 됩니다.

> [!NOTE]
> 모든 Jupyter 환경에서 표시 되도록 **~/cloudfiles/code/Users** 폴더의 어디에 나 파일 및 폴더를 추가 합니다.

터미널에 액세스 하려면:

1. [Azure Machine Learning studio](https://ml.azure.com)에서 작업 영역을 엽니다.
1. 왼쪽에서 **노트북**을 선택 합니다.
1. 왼쪽의 **사용자 파일** 섹션에 있는 모든 노트북을 선택 합니다.  노트북이 없으면 먼저 [노트북을 만듭니다](#create) .
1. 계산 대상을 선택 하거나 새 **계산** 대상을 만들고 실행 될 때까지 기다립니다.
1. **터미널 열기** 아이콘을 선택 합니다.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="터미널을 엽니다.":::

1. 아이콘이 표시 되지 않으면 계산 대상의 오른쪽에 **있는 ...** 를 선택한 다음, **터미널 열기** 를 선택 합니다.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="다음에서 터미널 열기 ...":::


[작업 영역 파일 시스템에 Git 리포지토리를 복제 하](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system)는 방법에 대해 자세히 알아보세요.


### <a name="share-notebooks-and-other-files"></a>전자 필기장 및 기타 파일 공유

URL을 복사 하 여 붙여넣어 서 노트북 또는 파일을 공유 합니다.  작업 영역의 다른 사용자만이 URL에 액세스할 수 있습니다.  [작업 영역에 대 한 액세스 권한을 부여 하](how-to-assign-roles.md)는 방법에 대해 자세히 알아보세요.

## <a name="edit-a-notebook"></a>노트북 편집

노트북을 편집 하려면 작업 영역의 **사용자 파일** 섹션에 있는 모든 노트북을 엽니다. 편집 하려는 셀을 클릭 합니다. 

실행 중인 계산 인스턴스가 실행 중일 때 모든 Python 노트북에서 [Intellisense](https://code.visualstudio.com/docs/editor/intellisense)에서 제공 하는 코드 완성 기능을 사용할 수도 있습니다.

노트북 도구 모음에서 Jupyter 또는 JupyterLab을 시작할 수도 있습니다.  Azure Machine Learning는 Microsoft 지원 경계 밖에 있는 오픈 소스 제품 이므로 업데이트를 제공 하 고 Jupyter 또는 JupyterLab의 버그를 수정 하지 않습니다.

### <a name="useful-keyboard-shortcuts"></a>유용한 바로 가기 키

|Keyboard  |작업  |
|---------|---------|
|Shift+Enter     |  셀 실행       |
|Ctrl + M (Windows)     |  노트북에서 탭 트래핑을 사용 하거나 사용 하지 않도록 설정 합니다.       |
|Ctrl + Shift + M (Mac & Linux)     |    노트북에서 탭 트래핑을 사용 하거나 사용 하지 않도록 설정 합니다.     |
|탭 (탭 트랩을 사용 하는 경우) | ' \T ' 문자 추가 (들여쓰기)
|탭 (탭 트래핑을 사용 하지 않는 경우) | 이 포커스를 받을 수 있는 항목 (셀 삭제 단추, 실행 단추 등)으로 포커스를 변경 합니다.

## <a name="delete-a-notebook"></a>Notebook 삭제

**샘플** 전자 필기장은 삭제할 *수 없습니다* .  이러한 노트북은 스튜디오에 포함 되어 있으며 새 SDK가 게시 될 때마다 업데이트 됩니다.  

다음 방법 중 하나를 사용 하 여 **사용자 파일** 노트북을 삭제할 *수* 있습니다.

* 스튜디오에서 폴더 또는 파일의 **끝에 있는 ...를 선택** 합니다.  지원 되는 브라우저 (Microsoft Edge, Chrome 또는 Firefox)를 사용 해야 합니다.
* 모든 노트북 도구 모음에서 [**터미널 열기**](#terminal) 를 선택 하 여 계산 인스턴스에 대 한 터미널 창에 액세스 합니다.
* Jupyter 또는 JupyterLab에서 해당 도구를 사용 합니다.

## <a name="run-an-experiment"></a>실험 실행

노트북에서 실험을 실행 하려면 먼저 실행 중인 [계산 인스턴스에](concept-compute-instance.md)연결 합니다. 계산 인스턴스가 없는 경우 다음 단계를 사용 하 여 하나를 만듭니다. 

1. 노트북 **+** 도구 모음에서를 선택 합니다. 
2. 계산 이름을로 하 고 **가상 머신 크기**를 선택 합니다. 
3. **만들기**를 선택합니다.
4. 계산 인스턴스는 전자 필기장에 자동으로 연결 되며 이제 셀을 실행할 수 있습니다.

사용자가 만든 계산 인스턴스만 보고 사용할 수 있습니다.  **사용자 파일** 은 VM과 별도로 저장 되 고 작업 영역의 모든 계산 인스턴스 간에 공유 됩니다.

### <a name="view-logs-and-output"></a>로그 및 출력 보기

[노트북 위젯을](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) 사용 하 여 실행 및 로그의 진행률을 볼 수 있습니다. 위젯은 비동기적 이며 학습을 완료할 때까지 업데이트를 제공 합니다. Azure Machine Learning 위젯은 Jupyter 및 Jupyter 에서도 지원 됩니다.

## <a name="change-the-notebook-environment"></a>노트북 환경 변경

노트북 도구 모음을 사용 하면 노트북이 실행 되는 환경을 변경할 수 있습니다.  

이러한 작업은 노트북 상태 또는 노트북에 있는 변수의 값을 변경 하지 않습니다.

|작업  |결과  |
|---------|---------| --------|
|커널을 중지 합니다.     |  실행 중인 셀을 모두 중지 합니다. 셀을 실행 하면 커널이 자동으로 다시 시작 됩니다. |
|다른 작업 영역 섹션으로 이동     |     실행 중인 셀은 중지 됩니다. |

이러한 작업은 노트북 상태를 다시 설정 하 고 노트북의 모든 변수를 다시 설정 합니다.

|작업  |결과  |
|---------|---------| --------|
| 커널 변경 | 노트북에서 새 커널 사용 |
| 계산 전환    |     노트북은 새 계산을 자동으로 사용 합니다. |
| 계산 다시 설정 | 셀을 실행 하려고 할 때 다시 시작 합니다. |
| 계산 중지     |    실행할 셀이 없습니다.  |
| Jupyter 또는 JupyterLab에서 노트북을 엽니다.     |    새 탭에서 노트북을 열었습니다.  |

### <a name="add-new-kernels"></a>새 커널 추가

노트북은 연결 된 계산 인스턴스에 설치 된 모든 Jupyter 커널을 자동으로 찾습니다.  커널을 계산 인스턴스에 추가 하려면 다음을 수행 합니다.

1. 노트북 도구 모음에서 [**터미널 열기**](#terminal) 를 선택 합니다.
1. 터미널 창을 사용 하 여 새 환경을 만들 수 있습니다.
1. 환경을 활성화합니다.  예를 들어 다음을 `newenv`만든 후:

    ```shell
    source activate newenv
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

[사용 가능한 Jupyter 커널을](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) 설치할 수 있습니다.

### <a name="status-indicators"></a>상태 표시기

**계산** 드롭다운 옆에 있는 표시기는 해당 상태를 표시 합니다.  상태는 드롭다운 자체에도 표시 됩니다.  

|색상 |상태 계산 |
|---------|---------| 
| 녹색 | 계산 실행 중 |
| 빨강 |계산 실패 | 
| 검정 | 계산 중지 됨 |
|  연한 파랑 |계산 만들기, 시작, 다시 시작, 설정 |
|  회색 |계산 삭제, 중지 |

**커널** 드롭다운 옆에 있는 표시기는 해당 상태를 표시 합니다.

|색상 |커널 상태 |
|---------|---------|
|  녹색 |커널 연결 됨, 유휴 상태, 사용 중|
|  회색 |커널이 연결 되지 않음 |

## <a name="find-compute-details"></a>계산 세부 정보 찾기 

[스튜디오](https://ml.azure.com)의 **계산** 페이지에서 계산 인스턴스에 대 한 세부 정보를 확인 합니다.

## <a name="next-steps"></a>다음 단계

* [첫 번째 실험 실행](tutorial-1st-experiment-sdk-train.md)
* [스냅숏을 사용 하 여 파일 저장소 백업](../storage/files/storage-snapshots-files.md)
