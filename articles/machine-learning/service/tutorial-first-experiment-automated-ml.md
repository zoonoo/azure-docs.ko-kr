---
title: 첫 번째 자동화 된 machine learning 실험 만들기
titleSuffix: Azure Machine Learning service
description: Azure Portal에서 자동화 된 machine learning을 사용 하 여 분류 모델을 학습 하 고 배포 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tsikiksr
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 07/23/2019
ms.openlocfilehash: 7c7d90d4ca1625edecc9d84e1ff7beec50032884
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444761"
---
# <a name="tutorial-train-and-deploy-a-classification-model-with-automated-machine-learning-in-the-azure-portal-preview"></a>자습서: Azure Portal에서 자동화 된 machine learning을 사용 하 여 분류 모델 학습 및 배포 (미리 보기)

이 자습서에서는 Azure Portal에서 첫 번째 자동화 된 machine learning 실험을 만드는 방법에 대해 알아봅니다. 이 예에서는 클라이언트가 은행에 약관을 구독 하는지 여부를 예측 하는 분류 모델을 만듭니다. 

서비스의 자동화 된 기계 학습 기능 및 Azure Portal를 사용 하 여 자동화 된 기계 학습 프로세스를 시작 하면 알고리즘 선택 및 하이퍼 매개 변수 튜닝이 자동으로 수행 됩니다. 자동화 된 기계 학습 기술은 조건에 따라 가장 적합 한 모델을 찾을 때까지 알고리즘 및 하이퍼 매개 변수의 여러 조합을 반복 하 여 코드를 한 줄도 작성할 필요가 없습니다.

이 자습서에서는 다음 작업에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Machine Learning Service 작업 영역 구성
> * 실험을 만듭니다.
> * 분류 모델을 자동으로 학습 합니다.
> * 학습 실행 세부 정보를 봅니다.
> * 모델을 배포합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

* **Bankmarketing_train** 데이터 파일입니다. [여기서](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) 다운로드하세요.

## <a name="create-a-workspace"></a>작업 영역 만들기

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-an-experiment"></a>실험 만들기

1. 작업 영역의 왼쪽 창으로 이동 합니다. **제작 (미리 보기)** 섹션에서 **자동화 된 Machine Learning** 를 선택 합니다.

    ![Azure Portal 탐색 창](media/tutorial-1st-experiment-automated-ml/nav-pane.png)

    자동화 된 Machine Learning의 첫 번째 실험에서 **자동화 된 Machine Learning 시작** 화면을 볼 수 있습니다. 

1.  **실험 만들기**를 선택 합니다. 그런 다음 실험 이름으로- **1-automl-실험** 을 입력 합니다.

1. **새 계산 만들기** 를 선택 하 고이 실험의 계산 컨텍스트를 구성 합니다.

    필드| 값
    ---|---
    컴퓨팅 이름| 계산 컨텍스트를 식별 하는 고유한 이름을 입력 합니다. 이 예에서는 **automl-compute**를 사용 합니다.
    가상 머신 크기| 계산에 대 한 가상 머신 크기를 선택 합니다. **Standard_DS12_V2**를 사용 합니다.
    추가 설정| *최소 노드*: 1. 데이터 프로 파일링을 사용 하도록 설정 하려면 하나 이상의 노드가 있어야 합니다. <br> *최대 노드*: 6. 

    새 계산 만들기를 시작 하려면 **만들기**를 선택 합니다. 몇 분 정도 걸릴 수 있습니다. 

    만들기가 완료 되 면 드롭다운에서 새 계산을 선택 하 고 **다음**을 선택 합니다.

1. 이 자습서에서는 기본 저장소 계정 및 새 계산으로 만든 컨테이너를 사용 합니다. 그러면 폼에 자동으로 채워집니다.

