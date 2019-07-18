---
title: '자습서 3: 신용 위험 모델 배포'
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio의 신용 위험 평가에 대한 예측 분석 솔루션을 만드는 방법을 보여주는 구체적인 자습서입니다. 이 자습서는 3부로 구성된 자습서 시리즈 중 제3부입니다. 이 자습서에서는 모델을 웹 서비스로 배포하는 방법을 보여줍니다.
keywords: 신용 위험, 예측 분석 솔루션, 위험 평가, 배포, 웹 서비스
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 6cdccd54546296c85864f1588b71109ed8b8f79f
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620517"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio"></a>자습서 3: 신용 위험 모델 배포 - Azure Machine Learning Studio

이 자습서에서는 예측 분석 솔루션을 개발하는 과정을 자세히 살펴보겠습니다. Machine Learning Studio에서 간단한 모델을 개발합니다.  그런 다음, 모델을 Azure Machine Learning 웹 서비스로 배포합니다.  이렇게 배포된 모델은 새 데이터를 사용하여 예측을 수행할 수 있습니다. 이 자습서는 **3부로 구성된 자습서 시리즈 중 제3부**입니다.

신용대출 지원 시 애플리케이션에서 제공한 정보를 기반으로 개인의 신용 위험을 예측해야 한다고 가정합니다.  

신용 위험 평가는 복잡한 문제이지만, 이 자습서에서는 약간 간소화하여 살펴보겠습니다. 이 신용 위험 평가는 Microsoft Azure Machine Learning Studio를 사용하는 예측 분석 솔루션을 만드는 방법의 예로 사용합니다. 이 솔루션에는 Azure Machine Learning Studio 및 Machine Learning 웹 서비스가 사용됩니다. 

이 3부로 구성된 자습서에서는 공개적으로 사용 가능한 신용 위험 데이터부터 시작합니다.  그런 다음, 예측 모델을 개발하고 학습합니다.  마지막으로 모델을 웹 서비스로 배포합니다.

[이 자습서의 제1부](tutorial-part1-credit-risk.md)에서는 Machine Learning Studio 작업 영역을 만들고, 데이터를 업로드하고, 실험을 만들었습니다.

[이 자습서의 제2부](tutorial-part2-credit-risk-train.md)에서는 모델을 학습시키고 평가했습니다.

이번 파트에서는 다음과 같은 일을 합니다.

> [!div class="checklist"]
> * 배포 준비
> * 웹 서비스 배포
> * 웹 서비스 테스트
> * 웹 서비스 관리
> * 웹 서비스 액세스

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="prerequisites"></a>필수 조건

[이 자습서의 제2부](tutorial-part2-credit-risk-train.md)를 완료합니다.

## <a name="prepare-for-deployment"></a>배포 준비
여러분이 이 자습서에서 개발한 예측 모델을 다른 사용자가 사용할 수 있도록, 모델을 Azure에 웹 서비스로 배포할 수 있습니다.

지금까지는 모델을 학습하는 실험을 진행했습니다. 하지만 배포된 서비스는 더 이상 학습을 수행하지 않고 모델에 따라 사용자 입력의 점수를 매겨서 새 예측을 생성합니다. 그러므로 몇 가지 예측을 수행하여 ***학습*** 실험을 ***예측*** 실험으로 변환하겠습니다. 

배포 준비는 다음과 같은 3단계로 이루어진 프로세스입니다.  

1. 모델 중 하나 제거
1. 만든 *학습 실험*을 *예측 실험*으로 변환
1. 예측 실험을 웹 서비스로 배포

### <a name="remove-one-of-the-models"></a>모델 중 하나 제거

먼저 이 실험을 약간 줄여야 합니다. 현재 실험에는 두 개의 다른 모델이 있지만, 웹 서비스로 배포할 때는 한 모델만 사용하려고 합니다.  

향상된 트리 모델이 SVM 모델보다 우수한 것으로 확인되었다고 가정해 봅시다. 가장 먼저 수행할 작업은 [2클래스 Support Vector Machine][two-class-support-vector-machine] 모듈과 학습에 사용된 모듈을 제거하는 것입니다. 먼저 실험 캔버스 아래쪽에 있는 **다른 이름으로 저장**을 클릭하여 실험 복사본을 만들 수 있습니다.

