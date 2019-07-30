---
title: '빠른 시작: 클라우드에서 Notebook 실행'
titleSuffix: Azure Machine Learning service
description: 이 자습서에서는 Azure Machine Learning Service를 시작하고 클라우드에서 관리형 Notebook 서버를 사용하는 방법을 보여 줍니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 1124bb17abb9340b442d8d6075551ffe0dc681f7
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68371047"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>빠른 시작: 클라우드 기반 Notebook 서버를 사용하여 Azure Machine Learning 시작

이 빠른 시작에서는 클라우드에서 관리형 Notebook 서버를 사용하여 Azure Machine Learning Service를 시작하는 방법에 대해 설명합니다. 설치가 필요하지 않습니다. 대신에 자체 Python 환경에 SDK를 설치하려면 [빠른 시작: 사용자 고유의 Notebook 서버를 사용하여 Azure Machine Learning 시작](quickstart-run-local-notebook.md)을 참조하세요.

이 빠른 시작에서는 [Azure Machine Learning Service 작업 영역](concept-azure-machine-learning-architecture.md)을 사용하여 ML(기계 학습) 실험을 추적하는 방법을 보여 줍니다. 이렇게 하려면 Jupyter Notebook 서버, JupyterLab 및 완전하게 준비된 ML 환경을 제공하는 안전한 클라우드 기반 Azure 워크스테이션인 [Notebook 가상 머신(미리 보기)](how-to-configure-environment.md#notebookvm)을 만듭니다. 그런 다음, 이 VM(가상 머신)에서 값을 작업 영역에 기록하는 Python Notebook을 실행합니다.

이렇게 하려면 다음 작업을 수행합니다.

* 작업 영역을 만듭니다.
* 작업 영역에서 Notebook VM을 만듭니다.
* Jupyter 웹 인터페이스를 엽니다.
* pi를 추정하는 코드를 포함하고 각 반복에서 오류를 기록하는 Notebook을 엽니다.
* Notebook을 실행합니다.
* 작업 영역의 기록된 오류 값을 확인합니다. 다음 예제에서는 작업 영역에서 스크립트에 생성된 정보를 추적하는 데 도움이 되는 방법을 보여 줍니다.

Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

## <a name="create-a-workspace"></a>작업 영역 만들기

Azure Machine Learning Service 작업 영역이 있으면 [다음 섹션](#create-notebook)으로 건너뜁니다. 그렇지 않으면 지금 작업 영역을 새로 만듭니다.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-notebook"></a>Notebook VM 만들기

 작업 영역에서 Jupyter Notebook을 사용하여 시작할 클라우드 리소스를 만듭니다. 이 리소스는 Azure Machine Learning Service를 실행하는 데 필요한 모든 요소가 미리 구성된 클라우드 기반 플랫폼입니다.

1. [Azure Portal](https://portal.azure.com/)에서 작업 영역을 엽니다. 포털에서 작업 영역을 찾는 방법을 잘 모르는 경우 [작업 영역을 보는 방법](how-to-manage-workspace.md#view)을 참조하세요.

1. 작업 영역 페이지의 왼쪽에서 **Notebook VM**을 선택합니다.

1. **+ 새로 만들기**를 선택하여 Notebook VM을 만듭니다.  

     ![새 VM 선택](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. VM 이름을 제공합니다. 그런 다음 **만들기**를 선택합니다.

    > [!NOTE]
    > Notebook VM 이름은 2~16자여야 합니다. 문자, 숫자 및 하이픈은 유효한 문자입니다. 이름은 Azure 구독 전체에서 고유해야 합니다.

    ![새 VM 만들기](media/quickstart-run-cloud-notebook/create-new-workstation.png)

1. 상태가 **실행 중**으로 변경될 때까지 약 4~5분 정도 기다립니다.


## <a name="open-the-jupyter-web-interface"></a>Jupyter 웹 인터페이스 열기

VM이 실행되면 **Notebook VM** 섹션을 사용하여 Jupyter 웹 인터페이스를 엽니다.

1. VM에 대한 **URI** 열에서 **Jupyter**를 선택합니다.  

    ![Jupyter Notebook 서버 시작](./media/quickstart-run-cloud-notebook/start-server.png)

    이 링크는 Notebook 서버를 시작하고, 새 브라우저 탭에서 Jupyter Notebook 웹 페이지를 엽니다.  이 링크는 VM을 만든 사용자만 사용할 수 있습니다.  작업 영역의 각 사용자는 자신의 고유한 VM을 만들어야 합니다.

1. Jupyter Notebook 웹 페이지에서 최상위 폴더 이름은 사용자 이름입니다. 이 폴더를 선택합니다.

    > [!TIP]
    > 이 폴더는 Notebook VM 자체가 아닌 작업 영역의 [스토리지 컨테이너](concept-workspace.md#resources)에 있습니다.  Notebook VM을 삭제하고 모든 작업을 그대로 유지할 수 있습니다.  나중에 새 Notebook VM를 만들면 동일한 폴더가 로드됩니다.  자신의 작업 영역을 다른 사용자와 공유하면 해당 사용자에게 해당 폴더가 표시되고 자신에게도 해당 사용자의 폴더가 표시됩니다. 

1. 샘플 폴더 이름에는 버전 번호(예: **samples-1.0.33.1**)가 포함됩니다. 샘플 폴더를 선택합니다.

1. **빠른 시작** 폴더를 선택합니다.

## <a name="run-the-notebook"></a>노트북 실행

pi를 추정하고 오류를 작업 영역에 기록하는 Notebook을 실행합니다.

1. **01.run-experiment.ipynb**를 선택하여 Notebook을 엽니다.

1. "커널을 찾을 수 없음" 경고가 표시되면 **Python 3.6 - AzureML** 커널을 선택하고(대략 목록 중간 아래쪽에 있음), 해당 커널을 설정합니다.

1. 첫 번째 코드 셀, **실행**을 차례로 선택합니다.

    > [!NOTE]
    > 코드 셀 앞에는 대괄호가 있습니다. 대괄호가 비어 있으면( __[  ]__ ) 코드가 실행되지 않았습니다. 코드가 실행 중인 동안 별표( __[*]__ )가 표시되며, 코드가 완료되면 **[1]** 숫자가 표시됩니다.  이 숫자는 셀이 실행된 순서를 알려줍니다.
    >
    > **Shift+Enter**를 바로 가기로 사용하여 셀을 실행하세요.

    ![첫 번째 코드 셀 실행](media/quickstart-run-cloud-notebook/cell1.png)

1. 두 번째 코드 셀을 실행합니다. 인증 지침이 표시되면 코드를 복사하고 링크를 따라 로그인합니다. 로그인하면 브라우저에서 이 설정을 기억합니다.  

    ![인증](media/quickstart-run-cloud-notebook/authenticate.png)

1. 코드 셀이 성공적으로 실행되면 __[2]__ 셀 번호가 표시됩니다. 로그인해야 하는 경우 성공적인 인증 상태 메시지가 표시됩니다.   로그인할 필요가 없으면 이 셀에 대한 출력이 표시되지 않습니다. 셀이 성공적으로 실행되었음을 나타내는 숫자만 표시됩니다.

    ![성공 메시지](media/quickstart-run-cloud-notebook/success.png)

1. 나머지 코드 셀을 실행합니다. 각 셀의 실행이 완료되면 해당 셀 번호가 표시됩니다. 마지막 셀에만 다른 출력이 표시됩니다.  

    가장 큰 코드 셀의 경우 `run.log`가 여러 위치에 표시됩니다. 각 `run.log`에서 해당 값을 작업 영역에 추가합니다.

## <a name="view-logged-values"></a>기록된 값 보기

1. `run` 셀의 출력에는 Azure Portal로 다시 연결되는 링크가 포함되어 있어 작업 영역에서 실험 결과를 볼 수 있습니다.

    ![실험 보기](./media/quickstart-run-cloud-notebook/view-exp.png)

1. 작업 영역에서 실행에 대한 정보를 보려면 **Azure Portal에 연결**을 선택합니다. 이 링크는 Azure Portal에서 작업 영역을 엽니다.

1. 표시되는 기록 값의 플롯은 작업 영역에서 자동으로 생성되었습니다. 동일한 이름 매개 변수를 사용하여 여러 값을 기록할 때마다 플롯이 자동으로 생성됩니다. 다음은 예제입니다.

   ![기록 보기](./media/quickstart-run-cloud-notebook/web-results.png)

Pi 근삿값을 계산하는 코드에서 임의 값을 사용하므로 도면에 다르게 보일 수 있습니다.  

## <a name="clean-up-resources"></a>리소스 정리

### <a name="stop-the-notebook-vm"></a>Notebook VM 중지

비용을 줄이기 위해 Notebook VM을 사용하지 않을 때는 Notebook VM을 중지합니다.  

1. 작업 영역에서 **Notebook VM**을 선택합니다.

   ![VM 서버 중지](./media/quickstart-run-cloud-notebook/stop-server.png)

1. 목록에서 VM을 선택합니다.

1. **중지**를 선택합니다.

1. 서버를 다시 사용할 준비가 되면 **시작**을 선택합니다.

### <a name="delete-everything"></a>모든 항목 삭제

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

또한 리소스 그룹을 유지하면서 단일 작업 영역을 삭제할 수도 있습니다. 작업 영역 속성을 표시하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이러한 작업이 완료되면 Jupyter Notebook 웹 페이지로 이동합니다. 웹 서비스를 배포하는 방법을 알아보려면 **빠른 시작** 폴더에서 **02.deploy-web-service.ipynb** Notebook을 열고 실행합니다.

다른 Python 패키지를 Python 환경에 설치하려면 Notebook 내에서 다음 코드를 사용합니다.

```
!source activate py36 && pip install <packagename>
```

또한 Azure Machine Learning Service에 대해 자세히 알아보려면 Jupyter Notebook 웹 페이지에서 샘플 폴더의 다른 Notebook을 찾아봅니다.

워크플로 환경을 자세히 살펴보려면 다음 Machine Learning 자습서에 따라 모델을 학습하고 배포하세요.  

> [!div class="nextstepaction"]
> [자습서: 이미지 분류 모델 학습시키기](tutorial-train-models-with-aml.md)
