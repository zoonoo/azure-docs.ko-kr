---
title: 작업 영역에 파일 만들기 및 관리
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 스튜디오의 작업 영역에 파일을 만들고 관리하는 방법을 알아봅니다.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 02/05/2021
ms.openlocfilehash: e1868794f7e47ccce3efa36b7954a1a2a704e8d6
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108739028"
---
# <a name="how-to-create-and-manage-files-in-your-workspace"></a>작업 영역에 파일을 만들고 관리하는 방법

Azure Machine Learning 작업 영역에 파일을 만들고 관리하는 방법을 알아봅니다.  이러한 파일은 기본 작업 영역 스토리지에 저장됩니다. 파일과 폴더를 작업 영역에 대한 읽기 액세스 권한이 있는 모든 사람과 공유하고 작업 영역의 컴퓨팅 인스턴스에서 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://aka.ms/AMLFree)을 만듭니다.
* Machine Learning 작업 영역. [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

## <a name="create-files"></a><a name="create"></a> 파일 만들기

기본 폴더(`Users > yourname`)에 새 파일을 만들려면 다음을 수행합니다.

1. [Azure Machine Learning 스튜디오](https://ml.azure.com)에서 작업 영역 열기
1. 왼쪽에서 **Notebook** 을 선택합니다.
1. **+** 이미지를 선택합니다.
1. **새 파일 만들기** 이미지를 선택합니다.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="새 파일을 만듭니다.":::

1. 파일 이름을 지정합니다.
1. 파일 형식을 선택합니다.
1. **만들기** 를 선택합니다.

Notebook 및 대부분의 텍스트 파일 형식은 미리 보기 섹션에 표시됩니다.  대부분의 다른 파일 형식에는 미리 보기가 없습니다.

다른 폴더에 새 파일을 만들려면 다음을 수행합니다.
1. 폴더 끝에 있는 “...”를 선택합니다.
1. **새 파일 만들기** 를 선택합니다.

> [!IMPORTANT]
> Notebook 및 스크립트의 콘텐츠는 세션에서 데이터를 읽고 Azure에 조직이 없어도 데이터에 액세스할 수 있습니다.  신뢰할 수 있는 원본에서 제공하는 파일만 로드합니다. 자세한 내용은 [보안 코드 모범 사례](concept-secure-code-best-practice.md#azure-ml-studio-notebooks)를 참조하세요.

## <a name="manage-files-with-git"></a>Git을 사용하여 파일 관리

[컴퓨팅 인스턴스 터미널을 사용](how-to-access-terminal.md#git)하여 Git 리포지토리를 복제하고 관리합니다.

## <a name="clone-samples"></a>샘플 복제

작업 영역에는 SDK를 탐색하고 사용자 고유의 기계 학습 프로젝트 예제로 제공하도록 설계된 Notebook이 포함된 **샘플 Notebook** 폴더가 있습니다.   Notebook을 사용자 고유의 폴더에 복제하여 실행 및 편집합니다.  

예제를 보려면 [ 자습서: 첫 번째 ML 실험 만들기](tutorial-train-models-with-aml.md#azure)를 참조하세요.

## <a name="share-files"></a>파일 공유

URL을 복사하고 붙여 넣어 파일을 공유합니다.  작업 영역의 다른 사용자만 이 URL에 액세스할 수 있습니다.  [작업 영역에 대한 액세스 권한 부여](how-to-assign-roles.md)에 대해 자세히 알아봅니다.

## <a name="delete-a-file"></a>파일 삭제

**샘플 Notebook** 파일을 삭제할 수 *없습니다*.  이러한 파일은 스튜디오에 포함되어 있으며 새 SDK가 게시될 때마다 업데이트됩니다.  

다음 방법 중 하나를 사용하여 **파일** 섹션에서 발견된 파일을 삭제할 수 *있습니다*.

* 스튜디오에서 폴더 또는 파일의 끝에 있는 **...** 를 선택합니다.  지원되는 브라우저(Microsoft Edge, Chrome 또는 Firefox)를 사용해야 합니다.
* 작업 영역의 컴퓨팅 인스턴스에서 [터미널을 사용](how-to-access-terminal.md)합니다. **~/cloudfiles** 폴더가 작업 영역 스토리지 계정의 스토리지에 매핑됩니다.
* Jupyter 또는 JupyterLab에서 해당 도구를 사용합니다.