1. **업로드** 를 선택 하 여 로컬 컴퓨터에서 **bankmarketing_train** 파일을 선택 하 고 기본 컨테이너에 업로드 합니다. 공개 미리 보기는 로컬 파일 업로드 및 Azure Blob Storage 계정만 지원 합니다. 업로드가 완료 되 면 목록에서 파일을 선택 합니다. 

    [![데이터 파일 선택](media/tutorial-1st-experiment-automated-ml/select-data-file.png)](media/tutorial-1st-experiment-automated-ml/select-data-file-expanded.png#lightbox)

1. **미리 보기** 탭에서는이 실험을 위해 데이터를 추가로 구성할 수 있습니다.

    미리 보기 탭에서 데이터에 헤더가 포함 되어 있음을 표시 합니다. 서비스는 기본적으로 학습을 위한 모든 기능 (열)을 포함 합니다. 이 예에서는 오른쪽으로 스크롤하고 **day_of_week** 기능을 **무시** 합니다.

    ![미리 보기 탭 구성](media/tutorial-1st-experiment-automated-ml/preview-tab-config.gif)


    >[!NOTE]
    > 최소 노드가 0 인 계산에는 데이터 프로 파일링을 사용할 수 없습니다.

1. 예측 작업으로 **분류** 를 선택 합니다.

1. 예측을 수행 하려는 대상 열로 **y** 를 선택 합니다. 이 열은 클라이언트에서 용어 입금 여부를 구독 하는지 여부를 나타냅니다.

1. **고급 설정을** 확장 하 고 다음과 같이 필드를 채웁니다.

    고급 설정|값
    ------|------
    기본 메트릭| AUC_weighted 
    종료 조건| 이러한 조건 중 하나라도 충족 되 면 학습 작업은 전체 완료 전에 종료 됩니다. <br> *학습 작업 시간 (분)* : 5  <br> *최대 반복 횟수*: 10 
    바꾸면| 자동 기계 학습으로 전처리를 사용 하도록 설정 합니다. 여기에는 자동 데이터 정리, 준비 및 가상 기능 생성을 위한 변환이 포함 됩니다.
    유효성 검사| 교차 유효성 검사의 수를 선택 하 고 2를 선택 합니다. 
    동시성| 최대 동시 반복 횟수로 5를 선택 합니다.

   >[!NOTE]
   > 이 실험에서는 메트릭 또는 최대 반복 임계값을 설정 하지 않으며 알고리즘을 테스트 하지 않도록 차단 합니다.

1. **시작** 을 클릭 하 여 실험을 실행 합니다.

   실험을 시작 하면 맨 위에 다음과 같은 상태를 표시 하는 빈 **실행 세부 정보** 화면이 표시 됩니다. 실험 준비 프로세스는 몇 분 정도 걸립니다. 준비 프로세스가 완료 되 면 상태 메시지가 **실행 중**으로 변경 됩니다.

      ![준비 실행](media/tutorial-1st-experiment-automated-ml/run-preparing.png)

##  <a name="view-experiment-details"></a>실험 세부 정보 보기

실험이 진행 됨에 따라 **실행 정보** 화면에 반복 되는 여러 반복 (모델)이 포함 된 반복 차트 및 목록이 업데이트 됩니다. 반복 목록은 메트릭 점수를 기준으로 정렬 되며, 기본적으로 **AUC_weighted** 메트릭에 따라 가장 높은 점수를 매기는 모델은 목록의 맨 위에 있습니다.

>[!TIP]
> 각 파이프라인이 실행을 완료 하는 데 몇 분 정도 걸릴 수 있습니다.

[![실행 세부 정보 대시보드](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-model"></a>모델 배포

이 실험의 경우 **VotingEnsemble** 는 **AUC_weighted** 메트릭을 기반으로 하는 최상의 모델 이라고 간주 됩니다. Azure Portal에서 자동화 된 machine learning을 사용 하 여이 모델을 웹 서비스로 배포 하 여 한 번의 클릭으로 새 데이터를 예측할 수 있습니다. 

1. **실행 세부 정보** 페이지에서 **최적의 모델 배포** 단추를 선택 합니다.

1. 다음과 같이 **최적의 모델 배포** 창을 채웁니다.

    필드| 값
    ----|----
    배포 이름| 내 automl-배포
    배포 설명| 첫 번째 자동화 된 machine learning 실험 배포
    점수 매기기 스크립트| 자동
    환경 스크립트| 자동
    
1. **배포**를 선택합니다. 배포를 완료 하는 데 약 20 분이 걸릴 수 있습니다.

    배포가 성공적으로 완료 되 면 다음 메시지가 표시 됩니다.

    ![배포 완료](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png)
    
    이제 끝났습니다! 예측을 생성 하는 작업 웹 서비스가 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

배포 파일은 데이터 및 실험 파일 보다 크기 때문에 저장 하는 데 더 많은 비용이 듭니다. 계정에 대 한 비용을 최소화 하 고 작업 영역 및 실험 파일을 유지 하려는 경우에만 배포 파일을 삭제 합니다. 그렇지 않은 경우에는 파일을 사용 하지 않으려는 경우 전체 리소스 그룹을 삭제 합니다.  

### <a name="delete-deployment-instance"></a>배포 인스턴스 삭제

다른 자습서 및 탐색을 위해 리소스 그룹 및 작업 영역을 유지 하려는 경우 Azure Portal에서 배포 인스턴스만 삭제 합니다. 

1. 왼쪽의 **자산** 창으로 이동 하 고 **배포**를 선택 합니다. 

1. 삭제 하려는 배포를 선택 하 고 **삭제**를 선택 합니다. 

1. **계속**을 선택 합니다.

### <a name="delete-resource-group"></a>리소스 그룹 삭제

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 자동화 된 기계 학습 자습서에서는 Azure Portal를 사용 하 여 분류 모델을 만들고 배포 했습니다. 자세한 내용 및 다음 단계는 다음 문서를 참조 하세요.

+ [웹 서비스를 사용](how-to-consume-web-service.md)하는 방법
+ [전처리](how-to-create-portal-experiments.md#preprocess)에 대해 자세히 알아보세요.
+ [데이터 프로 파일링](how-to-create-portal-experiments.md#profile)에 대해 자세히 알아보세요.
+ [자동화 된 machine learning](concept-automated-ml.md)에 대해 자세히 알아보세요.

>[!NOTE]
> 이 은행 마케팅 데이터 집합은 [Creative Commons에서 사용할 수 있습니다 (CCO: 공용 도메인) 라이선스](https://creativecommons.org/publicdomain/zero/1.0/). 데이터베이스의 개별 콘텐츠에 대 한 모든 권한은 [데이터베이스 콘텐츠 라이선스](https://creativecommons.org/publicdomain/zero/1.0/) 에서 사용이 허가 되 고 [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset)에서 사용할 수 있습니다. 이 데이터 집합은 원래 [UCI Machine Learning 데이터베이스](https://archive.ics.uci.edu/ml/datasets/bank+marketing)내에서 사용할 수 있습니다.<br><br>
>  다음 작업을 인용 하세요. <br> [모 (모), 2014] S..-모 o, P. Cortez 및 Rita. Bank Telemarketing의 성공을 예측 하는 데이터 기반 방식입니다. 의사 결정 지원 시스템, Elsevier, 62:22-31, 6 월 2014