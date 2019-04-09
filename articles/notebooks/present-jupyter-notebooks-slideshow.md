---
title: Jupyter Notebook을 Azure에서 슬라이드 쇼로 프레젠테이션
description: Jupyter Notebook에서 슬라이드 쇼 모드로 셀을 구성한 다음 RISE 확장을 사용하여 슬라이드 쇼를 프레젠테이션하는 방법입니다.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: c372175b-beb5-4b45-b2f8-34cb06990117
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 405fe71676de311ed7e59ea72798ff4fd2db0f62
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280471"
---
# <a name="run-a-notebook-slideshow"></a>Notebook 슬라이드 쇼 실행

Azure Notebooks에는 Jupyter/IPython Slideshow Extension (RISE)이 미리 구성되어 있어 Notebook을 슬라이드 쇼로 바로 프레젠테이션할 수 있습니다. 슬라이드 쇼에서 셀은 대형 화면에 프레젠테이션하기에 적절한 글꼴 크기를 사용하여 한 번에 하나씩 표시되며, 별도의 데모 컴퓨터로 전환하는 대신 코드를 계속 실행할 수 있습니다.

다음 이미지에는 Markdown 및 코드 셀을 함께 확인할 수 있는 표준 Notebook 보기를 보여줍니다.

![표준 보기의 노트북](media/slideshow/slideshow-notebook-view.png)

슬라이드 쇼를 시작하면 첫 번째 셀은 브라우저에 맞게 확대됩니다. 여기서 왼쪽 위에 있는 **X**는 슬라이드 쇼를 종료하고, 왼쪽 아래의 **?** 는 바로 가기 키를 표시하며 오른쪽 아래의 화살표는 슬라이드 사이를 이동합니다.

![슬라이드 쇼 모드의 Notebook](media/slideshow/slideshow-slide-view.png)

Notebook을 슬라이드 쇼로 준비하려면 두 기본 작업을 거처야 합니다.

1. Markdown 셀은 큰 글꼴을 사용하여 렌더링되기 때문에 일부 콘텐츠는 슬라이드 쇼에 표시되지 않을 수 있습니다. 따라서 일반적으로, 지정된 셀의 텍스트 양을 제한하며 보통 4줄에서 6줄 헤더가 가장 적합합니다. 더 많은 텍스트가 있는 경우 해당 정보를 여러 셀로 분할합니다.

2. 슬라이드 쇼 셀 도구 모음을 사용하여 슬라이드 쇼에 각 셀의 동작을 구성합니다. 셀 형식은 탐색 단추의 동작을 결정합니다.

## <a name="the-anatomy-of-a-slideshow"></a>슬라이드 쇼 분석

임의의 Notebook을 슬라이드 쇼에 사용할 경우 일반적으로 모든 셀이 뒤섞여지고, 콘텐츠의 대부분이 브라우저 창의 아래쪽에 숨겨집니다. 효과적인 프레젠테이션을 하려면 슬라이드 쇼 셀 도구 모음을 사용하여 슬라이드 쇼 형식을 각 셀에 할당해야 합니다.

1. **보기** 메뉴에서 **셀 도구 모음** > **슬라이드 쇼**를 선택합니다.

    ![셀 슬라이드 쇼 도구 모음 켜기](media/slideshow/slideshow-view-cell-toolbar.png)

1. **슬라이드 형식** 드롭다운이 Notebook에서 각 셀의 오른쪽 위에 표시됩니다.

    ![셀 슬라이드 쇼 도구 모음](media/slideshow/slideshow-cell-toolbar.png)

1. 각 셀에 대해 5개 형식 중 하나를 선택합니다.

    ![셀 슬라이드 쇼 형식](media/slideshow/slideshow-cell-slide-types.png)

    | 슬라이드 형식 | 동작 |
    | --- | --- |
    | - (설정 안 함) | 셀이 이전 셀과 함께 표시되며, 슬라이드 쇼에서 원하는 효과가 아닌 경우도 있습니다. |
    | 슬라이드 | 셀이 기본 슬라이드이며, 탐색 컨트롤의 왼쪽 및 오른쪽 화살표를 사용하여 탐색됩니다. |
    | 하위 슬라이드 | 셀이 기본 슬라이드 “하위”에 있으며, 탐색 컨트롤의 왼쪽 및 오른쪽 화살표를 사용하여 탐색됩니다. 위쪽 화살표는 기본 슬라이드로 돌아갑니다. 하위 슬라이드는 프레젠테이션의 기본 경로에서 건너뛸 수 있는 보조 자료에 사용되지만, 필요한 경우 즉시 사용 가능합니다. |
    | 조각 | 셀 콘텐츠는 아래쪽 탐색 화살표를 사용하는 경우 이전 슬라이드 또는 하위 슬라이드의 컨텍스트에서 나타납니다(조각은 위쪽 화살표를 사용하는 경우 제거됨). 코드 셀이 있는 조각을 사용해 그 코드를 슬라이드에 표시되도록 하거나 여러 조각을 사용하여 텍스트 글머리 기호가 하나씩 표시되도록 할 수 있습니다(다음 섹션의 예제 참조). 조각은 현재 슬라이드에서 빌드되므로, 과도한 조각은 브라우저 창 아래쪽에 나타나지 않습니다. |
    | Skip | 셀이 슬라이드 쇼에 표시되지 않습니다. |
    | 메모 | 셀에 슬라이드 쇼에는 표시되지 않는 발표자 메모가 포함됩니다. |

