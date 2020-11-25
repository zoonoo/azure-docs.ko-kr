---
title: Apache Spark Machine Learning
description: 이 문서에서는 Azure Synapse Analytics에서 Apache Spark를 통해 제공 되는 기계 학습 및 데이터 과학 기능에 대 한 개념적 개요를 제공 합니다.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 11/13/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: ced78955c71f37a5a6f5231f61e5327428834f00
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919800"
---
# <a name="machine-learning-with-apache-spark"></a>Apache Spark를 사용한 기계 학습

Azure Synapse Analytics에서 Apache Spark는 빅 데이터를 사용 하 여 기계 학습을 가능 하 게 하 고, 대량의 구조화 되 고 구조화 되지 않은 데이터 및 빠른 이동 데이터에서 중요 한 통찰력을 얻을 수 있는 기능을 제공 합니다. 

이 섹션에는 예비 데이터 분석, 기능 엔지니어링, 모델 학습, 모델 점수 매기기 및 배포를 포함 하 여 기계 학습 워크플로에 대 한 개요 및 자습서가 포함 되어 있습니다.  

## <a name="synapse-runtime"></a>Synapse 런타임 
Synapse 런타임은 데이터 과학 및 기계 학습에 최적화 된 큐 레이트 환경입니다. Synapse 런타임은 기본적으로 Azure Machine Learning SDK에서 널리 사용 되는 다양 한 오픈 소스 라이브러리 및 빌드를 제공 합니다. Synapse 런타임은 PyTorch, Scikit, XGBoost 등을 비롯 한 여러 외부 라이브러리도 포함 합니다.

게시 된 [Azure Synapse Analytics 런타임을](../spark/apache-spark-version-support.md)확인 하 여 사용 가능한 라이브러리 및 관련 버전에 대해 자세히 알아보세요.

## <a name="exploratory-data-analysis"></a>예비 데이터 분석
Azure Synapse Analytics에서 Apache Spark를 사용 하는 경우 Synapse 노트북 차트 옵션, Seaborn 및 Matplotlib와 같은 인기 있는 오픈 소스 라이브러리에 대 한 액세스 뿐만 아니라 Synapse SQL 및 Power BI와의 통합을 포함 하 여 데이터를 시각화 하는 데 도움이 되는 다양 한 기본 제공 옵션이 있습니다.

[Azure Synapse 노트북을 사용 하 여 데이터를 시각화](../spark/apache-spark-data-visualization.md)하는 방법에 대 한 문서를 참조 하 여 데이터 시각화 및 데이터 분석 옵션에 대해 자세히 알아보세요.

## <a name="feature-engineering"></a>기능 엔지니어링
기본적으로 Synapse 런타임에는 기능 엔지니어링에 일반적으로 사용 되는 라이브러리 집합이 포함 되어 있습니다. 대량 데이터 집합의 경우 기능 엔지니어링에 Spark SQL, MLlib 및 Koalas를 사용할 수 있습니다. 작은 데이터 집합의 경우 Numpy, Pandas 및 Scikit와 같은 타사 라이브러리도 이러한 시나리오에 유용한 메서드를 제공 합니다.

## <a name="train-models"></a>모델 학습
Azure Synapse Analytics에서 Azure Spark를 사용 하 여 기계 학습 모델을 학습 하는 경우 Apache Spark MLlib, Azure Machine Learning 및 기타 다양 한 오픈 소스 라이브러리가 있습니다. 

[Azure Synapse Analytics에서 모델을 학습](../spark/apache-spark-machine-learning-training.md)하는 방법에 대 한 문서를 검토 하 여 기계 학습 기능에 대해 자세히 알아보세요.

