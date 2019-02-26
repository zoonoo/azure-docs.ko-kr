---
title: '자습서 1: 신용 위험 예측'
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio의 신용 위험 평가에 대한 예측 분석 솔루션을 만드는 방법을 보여 주는 구체적인 자습서입니다. 이 자습서는 3부로 구성된 자습서 시리즈 중 제1부입니다.  작업 영역을 만들고, 데이터를 업로드하고, 실험을 만드는 방법을 보여 줍니다.
keywords: 신용 위험, 예측 분석 솔루션, 위험 평가
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: c067b0e6a85e0b5c4bd4cbb582de13bb1bc87774
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453616"
---
# <a name="tutorial-1-predict-credit-risk---azure-machine-learning-studio"></a>자습서 1: 신용 위험 예측 - Azure Machine Learning Studio

이 자습서에서는 예측 분석 솔루션을 개발하는 프로세스를 자세히 살펴봅니다. Machine Learning Studio에서 간단한 모델을 개발합니다.  그런 다음, 모델을 Azure Machine Learning 웹 서비스로 배포합니다.  이 배포된 모델은 새 데이터를 사용하여 예측을 수행할 수 있습니다. 이 자습서는 **3부로 구성된 자습서 시리즈 중 제1부**입니다.

신용대출 지원 시 애플리케이션에서 제공한 정보를 기반으로 개인의 신용 위험을 예측해야 한다고 가정합니다.  

신용 위험 평가는 복잡한 문제이지만, 이 자습서에서는 약간 간소화하여 살펴보겠습니다. 이 신용 위험 평가는 Microsoft Azure Machine Learning Studio를 사용하는 예측 분석 솔루션을 만드는 방법의 예로 사용합니다. 이 솔루션에는 Azure Machine Learning Studio 및 Machine Learning 웹 서비스가 사용됩니다.  

이 3부로 구성된 자습서에서는 공개적으로 사용 가능한 신용 위험 데이터부터 시작합니다.  그런 다음, 예측 모델을 개발하고 학습합니다.  마지막으로 모델을 웹 서비스로 배포합니다.

1부 자습서에서 수행하는 작업은 다음과 같습니다. 
 
> [!div class="checklist"]
> * Machine Learning Studio 작업 영역 만들기
> * 기존 데이터 업로드
> * 실험 만들기

그런 다음, 이 실험을 사용하여 [2부 자습서에서 모델을 학습](tutorial-part2-credit-risk-train.md)하고, [3부 자습서에서 배포](tutorial-part3-credit-risk-deploy.md)할 수 있습니다.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="prerequisites"></a>필수 조건

이 자습서에서는 이전에 Machine Learning Studio를 한 번 이상 사용한 경험이 있으며, 기계 학습 개념에 대해 어느 정도 이해하고 있다고 가정합니다. 그러나 어느 쪽이든 전문가는 아니라고 가정합니다.

**Azure Machine Learning Studio**를 사용한 경험이 없는 경우 [Azure Machine Learning Studio에서 첫 번째 데이터 과학 실험 만들기](create-experiment.md) 빠른 시작을 시작하는 것이 좋습니다. 빠른 시작을 사용하면 Machine Learning Studio를 처음으로 익힐 수 있습니다. 이 자습서는 실험에 모듈을 끌어 놓고 서로 연결하고 실험을 실행하고 결과를 확인하는 방법의 기본 사항을 보여 줍니다.