1. 처음에 모든 셀에 대해 **슬라이드**를 선택하는 것이 유용합니다. 그래야 슬라이드 쇼를 실행하고 적절히 조정할 수 있습니다.

### <a name="example-fragment-cells-for-bullet-items"></a>예: 글머리 기호 항목에 대한 조각 셀

슬라이드에 글머리 기호를 하나씩 표시하려면 **슬라이드** 형식이 포함된 Markdown 셀에 슬라이드 헤더를 배치한 다음, **조각** 형식이 포함된 별도의 Markdown 셀에 각 글머리 기호를 배치합니다.

![글머리 기호 항목에 대해 여러 Markdown 셀 만들기 예제](media/slideshow/slideshow-fragments.png)

슬라이드 쇼는 모든 글머리 기호가 같은 셀에 있을 때보다 더 넓은 세로 간격으로 조각을 렌더링하므로, 많은 글머리 기호 항목으로 사용할 수 없을 수 있습니다.

## <a name="run-the-slideshow"></a>슬라이드 쇼 실행

1. Markdown 셀을 편집한 경우 셀을 실행하여 해당 HTML을 렌더링해야합니다. 그렇지 않으면 슬라이드 쇼에서 Markdown*으로* 나타납니다.

1. 각 셀에 대한 **슬라이드 형식**을 구성하면 슬라이드 쇼를 시작할 셀을 선택한 다음, 기본 도구 모음의 **RISE 슬라이드 쇼 시작/종료** 단추를 선택합니다.

    ![기본 도구 모음의 RISE 슬라이드 쇼 시작/종료 단추](media/slideshow/slideshow-start.png)

1. 슬라이드뿐만 아니라 조각을 탐색하려면 탐색 컨트롤의 왼쪽 및 오른쪽 화살표를 사용합니다. 컨트롤의 텍스트는 *slide.sub-slide*를 나타내는 숫자를 보여줍니다.

    ![슬라이드 쇼 탐색 컨트롤](media/slideshow/slideshow-navigation-control.png)

1. 조각뿐만 아니라 슬라이드와 하위 슬라이드를 탐색하려면 위쪽 및 아래쪽 화살표를 사용합니다(사용 설정된 경우).

    ![하위 슬라이드의 슬라이드 쇼 탐색 컨트롤](media/slideshow/slideshow-navigation-control-subslide.png)

1. 코드 셀에서 재생 단추를 사용하여 코드를 실행하면 출력이 슬라이드에 표시됩니다.

    ![코드 셀을 실행하는 재생 단추](media/slideshow/slideshow-run-code-cell.png)

    ![슬라이드 쇼에 코드 셀 출력이 나타납니다.](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > 슬라이드 쇼에서 셀 출력은 셀의 일부로 간주됩니다. Notebook 또는 슬라이드 쇼 보기에서 셀을 실행하는 경우 출력은 다른 보기에서도 나타납니다. 출력을 취소하려면 **셀** > **현재 출력** >  **지우기** 명령(현재 셀) 또는 **셀**  >  **모든 출력** > **지우기**(모든 셀) 명령을 사용합니다.

1. 슬라이드 쇼를 마친 경우 **X**를 사용하여 Notebook 보기로 돌아갑니다.

## <a name="next-steps"></a>다음 단계

- [방법: 구성 및 프로젝트 관리](configure-manage-azure-notebooks-projects.md)
- [방법: 노트 내에서 패키지 설치](install-packages-jupyter-notebook.md)
- [방법: 데이터 파일 작업](work-with-project-data-files.md)
- [방법: 데이터 리소스에 액세스](access-data-resources-jupyter-notebooks.md)
