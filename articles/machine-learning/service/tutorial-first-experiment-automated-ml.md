---
title: 처음으로 자동화된 기계 학습 실험 만들어보기
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning의 작업 영역 방문 페이지(미리 보기)에서 자동화된 기계 학습을 사용하여 분류 모델을 학습하고 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 09/09/2019
ms.openlocfilehash: 0dd4447736469644875dff914c6284b087be87d0
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910214"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>자습서: 자동화된 기계 학습을 사용하여 처음으로 분류 모델 만들어보기

이 자습서에서는 코드 줄을 하나도 작성하지 않고 작업 영역 방문 페이지(미리 보기)를 통해 첫 번째 자동화된 기계 학습 실험을 만드는 방법을 알아봅니다. 이 예제에서는 클라이언트가 금융 기관의 정기 예금을 신청할 것인지 예측하는 분류 모델을 만듭니다.

자동화된 기계 학습을 사용하면 시간이 많이 걸리는 작업을 자동화할 수 있습니다. 자동화된 기계 학습은 사용자가 선택한 성공 메트릭을 기반으로 최상의 모델을 발견할 수 있도록 알고리즘과 하이퍼 매개 변수의 여러 조합을 빠르게 반복합니다.

이 자습서에서는 다음 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Machine Learning Service 작업 영역을 만듭니다.
> * 자동화된 기계 학습 실험을 실행합니다.
> * 실험 세부 정보를 봅니다.
> * 모델을 배포합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 아직 없는 경우 [무료 계정](https://aka.ms/AMLFree)을 만듭니다.

* [**bankmarketing_train.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) 데이터 파일을 다운로드합니다. **y** 열은 고객이 정기 예금에 가입했는지 여부를 나타내며, 뒷부분에서 이 자습서의 예측에 대한 대상 열로 식별됩니다. 

## <a name="create-a-workspace"></a>작업 영역 만들기

Azure Machine Learning 작업 영역은 기계 학습 모델을 실험하고, 학습시키고, 배포하는 데 사용하는 클라우드의 기본 리소스입니다. Azure 구독 및 리소스 그룹을 서비스에서 사용하기 쉬운 개체에 연결합니다. 

Azure 리소스를 관리하기 위한 웹 기반 콘솔인 Azure Portal을 통해 작업 영역을 만듭니다. 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> **작업 영역** 및 **구독**을 적어 둡니다. 올바른 작업 영역에 실험을 만들려면 이 정보가 필요합니다. 

## <a name="create-and-run-the-experiment"></a>실험 만들기 및 실행

모든 기술 수준의 데이터 과학 전문가용 데이터 과학 시나리오를 수행하기 위한 기계 학습 도구를 포함하는 통합 인터페이스인 작업 영역 방문 페이지에서 다음 실험 설정 및 실행 단계를 완료합니다.

1. [작업 영역 방문 페이지](https://ml.azure.com/workspaceportal/)에 로그인합니다.

1. 해당 구독과 직접 만든 작업 영역을 선택합니다.

1. **시작**을 선택합니다.

1.  왼쪽 창의 **작성** 섹션에서 **자동화된 ML**을 선택합니다.
자동화된 기계 학습을 사용한 첫 번째 실험이므로 **시작** 화면이 표시됩니다.

    ![Azure Machine Learning Studio](media/tutorial-1st-experiment-automated-ml/get-started.png)

1. **실험 만들기**를 선택합니다. 

1. 실험 이름으로 **my-1st-automl-experiment**를 입력합니다.

1. **새 컴퓨팅 만들기**를 선택합니다. 

    1. 이 실험의 컴퓨팅 컨텍스트를 구성합니다.
        
        필드 | 값
        ----|---
        컴퓨팅 이름 |  컴퓨팅 컨텍스트를 식별하는 고유한 이름을 입력합니다. 이 예제에서는 **automl-compute**를 사용합니다.
        가상 머신 크기| 컴퓨팅에 사용할 가상 머신 크기를 선택합니다. 기본값인 **Standard_DS12_V2**를 사용합니다.
        추가 설정| *최소 노드*: 1. 데이터 프로파일링을 사용하려면 하나 이상의 노드가 있어야 합니다. <br> *최대 노드*: 6.
 
    1. 새 컴퓨팅을 만들려면 **만들기**를 선택합니다. 이 작업은 완료하는 데 2~3분이 걸립니다. 

    1. 만들기가 완료되면 드롭다운 목록에서 새 컴퓨팅을 선택하고 **다음**을 선택합니다.

    >[!NOTE]
    >이 자습서에서는 기본 스토리지 계정과 새 컴퓨팅으로 만든 컨테이너를 사용합니다. 이 계정과 컨테이너가 양식에 자동으로 채워집니다.

1. **로컬 파일에서 업로드**를 선택합니다. 여기서, 이전에 이 자습서를 위해 다운로드한 **bankmarketing_train.csv** 파일을 사용하여 새 데이터 세트를 만듭니다. 

    1. **찾아보기**를 선택한 다음, 로컬 컴퓨터에 있는 **bankmarketing_train.csv** 파일을 선택합니다. 

    1. 데이터 세트에 고유 이름을 지정하고 선택적 설명을 입력합니다. 

    1. **다음**을 선택하여 작업 영역을 만드는 동안 자동으로 설정된 기본 컨테이너에 데이터 세트를 업로드합니다. 공개 미리 보기에서는 로컬 파일 업로드만 지원합니다. 

    1. 업로드가 완료되면, **설정 및 미리 보기** 폼이 파일 형식에 따라 지능적으로 채워집니다. 폼이 다음과 같이 채워졌는지 확인합니다.
        
        필드|값
        ---|---
        파일 형식| 구분됨
        구분 기호| 쉼표
        Encoding| UTF-8
        열 머리글| 모든 파일에 동일한 머리글이 있음            행 건너뛰기 | 없음

        >[!NOTE]
        > 이 폼의 설정을 업데이트하면 미리 보기도 그에 따라 업데이트됩니다.

        **다음**을 선택합니다.
    

    1. **스키마** 폼을 사용하여 이 실험을 위해 데이터를 추가로 구성할 수 있습니다. 이 예제에서는 **day_of_week** 기능의 토글 스위치를 선택하여 이 실험에 포함되지 않도록 합니다. **완료**를 선택하여 실험에 사용할 데이터 세트를 만드는 작업과 파일 업로드를 완료합니다.

        ![미리 보기 탭 구성](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

        
1. 예측 작업으로 **분류**를 선택합니다.

1. 예측하려는 항목인 대상 열로 **y**를 선택합니다. 이 열은 클라이언트가 정기 예금에 가입했는지 여부를 나타냅니다.

1. **고급 설정**을 확장하고 다음과 같이 필드를 채웁니다.

    고급 설정|값
    ------|------
    기본 메트릭| AUC_weighted 
    종료 기준| 이러한 조건 중 하나라도 충족되면 학습 작업이 완료되지 않은 상태로 종료됩니다. <br> *학습 작업 시간(분)* : 5  <br> *최대 반복 횟수*: 10 
    전처리| 자동화된 기계 학습을 통해 전처리를 수행할 수 있도록 설정합니다. 여기에는 자동 데이터 정리, 준비 및 가상 기능 생성을 위한 변환이 포함됩니다.
    유효성 검사| K 접기 교차 유효성 검사를 선택하고, 교차 유효성 검사 횟수로 **2**를 선택합니다. 
    동시성| 최대 동시 반복 횟수로 **5**를 선택합니다.

   >[!NOTE]
   > 이 실험에서는 메트릭 또는 반복당 최대 코어 임계값을 설정하지 않습니다. 또한 알고리즘 테스트를 차단하지 않습니다.

1. **시작**을 선택하여 실험을 실행합니다.

   실험이 시작되면 맨 위에 상태 메시지가 표시된 빈 화면이 나타납니다.

실험 준비 프로세스는 몇 분 정도 걸립니다. 해당 프로세스가 완료되면, 상태 메시지가 **실행 중**으로 바뀝니다.

##  <a name="view-experiment-details"></a>실험 세부 정보 보기

실험이 진행됨에 따라 화면의 **반복 차트**와 **반복 목록**이 실행되는 다른 반복(모델)으로 업데이트됩니다. 반복 목록은 메트릭 점수 순서대로 정렬됩니다. 기본적으로 **AUC_weighted** 메트릭에 따라 가장 높은 점수를 획득한 모델이 목록 맨 위에 표시됩니다.

>[!WARNING]
> 각 파이프라인에서 실행을 완료하면 학습 작업이 완료되며, 이 과정은 몇 분 정도 걸립니다.

[![실행 세부 정보 대시보드](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-the-model"></a>모델 배포

작업 영역 방문 페이지에서 자동화된 기계 학습을 사용하면 최적 모델을 웹 서비스로 배포하여 새 데이터를 예측하고 잠재적인 기회 영역을 파악할 수 있습니다. 이 실험에서 배포는 이제 금융 기관이 잠재적 정기 예금 고객을 식별할 수 있는 반복적이고 확장 가능한 솔루션을 확보했다는 것을 의미합니다.

**AUC_weighted** 메트릭에 따라 이 실험에서 가장 적합한 모델은 **VotingEnsemble**입니다.  이 모델을 배포할 것이며, 배포 시간이 약 20분 정도 걸립니다.

1. **실행 세부 정보** 페이지에서 오른쪽 위에 있는 **최적 모델 배포** 단추를 선택합니다.

1. **최적 모델 배포** 창을 다음과 같이 채웁니다.

    필드| 값
    ----|----
    배포 이름| my-automl-deploy
    배포 설명| 첫 번째 자동화된 기계 학습 실험 배포
    채점 스크립트| 자동 생성
    환경 스크립트| 자동 생성
    
1. **배포**를 선택합니다.

    배포가 성공적으로 완료되면 배포 완료 메시지가 표시됩니다.
    
이제 예측을 생성하는 운영 웹 서비스가 생겼습니다.

## <a name="clean-up-resources"></a>리소스 정리

배포 파일은 데이터 및 실험 파일보다 크기 때문에 더 많은 저장 비용이 발생합니다. 작업 영역 및 실험 파일을 유지하려는 경우에는 배포 파일만 삭제하여 계정 비용을 최소화할 수 있습니다. 또는 어떤 파일도 사용할 계획이 없으면 전체 리소스 그룹을 삭제합니다.  

### <a name="delete-the-deployment-instance"></a>배포 인스턴스 삭제

다른 자습서에서 사용하거나 탐색할 수 있도록 리소스 그룹과 작업 영역을 유지하려면 Azure Portal에서 배포 인스턴스만 삭제합니다. 

1. [Azure 포털](https://portal.azure.com//)로 이동합니다. 작업 영역으로 이동한 다음, 왼쪽 **자산** 창 아래에서 **배포**를 선택합니다. 

1. 삭제하려는 배포를 선택하고 **삭제**를 선택합니다. 

1. **계속**을 선택합니다.

### <a name="delete-the-resource-group"></a>리소스 그룹 삭제

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 자동화된 기계 학습 자습서에서는 작업 영역 방문 페이지를 사용하여 분류 모델을 만들고 배포했습니다. 자세한 내용과 다음 단계는 아래 문서를 참조하세요.

> [!div class="nextstepaction"]
> [웹 서비스 사용](how-to-consume-web-service.md)

+ [전처리](how-to-create-portal-experiments.md#preprocess)에 대한 자세한 정보
+ [데이터 프로파일링](how-to-create-portal-experiments.md#profile)에 대한 자세한 정보
+ [자동화된 기계 학습](concept-automated-ml.md)에 대한 자세한 정보

>[!NOTE]
> 이 은행 마케팅 데이터 세트는 [Creative Commons(CCO: Public Domain) 라이선스](https://creativecommons.org/publicdomain/zero/1.0/)에서 사용이 허가됩니다. 데이터베이스의 개별 콘텐츠에 대한 모든 권한은 [데이터베이스 콘텐츠 라이선스](https://creativecommons.org/publicdomain/zero/1.0/)를 따르며 [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset)에서 사용할 수 있습니다. 이 데이터 세트는 원래 [UCI Machine Learning 데이터베이스](https://archive.ics.uci.edu/ml/datasets/bank+marketing) 내에 제공됩니다.<br><br>
> [Moro 외, 2014] S. Moro, P. Cortez 및 P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22-31, 2014년 6월.
