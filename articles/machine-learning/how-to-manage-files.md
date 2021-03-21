---
title: 작업 영역에서 파일 만들기 및 관리
titleSuffix: Azure Machine Learning
description: Azure Machine Learning studio에서 작업 영역에 파일을 만들고 관리 하는 방법을 알아봅니다.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: 474b3123513e4b8acf19ba9cdb42c3384ea3ced2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100101587"
---
# <a name="how-to-create-and-manage-files-in-your-workspace"></a>작업 영역에서 파일을 만들고 관리 하는 방법

Azure Machine Learning 작업 영역에서 파일을 만들고 관리 하는 방법을 알아봅니다.  이러한 파일은 기본 작업 영역 저장소에 저장 됩니다. 파일 및 폴더는 작업 영역에 대 한 읽기 액세스 권한이 있는 다른 사용자와 공유 하 고 작업 영역의 모든 계산 인스턴스에서 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://aka.ms/AMLFree)을 만듭니다.
* Machine Learning 작업 영역. [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

## <a name="create-files"></a><a name="create"></a> 파일 만들기

기본 폴더 ()에 새 파일을 만들려면 `Users > yourname` 다음을 수행 합니다.

1. [Azure Machine Learning 스튜디오](https://ml.azure.com)에서 작업 영역 열기
1. 왼쪽에서 **Notebook** 을 선택합니다.
1. 이미지를 선택 **+** 합니다.
1. **새 파일 만들기** 이미지를 선택 합니다.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="새 파일을 만듭니다.":::

1. 파일 이름을 지정합니다.
1. 파일 형식을 선택 합니다.
1. **만들기** 를 선택합니다.

Notebook 및 대부분의 텍스트 파일 형식은 미리 보기 섹션에 표시됩니다.  대부분의 다른 파일 형식에는 미리 보기가 없습니다.

다른 폴더에 새 파일을 만들려면 다음을 수행 합니다.
1. "..."를 선택 합니다. 폴더 끝에 있는
1. **새 파일 만들기** 를 선택합니다.

> [!IMPORTANT]
> 노트북 및 스크립트의 콘텐츠는 잠재적으로 세션에서 데이터를 읽고 Azure에서 조직 없이 데이터에 액세스할 수 있습니다.  신뢰할 수 있는 원본 에서만 파일을 로드 합니다. 자세한 내용은 [보안 코드 모범 사례](concept-secure-code-best-practice.md#azure-ml-studio-notebooks)를 참조 하세요.

## <a name="manage-files-with-git"></a>Git를 사용 하 여 파일 관리

[계산 인스턴스 터미널을 사용](how-to-access-terminal.md#git) 하 여 Git 리포지토리를 복제 및 관리 합니다.

## <a name="clone-samples"></a>샘플 복제

작업 영역에는 SDK를 탐색 하는 데 도움이 되 고 고유한 machine learning 프로젝트에 대 한 예제로 제공 되는 노트북이 포함 된 **샘플 전자 필기장** 폴더가 있습니다.   이러한 노트북을 자신의 폴더에 유일한 실행 및 편집 합니다.  

예제를 보려면 [ 자습서: 첫 번째 ML 실험 만들기](tutorial-1st-experiment-sdk-setup.md#azure)를 참조하세요.

## <a name="share-files"></a>파일 공유

URL을 복사 하 여 붙여넣어 서 파일을 공유 합니다.  작업 영역의 다른 사용자만이 URL에 액세스할 수 있습니다.  [작업 영역에 대한 액세스 권한 부여](how-to-assign-roles.md)에 대해 자세히 알아봅니다.

## <a name="delete-a-file"></a>파일 삭제

**샘플 전자 필기장** 파일은 삭제할 *수 없습니다* .  이러한 파일은 studio의 일부 이며 새 SDK가 게시 될 때마다 업데이트 됩니다.  

다음 방법 중 하나를 통해 **파일** 섹션에 있는 파일을 삭제할 *수* 있습니다.

* 스튜디오에서 폴더 또는 파일의 끝에 있는 **...** 를 선택합니다.  지원되는 브라우저(Microsoft Edge, Chrome 또는 Firefox)를 사용해야 합니다.
* 작업 영역의 모든 계산 인스턴스에서 [터미널을 사용](how-to-access-terminal.md) 합니다. **~/Cloudfiles** 폴더는 작업 영역 저장소 계정의 저장소에 매핑됩니다.
* Jupyter 또는 JupyterLab에서 해당 도구를 사용합니다.