다음 모듈을 삭제해야 합니다.  

* [2클래스 Support Vector Machine][two-class-support-vector-machine]
* 연결된 [모델 학습][train-model] 및 [모델 점수 매기기][score-model] 모듈
* [데이터 표준화][normalize-data](둘 다)
* [모델 평가][evaluate-model](모델 평가를 완료했으므로)

각 모듈을 선택하고 Delete 키를 누르거나 모듈을 마우스 오른쪽 단추로 클릭하고 **삭제**를 선택합니다. 

![지원 벡터 머신 모델을 제거하기 위해 삭제할 모듈 강조 표시](./media/tutorial-part3-credit-risk-deploy/publish3a.png)

모델은 다음과 같이 표시됩니다.

![지원 벡터 머신 모델이 삭제될 때의 결과 실험](./media/tutorial-part3-credit-risk-deploy/publish3.png)

이제 [2클래스 향상된 의사 결정 트리][two-class-boosted-decision-tree]를 사용하여 이 모듈을 배포할 준비가 되었습니다.

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>학습 실험에서 예측 실험으로 변환

이 모델을 배포하도록 준비하려면 이 학습 실험을 예측 실험으로 변환해야 합니다. 이는 세 가지 단계를 포함합니다.

1. 학습한 모델을 저장한 후 학습 모듈 바꾸기
1. 학습에만 필요한 모듈을 제거하여 실험 축소
1. 웹 서비스에서 입력을 수락할 위치 및 출력을 생성할 위치 정의

이 작업을 수동으로 수행할 수 있지만, 다행히도 실험 캔버스의 맨 아래에 있는 **웹 서비스 설정**을 클릭하여 세 단계를 모두 수행할 수 있습니다(및 **예측 웹 서비스** 옵션 선택).

> [!TIP]
> 학습 실험을 예측 실험으로 변환할 때 발생하는 결과를 자세히 알아보려면 [Azure Machine Learning Studio에서 배포 모델을 준비하는 방법](convert-training-experiment-to-scoring-experiment.md)을 참조하세요.

**웹 서비스 설정**을 클릭하면 다음과 같은 결과가 발생합니다.

* 학습한 모델이 단일 **학습된 모델** 모듈로 변환된 후 실험 캔버스 왼쪽의 모듈 팔레트에 저장됩니다(**학습된 모델** 아래에서 찾을 수 있음).
* 다음을 비롯하여 학습에 사용된 모듈은 제거됩니다.
  * [2클래스 향상된 의사 결정 트리][two-class-boosted-decision-tree]
  * [모델 학습][train-model]
  * [데이터 분할][split]
  * 테스트 데이터에 사용된 두 번째 [R 스크립트 실행][execute-r-script] 모듈
* 저장된 학습된 모델이 실험에 다시 추가됩니다.
* **웹 서비스 입력** 및 **웹 서비스 출력** 모듈이 추가됩니다(이러한 모듈은 사용자 데이터가 모델에 입력되는 위치, 반환된 데이터, 웹 서비스가 액세스되는 경우를 식별함).

> [!NOTE]
> 실험이 탭 아래에 두 부분으로 저장되고 실험 캔버스 위쪽에 추가되는 것을 볼 수 있습니다. 원래 학습 실험은 **학습 실험** 탭 아래에 표시되고, 새로 만든 예측 실험은 **예측 실험** 아래에 표시됩니다. 예측 실험은 웹 서비스로 배포됩니다.

이 특정 실험과 관련하여 한 가지 추가 단계를 수행해야 합니다.
두 개의 [R 스크립트 실행][execute-r-script] 모듈을 추가하여 데이터에 가중치 기능을 제공했습니다. 이 모듈은 학습 및 테스트에만 필요하므로 최종 모델에서는 삭제할 수 있습니다.
Machine Learning Studio는 [분할][split] 모듈을 제거할 때 [R 스크립트 실행][execute-r-script] 모듈을 하나 제거했습니다. 이제 다른 모듈을 제거하고 [메타데이터 편집기][metadata-editor]를 [채점 모델][score-model]에 직접 연결할 수 있습니다.    

이제 실험은 다음과 같이 표시됩니다.  

