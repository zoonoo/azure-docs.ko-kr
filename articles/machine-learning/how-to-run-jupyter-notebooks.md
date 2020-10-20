---
title: 작업 영역에서 Jupyter Notebooks를 실행하는 방법
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 스튜디오에서 작업 영역을 벗어나지 않고 Jupyter Notebook을 실행하는 방법을 알아봅니다.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 06/27/2020
ms.openlocfilehash: 4373618bacad00675d5f639225c435296010949b
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221425"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>작업 영역에서 Jupyter Notebooks를 실행하는 방법


Azure Machine Learning 스튜디오의 작업 영역에서 Jupyter Notebook을 직접 실행하는 방법을 알아봅니다. [Jupyter](https://jupyter.org/) 또는 [JupyterLab](https://jupyterlab.readthedocs.io)을 시작할 수 있으며 작업 영역을 벗어나지 않고 Notebook을 편집 및 실행할 수도 있습니다.

자세한 내용은 다음을 참조하세요.

* 작업 영역에서 Jupyter Notebooks 만들기
* Notebook에서 실험 실행
* Notebook 환경 변경
* Notebook을 실행하는 데 사용된 컴퓨팅 인스턴스의 세부 정보 찾기

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://aka.ms/AMLFree)을 만듭니다.
* Machine Learning 작업 영역. [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

## <a name="create-notebooks"></a><a name="create"></a> Notebook 만들기

Azure Machine Learning 작업 영역에서 새 Jupyter Notebook을 만들고 작업을 시작합니다. 새로 만든 Notebook은 기본 작업 영역 스토리지에 저장됩니다. 이 Notebook은 작업 영역에 대한 액세스 권한이 있는 사용자와 공유할 수 있습니다. 

새 Notebook을 만들려면: 

1. [Azure Machine Learning 스튜디오](https://ml.azure.com)에서 작업 영역 열기
1. 왼쪽에서 **Notebook**을 선택합니다. 
1. **내 파일** 섹션에서 목록 **사용자 파일** 위에 있는 **새 파일 만들기** 아이콘을 선택합니다.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="새 파일을 만듭니다.":::

1. 파일 이름을 지정합니다. 
1. Jupyter Notebook 파일의 경우 파일 형식으로 **노트북** 을 선택 합니다.
1. 파일 디렉터리를 선택합니다.
1. **만들기**를 선택합니다.

텍스트 파일도 만들 수 있습니다.  파일 형식으로 **텍스트**를 선택하고 이름에 확장명을 추가합니다(예: myfile.py 또는 myfile.txt).  

Notebook 페이지의 맨 위에 있는 도구를 사용하여 Notebook을 포함하는 폴더 및 파일을 업로드할 수도 있습니다.  Notebook 및 대부분의 텍스트 파일 형식은 미리 보기 섹션에 표시됩니다.  대부분의 다른 파일 형식에는 미리 보기를 사용할 수 없습니다.

> [!IMPORTANT]
> 노트북 및 스크립트의 콘텐츠는 잠재적으로 세션에서 데이터를 읽고 Azure에서 조직 없이 데이터에 액세스할 수 있습니다.  신뢰할 수 있는 원본 에서만 파일을 로드 합니다. 자세한 내용은 [보안 코드 모범 사례](concept-secure-code-best-practice.md#azure-ml-studio-notebooks)를 참조 하세요.

### <a name="clone-samples"></a>샘플 복제

작업 영역에는 SDK를 탐색하고 사용자의 기계 학습 프로젝트에 대한 예로 제공하도록 설계된 Notebook을 포함하는 **샘플** 폴더가 있습니다.  작업 영역 스토리지 컨테이너의 고유한 폴더에 Notebook을 복제할 수 있습니다.  

예제를 보려면 [ 자습서: 첫 번째 ML 실험 만들기](tutorial-1st-experiment-sdk-setup.md#azure)를 참조하세요.

### <a name="use-files-from-git-and-version-my-files"></a><a name="terminal"></a> Git의 파일 및 내 파일 버전 사용

터미널 창을 사용하여 모든 Git 작업에 액세스할 수 있습니다. 모든 Git 파일 및 폴더는 작업 영역 파일 시스템에 저장됩니다.

> [!NOTE]
> 모든 Jupyter 환경에서 볼 수 있도록 **~/cloudfiles/code/Users** 폴더의 어디에나 파일 및 폴더를 추가합니다.

터미널에 액세스하려면 다음을 수행합니다.

1. [Azure Machine Learning 스튜디오](https://ml.azure.com)에서 작업 영역 열기
1. 왼쪽에서 **Notebook**을 선택합니다.
1. 왼쪽의 **사용자 파일** 섹션에 있는 모든 Notebook을 선택합니다.  여기에 Notebook이 없으면 먼저 [Notebook을 만듭니다.](#create)
1. 계산 대상을 선택 하거나 새 **계산** 대상을 만들고 실행 될 때까지 기다립니다.
1. **터미널 열기** 아이콘을 선택합니다.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="새 파일을 만듭니다.":::

1. 아이콘이 표시 되지 않으면 계산 대상의 오른쪽에 **있는 ...** 를 선택한 다음, **터미널 열기**를 선택 합니다.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="새 파일을 만듭니다.":::


[작업 영역 파일 시스템에 Git 리포지토리를 복제하는 방법](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system)에 대해 자세히 알아보세요.

### <a name="copy-and-paste-in-terminal"></a>터미널에서 복사 및 붙여넣기

> * Windows: `Ctrl-Insert` 복사 하 여 사용 `Ctrl-Shift-v` 하거나 `Shift-Insert` 붙여 넣을 수 있습니다.
> * Mac OS: `Cmd-c`: 복사, `Cmd-v`: 붙여넣기
> * FireFox/IE에서 클립보드 사용 권한을 제대로 지원하지 않을 수 있습니다.

### <a name="share-notebooks-and-other-files"></a>Notebook 및 기타 파일 공유

URL을 복사하고 붙여넣어 Notebook 또는 파일을 공유합니다.  작업 영역의 다른 사용자만이 URL에 액세스할 수 있습니다.  [작업 영역에 대한 액세스 권한 부여](how-to-assign-roles.md)에 대해 자세히 알아봅니다.

## <a name="edit-a-notebook"></a>Notebook 편집

Notebook을 편집하려면 작업 영역의 **사용자 파일** 섹션에 있는 모든 Notebook을 엽니다. 편집하려는 셀을 클릭합니다. 

계산 인스턴스에 연결 하지 않고 노트북을 편집할 수 있습니다.  노트북에서 셀을 실행 하려는 경우 계산 인스턴스를 선택 하거나 만듭니다.  중지 된 계산 인스턴스를 선택 하면 첫 번째 셀을 실행할 때 자동으로 시작 됩니다.

계산 인스턴스가 실행 되는 경우 모든 Python 노트북에서 [Intellisense](https://code.visualstudio.com/docs/editor/intellisense)에서 제공 하는 코드 완성 기능을 사용할 수도 있습니다.

Notebook 도구 모음에서 Jupyter 또는 JupyterLab을 시작할 수도 있습니다.  Azure Machine Learning은 Microsoft 지원이 안 되는 오픈 소스 제품이므로 Jupyter 또는 JupyterLab에서 업데이트를 제공하거나 버그를 수정하지 않습니다.

### <a name="focus-mode"></a>포커스 모드

활성 탭에 집중할 수 있도록 포커스 모드를 사용 하 여 현재 보기를 확장 합니다. 포커스 모드는 전자 필기장 파일 탐색기를 숨깁니다.

1. 터미널 창 도구 모음 **에서 포커스 모드를 선택**   하 여 포커스 모드를 설정 합니다. 창 너비에 따라 도구 모음의 **...** 메뉴 항목 아래에 있을 수 있습니다.
1. 포커스 모드에서 **표준 뷰**를 선택 하 여 표준 뷰로 돌아갑니다.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="새 파일을 만듭니다.":::


### <a name="use-intellisense"></a>IntelliSense 사용

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) 는 멤버 목록, 매개 변수 정보, 요약 정보 및 단어 자동 완성 등 다양 한 기능을 포함 하는 코드 완성 지원입니다. 이러한 기능을 사용하면 사용 중인 코드에 대한 자세한 정보를 보고, 입력하는 매개 변수를 계속 추적하고, 단 몇 번의 키 입력만으로 속성과 메서드에 대한 호출을 추가할 수 있습니다.  

코드를 입력할 때 Ctrl + Space를 사용 하 여 IntelliSense를 트리거합니다.

### <a name="clean-your-notebook-preview"></a>노트북 청소 (미리 보기)

> [!IMPORTANT]
> 수집 기능은 현재 공개 미리 보기로 제공 됩니다.
> 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

노트북을 만드는 과정에서 일반적으로 데이터 탐색 또는 디버깅에 사용한 셀이 표시 됩니다. *수집* 기능은 이러한 불필요 한 셀 없이 깨끗 한 노트북을 생성 하는 데 도움이 됩니다.

1. 모든 노트북 셀을 실행 합니다.
1. 새 전자 필기장을 실행 하려는 코드가 포함 된 셀을 선택 합니다. 예를 들어 실험을 제출 하는 코드 또는 모델을 등록 하는 코드를 예로 들 수도 있습니다.
1. 셀 도구 모음에 표시 되는 **수집** 아이콘을 선택 합니다.
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="새 파일을 만듭니다." 노트북의 이름을 입력 합니다.  

새 노트북에는 코드 셀만 포함 되어 있으며, 모든 셀은 수집 하도록 선택한 셀과 동일한 결과를 생성 하는 데 필요 합니다.

### <a name="save-and-checkpoint-a-notebook"></a>노트북 저장 및 검사점

Azure Machine Learning는 *ipynb*파일을 만들 때 검사점 파일을 만듭니다   .

노트북 도구 모음에서 메뉴를 선택 하 고 **파일 &gt; 저장 및 검사점** 을 선택 하 여 노트북을 수동으로 저장 하 고 노트북에 연결 된 검사점 파일을 추가 합니다.

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="새 파일을 만듭니다.":::

모든 노트북은 30 초 마다 자동으로 저장 됩니다.자동 저장은 검사점 파일이 아닌 초기 *ip이상 b*   파일만 업데이트 합니다.
 
노트북 메뉴에서 **검사점** 을 선택 하 여 명명 된 검사점을 만들고 노트북을 저장 된 검사점으로 되돌립니다.


### <a name="useful-keyboard-shortcuts"></a>유용한 바로 가기 키

|키보드  |작업  |
|---------|---------|
|Shift+Enter     |  셀 실행       |
|Ctrl+스페이스바 | IntelliSense 활성화 |
|Ctrl+M(Windows)     |  Notebook에서 탭 트래핑 사용/사용 안 함       |
|Ctrl+Shift+M(Mac & Linux)     |    Notebook에서 탭 트래핑 사용/사용 안 함     |
|Tab(탭 트래핑을 사용할 경우) | '\t' 문자 추가(들여쓰기)
|Tab(탭 트래핑을 사용하지 않을 경우) | 다음 포커스 가능 항목(셀 삭제 단추, 실행 단추 등)으로 포커스를 변경합니다.

## <a name="delete-a-notebook"></a>Notebook을 삭제합니다.

**샘플** Notebook을 삭제*할 수 없습니다*.  이러한 Notebook은 스튜디오에 포함되어 있으며 새 SDK가 게시될 때마다 업데이트됩니다.  

다음과 같은 방법으로 **사용자 파일** Notebook을 삭제*할 수 있습니다*.

* 스튜디오에서 폴더 또는 파일의 끝에 있는 **...** 를 선택합니다.  지원되는 브라우저(Microsoft Edge, Chrome 또는 Firefox)를 사용해야 합니다.
* 모든 Notebook 도구 모음에서 [**터미널 열기**](#terminal)를 선택하여 컴퓨팅 인스턴스에 대한 터미널 창에 액세스합니다.
* Jupyter 또는 JupyterLab에서 해당 도구를 사용합니다.

## <a name="run-an-experiment"></a>실험을 실행합니다.

Notebook에서 실험을 실행하려면 먼저 실행 중인 [컴퓨팅 인스턴스](concept-compute-instance.md)에 연결합니다. 컴퓨팅 인스턴스가 없는 경우 다음 단계를 사용하여 만듭니다. 

1. Notebook 도구 모음에서 **+** 를 선택합니다. 
2. 컴퓨팅 이름을 지정하고 **Virtual Machine 크기**를 선택합니다. 
3. **만들기**를 선택합니다.
4. 컴퓨팅 인스턴스는 Notebook에 자동으로 연결되고 이제 셀을 실행할 수 있습니다.

사용자가 만든 컴퓨팅 인스턴스만 보고 사용할 수 있습니다.  **사용자 파일**은 VM과 별도로 저장되고 작업 영역의 모든 컴퓨팅 인스턴스 간에 공유됩니다.

### <a name="view-logs-and-output"></a>로그 및 출력 보기

[Notebook 위젯](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py&preserve-view=true)을 사용하여 실행 및 로그의 진행률을 볼 수 있습니다. 위젯은 비동기적이며 학습을 마칠 때까지 업데이트를 제공합니다. Azure Machine Learning 위젯은 Jupyter 및 JupyterLab에서도 지원됩니다.

## <a name="change-the-notebook-environment"></a>Notebook 환경 변경

Notebook 도구 모음을 사용하면 Notebook이 실행되는 환경을 변경할 수 있습니다.  

다음 작업은 Notebook 상태 또는 Notebook에 있는 변수 값을 변경하지 않습니다.

|작업  |결과  |
|---------|---------| --------|
|커널 중지     |  실행 중인 셀을 모두 중지합니다. 셀을 실행하면 커널이 자동으로 다시 시작됩니다. |
|다른 작업 영역 섹션으로 이동     |     실행 중인 셀이 중지됩니다. |

다음 작업은 Notebook 상태를 다시 설정하고 Notebook의 모든 변수를 다시 설정합니다.

|작업  |결과  |
|---------|---------| --------|
| 커널 변경 | Notebook에서 새 커널 사용 |
| 컴퓨팅 전환    |     Notebook은 새 컴퓨팅을 자동으로 사용합니다. |
| 컴퓨팅 다시 설정 | 셀을 실행하려고 할 때 다시 시작합니다. |
| 컴퓨팅 중지     |    실행할 셀 없음  |
| Jupyter 또는 JupyterLab에서 Notebook 열기     |    새 탭에서 Notebook을 열었습니다.  |

### <a name="add-new-kernels"></a>새 커널 추가

Notebook은 연결된 컴퓨팅 인스턴스에 설치된 모든 Jupyter 커널을 자동으로 찾습니다.  커널을 컴퓨팅 인스턴스에 추가하려면 다음을 수행합니다.

1. Notebook 도구 모음에서 [**터미널 열기**](#terminal)를 선택합니다.
1. 터미널 창을 사용하여 새 환경을 만듭니다.  예를 들어 아래 코드는를 만듭니다 `newenv` .
    ```shell
    conda create --name newenv
    ```
1. 환경을 활성화합니다.  예를 들어 `newenv`를 만든 후에는 다음을 수행합니다.

    ```shell
    conda activate newenv
    ```
1. 새 환경에 pip 및 ipykernel 패키지를 설치 하 고 해당 conda env의 커널 만들기

    ```shell
    conda install pip
    conda install ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

> [!NOTE]
> 노트북 내에서 패키지를 관리 하려면 **% pip** 또는 **% conda** magic 함수를 사용 하 여 현재 실행 중인 **커널**에 패키지를 자동으로 설치 합니다 .이는 모든 패키지를 참조 하는 **! pip** 또는 **! conda** 이 아닌 현재 실행 중인 커널 외부의 패키지를 포함 합니다.

[사용할 수 있는 Jupyter 커널](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels)을 설치할 수 있습니다.

### <a name="status-indicators"></a>상태 표시기

**컴퓨팅** 드롭다운 옆의 표시기에 해당 상태가 표시됩니다.  상태는 드롭다운 자체에도 표시됩니다.  

|색 |컴퓨팅 상태 |
|---------|---------| 
| 녹색 | 컴퓨팅 실행 중 |
| 빨강 |컴퓨팅 실패 | 
| 검정 | 컴퓨팅 중지됨 |
|  연한 파란색 |컴퓨팅 만들기, 시작, 다시 시작, 설정 |
|  회색 |컴퓨팅 삭제, 중지 |

**커널** 드롭다운 옆의 표시기에 해당 상태가 표시됩니다.

|색 |커널 상태 |
|---------|---------|
|  녹색 |커널이 연결됨, 유휴 상태, 사용 중|
|  회색 |커널이 연결되지 않음 |

## <a name="find-compute-details"></a>컴퓨팅 세부 정보 찾기 

[스튜디오](https://ml.azure.com)의 **컴퓨팅** 페이지에서 컴퓨팅 인스턴스에 대한 세부 정보를 찾습니다.

## <a name="next-steps"></a>다음 단계

* [첫 번째 실험 실행](tutorial-1st-experiment-sdk-train.md)
* [스냅샷으로 파일 스토리지 백업](../storage/files/storage-snapshots-files.md)
