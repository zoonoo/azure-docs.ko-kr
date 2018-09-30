---
title: '빠른 시작: 기계 학습 작업 영역 만들기 - Azure'
description: Azure Portal을 사용하여 Azure Machine Learning 작업 영역을 만듭니다. 이 작업 영역은 Azure Machine Learning 서비스로 기계 학습 모델을 실험하고, 교육하고, 배포하는 데 사용되는 클라우드의 기본 요소입니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: rastala
ms.author: roastala
ms.date: 09/24/2018
ms.openlocfilehash: c1610291e06255e2c724268f63d740f7e4debea4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46959991"
---
# <a name="quickstart-get-started-with-azure-machine-learning-service"></a>빠른 시작: Azure Machine Learning 서비스 시작

이 빠른 시작에서는 Azure Portal을 사용하여 Azure Machine Learning 작업 영역을 만듭니다. 이 작업 영역은 Azure Machine Learning 서비스로 기계 학습 모델을 실험하고, 교육하고, 배포하는 데 사용되는 클라우드의 기본 요소입니다. 

이 자습서에서는 다음을 수행합니다.

* Azure 구독에 작업 영역 만들기
* Azure Notebooks에서 Python을 사용해 보고 여러 번 반복 후 값 기록
* 작업 영역에서 기록된 값 보기

편의상 해당 지역에서 사용 가능한 경우 [Container Registry](https://azure.microsoft.com/services/container-registry/), [Storage](https://azure.microsoft.com/services/storage/), [Application Insights](https://azure.microsoft.com/services/application-insights/) 및 [Key Vault](https://azure.microsoft.com/services/key-vault/) Azure 리소스는 사용자 작업 영역에 자동으로 추가됩니다.

사용자가 만드는 리소스는 다른 Azure Machine Learning 자습서 및 방법 문서의 필수 구성 요소로 사용할 수 있습니다. 다른 Azure 서비스와 마찬가지로, Azure Machine Learning 서비스와 관련하여 특정 리소스에 대한 제한(예: BatchAI 클러스터 크기)이 있습니다. 기본 제한 및 추가 할당량을 요청하는 방법에 대한 [이 문서](how-to-manage-quotas.md)를 읽어 보세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.


## <a name="create-a-workspace"></a>작업 영역 만들기 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

작업 영역 페이지에서 `Explore your Azure Machine Learning Workspace`를 클릭합니다.

 ![작업 영역 탐색](./media/quickstart-get-started/explore_aml.png)


## <a name="use-the-workspace"></a>작업 영역 사용

이제 작업 영역으로 기계 학습 스크립트를 관리하는 방법을 알았습니다. 이 섹션에서는 다음을 수행합니다.

* Azure Notebooks에서 노트북 열기
* 일부 기록된 값을 만드는 코드 실행
* 작업 영역에서 기록된 값 보기

이는 작업 영역으로 스크립트에 생성된 정보를 추적하는 방법에 대한 한 가지 예입니다. 

### <a name="open-a-notebook"></a>노트북 열기 

Azure Notebooks는 Jupyter Notebooks를 위한 무료 클라우드 플랫폼을 제공하며, Azure Machine Learning 서비스를 실행하는 데 필요한 모든 것이 미리 구성되어 있습니다.  

`Open Azure Notebooks` 단추를 클릭하여 첫 번째 실험을 시작해 보세요.

 ![Azure Notebooks 시작](./media/quickstart-get-started/explore_ws.png)

로그인하면 새 탭이 열리고 `Clone Library` 프롬프트가 표시됩니다.  `Clone`을 클릭합니다.


### <a name="run-the-notebook"></a>노트북 실행

두 노트북과 함께 `config.json` 파일이 표시됩니다.  이 구성 파일에는 방금 만든 작업 영역에 대한 정보가 포함되어 있습니다.  

`01.run-experiment.ipynb`를 클릭하여 노트북을 엽니다.

`Shift`+`Enter`를 사용하여 셀을 한 번에 하나씩 실행할 수 있습니다.  또는 `Cells` > `Run All` 메뉴를 사용하여 전체 노트북을 실행할 수 있습니다.

로그인하라는 메시지가 표시됩니다.  메시지의 코드를 복사한 후 링크를 클릭하고 코드를 새 창에 붙여 넣습니다.  코드 앞뒤에 있는 공백을 복사하지 않아야 합니다.

 ![로그인](./media/quickstart-get-started/login.png)

노트북에서 두 번째 셀이 `config.json`에서 읽히고 작업 영역에 연결합니다.
```
ws = Workspace.from_config()
```

코드의 세 번째 셀은 "my-first-experiment"라는 이름으로 실험을 시작합니다.  이름은 작업 영역에서 다시 실행에 대한 정보를 찾는 데 사용합니다.

```
experiment = Experiment(workspace_object=ws, name = "my-first-experiment")
```

노트북의 마지막 셀에서 로그 파일에 기록된 값을 확인합니다.

```
# Log final results
run.log("Final estimate: ",pi_estimate)
run.log("Final error: ",math.pi-pi_estimate)
```

코드가 실행된 후 작업 영역에서 이러한 값을 볼 수 있습니다.

## <a name="view-logged-values"></a>기록된 값 보기

노트북의 모든 셀이 완성된 후 포털 페이지로 다시 이동합니다.  

`View Experiments`를 클릭합니다.

![실험 보기](./media/quickstart-get-started/view_exp.png)

`Reports` 팝업 항목을 닫습니다.

`my-first-experiment`를 클릭합니다.

방금 수행한 실행에 대한 정보를 참조하세요.  페이지 아래쪽으로 스크롤 하여 실행 테이블을 찾고 실행 번호 링크를 클릭합니다.

 ![실행 기록 링크](./media/quickstart-get-started/report.png)

자동으로 생성된 기록된 값에 대한 플롯이 표시됩니다.

   ![기록 보기](./media/quickstart-get-started/plots.png)

## <a name="clean-up-resources"></a>리소스 정리 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

또한 리소스 그룹은 보존하되, 작업 영역 속성을 표시하고 삭제 단추를 선택하여 단일 작업 영역을 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 모델을 사용해 보고 배포하는 과정을 시작하는 데 필요한 리소스를 만들었습니다. 또한 노트북에서 일부 코드를 실행하고 클라우드에서 작업 영역의 해당 코드에서 실행 기록을 탐색했습니다.

이 워크플로를 더 자세히 살펴보려면 모델 학습 및 배포 관련 Azure Machine Learning 자습서를 진행하세요.  

> [!div class="nextstepaction"]
> [자습서: 이미지 분류 모델 학습](tutorial-train-models-with-aml.md)