![학습된 모델 점수 매기기](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> 예측 실험에서 UCI 독일어 신용 카드 데이터의 데이터 세트를 남겨둔 이유가 궁금할 것입니다. 서비스에서는 원래 데이터 세트가 아니라 사용자 데이터를 사용하려고 하는데 모델에 원래 데이터 세트로 두는 이유는 무엇인가요?
> 
> 서비스에 원래 신용 카드 데이터가 필요하지 않은 것은 사실입니다. 하지만 열 수 및 숫자인 열 같은 정보가 포함된 해당 데이터에 대한 스키마는 여전히 필요합니다. 이 스키마 정보는 사용자 데이터를 해석하기 위해 필요합니다. 서비스가 실행 중일 때 채점 모듈에 데이터 세트 스키마가 포함되도록 이러한 구성 요소를 연결된 상태로 유지합니다. 데이터는 사용되지 않고 스키마만 사용됩니다.  
> 
>한 가지 알아두어야 할 중요한 사실은 원래 데이터 세트에 레이블이 포함되어 있다면 웹 입력의 올바른 스키마도 레이블이 있는 열을 기대하게 됩니다. 이 문제를 해결하는 방법은 레이블과 교육 데이터 세트에 있던 다른 데이터를 제거하는 것이지만, 웹 입력과 교육 데이터 세트를 일반 모듈에 연결하기 전에는 웹 입력에 없을 것입니다. 
> 

마지막으로 실험을 한 번 실행합니다(**실행** 클릭). 모델이 계속 작동 중인지 확인하려면 [모델 점수 매기기][score-model] 모듈의 출력을 클릭하고 **결과 보기**를 선택합니다. 원래 데이터가 신용 위험 값("점수를 매긴 레이블") 및 점수 매기기 확률 값("점수를 매긴 확률")과 함께 표시됩니다. 

## <a name="deploy-the-web-service"></a>웹 서비스 배포
실험을 Azure Resource Manager에 기반하는 기존 웹 서비스 또는 새 웹 서비스로 배포할 수 있습니다.

### <a name="deploy-as-a-classic-web-service"></a>기존 웹 서비스로 배포
실험에서 파생된 기존 웹 서비스를 배포하려면 캔버스에서 **웹 서비스 배포**를 클릭하고 **웹 서비스 배포[기존]** 를 선택합니다. Machine Learning Studio에서 실험을 웹 서비스로 배포하고 웹 서비스에 대한 대시보드로 이동합니다. 이 페이지에서 실험으로 돌아가서(**스냅샷 보기** 또는 **최신 항목 보기**) 간단한 웹 서비스 테스트를 실행할 수 있습니다(아래 **웹 서비스 테스트** 참조). 여기에는 웹 서비스에 액세스할 수 있는 애플리케이션 만들기에 관한 정보도 있습니다(자세한 내용은 이 자습서의 다음 단계 참조).

![웹 서비스 대시보드](./media/tutorial-part3-credit-risk-deploy/publish6.png)


**구성** 탭을 클릭하여 서비스를 구성할 수 있습니다. 여기서 서비스 이름(기본적으로 실험 이름이 지정됨)을 수정하고 설명을 제공할 수 있습니다. 입력 및 출력 데이터에 대해 더 친숙한 레이블을 제공할 수도 있습니다.  

![웹 서비스 구성](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>새 웹 서비스로 배포

> [!NOTE] 
> 새 웹 서비스를 배포하려면 웹 서비스를 배포하는 구독에 충분한 권한이 있어야 합니다. 자세한 내용은 [Azure Machine Learning 웹 서비스 포털에서 웹 서비스 관리](manage-new-webservice.md)를 참조하세요. 

실험에서 파생된 새 웹 서비스를 배포하려면:

1. 캔버스 아래에서 **웹 서비스 배포**를 클릭하고 **웹 서비스 배포[신규]** 를 선택합니다. Machine Learning Studio를 통해 Azure Machine Learning 웹 서비스 **배포 실험** 페이지로 이동합니다.

1. 웹 서비스의 이름을 입력합니다. 

1. **가격 책정 계획**의 경우 기존 가격 책정 계획을 선택하거나 "새로 만들기"를 선택하고 새 계획 이름을 지정한 후 월별 계획 옵션을 선택할 수 있습니다. 계획 계층이 기본적으로 기본 하위 지역에 대한 계획으로 설정되고 웹 서비스는 해당 하위 지역에 배포됩니다.

1. **배포**을 참조하십시오.

몇 분 후에 웹 서비스에 대한 **빠른 시작** 페이지가 열립니다.

**구성** 탭을 클릭하여 서비스를 구성할 수 있습니다. 여기에서 서비스를 수정하고 설명을 지정할 수 있습니다. 

웹 서비스를 테스트하려면 **테스트** 탭을 클릭합니다(아래의 **웹 서비스 테스트** 참조). 웹 서비스에 액세스할 수 있는 애플리케이션 만들기에 대한 정보를 보려면 **사용** 탭을 클릭합니다(자세한 내용은 이 자습서의 다음 단계 참조).

> [!TIP]
> 웹 서비스를 배포하고 나서 업데이트할 수 있습니다. 예를 들어 모델을 변경하려면 학습 실험을 편집하고, 모델 매개 변수를 조정하고, **웹 서비스 배포**를 클릭한 후 **웹 서비스 배포[클래식]** 또는 **웹 서비스 배포[신규]** 를 선택합니다. 실험을 다시 배포하면 이 실험이 웹 서비스를 대체하고 이제 업데이트된 모델을 사용합니다.  
> 
> 

## <a name="test-the-web-service"></a>웹 서비스 테스트

웹 서비스에 액세스될 때 사용자 데이터는 **웹 서비스 입력** 모듈로 이동됩니다. 여기서 [모델 점수 매기기][score-model] 모듈로 전달되고 점수가 매겨집니다. 예측 실험을 설정했던 방법대로, 모델은 원래 신용 위험 데이터 세트와 동일한 형식의 데이터를 요구합니다.
결과가 웹 서비스로부터 **웹 서비스 출력** 모듈을 거쳐 사용자에게 반환됩니다.

> [!TIP]
> 예측 실험을 구성한 방법대로, [모델 점수 매기기][score-model] 모듈의 전체 결과가 반환됩니다. 여기에는 모든 입력 데이터와 신용 위험 값 및 점수 매기기 확률이 포함됩니다. 하지만 원하는 경우 다른 것을 반환할 수 있습니다. 예를 들어 신용 위험 값만 반환할 수 있습니다. 이를 수행하려면 [모델 점수 매기기][score-model]와 **웹 서비스 출력** 사이에 [열 선택][select-columns] 모듈을 삽입하여 웹 서비스에서 반환하지 않으려는 열을 제거합니다. 
> 
> 

**Machine Learning Studio** 또는 **Azure Machine Learning 웹 서비스** 포털에서 기존 웹 서비스를 테스트할 수 있습니다.
**Machine Learning 웹 서비스** 포털에서만 새 웹 서비스를 테스트할 수 있습니다.

> [!TIP]
> Azure Machine Learning 웹 서비스 포털에서 테스트하는 경우 요청-응답 서비스를 테스트하는 데 사용할 수 있는 샘플 데이터가 포털에서 생성되도록 할 수 있습니다. **구성** 페이지에서 **샘플 데이터 사용 여부**에 대해 "예"를 선택합니다. **테스트** 페이지에서 요청-응답 탭을 열면 포털은 원래 신용 위험 데이터 세트에서 가져온 샘플 데이터를 채웁니다.

### <a name="test-a-classic-web-service"></a>기존 웹 서비스 테스트

Machine Learning Studio 또는 Machine Learning 웹 서비스 포털에서 기존 웹 서비스를 테스트할 수 있습니다. 

#### <a name="test-in-machine-learning-studio"></a>Machine Learning Studio에서 테스트

1. 웹 서비스에 대한 **대시보드** 페이지에서 **기본 엔드포인트**의 **테스트** 단추를 클릭합니다. 서비스에 대한 입력 데이터를 요청하는 대화 상자가 나타납니다. 이는 원래 신용 위험 데이터 세트에 나타난 열과 같습니다.  

1. 데이터 집합을 입력하고 **확인**을 클릭합니다. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Machine Learning 웹 서비스 포털에서 테스트

1. 웹 서비스에 대한 **대시보드** 페이지에서 **기본 엔드포인트** 아래의 **테스트 미리 보기** 링크를 클릭합니다. 웹 서비스 엔드포인트에 대한 Azure Machine Learning 웹 서비스 포털의 테스트 페이지가 열리고 서비스에 사용할 입력 데이터를 입력하라는 메시지가 표시됩니다. 이는 원래 신용 위험 데이터 세트에 나타난 열과 같습니다.

2. **요청-응답 테스트**를 클릭합니다. 

### <a name="test-a-new-web-service"></a>새 웹 서비스 테스트

Machine Learning 웹 서비스 포털에서만 새 웹 서비스를 테스트할 수 있습니다.

1. [Azure Machine Learning 웹 서비스](https://services.azureml.net/quickstart) 포털에서 페이지의 맨 위에 있는 **테스트**를 클릭합니다. **테스트** 페이지가 열리면 서비스에 대한 데이터를 입력할 수 있습니다. 표시된 입력 필드는 원래 신용 위험 데이터 세트에 나타난 열과 같습니다. 

1. 데이터 집합을 입력하고 **요청-응답 테스트**를 클릭합니다.

테스트의 결과는 출력 열에 있는 페이지의 오른쪽에 표시됩니다. 


## <a name="manage-the-web-service"></a>웹 서비스 관리

기존 또는 신규의 웹 서비스를 배포한 후에는 [Microsoft Azure Machine Learning 웹 서비스 포털](https://services.azureml.net/quickstart)에서 관리할 수 있습니다.

웹 서비스의 성능을 모니터링하려면 다음을 수행합니다.

1. [Microsoft Azure Machine Learning 웹 서비스](https://services.azureml.net/quickstart) 포털에 로그인합니다.
1. **웹 서비스**를 클릭합니다.
1. 해당 웹 서비스를 클릭합니다.
1. **대시보드**를 클릭합니다.

## <a name="access-the-web-service"></a>웹 서비스 액세스

이 자습서의 이전 단계에서는 신용 위험 예측 모델을 사용하는 웹 서비스를 배포했습니다. 이제 사용자는 서비스에 데이터를 보내고 결과를 받을 수 있습니다. 

웹 서비스는 다음 두 방법의 하나로 REST API를 사용하여 데이터를 받고 반환할 수 있는 Azure 웹 서비스입니다.  

* **요청/응답** - 사용자가 HTTP 프로토콜을 사용하여 하나 이상의 신용 데이터 행을 서비스에 보내고 서비스에서는 하나 이상의 결과 집합으로 응답합니다.
* **Batch 실행** - 사용자가 Azure Blob 신용 데이터 행 하나 이상을 저장한 다음 Blob 위치를 서비스에 보냅니다. 서비스에서는 입력 Blob의 모든 데이터 행에 대한 점수를 매기고 결과를 다른 Blob에 저장한 다음 해당 컨테이너의 URL을 반환합니다.  

클래식 웹 서비스에 액세스하는 가장 빠르고 쉬운 방법은 [Azure ML 요청-응답 서비스 웹앱](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/) 또는 [Azure ML Batch 실행 서비스 웹앱 템플릿](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)을 통하는 것입니다.

이러한 웹앱 템플릿은 웹 서비스의 입력 데이터 및 예상 결과를 알고 있는 사용자 지정 웹앱을 구축할 수 있습니다. 따라서 웹 서비스 및 데이터에 액세스하도록 하기만 하면 나머지 작업은 템플릿이 수행합니다.

웹앱 템플릿 사용에 대한 자세한 내용은 [웹앱 템플릿을 사용한 Azure Machine Learning 웹 서비스 사용](/azure/machine-learning/studio/consume-web-services)을 참조하세요.



## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 단계를 완료했습니다.

> [!div class="checklist"]
> * 배포 준비
> * 웹 서비스 배포
> * 웹 서비스 테스트
> * 웹 서비스 관리
> * 웹 서비스 액세스

또한 R, C# 및 Python 프로그래밍 언어에서 제공하는 시작 코드를 사용하여 웹 서비스에 액세스하는 사용자 지정 애플리케이션을 개발할 수도 있습니다.

> [!div class="nextstepaction"]
> [Azure Machine Learning 웹 서비스 사용](consume-web-services.md)

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