> [!TIP] 
> 이 자습서에서 개발한 실험의 작업 복사본은 [Azure AI Gallery](https://gallery.azure.ai)에서 찾을 수 있습니다. **[자습서 - 신용 위험 예측](https://gallery.azure.ai/Experiment/Walkthrough-Credit-risk-prediction-1)** 으로 이동하고, **Studio에서 열기**를 클릭하여 실험의 복사본을 Machine Learning Studio 작업 영역으로 다운로드합니다.
> 


## <a name="create-a-machine-learning-studio-workspace"></a>Machine Learning Studio 작업 영역 만들기

Machine Learning Studio를 사용하려면 Microsoft Azure Machine Learning Studio 작업 영역이 있어야 합니다. 이 작업 영역에는 실험을 만들고 관리, 게시하는 데 필요한 도구가 들어 있습니다.  

작업 영역을 만들려면 [Azure Machine Learning Studio 작업 영역 만들기 및 공유](create-workspace.md)를 참조하세요.

작업 영역을 만든 후 Machine Learning Studio([https://studio.azureml.net/Home](https://studio.azureml.net/Home))를 엽니다. 둘 이상의 작업 영역이 있는 경우 창의 오른쪽 위 모서리에 있는 도구 모음에서 작업 영역을 선택할 수 있습니다.

![Studio에서 작업 영역 선택](./media/tutorial-part1-credit-risk/open-workspace.png)

> [!TIP]
> 작업 영역의 소유자인 경우 다른 사용자를 작업 영역에 초대하여 작업 중인 실험을 공유할 수 있습니다. Machine Learning Studio의 **SETTINGS** 페이지에서 이 작업을 할 수 있습니다. 각 사용자에 대한 Microsoft 계정 또는 조직 계정만 있으면 됩니다.
> 
> **설정** 페이지에서 **사용자**를 클릭한 다음 창의 아래쪽에 있는 **더 많은 사용자 초대**를 클릭합니다.
> 

## <a name="upload"></a>기존 데이터 업로드

신용 위험에 대한 예측 모델을 개발하려면 모델을 학습하고 테스트하는 데 사용할 수 있는 데이터가 필요합니다. 이 자습서에서는 UC Irvine Machine Learning 리포지토리의 "UCI Statlog(독일 신용 데이터) 데이터 세트"를 사용합니다. 이 데이터 집합은   
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

**german.data**라는 파일을 사용합니다. 로컬 하드 드라이브로 이 파일을 다운로드하세요.  

**german.data** 데이터 세트에는 지난 1000명의 신용대출 신청자에 대한 20개 변수 행이 포함되어 있습니다. 이러한 20개 변수는 각 대출 신청자에 대한 특성을 파악하는 기능의 데이터 세트(*기능 벡터*)를 나타냅니다. 각 행의 추가 열은 신청자의 계산된 신용 위험을 나타내며, 700명의 신청자는 신용 위험이 낮은 것으로, 300명의 신청자는 위험이 높은 것으로 파악되었습니다.

UCI 웹 사이트에서는 이 데이터에 대한 기능 벡터의 특성을 설명합니다. 이 데이터에는 재무 정보, 신용 기록, 고용 상태 및 개인 정보가 포함됩니다. 각 지원자에 대해 신용 위험이 낮은지 아니면 높은지 여부를 나타내는 이진 등급이 제공되었습니다. 

이 데이터를 사용하여 예측 분석 모델을 학습합니다. 완료되면 모델에서 새 개인에 대한 기능 벡터를 허용하여 신용 위험이 낮거나 높은지 예측할 수 있어야 합니다.  

흥미로운 변형이 있습니다.

UCI 웹 사이트의 데이터 세트에 대한 설명은 개인의 신용 위험을 잘못 분류하는 경우 발생하는 비용에 대해 언급합니다.
모델이 실제로 신용 위험이 낮은 개인에 대해 신용 위험을 높게 예측하는 경우 모델은 오분류를 만들었습니다.

하지만 역방향 오분류(모델이 실제로 신용 위험이 높은 개인에 대해 신용 위험을 낮게 예측하는 경우)는 금융 기관에 대해 5배의 비용이 더 듭니다.

따라서 이 후자 오분류 유형의 비용이 다른 방법으로 오분류하는 것보다 5배 더 높도록 모델을 학습하려고 합니다.

실험에서 모델을 학습할 때 이를 수행하는 간단한 한 가지 방법은 신용 위험이 높은 사람을 나타내는 항목을 복제(5회)하는 것입니다. 

그런 다음 모델이 실제로 위험이 높을 때 신용 위험을 낮게 잘못 분류하는 경우 모델은 각 복제마다 한 번씩 동일한 오분류를 5번 수행합니다. 그러면 학습 결과에서 이 오류의 비용이 증가됩니다.


### <a name="convert-the-dataset-format"></a>데이터 세트 형식 변환

원래 데이터 세트는 공백으로 구분된 형식을 사용합니다. Machine Learning Studio는 CSV(쉼표로 구분된 값) 파일에서 더 효율적으로 작동하므로 공백을 쉼표로 바꿔서 데이터 세트를 변환합니다.  

여러 가지 방법으로 이 데이터를 변환할 수 있습니다. 한 가지 방법은 다음 Windows PowerShell 명령을 사용하는 것입니다.   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

또 다른 방법은 Unix sed 명령을 사용하는 것입니다.  

    sed 's/ /,/g' german.data > german.csv  

두 경우 모두 쉼표로 구분된 데이터 버전을 실험에 사용할 수 있는 **german.csv**라는 파일에 만들었습니다.

### <a name="upload-the-dataset-to-machine-learning-studio"></a>Machine Learning Studio에 데이터 세트 업로드

데이터가 CSV 형식으로 변환되면 이를 Machine Learning Studio에 업로드해야 합니다. 

1. Machine Learning Studio 홈페이지([https://studio.azureml.net](https://studio.azureml.net))를 엽니다. 

2. 창의 왼쪽 상단 모서리에 있는 ![메뉴](./media/tutorial-part1-credit-risk/menu.png)를 클릭하고 **Azure Machine Learning**을 클릭하고 **Studio**를 선택한 다음 로그인합니다.

3. 창 아래쪽에서 **+새로 만들기** 를 클릭합니다.

4. **데이터 세트**를 선택합니다.

5. **로컬 파일에서**를 선택합니다.

    ![로컬 파일에서 데이터 세트 추가](./media/tutorial-part1-credit-risk/add-dataset.png)

6. **새 데이터 세트 업로드** 대화 상자에서 [찾아보기]를 클릭하고 만든 **german.csv** 파일을 찾습니다.

7. 데이터 세트의 이름을 입력합니다. 이 자습서에서는 이 데이터 세트를 "UCI 독일 신용 카드 데이터"라고 합니다.

8. 데이터 형식으로 **머리글 없는 일반 CSV 파일(.nh.csv)** 을 선택합니다.

9. 원하는 경우 설명을 추가합니다.

10. **확인** 확인 표시를 클릭합니다.  

    ![데이터 세트 업로드](./media/tutorial-part1-credit-risk/upload-dataset.png)

그러면 데이터가 실험에 사용할 수 있는 데이터 세트 모듈에 업로드됩니다.

스튜디오 창 왼쪽의 **데이터 세트** 탭을 클릭하여 스튜디오에 업로드된 데이터 세트를 관리할 수 있습니다.

![데이터 세트 관리](./media/tutorial-part1-credit-risk/dataset-list.png)

다른 데이터 형식을 실험으로 가져오는 방법에 대한 자세한 내용은 [Azure Machine Learning Studio로 학습 데이터 가져오기](import-data.md)를 참조하세요.

## <a name="create-an-experiment"></a>실험 만들기

이 자습서의 다음 단계는 업로드한 데이터 세트를 사용하는 Machine Learning Studio에서 실험을 만드는 것입니다.  

1. 스튜디오의 창 하단에 있는 **+새로 만들기**를 클릭합니다.
1. **실험**을 선택하고 "빈 실험"을 선택합니다. 

    ![새로운 실험 만들기](./media/tutorial-part1-credit-risk/create-new-experiment.png)


1. 캔버스 위쪽에서 기본 실험 이름을 선택하고 이를 의미 있는 이름으로 바꿉니다.

    ![실험 이름 바꾸기](./media/tutorial-part1-credit-risk/rename-experiment.png)

   > [!TIP]
   > **속성** 창에 실험에 대한 **요약** 및 **설명**을 입력하는 것이 좋습니다. 이러한 속성을 사용하면 실험을 문서화할 수 있어 나중에 실험을 참조하는 모든 사용자가 실험의 목표와 방법론을 이해할 수 있습니다.
   > 
   > ![실험 속성](./media/tutorial-part1-credit-risk/experiment-properties.png)
   > 

1. 실험 캔버스의 왼쪽에 있는 모듈 팔레트에서 **저장된 데이터 세트**를 확장합니다.
1. **내 데이터 세트** 아래에서 만든 데이터 세트를 찾고 캔버스로 끌어 옵니다. 팔레트 위의 **검색** 상자에 이름을 입력하여 데이터 세트를 찾을 수도 있습니다.  

    ![실험에 데이터 세트 추가](./media/tutorial-part1-credit-risk/add-dataset-to-experiment.png)


### <a name="prepare-the-data"></a>데이터 준비

데이터의 처음 100개 행 및 전체 데이터 세트에 대한 일부 통계 정보를 볼 수 있습니다. 데이터 세트(맨 아래에 작은 원)의 출력 포트를 클릭하고 **시각화**를 선택합니다.  

데이터 파일에는 열 제목이 없으므로 스튜디오에서 일반적인 제목(Col1, Col2, *등*)을 제공했습니다. 적합한 제목은 모델 작성 시 필수 사항은 아니지만 제목이 있으면 실험에서 데이터를 더 쉽게 사용할 수 있습니다. 또한 최종적으로 이 모델을 웹 서비스에 게시할 때 제목을 사용하면 서비스 사용자에 대한 열을 식별할 수 있습니다.  

열 제목은 [메타데이터 편집][edit-metadata] 모듈을 사용하여 추가할 수 있습니다.

[메타데이터 편집][edit-metadata] 모듈을 사용하여 데이터 세트와 연결된 메타데이터를 변경합니다. 이 경우 이를 사용하여 열 머리글에 더 친숙한 이름을 제공합니다. 

[메타데이터 편집][edit-metadata]을 사용하려면 먼저 수정할 열(이 경우 모두)을 지정합니다. 다음으로 해당 열에서 수행할 작업(이 경우 열 머리글 변경)을 지정합니다.

1. 모듈 팔레트에서 **검색** 상자에 "metadata"를 입력합니다. [메타데이터 편집][edit-metadata]이 모듈 목록에 나타납니다.

1. [메타데이터 편집][edit-metadata] 모듈을 클릭하고 캔버스로 끌어서 이전에 추가한 데이터 세트 아래에 놓습니다.

1. 데이터 세트를 [메타데이터 편집][edit-metadata]에 연결합니다. 데이터 세트의 출력 포트(데이터 세트 맨 아래의 작은 원)를 클릭하여 [메타데이터 편집][edit-metadata]의 입력 포트(모듈 맨 위의 작은 원)로 끌어 놓은 다음 마우스 단추를 놓습니다. 데이터 세트와 모듈은 캔버스에서 이동해도 연결 상태를 유지합니다.
 
    이제 실험이 다음과 같이 표시됩니다.  

    ![메타데이터 편집 추가](./media/tutorial-part1-credit-risk/experiment-with-edit-metadata-module.png)

    빨간색 느낌표는 이 모듈에 대한 속성이 아직 설정되지 않았음을 나타냅니다. 이 작업은 다음에 수행합니다.

    > [!TIP]
    > 모듈을 두 번 클릭하고 텍스트를 입력하여 모듈에 주석을 추가할 수 있습니다. 그러면 모듈이 실험에서 수행하는 내용을 한눈에 볼 수 있습니다. 이 경우 [메타데이터 편집][edit-metadata] 모듈을 두 번 클릭하고 주석 "열 제목 추가"를 입력합니다. 캔버스의 다른 곳을 클릭하여 텍스트 상자를 닫습니다. 주석을 표시하려면 모듈에서 아래쪽 화살표를 클릭합니다.
    > 
    > ![주석이 추가된 메타데이터 모듈 편집](./media/tutorial-part1-credit-risk/edit-metadata-with-comment.png)
    > 

1. [메타데이터 편집][edit-metadata]을 선택한 다음 캔버스 오른쪽의 **속성** 창에서 **열 선택기 시작**을 클릭합니다.

1. **열 선택** 대화 상자의 **사용 가능한 열**에서 모든 행을 선택하고 >를 클릭하여 **선택한 열**로 이동시킵니다.
   대화 상자는 다음과 같습니다.

   ![모든 열이 선택된 열 선택기](./media/tutorial-part1-credit-risk/select-columns.png)


1. **확인** 확인 표시를 클릭합니다.

1. **속성** 창으로 돌아가서 **새 열 이름** 매개 변수를 찾습니다. 이 필드에 데이터 세트의 21개 열에 대한 이름 목록을 쉼표로 구분하여 열 순서로 입력합니다. UCI 웹 사이트의 데이터 세트 설명서에서 열 이름을 얻거나 편의를 위해 다음 목록을 복사하여 붙여넣을 수 있습니다.  

  ```   
  Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
  ```

  속성 창의 모양은 다음과 같습니다.

  ![메타데이터 편집에 대한 속성](./media/tutorial-part1-credit-risk/edit-metadata-properties.png)

  > [!TIP]
  > 열 제목을 확인하려면 실험을 실행(실험 캔버스 아래의 **실행** 클릭)합니다. 실행이 완료되면([메타데이터 편집][edit-metadata]에 녹색 확인 표시가 나타남) [메타데이터 편집][edit-metadata] 모듈의 출력 포트를 클릭하고 **시각화**를 선택합니다. 같은 방법으로 모듈의 출력을 표시하여 실험을 통해 데이터 진행률을 확인할 수 있습니다.
  > 
  > 

### <a name="create-training-and-test-datasets"></a>학습 및 테스트 데이터 세트 만들기

데이터 중 일부는 모델을 학습하는 데 필요하고, 일부는 테스트하는 데 필요합니다.
따라서 실험의 다음 단계에서는 데이터 세트를 두 개의 개별 데이터 세트(모델 학습용 및 테스트용)로 분할합니다.

이렇게 하려면 [데이터 분할][split] 모듈을 사용합니다.  

1. [데이터 분할][split] 모듈을 찾고 캔버스로 끌어서 [메타데이터 편집][edit-metadata] 모듈에 연결합니다.

1. 기본적으로 분할 비율은 0.5이고 **무작위 분할** 매개 변수가 설정됩니다. 이는 데이터의 임의 절반이 [데이터 분할][split] 모듈의 한 포트를 통해 출력되고 나머지 절반은 다른 포트를 통해 출력됨을 의미합니다. 이러한 매개 변수와 **임의 초기값** 매개 변수를 조정하여 학습 및 테스트 데이터 간의 분할을 변경합니다. 이 예제에서는 있는 그대로 둡니다.
   
   > [!TIP]
   > **첫 번째 출력 데이터 세트의 행의 비율** 속성은 *왼쪽* 출력 포트를 통해 출력되는 데이터의 양을 결정합니다. 예를 들어 비율을 0.7로 설정하면 데이터의 70%가 왼쪽 포트를 통해 출력되고 30%는 오른쪽 포트를 통해 출력됩니다.  
   > 
   > 

1. [데이터 분할][split] 모듈을 두 번 클릭하고 주석 "데이터 분할 50% 학습/테스트"를 입력합니다. 

[데이터 분할][split] 모듈의 출력은 원하는 대로 사용할 수 있지만, 왼쪽 출력은 학습 데이터로 사용하고 오른쪽 출력은 테스트 데이터로 사용하도록 선택합니다.  

[이전 단계](tutorial-part1-credit-risk.md#upload)에서 언급한 대로 높은 신용 위험을 낮은 것으로 잘못 분류한 비용은 낮은 신용 위험을 높은 것으로 잘못 분류한 비용보다 5배 더 높습니다. 이를 설명하기 위해 이 비용 함수를 반영하는 새 데이터 세트를 생성합니다. 새 데이터 세트에서 위험이 높은 각 예제는 5번 복제되지만 위험이 낮은 각 예제는 복제되지 않습니다.   

이 복제는 다음과 같이 R 코드를 사용하여 수행할 수 있습니다.  

1. [R 스크립트 실행][execute-r-script] 모듈을 찾아 실험 캔버스로 끌어 놓습니다. 

1. [데이터 분할][split] 모듈의 왼쪽 출력 포트를 [R 스크립트 실행][execute-r-script] 모듈의 첫 번째 입력 포트("Dataset1")에 연결합니다.

1. [R 스크립트 실행][execute-r-script] 모듈을 두 번 클릭하고 주석 "비용 조정 설정"을 입력합니다.

1. **속성** 창에서 **R 스크립트** 매개 변수의 기본 텍스트를 삭제하고 다음 스크립트를 입력합니다.
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![R 스크립트 실행 모듈의 R 스크립트](./media/tutorial-part1-credit-risk/execute-r-script.png)

학습 및 테스트 데이터에 동일한 비용 조정이 포함되도록 [데이터 분할][split] 모듈의 각 출력에 대해 이와 같은 동일한 복제 작업을 수행해야 합니다. 이 작업을 수행하는 가장 쉬운 방법은 방금 만든 [R 스크립트 실행][execute-r-script] 모듈을 복제하고 [데이터 분할][split] 모듈의 다른 출력 포트에 연결하는 것입니다.

1. [R 스크립트 실행][execute-r-script] 모듈을 마우스 오른쪽 단추로 클릭하고 **복사**를 선택합니다.

1. 실험 캔버스를 마우스 오른쪽 단추로 클릭하고 **붙여넣기**를 선택합니다.

1. 새 모듈을 해당 위치로 끌어 놓은 다음 [데이터 분할][split] 모듈의 오른쪽 출력 포트를 이 새로운 [R 스크립트 실행][execute-r-script] 모듈의 첫 번째 입력 포트에 연결합니다. 

1. 캔버스 맨 아래에서 **실행**을 클릭합니다. 

> [!TIP]
> R 스크립트 실행 모듈의 복사본에는 원래 모듈과 같은 스크립트가 포함됩니다. 캔버스에서 모듈을 복사하고 붙여넣으면 복사본에서 원본의 모든 속성이 유지됩니다.  
> 
>

이제 실험은 다음과 같이 표시됩니다.

![분할 모듈 및 R 스크립트 추가](./media/tutorial-part1-credit-risk/experiment.png)

실험에서의 R 스크립트 사용에 대한 자세한 내용은 [R을 사용하여 실험 확장](extend-your-experiment-with-r.md)을 참조하세요.


## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 단계를 완료했습니다. 
 
> [!div class="checklist"]
> * Machine Learning Studio 작업 영역 만들기
> * 작업 영역에 기존 데이터 업로드
> * 실험 만들기

이제 이 데이터에 대한 모델을 학습하고 평가할 준비가 되었습니다.

> [!div class="nextstepaction"]
> [자습서 2 - 모델 학습 및 평가](tutorial-part2-credit-risk-train.md)

<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/