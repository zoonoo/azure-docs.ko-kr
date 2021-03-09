---
title: 작업 영역에서 Jupyter 노트북 실행
titleSuffix: Azure Machine Learning
description: Azure Machine Learning studio에서 작업 영역을 벗어나지 않고 Jupyter 노트북을 실행 하는 방법을 알아봅니다.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 01/19/2021
ms.openlocfilehash: 5748bf3d428102e296067dc5d1927ba487d575bc
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518724"
---
# <a name="run-jupyter-notebooks-in-your-workspace"></a>작업 영역에서 Jupyter 노트북 실행

Azure Machine Learning studio의 작업 영역에서 직접 Jupyter 노트북을 실행 하는 방법을 알아봅니다. [Jupyter](https://jupyter.org/) 또는 [JupyterLab](https://jupyterlab.readthedocs.io)을 시작할 수 있으며 작업 영역을 벗어나지 않고 Notebook을 편집 및 실행할 수도 있습니다.

전자 필기장을 포함 하 여 파일을 만들고 관리 하는 방법에 대 한 자세한 내용은 [작업 영역에서 파일 만들기 및 관리](how-to-manage-files.md)를 참조 하세요.

## <a name="prerequisites"></a>필수 조건

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://aka.ms/AMLFree)을 만듭니다.
* Machine Learning 작업 영역. [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

## <a name="edit-a-notebook"></a>Notebook 편집

Notebook을 편집하려면 작업 영역의 **사용자 파일** 섹션에 있는 모든 Notebook을 엽니다. 편집하려는 셀을 클릭합니다.  이 섹션에 노트북이 없으면 [작업 영역에서 파일 만들기 및 관리](how-to-manage-files.md)를 참조 하세요.

계산 인스턴스에 연결 하지 않고 노트북을 편집할 수 있습니다.  노트북에서 셀을 실행 하려는 경우 계산 인스턴스를 선택 하거나 만듭니다.  중지 된 계산 인스턴스를 선택 하면 첫 번째 셀을 실행할 때 자동으로 시작 됩니다.

계산 인스턴스가 실행 되는 경우 모든 Python 노트북에서 [Intellisense](https://code.visualstudio.com/docs/editor/intellisense)에서 제공 하는 코드 완성 기능을 사용할 수도 있습니다.

노트북 도구 모음에서 Jupyter 또는 JupyterLab을 시작할 수도 있습니다.  Azure Machine Learning은 Microsoft 지원이 안 되는 오픈 소스 제품이므로 Jupyter 또는 JupyterLab에서 업데이트를 제공하거나 버그를 수정하지 않습니다.

## <a name="focus-mode"></a>포커스 모드

활성 탭에 집중할 수 있도록 포커스 모드를 사용 하 여 현재 보기를 확장 합니다. 포커스 모드는 전자 필기장 파일 탐색기를 숨깁니다.

1. 터미널 창 도구 모음 **에서 포커스 모드를 선택 하** 여 포커스 모드를 설정 합니다. 창 너비에 따라 도구 모음의 **...** 메뉴 항목 아래에 있을 수 있습니다.
1. 포커스 모드에서 **표준 뷰** 를 선택 하 여 표준 뷰로 돌아갑니다.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="포커스 모드/표준 뷰 설정/해제":::

## <a name="use-intellisense"></a>IntelliSense 사용

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) 는 멤버 목록, 매개 변수 정보, 요약 정보 및 단어 자동 완성 등 다양 한 기능을 포함 하는 코드 완성 지원입니다. 이러한 기능을 사용하면 사용 중인 코드에 대한 자세한 정보를 보고, 입력하는 매개 변수를 계속 추적하고, 단 몇 번의 키 입력만으로 속성과 메서드에 대한 호출을 추가할 수 있습니다.  

코드를 입력할 때 Ctrl + Space를 사용 하 여 IntelliSense를 트리거합니다.

## <a name="clean-your-notebook-preview"></a>노트북 청소 (미리 보기)

> [!IMPORTANT]
> 수집 기능은 현재 공개 미리 보기로 제공 됩니다.
> 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

노트북을 만드는 과정에서 일반적으로 데이터 탐색 또는 디버깅에 사용한 셀이 표시 됩니다. *수집* 기능은 이러한 불필요 한 셀 없이 깨끗 한 노트북을 생성 하는 데 도움이 됩니다.

1. 모든 노트북 셀을 실행 합니다.
1. 새 전자 필기장을 실행 하려는 코드가 포함 된 셀을 선택 합니다. 예를 들어 실험을 제출 하는 코드 또는 모델을 등록 하는 코드를 예로 들 수도 있습니다.
1. 셀 도구 모음에 표시 되는 **수집** 아이콘을 선택 합니다.
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="스크린샷: 수집 아이콘을 선택 합니다.":::
1. 새 "수집 된" 노트북의 이름을 입력 합니다.  

새 노트북에는 코드 셀만 포함 되어 있으며, 모든 셀은 수집 하도록 선택한 셀과 동일한 결과를 생성 하는 데 필요 합니다.

## <a name="save-and-checkpoint-a-notebook"></a>노트북 저장 및 검사점

Azure Machine Learning는 *ipynb* 파일을 만들 때 검사점 파일을 만듭니다.

노트북 도구 모음에서 메뉴를 선택 하 고 **파일 &gt; 저장 및 검사점** 을 선택 하 여 노트북을 수동으로 저장 하 고 노트북에 연결 된 검사점 파일을 추가 합니다.

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="노트북 도구 모음에 있는 저장 도구의 스크린샷":::

모든 노트북은 30 초 마다 자동으로 저장 됩니다. 자동 저장은 검사점 파일이 아닌 초기 *ip이상 b* 파일만 업데이트 합니다.
 
노트북 메뉴에서 **검사점** 을 선택 하 여 명명 된 검사점을 만들고 노트북을 저장 된 검사점으로 되돌립니다.

## <a name="export-a-notebook"></a>노트북 내보내기

노트북 도구 모음에서 메뉴를 선택 하 고 다음 **으로 내보내기** 를 선택 하 여 지원 되는 유형으로 노트북을 내보냅니다.

* Notebook
* Python
* HTML
* LaTeX

:::image type="content" source="media/how-to-run-jupyter-notebooks/export-notebook.png" alt-text="컴퓨터에 노트북 내보내기":::

내보낸 파일이 컴퓨터에 저장 됩니다.

## <a name="run-a-notebook-or-python-script"></a>노트북 또는 Python 스크립트 실행

노트북 또는 Python 스크립트를 실행 하려면 먼저 실행 중인 [계산 인스턴스에](concept-compute-instance.md)연결 합니다.

* 컴퓨팅 인스턴스가 없는 경우 다음 단계를 사용하여 만듭니다.

    1. 노트북 또는 스크립트 도구 모음에서 계산 드롭다운 오른쪽에 있는 **+ 새 계산** 을 선택 합니다. 화면 크기에 따라 **...** 메뉴 아래에 있을 수 있습니다.
        :::image type="content" source="media/how-to-run-jupyter-notebooks/new-compute.png" alt-text="새 계산 만들기":::
    1. 컴퓨팅 이름을 지정하고 **Virtual Machine 크기** 를 선택합니다. 
    1. **만들기** 를 선택합니다.
    1. 계산 인스턴스는 파일에 자동으로 연결 됩니다.  이제 계산 인스턴스 왼쪽의 도구를 사용 하 여 노트북 셀 또는 Python 스크립트를 실행할 수 있습니다.

* 중지 된 계산 인스턴스가 있는 경우 계산 드롭다운 오른쪽의  **계산 시작** 을 선택 합니다. 화면 크기에 따라 **...** 메뉴 아래에 있을 수 있습니다.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/start-compute.png" alt-text="계산 인스턴스 시작":::

사용자가 만든 컴퓨팅 인스턴스만 보고 사용할 수 있습니다.  **사용자 파일** 은 VM과 별도로 저장되고 작업 영역의 모든 컴퓨팅 인스턴스 간에 공유됩니다.

### <a name="view-logs-and-output"></a>로그 및 출력 보기

[노트북 위젯을](/python/api/azureml-widgets/azureml.widgets) 사용 하 여 실행 및 로그의 진행률을 볼 수 있습니다. 위젯은 비동기적이며 학습을 마칠 때까지 업데이트를 제공합니다. Azure Machine Learning 위젯은 Jupyter 및 JupyterLab에서도 지원됩니다.

:::image type="content" source="media/how-to-run-jupyter-notebooks/jupyter-widget.png" alt-text="스크린샷: Jupyter 노트 위젯 ":::

## <a name="explore-variables-in-the-notebook"></a>노트북의 변수 살펴보기

노트북 도구 모음에서 **변수 탐색기** 도구를 사용 하 여 노트북에 생성 된 모든 변수에 대 한 이름, 유형, 길이 및 샘플 값을 표시 합니다.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer.png" alt-text="스크린샷: 변수 탐색기 도구":::

변수 탐색기 창을 표시 하려면 도구를 선택 합니다.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer-window.png" alt-text="스크린 샷: 변수 탐색기 창":::

## <a name="navigate-with-a-toc"></a>TOC로 탐색

노트 도구 모음에서 목차 도구를 사용 하  **여 목차를** 표시 하거나 숨깁니다.  제목으로 markdown 셀을 시작 하 여 목차에 추가 합니다. 테이블에서 항목을 클릭 하 여 노트북의 해당 셀로 스크롤합니다.  

:::image type="content" source="media/how-to-run-jupyter-notebooks/table-of-contents.png" alt-text="스크린샷: 노트북의 목차":::

## <a name="change-the-notebook-environment"></a>Notebook 환경 변경

노트북 도구 모음을 사용 하면 노트북이 실행 되는 환경을 변경할 수 있습니다.  

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

## <a name="add-new-kernels"></a>새 커널 추가

[터미널을 사용 ](how-to-access-terminal.md#add-new-kernels) 하 여 계산 인스턴스에 새 커널을 만들고 추가 합니다. 노트북은 연결 된 계산 인스턴스에 설치 된 모든 Jupyter 커널을 자동으로 찾습니다.

오른쪽의 커널 드롭다운을 사용 하 여 설치 된 커널 중 하나를 변경할 수 있습니다.  


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

## <a name="useful-keyboard-shortcuts"></a>유용한 바로 가기 키
Jupyter 노트북과 마찬가지로 Azure Machine Learning Studio 노트북에는 모달 사용자 인터페이스가 있습니다. 키보드는 Notebook 셀이 있는 모드에 따라 다른 작업을 수행합니다. Azure Machine Learning Studio 노트북은 지정 된 코드 셀에 대해 명령 모드 및 편집 모드의 두 가지 모드를 지원 합니다.

### <a name="command-mode-shortcuts"></a>명령 모드 바로 가기

입력하라는 텍스트 커서가 없으면 셀은 명령 모드에 있습니다. 셀이 명령 모드에 있으면 Notebook을 전체적으로 편집할 수 있지만 개별 셀에는 입력할 수 없습니다. `ESC`마우스를 사용 하거나 마우스를 사용 하 여 셀의 편집기 영역 밖에 서 선택 하 여 명령 모드를 시작 합니다.  활성 셀의 왼쪽 테두리는 파란색이 고 solid 이며 **실행** 단추는 파란색입니다.

   :::image type="content" source="media/how-to-run-jupyter-notebooks/command-mode.png" alt-text="명령 모드의 전자 필기장 셀 ":::

| 바로 가기                      | 설명                          |
| ----------------------------- | ------------------------------------|
| Enter 키                         | 편집 모드 시작             |        
| Shift+Enter                 | 셀 실행, 아래 선택         |     
| 제어/명령 + Enter       | 셀 실행                            |
| Alt + Enter                   | 셀 실행, 아래에 코드 셀 삽입    |
| 컨트롤/명령 + Alt + Enter | 셀 실행, 아래에 markdown 셀 삽입|
| Alt + R                       | 모두 실행      |                       
| Y                             | 셀을 코드로 변환    |                         
| M                             | 셀을 markdown로 변환  |                       
| Up/K                          | 위 셀 선택    |               
| Down/J                        | 아래 셀 선택    |               
| A                             | 위에 코드 셀 삽입  |            
| b                             | 아래에 코드 셀 삽입   |           
| 컨트롤/명령 + Shift + A   | 위에 markdown 셀 삽입    |      
| 컨트롤/명령 + Shift + B   | 아래에 markdown 셀 삽입   |       
| X                             | 선택한 셀 잘라내기    |               
| C                             | 선택한 셀 복사   |               
| Shift + V                     | 선택한 셀을 위에 붙여넣기           |
| V                             | 선택한 셀을 아래에 붙여 넣습니다.    |       
| D D                           | 선택한 셀 삭제|                
| O                             | Toggle Output         |              
| Shift + O                     | 출력 스크롤 설정/해제   |          
| I I                           | 인터럽트 커널 |                   
| 0 0                           | 커널 다시 시작 |                     
| Shift + 스페이스바                 | 위로 스크롤  |                         
| Space                         | 아래로 스크롤|
| 탭                           | 다음으로 포커스를 받을 수 있는 항목으로 포커스 변경 (탭 트래핑을 사용 하지 않는 경우)|
| 제어/명령 + S           | 노트북 저장 |                      
| 1                             | H1으로 변경|                       
| 2                             | H2로 변경|                        
| 3                             | H3으로 변경|                        
| 4                             | H4으로 변경 |                       
| 5                             | H5으로 변경 |                       
| 6                             | H6으로 변경 |                       

### <a name="edit-mode-shortcuts"></a>편집 모드 바로 가기

편집 모드에서는 편집기 영역에 입력하라는 텍스트 커서가 표시됩니다. 셀이 편집 모드에 있는 경우 셀에 입력할 수 있습니다. 마우스를 누르거나 마우스를 `Enter` 사용 하 여 셀의 편집기 영역을 선택 하 여 편집 모드를 시작 합니다. 활성 셀의 왼쪽 테두리는 녹색 및 빗금 이며 **실행** 단추는 녹색입니다. 편집 모드의 셀에 커서 프롬프트가 표시 됩니다.

   :::image type="content" source="media/how-to-run-jupyter-notebooks/edit-mode.png" alt-text="편집 모드의 전자 필기장 셀":::

다음 키 입력 바로 가기를 사용 하 여 편집 모드에 있을 때 Azure Machine Learning 노트북에서 코드를 보다 쉽게 탐색 하 고 실행할 수 있습니다.

| 바로 가기                      | 설명|                                     
| ----------------------------- | ----------------------------------------------- |
| 이스케이프                        | 명령 모드 입력|  
| 컨트롤/명령 + 공간       | IntelliSense 활성화 |
| Shift+Enter                 | 셀 실행, 아래 선택 |                         
| 제어/명령 + Enter       | 셀 실행  |                                      
| Alt + Enter                   | 셀 실행, 아래에 코드 셀 삽입  |              
| 컨트롤/명령 + Alt + Enter | 셀 실행, 아래에 markdown 셀 삽입  |          
| Alt + R                       | 모든 셀 실행     |                              
| 위로                            | 커서를 위 또는 이전 셀로 이동    |             
| 아래로                          | 커서를 아래로 이동 또는 다음 셀 이동 |                  
| 제어/명령 + S           | 노트북 저장   |                                
| Control/Command + Up          | 셀 시작으로 이동   |                             
| Control/Command + Down        | 셀 끝으로 이동 |                                 
| 탭                           | 코드 완성 또는 들여쓰기 (탭 트랩을 사용 하는 경우) |
| Control/Command + M           | 탭 트랩 사용/사용 안 함  |                       
| Control/Command +]           | 들여쓰기 |                                         
| Control/Command + [           | 내어쓰기  |                                        
| 컨트롤/명령 + A           | 모두 선택|                                      
| Control/Command + Z           | 실행 취소 |                                           
| Ctrl/Command + Shift + Z   | 다시 실행 |                                           
| Control/Command + Y           | 다시 실행 |                                           
| Control/Command + Home        | 셀 시작으로 이동|                                
| 컨트롤/명령 + 끝         | 셀 끝으로 이동   |                               
| 컨트롤/명령 + 왼쪽        | 한 단어 왼쪽으로 이동 |                               
| Control/Command + Right       | 한 단어 오른쪽으로 이동 |                              
| 컨트롤/명령 + 백스페이스   | 이전 단어 삭제 |                             
| 제어/명령 + 삭제      | 다음 단어 삭제 |                              
| Control/Command +/           | 셀에 주석 설정/해제

## <a name="troubleshooting"></a>문제 해결

* 노트북에 연결할 수 없는 경우 웹 소켓 통신 **을 사용 하지 않도록 설정** 해야 합니다. 계산 인스턴스 Jupyter 기능이 작동 하려면 웹 소켓 통신을 사용 하도록 설정 해야 합니다. 네트워크에서 *. instances.azureml.net 및 *. instances.azureml.ms에 대 한 websocket 연결을 허용 하는지 확인 하세요. 

* 계산 인스턴스가 개인 링크 작업 영역에 배포 된 경우 [가상 네트워크 내 에서만 액세스할](https://docs.microsoft.com/azure/machine-learning/how-to-secure-training-vnet#compute-instance)수 있습니다. 사용자 지정 DNS 또는 호스트 파일을 사용 하는 경우 < 인스턴스 이름 >에 대 한 항목을 추가 하세요. < 지역 전용 끝점의 개인 IP 주소를 사용 하 여 instances.azureml.ms >. 자세한 내용은 [사용자 지정 DNS](./how-to-custom-dns.md?tabs=azure-cli) 문서를 참조 하세요.
    
## <a name="next-steps"></a>다음 단계

* [첫 번째 실험 실행](tutorial-1st-experiment-sdk-train.md)
* [스냅샷으로 파일 스토리지 백업](../storage/files/storage-snapshots-files.md)
* [보안 환경에서 작업](./how-to-secure-training-vnet.md#compute-instance)
