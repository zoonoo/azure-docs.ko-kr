---
title: 처음으로 자동화된 기계 학습 실험 만들어보기
titleSuffix: Azure Machine Learning service
description: Azure Portal에서 자동화된 기계 학습을 사용하여 분류 모델을 학습시키고 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 08/14/2019
ms.openlocfilehash: e53cd92a9dfd8f823918fb38e14c2b73c2ce071f
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534405"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>자습서: 자동화된 기계 학습을 사용하여 처음으로 분류 모델 만들어보기

이 자습서에서는 코드를 한 줄도 작성하지 않고 Azure Portal(미리 보기)에서 처음으로 자동화된 기계 학습 실험을 만드는 방법에 대해 알아봅니다. 이 예제에서는 클라이언트가 금융 기관의 정기 예금을 신청할 것인지 예측하는 분류 모델을 만듭니다.

Azure Machine Learning Service 및 Azure Portal의 자동화된 기계 학습 기능을 사용하여 자동화된 기계 학습 프로세스를 시작합니다. 알고리즘 선택 및 하이퍼 매개 변수 튜닝은 자동으로 수행됩니다. 자동화된 Machine Learning 기술은 사용자의 기준을 기반으로 최상의 모델을 발견할 때까지 알고리즘과 하이퍼 매개 변수의 여러 조합을 반복합니다.

