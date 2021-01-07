---
title: '자습서: 자동화된 ML을 사용하여 모델 학습'
description: Apache Spark 및 자동화된 ML을 사용하여 Azure Synapse에서 코드 프리 기계 학습 모델을 학습하는 방법에 대한 자습서입니다.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 4967d5305b4b438f3baa6fca078d7b3169612590
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093403"
---
# <a name="tutorial-train-a-machine-learning-model-code-free-in-azure-synapse-with-apache-spark-and-automated-ml"></a>자습서: Apache Spark 및 자동화된 ML을 사용하여 Azure Synapse에서 코드 프리 기계 학습 모델 학습

[Azure Machine Learning에서 자동화된 ML](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml)을 사용하여 학습하는 새로운 기계 학습 모델을 통해 Spark 테이블의 데이터를 쉽게 보강하는 방법을 알아봅니다.  Synapse의 사용자는 Azure Synapse 작업 영역에서 Spark 테이블을 선택하기만 하면 코드 프리 환경에서 기계 학습 모델을 빌드하기 위한 학습 데이터 세트로 사용할 수 있습니다.

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> - Azure Machine Learning에서 자동화된 ML을 사용하는 Azure Synapse Studio에서 코드 프리 환경을 사용하여 기계 학습 모델을 학습합니다. 학습되는 모델의 유형은 해결하려는 문제에 따라 달라집니다.