### <a name="sparkml-and-mllib"></a>SparkML 및 MLlib
Spark는 메모리 내 분산형 계산 기능을 지원하여 기계 학습 및 그래프 계산에 사용된 반복 알고리즘에 적합합니다. ```spark.ml``` 는 사용자가 기계 학습 파이프라인을 만들고 조정 하는 데 도움이 되는 통일 된 고급 Api 집합을 제공 합니다. 에 대해 자세히 알아보려면 ```spark.ml``` [Apache Spark ML 프로그래밍 가이드](https://spark.apache.org/docs/1.2.2/ml-guide.html)를 참조 하세요.

### <a name="azure-machine-learning-automl"></a>Azure Machine Learning AutoML
[AutoML](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) (자동화 된 machine learning)을 Azure Machine Learning 하 여 기계 학습 모델을 개발 하는 프로세스를 자동화할 수 있습니다. 데이터 과학자, 분석가 및 개발자는 모델 품질을 유지하면서 확장성, 효율성 및 생산성이 높은 ML 모델을 빌드할 수 있습니다. AutoML SDK Azure Machine Learning를 실행 하는 구성 요소는 Synapse 런타임으로 직접 빌드됩니다.

### <a name="open-source-libraries"></a>오픈 소스 라이브러리
Azure Synapse Analytics의 모든 Apache Spark 풀에는 미리 로드 되 고 인기 있는 기계 학습 라이브러리 집합이 제공 됩니다.  기본적으로 포함 된 관련 기계 학습 라이브러리 중 일부는 다음과 같습니다.

- [Scikit-](https://scikit-learn.org/stable/index.html) 기존 ML 알고리즘에 대해 가장 인기 있는 단일 노드 기계 학습 라이브러리 중 하나입니다. Scikit-학습 및 자율 학습 알고리즘의 대부분을 지원 하 고 데이터 마이닝 및 데이터 분석에도 사용할 수 있습니다.
  
- [Xgboost](https://xgboost.readthedocs.io/en/latest/) 는 의사 결정 트리와 임의 포리스트를 학습 하기 위한 최적화 된 알고리즘이 포함 된 인기 있는 기계 학습 라이브러리입니다. 
  
- [PyTorch](https://pytorch.org/)  &  [Tensorflow](https://www.tensorflow.org/) 은 강력한 Python 심층 학습 라이브러리입니다. Azure Synapse Analytics의 Apache Spark 풀 내에서 이러한 라이브러리를 사용 하 여 풀의 실행자 수를 0으로 설정 하 여 단일 컴퓨터 모델을 빌드할 수 있습니다. 이 구성에서 Apache Spark 작동 하지 않더라도 단일 컴퓨터 모델을 만드는 간단 하 고 비용 효율적인 방법입니다.

## <a name="track-model-development"></a>모델 개발 추적
[Mlflow](https://www.mlflow.org/) 는 machine learning 실험의 수명 주기를 관리 하기 위한 오픈 소스 라이브러리입니다. MLFlow 추적은 학습 실행 메트릭과 모델 아티팩트를 기록 하 고 추적 하는 MLflow의 구성 요소입니다. Azure Synapse Analytics 및 Azure Machine Learning를 통해 MLFlow 추적을 사용 하는 방법에 대 한 자세한 내용은 [MLFlow를 사용 하는 방법](https://docs.microsoft.com/azure/machine-learning/how-to-use-mlflow)에 대 한이 자습서를 참조 하세요.

## <a name="model-scoring"></a>모델 점수 매기기
모델 점수 매기기 또는 추론는 모델을 사용 하 여 예측을 수행 하는 단계입니다. SparkML 또는 MLLib를 사용 하는 모델 점수 매기기의 경우 기본 Spark 메서드를 활용 하 여 Spark 데이터 프레임에서 직접 추론을 수행할 수 있습니다. 다른 오픈 소스 라이브러리 및 모델 유형의 경우에는 Spark UDF를 만들어 대규모 데이터 집합에 대 한 유추를 확장할 수도 있습니다. 작은 데이터 집합의 경우 라이브러리에서 제공 하는 네이티브 모델 유추 메서드를 사용할 수도 있습니다.

## <a name="register-and-serve-models"></a>모델 등록 및 제공
모델을 등록 하면 작업 영역에서 모델에 대 한 메타 데이터를 저장 하 고, 버전을 추적 하 고, 추적할 수 있습니다. 모델 학습을 완료 하면 모델을 [Azure Machine Learning 모델 레지스트리에](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment#register-package-and-deploy-models-from-anywhere)등록할 수 있습니다. 등록 한 후에는 ONNX 모델을 사용 하 여 전용 SQL 풀에 저장 된 [데이터를 보강](../machine-learning/tutorial-sql-pool-model-scoring-wizard.md) 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
Azure Synapse Analytics에서 기계 학습을 시작 하려면 다음 자습서를 확인 하세요.
- [Azure Synapse 노트북을 사용 하 여 데이터 분석](../spark/apache-spark-data-visualization-tutorial.md)

- [AutoML을 사용 하 여 기계 학습 모델 학습](../spark/apache-spark-azure-machine-learning-tutorial.md)

- [Apache Spark MLlib를 사용 하 여 기계 학습 모델 학습](../spark/apache-spark-machine-learning-mllib-notebook.md)
  