이 자습서에서는 다음 작업에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Machine Learning Service 작업 영역 구성
> * 실험 만들기
> * 분류 모델 자동 학습
> * 학습 실행 세부 정보 보기
> * 모델을 배포합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 아직 없는 경우 [무료 계정](https://aka.ms/AMLFree)을 만듭니다.

* [**bankmarketing_train.csv** ](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) 데이터 파일을 다운로드합니다. **y** 열은 고객이 정기 예금에 가입했는지 여부를 나타내며, 뒷부분에서 이 자습서의 예측에 대한 대상 열로 식별됩니다. 

## <a name="create-a-workspace"></a>작업 영역 만들기

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-an-experiment"></a>실험 만들기

다음 단계는 데이터 선택부터 기본 메트릭 및 모델 유형 선택까지 실험 설정을 안내합니다. 

1. 작업 영역의 왼쪽 창으로 이동합니다. **작성(미리 보기**) 섹션에서 **자동화된 기계 학습**을 선택합니다.
자동화된 Machine Learning을 사용하여 처음으로 실험을 만드는 것이기 때문에 **자동화된 Machine Learning 시작** 화면이 표시됩니다.

    ![Azure Portal 탐색 창](media/tutorial-1st-experiment-automated-ml/nav-pane.png)



1. **실험 만들기**를 선택합니다. 실험 이름으로 **my-1st-automl-experiment**를 입력합니다.

1. **새 컴퓨팅 만들기**를 선택하고 이 실험의 컴퓨팅 컨텍스트를 구성합니다.

    필드| 값
    ---|---
    컴퓨팅 이름| 컴퓨팅 컨텍스트를 식별하는 고유한 이름을 입력합니다. 이 예제에서는 **automl-compute**를 사용합니다.
    가상 머신 크기| 컴퓨팅에 사용할 가상 머신 크기를 선택합니다. 여기서는 **Standard_DS12_V2**를 사용합니다.
    추가 설정| *최소 노드*: 1. 데이터 프로파일링을 사용하려면 하나 이상의 노드가 있어야 합니다. <br> *최대 노드*: 6. 

    새 컴퓨팅을 만들려면 **만들기**를 선택합니다. 이 작업은 몇 분 정도 걸립니다. 

    만들기가 완료되면 드롭다운 목록에서 새 컴퓨팅을 선택하고 **다음**을 선택합니다.

    >[!NOTE]
    >이 자습서에서는 기본 스토리지 계정 및 새 컴퓨팅으로 만든 컨테이너를 사용합니다. 이 계정과 컨테이너가 양식에 자동으로 채워집니다.

1. **업로드**를 선택하고, 로컬 컴퓨터에서 **bankmarketing_train.csv** 파일을 선택하여 기본 컨테이너에 업로드합니다. 공개 미리 보기는 로컬 파일 업로드 및 Azure Blob 스토리지 계정만 지원합니다. 업로드가 완료되면 목록에서 해당 파일을 선택합니다. 

    [![데이터 파일 선택](media/tutorial-1st-experiment-automated-ml/select-data-file.png)](media/tutorial-1st-experiment-automated-ml/select-data-file-expanded.png#lightbox)

1. **미리 보기** 탭에서는 이 실험에 대한 데이터를 추가로 구성할 수 있습니다.

    **미리 보기** 탭에서 데이터에 헤더가 포함되도록 표시합니다. 서비스는 기본적으로 학습을 위한 모든 기능(열)을 포함합니다. 이 예제에서는 오른쪽으로 스크롤하여 **day_of_week** 기능을 **무시**합니다.

    ![미리 보기 탭 구성](media/tutorial-1st-experiment-automated-ml/preview-tab-config.gif)


    >[!NOTE]
    > 데이터 프로파일링은 최소 노드가 0인 컴퓨팅에는 사용할 수 없습니다.

1. 예측 작업으로 **분류**를 선택합니다.

1. 대상 열로 **y**를 선택합니다. 이 열에서 예측을 수행할 것입니다. 이 열은 클라이언트가 정기 예금에 가입했는지 여부를 나타냅니다.

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

   실험이 시작되면 빈 **실행 세부 정보** 화면이 나타나고 맨 위에 다음과 같은 상태가 표시됩니다. 

      ![실행 준비 중](media/tutorial-1st-experiment-automated-ml/run-preparing.png)
      
실험 준비 프로세스는 몇 분 정도 걸립니다. 프로세스가 완료되면 상태 메시지가 **실행을 실행하는 중**으로 변경됩니다.

##  <a name="view-experiment-details"></a>실험 세부 정보 보기

실험이 진행됨에 따라, **실행 세부 정보** 화면의 반복 차트와 목록이 실행되는 다른 반복(모델)으로 업데이트됩니다. 반복 목록은 메트릭 점수 순서대로 정렬됩니다. 기본적으로 **AUC_weighted** 메트릭에 따라 가장 높은 점수를 획득한 모델이 목록 맨 위에 표시됩니다.

>[!TIP]
> 각 파이프라인에서 실행을 완료하면 학습 작업이 완료되며, 이 과정은 몇 분 정도 걸립니다.

[![실행 세부 정보 대시보드](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-the-model"></a>모델 배포

Azure Portal에서 자동화된 기계 학습을 사용하면 가장 적합한 모델을 웹 서비스로 배포하여 새 데이터를 예측하고 잠재적인 기회 영역을 파악할 수 있습니다. 이 실험에서 배포는 이제 금융 기관이 잠재적 정기 예금 고객을 식별할 수 있는 반복적이고 확장 가능한 솔루션을 확보했다는 것을 의미합니다.

**AUC_weighted** 메트릭에 따라 이 실험에서 가장 적합한 모델은 **VotingEnsemble**입니다.  이 모델을 배포할 것이며, 배포 시간이 약 20분 정도 걸립니다.

1. **실행 세부 정보** 페이지에서 **최적 모델 배포** 단추를 선택합니다.

1. **최적 모델 배포** 창을 다음과 같이 채웁니다.

    필드| 값
    ----|----
    배포 이름| my-automl-deploy
    배포 설명| 첫 번째 자동화된 기계 학습 실험 배포
    채점 스크립트| 자동 생성
    환경 스크립트| 자동 생성
    
1. **배포**를 선택합니다.

    배포가 성공적으로 완료되면 다음 메시지가 표시됩니다.

    ![배포 완료](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png)
    
    이제 예측을 생성하는 운영 웹 서비스가 생겼습니다.

## <a name="clean-up-resources"></a>리소스 정리

배포 파일은 데이터 및 실험 파일보다 크기 때문에 더 많은 저장 비용이 발생합니다. 작업 영역 및 실험 파일을 유지하려는 경우에는 배포 파일만 삭제하여 계정 비용을 최소화할 수 있습니다. 또는 어떤 파일도 사용할 계획이 없으면 전체 리소스 그룹을 삭제합니다.  

### <a name="delete-the-deployment-instance"></a>배포 인스턴스 삭제

다른 자습서에서 사용하거나 탐색할 수 있도록 리소스 그룹과 작업 영역을 유지하려면 Azure Portal에서 배포 인스턴스만 삭제합니다. 

1. 왼쪽의 **자산** 창으로 이동하여 **배포**를 선택합니다. 

1. 삭제하려는 배포를 선택하고 **삭제**를 선택합니다. 

1. **계속**을 선택합니다.

### <a name="delete-the-resource-group"></a>리소스 그룹 삭제

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 자동화된 기계 학습 자습서에서는 Azure Portal를 사용하여 분류 모델을 만들고 배포했습니다. 자세한 내용과 다음 단계는 아래 문서를 참조하세요.

> [!div class="nextstepaction"]
> [웹 서비스 사용](how-to-consume-web-service.md)


+ [전처리](how-to-create-portal-experiments.md#preprocess)에 대한 자세한 정보
+ [데이터 프로파일링](how-to-create-portal-experiments.md#profile)에 대한 자세한 정보
+ [자동화된 기계 학습](concept-automated-ml.md)에 대한 자세한 정보

>[!NOTE]
> 이 은행 마케팅 데이터 세트는 [Creative Commons(CCO: Public Domain) 라이선스](https://creativecommons.org/publicdomain/zero/1.0/)에서 사용이 허가됩니다. 데이터베이스의 개별 콘텐츠에 대한 모든 권한은 [데이터베이스 콘텐츠 라이선스](https://creativecommons.org/publicdomain/zero/1.0/)를 따르며 [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset)에서 사용할 수 있습니다. 이 데이터 세트는 원래 [UCI Machine Learning 데이터베이스](https://archive.ics.uci.edu/ml/datasets/bank+marketing) 내에 제공됩니다.<br><br>
> 다음 논문을 참조하세요. <br> [Moro 외, 2014] S. Moro, P. Cortez 및 P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22-31, 2014년 6월.