Azure 구독이 없는 경우 [시작하기 전에 체험 계정을 만듭니다](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>사전 요구 사항

- 기본 스토리지로 구성된 ADLS Gen2 스토리지 계정이 있는 [Synapse Analytics 작업 영역](../get-started-create-workspace.md). 작업하려는 ADLS Gen2 파일 시스템의 **Storage Blob 데이터 기여자** 여야 합니다.
- Azure Synapse Analytics 작업 영역의 Spark 풀. 자세한 내용은 [Azure Synapse에서 Spark 풀 만들기](../quickstart-create-sql-pool-studio.md)를 참조하세요.
- Azure Synapse Analytics 작업 영역의 Azure Machine Learning 연결 서비스. 자세한 내용은 [Azure Synapse에서 Azure Machine Learning 연결된 서비스 만들기](quickstart-integrate-azure-machine-learning.md)를 참조하세요.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure 포털](https://portal.azure.com/)

## <a name="create-a-spark-table-for-training-dataset"></a>학습 데이터 세트용 Spark 테이블 만들기

이 자습서에는 Spark 테이블이 필요합니다. 다음 Notebook에서 Spark 테이블을 만듭니다.

1. [Create-Spark-Table-NYCTaxi- Data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149229) Notebook을 다운로드합니다.

1. Notebook을 Azure Synapse Studio로 가져옵니다.
![Notebook 가져오기](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. 사용하려는 Spark 풀을 선택하고, `Run all`을 클릭합니다. 이 Notebook이 실행되면 열린 데이터 세트에서 뉴욕 택시 데이터를 가져와서 기본 Spark 데이터베이스에 저장합니다.
![모두 실행](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. Notebook 실행이 완료되면 새 Spark 테이블이 기본 Spark 데이터베이스 아래에 만들어집니다. 데이터 허브로 이동하여 `nyc_taxi`로 명명된 테이블을 찾습니다.
![Spark 테이블](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="launch-automated-ml-wizard-to-train-a-model"></a>자동화된 ML 마법사를 시작하여 모델 학습

마우스 오른쪽 단추로 이전 단계에서 만든 Spark 테이블을 클릭합니다. "Machine Learning-> 새 모델로 보강"을 차례로 선택하여 마법사를 엽니다.
![자동화된 ML 마법사 시작](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

구성 패널이 표시되고, Azure Machine Learning에서 자동화된 ML 실험을 만들기 위한 구성 세부 정보를 제공하라는 메시지가 표시됩니다. 이 실행은 여러 모델을 학습시키고, 성공적인 최상의 실행 모델이 Azure Machine Learning 모델 레지스트리에 등록됩니다.

![실행 구성 단계 1](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

- **Azure Machine Learning 작업 영역**: 자동화된 ML 실험 실행을 만들려면 Azure Machine Learning 작업 영역이 필요합니다. 또한 [연결된 서비스](quickstart-integrate-azure-machine-learning.md)를 사용하여 Azure Synapse 작업 영역을 Azure Machine Learning 작업 영역과 연결해야 합니다. 모든 필수 구성 요소가 있으면 이 자동화된 ML 실행에 사용할 Azure Machine Learning 작업 영역을 지정할 수 있습니다.

- **실험 이름**: 실험 이름을 지정합니다. 자동화된 ML 실행을 제출할 때 실험 이름을 제공합니다. 실행에 대한 정보는 Azure Machine Learning 작업 영역의 해당 실험 아래에 저장됩니다. 이 환경에서는 기본적으로 새 실험을 만들고 제안된 이름을 생성하지만 기존 실험의 이름을 제공할 수도 있습니다.

- **최상의 모델**: 자동화된 ML 실행에서 최상의 모델에 대한 이름을 지정합니다. 최상의 모델에는 이 이름이 지정되고, 이 실행 후에 Azure Machine Learning 모델 레지스트리에 자동으로 저장됩니다. 자동화된 ML 실행은 많은 기계 학습 모델을 만듭니다. 이후 단계에서 선택하는 기본 메트릭에 따라 이러한 모델을 비교하여 최상의 모델을 선택할 수 있습니다.

- **대상 열**: 이는 예측을 위해 모델에서 학습되는 항목입니다. 예측하려는 열을 선택합니다.

- **Spark 풀**: 자동화된 ML 실험 실행에 사용할 Spark 풀입니다. 계산은 지정한 풀에서 실행됩니다.

- **Spark 구성 세부 정보**: Spark 풀 외에도 세션 구성 세부 정보를 제공하는 옵션도 있습니다.

이 자습서에서는 `fareAmount` 숫자 열을 대상 열로 선택합니다.

"계속"을 클릭합니다.

## <a name="choose-task-type"></a>작업 유형 선택

답변하려는 질문에 따라 실험에 대한 기계 학습 모델 유형을 선택합니다. 대상 열로 `fareAmount`를 선택했으며 숫자 값이므로 *회귀* 를 선택합니다.

"계속"을 클릭하여 추가 설정을 구성합니다.

![작업 유형 선택](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>추가 구성

*분류* 또는 *회귀* 유형을 선택하는 경우 추가 구성은 다음과 같습니다.

- **기본 메트릭**: 모델의 성능을 측정하는 데 사용되는 메트릭입니다. 이는 자동화된 ML 실행에서 만든 여러 모델을 비교하여 가장 효율적으로 수행되는 모델을 결정하는 데 사용되는 메트릭입니다.

- **학습 작업 시간(시간)** : 실험에서 모델을 실행하고 학습시키는 데 걸리는 최대 시간(시간)입니다. 1보다 작은 값을 제공할 수도 있습니다. 예: `0.5`.

- **최대 동시 반복 횟수**: 동시에 실행되는 최대 반복 횟수를 나타냅니다.

- **ONNX 모델 호환성**: 사용하도록 설정되면 자동화된 ML을 통해 학습된 모델이 ONNX 형식으로 변환됩니다. 이는 특히 모델을 Azure Synapse SQL 풀의 점수 매기기에 사용하려는 경우에 적합합니다.

이러한 설정에는 모두 사용자 지정할 수 있는 기본값이 있습니다.
![추가 구성](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

> "시계열 예측"을 선택하는 경우 더 많은 구성이 필요합니다. 또한 예측은 ONNX 모델 호환성을 지원하지 않습니다.

필요한 모든 구성이 완료되면 자동화된 ML 실행을 시작할 수 있습니다.

Azure Synapse에서 자동화된 ML 실행을 시작하는 방법에는 두 가지가 있습니다. 코드 프리 환경에서는 **실행 만들기** 를 직접 선택할 수 있습니다. 코드를 선호하는 경우 **Notebook에서 열기** 를 선택할 수 있습니다. 그러면 실행을 만들고 Notebook을 실행하는 코드를 확인할 수 있습니다.

### <a name="create-run-directly"></a>직접 실행 만들기

"실행 시작"을 클릭하여 자동화된 ML 실행을 직접 시작합니다. 자동화된 ML 실행이 시작되고 있다는 알림이 표시됩니다.

자동화된 ML 실행이 성공적으로 시작되면 또 다른 성공 알림이 표시됩니다. 알림 단추를 클릭하여 실행 제출 상태를 확인할 수도 있습니다.
성공 알림의 링크를 클릭하여 Azure Machine Learning을 확인합니다.
![성공 알림](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-run-with-notebook"></a>Notebook을 사용하여 실행 만들기

*Notebook에서 열기* 를 선택하여 Notebook을 생성합니다. *모두 실행* 을 클릭하여 Notebook을 실행합니다.
이렇게 하면 추가 설정을 자동화된 ML 실행에 추가할 수도 있습니다.

![Notebook 열기](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

Notebook의 실행이 성공적으로 제출되면 Notebook 출력에 Azure Machine Learning 작업 영역의 실험 실행에 대한 링크가 있습니다. 링크를 클릭하여 Azure Machine Learning에서 자동화된 ML 실행을 모니터링할 수 있습니다.
![Notebook 모두 실행](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png)

## <a name="next-steps"></a>다음 단계

- [자습서: Azure Synapse 전용 SQL 풀에서 기계 학습 모델 점수 매기기](tutorial-sql-pool-model-scoring-wizard.md)
- [빠른 시작: Azure Synapse에서 새 Azure Machine Learning 연결된 서비스 만들기](quickstart-integrate-azure-machine-learning.md)
- [Azure Synapse Analytics의 Machine Learning 기능](what-is-machine-learning.md)
