---
title: '자습서: 환경 및 작업 영역을 설정합니다.'
titleSuffix: Azure Machine Learning service
description: 이 자습서 시리즈에서는 Jupyter Notebook에서 실행되는 Azure Machine Learning Python SDK를 시작하기 위한 엔드투엔드 단계를 수행합니다.  1부에서는 클라우드 Notebook 서버 환경을 만들고 실험 및 기계 학습 모델을 관리하는 작업 영역을 만드는 과정을 다룹니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 07/20/2019
ms.openlocfilehash: 70fee90fb82618a409d2566a3235ad8ca42e1760
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934423"
---
# <a name="tutorial-set-up-environment-and-workspace"></a>자습서: 환경 및 작업 영역 설정

이 자습서에서는 Jupyter Notebook에서 실행되는 Azure Machine Learning Python SDK를 시작하기 위한 엔드투엔드 단계를 수행합니다. 이 자습서는 **2부로 구성된 자습서 시리즈 중 제1부**입니다. Python 환경 설정 및 구성에 대해 설명하고, 실험 및 기계 학습 모델을 관리하기 위한 작업 영역을 만듭니다. [**2부**](tutorial-1st-experiment-sdk-train.md)에서는 이를 기반으로 하여 여러 기계 학습 모델을 학습시키고 Azure Portal과 SDK를 모두 사용하여 모델 관리 프로세스를 소개합니다.

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * 다음 자습서에서 사용할 기계 학습 작업 영역을 만듭니다.
> * Azure Machine Learning Python SDK가 설치되고 미리 구성된 클라우드 기반 Jupyter Notebook VM을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 자습서의 유일한 필수 조건은 Azure 구독입니다. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 바로 [Azure Machine Learning Service의 무료 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

## <a name="create-a-workspace"></a>작업 영역 만들기

작업 영역은 기계 학습 모델을 실험하고, 학습시키고, 배포하는 데 사용하는 클라우드의 기본 리소스입니다. Azure 구독 및 리소스 그룹을 SDK에서 쉽게 사용되는 개체에 연결합니다. Azure Machine Learning Service 작업 영역이 이미 있으면 [다음 섹션](#azure)으로 건너뜁니다. 그렇지 않으면 지금 작업 영역을 새로 만듭니다.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="azure"></a>클라우드 Notebook 서버 만들기

이 예에서는 작업 영역의 클라우드 Notebook 서버를 설치하지 않는 미리 구성된 환경으로 사용합니다. 환경, 패키지 및 종속성을 제어하려면 [사용자 고유의 환경](how-to-configure-environment.md#local)을 사용합니다.

작업 영역에서 Juffyter Notebook을 사용하여 시작할 클라우드 리소스를 만듭니다. 이 리소스는 Azure Machine Learning Service를 실행하는 데 필요한 모든 항목이 미리 구성된 클라우드 기반 Linux 가상 머신입니다.

1. [Azure Portal](https://portal.azure.com/)에서 작업 영역을 엽니다.  포털에서 작업 영역을 찾는 방법을 잘 모르는 경우 [작업 영역을 찾는 방법](how-to-manage-workspace.md#view)을 참조하세요.

1. Azure Portal의 작업 영역 페이지에서 왼쪽에 있는 **Notebook VM**을 선택합니다.

1. **+ 새로 만들기**를 선택하여 Notebook VM을 만듭니다.

     ![새 VM 선택](./media/tutorial-1st-experiment-sdk-setup/add-workstation.png)

1. VM 이름을 제공합니다. 그런 다음 **만들기**를 선택합니다.

    > [!NOTE]
    > Notebook VM 이름은 2~16자 사이여야 합니다. 유효한 문자는 문자, 숫자 및 - 문자입니다.  이름은 Azure 구독 전체에서 고유해야 합니다.

1. 상태가 **실행 중**으로 바뀔 때까지 기다립니다.

### <a name="launch-jupyter-web-interface"></a>Jupyter 웹 인터페이스 시작

VM이 실행되면 **Notebook VM** 섹션을 사용하여 Jupyter 웹 인터페이스를 시작합니다.

1. VM에 대한 **URI** 열에서 **Jupyter**를 선택합니다.

    ![Jupyter Notebook 서버 시작](./media/tutorial-1st-experiment-sdk-setup/start-server.png)

    이 링크는 Notebook 서버를 시작하고, 새 브라우저 탭에서 Jupyter Notebook 웹 페이지를 엽니다.  이 링크는 VM을 만든 사용자만 사용할 수 있습니다. 작업 영역의 각 사용자는 자신의 고유한 VM을 만들어야 합니다.

1. Jupyter Notebook 웹 페이지에서 최상위 폴더 이름은 사용자 이름입니다.  이 폴더를 선택합니다.

    > [!TIP]
    > 이 폴더는 Notebook VM 자체가 아닌 작업 영역의 [스토리지 컨테이너](concept-workspace.md#resources)에 있습니다.  Notebook VM을 삭제하고 모든 작업을 그대로 유지할 수 있습니다.  나중에 새 Notebook VM를 만들면 이 동일한 폴더가 로드됩니다. 자신의 작업 영역을 다른 사용자와 공유하면 해당 사용자에게 해당 폴더가 표시되고 자신에게도 해당 사용자의 폴더가 표시됩니다.

1. `samples-*` 하위 디렉터리를 연 다음, `tutorials/tutorial-1st-experiment-sdk-train.ipynb`를 열어 자습서의 **2부**를 실행합니다.

## <a name="end"></a> 리소스 정리

자습서의 **2부**로 계속 진행하려면 이 섹션을 완료하지 마세요.

### <a name="stop-the-notebook-vm"></a>Notebook VM 중지

클라우드 Notebook 서버를 사용한 경우 비용을 줄이기 위해 VM을 사용하지 않을 때는 해당 VM을 중지합니다.

1. 작업 영역에서 **Notebook VM**을 선택합니다.

   ![VM 서버 중지](./media/tutorial-1st-experiment-sdk-setup/stop-server.png)

1. 목록에서 VM을 선택합니다.

1. **중지**를 선택합니다.

1. 서버를 다시 사용할 준비가 되면 **시작**을 선택합니다.

### <a name="delete-everything"></a>모든 항목 삭제

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

또한 리소스 그룹을 유지하면서 단일 작업 영역을 삭제할 수도 있습니다. 작업 영역 속성을 표시하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업을 수행했습니다.

* Azure Machine Learning Service 작업 영역 만들기
* 작업 영역에서 클라우드 Notebook 서버 만들기 및 구성

간단한 기계 학습 모델을 학습시키려면 이 자습서의 **2부**로 계속 진행하세요.

> [!div class="nextstepaction"]
> [자습서: 첫 번째 모델 학습](tutorial-1st-experiment-sdk-train.md)
