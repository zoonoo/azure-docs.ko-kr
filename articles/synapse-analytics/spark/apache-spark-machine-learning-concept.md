---
title: Apache Spark를 사용한 Machine Learning
description: 이 문서에서는 Azure Synapse Analytics의 Apache Spark를 통해 사용할 수 있는 기계 학습 및 데이터 과학 기능에 대한 개념적 개요를 제공합니다.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 11/13/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: 0485f697b9360b0f2dfe94fdf07629978b5127c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98116928"
---
# <a name="machine-learning-with-apache-spark"></a>Apache Spark를 사용한 기계 학습

Azure Synapse Analytics의 Apache Spark는 빅 데이터로 기계 학습을 가능하게 하여 대량의 정형, 비정형 및 빠르게 이동하는 데이터에서 중요한 인사이트를 얻을 수 있는 기능을 제공합니다. 

이 섹션에는 예비 데이터 분석, 특징 엔지니어링, 모델 학습, 모델 채점 및 배포를 포함하여 기계 학습 워크플로에 대한 개요 및 자습서가 포함되어 있습니다.  

## <a name="synapse-runtime"></a>Synapse 런타임 
Synapse 런타임은 데이터 과학 및 기계 학습에 최적화된 큐레이팅된 환경입니다. Synapse 런타임은 기본적으로 Azure Machine Learning SDK에서 인기 있는 다양한 오픈 소스 라이브러리 및 빌드를 제공합니다. Synapse 런타임은 PyTorch, Scikit-Learn, XGBoost 등을 비롯한 여러 외부 라이브러리도 포함합니다.

게시된 [Azure Synapse Analytics 런타임](../spark/apache-spark-version-support.md)을 참조하여 사용 가능한 라이브러리 및 관련 버전에 대해 자세히 알아봅니다.

## <a name="exploratory-data-analysis"></a>예비 데이터 분석
Azure Synapse Analytics에서 Apache Spark를 사용하는 경우 Synapse Notebook 차트 옵션, Seaborn 및 Matplotlib와 같은 인기 있는 오픈 소스 라이브러리에 대한 액세스 권한, Synapse SQL 및 Power BI와의 통합 등 데이터를 시각화하는 데 도움이 되는 다양한 기본 제공 옵션이 있습니다.

[Azure Synapse Notebook을 사용하여 데이터를 시각화](../spark/apache-spark-data-visualization.md)하는 방법에 대한 문서를 참조하여 데이터 시각화 및 데이터 분석 옵션에 대해 자세히 알아봅니다.

## <a name="feature-engineering"></a>특징 엔지니어링
기본적으로 Synapse 런타임에는 특징 엔지니어링에 일반적으로 사용되는 라이브러리 집합이 포함되어 있습니다. 대규모 데이터 세트에서 특징 엔지니어링에 Spark SQL, MLlib 및 Koalas를 사용할 수 있습니다. 소규모 데이터 세트의 경우 Numpy, Pandas 및 Scikit-learn과 같은 타사 라이브러리도 이러한 시나리오에 유용한 방법을 제공합니다.

## <a name="train-models"></a>모델 학습
Azure Synapse Analytics에서 Azure Spark를 사용하여 기계 학습 모델을 학습하는 경우 Apache Spark MLlib, Azure Machine Learning, 기타 다양한 오픈 소스 라이브러리와 같은 몇 가지 옵션이 있습니다. 

[Azure Synapse Analytics에서 모델을 학습](../spark/apache-spark-machine-learning-training.md)하는 방법에 대한 문서를 참조하여 기계 학습 기능에 대해 자세히 알아봅니다.

### <a name="sparkml-and-mllib"></a>SparkML 및 MLlib
Spark는 메모리 내 분산형 계산 기능을 지원하여 기계 학습 및 그래프 계산에 사용된 반복 알고리즘에 적합합니다. ```spark.ml```은 사용자가 기계 학습 파이프라인을 만들고 조정하는 데 도움이 되는 균일한 고급 API 집합을 제공합니다. ```spark.ml```에 대해 자세히 알아보려면 [Apache Spark ML 프로그래밍 가이드](https://spark.apache.org/docs/1.2.2/ml-guide.html)를 참조하세요.

### <a name="azure-machine-learning-automated-ml"></a>Azure Machine Learning의 자동화된 ML
[Azure Machine Learning의 자동화된 ML](../../machine-learning/concept-automated-ml.md)(자동화된 Machine Learning)은 기계 학습 모델을 개발하는 프로세스를 자동화하는 데 도움이 됩니다. 데이터 과학자, 분석가 및 개발자는 모델 품질을 유지하면서 확장성, 효율성 및 생산성이 높은 ML 모델을 빌드할 수 있습니다. Azure Machine Learning의 자동화된 ML SDK를 실행하는 구성 요소는 Synapse 런타임에 직접 빌드됩니다.

### <a name="open-source-libraries"></a>오픈 소스 라이브러리
Azure Synapse Analytics의 모든 Apache Spark 풀에는 미리 로드되고 인기 있는 기계 학습 라이브러리 집합이 제공됩니다.  기본적으로 포함된 관련 기계 학습 라이브러리 중 일부는 다음과 같습니다.

- [Scikit-learn](https://scikit-learn.org/stable/index.html)은 기존 ML 알고리즘에 대해 가장 인기 있는 단일 노드 기계 학습 라이브러리 중 하나입니다. Scikit-learn은 대부분의 감독 및 자율 학습 알고리즘을 지원하며 데이터 마이닝 및 데이터 분석에도 사용할 수 있습니다.
  
- [XGBoost](https://xgboost.readthedocs.io/en/latest/)는 의사 결정 트리와 임의 포리스트를 학습하기 위해 최적화된 알고리즘이 포함된 인기 있는 기계 학습 라이브러리입니다. 
  
- [PyTorch](https://pytorch.org/) & [Tensorflow](https://www.tensorflow.org/)는 강력한 Python 딥 러닝 라이브러리입니다. Azure Synapse Analytics의 Apache Spark 풀 내에서 이러한 라이브러리를 사용하여 풀의 실행기 수를 0으로 설정하여 단일 머신 모델을 빌드할 수 있습니다. 이 구성에서는 Apache Spark가 작동하지 않지만 단일 머신 모델을 만드는 간단하고 비용 효율적인 방법입니다.

## <a name="track-model-development"></a>모델 개발 추적
[MLFlow](https://www.mlflow.org/)는 기계 학습 실험의 수명 주기를 관리하기 위한 오픈 소스 라이브러리입니다. MLFlow 추적은 학습 실행 메트릭 및 모델 아티팩트를 로그하고 추적하는 MLflow의 구성 요소입니다. Azure Synapse Analytics 및 Azure Machine Learning을 통해 MLFlow 추적을 사용하는 방법에 대한 자세한 내용은 [MLFlow를 사용하는 방법](../../machine-learning/how-to-use-mlflow.md)에 대한 이 자습서를 참조하세요.

## <a name="model-scoring"></a>모델 채점
모델 채점 또는 유추는 모델을 사용하여 예측을 수행하는 단계입니다. SparkML 또는 MLLib를 사용하는 모델 채점의 경우 네이티브 Spark 방법을 활용하여 Spark DataFrame에서 직접 유추할 수 있습니다. 다른 오픈 소스 라이브러리 및 모델 유형의 경우에는 Spark UDF를 만들어 대규모 데이터 세트에 대한 유추를 스케일 아웃할 수도 있습니다. 소규모 데이터 세트의 경우 라이브러리에서 제공하는 네이티브 모델 유추 방법을 사용합니다.

## <a name="register-and-serve-models"></a>모델 등록 및 제공
모델을 등록하면 작업 영역에서 모델에 대한 메타데이터를 저장 및 추적하고, 메타데이터의 버전을 관리할 수 있습니다. 모델 학습을 완료하면 모델을 [Azure Machine Learning 모델 레지스트리](../../machine-learning/concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere)에 등록할 수 있습니다. 등록 후에는 ONNX 모델을 사용하여 전용 SQL 풀에 저장된 [데이터를 보강](../machine-learning/tutorial-sql-pool-model-scoring-wizard.md)할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
Azure Synapse Analytics에서 기계 학습을 시작하려면 다음 자습서를 확인하세요.
- [Azure Synapse Notebook을 사용하여 데이터 분석](../spark/apache-spark-data-visualization-tutorial.md)

- [자동화된 ML을 사용하여 기계 학습 모델 학습](../spark/apache-spark-azure-machine-learning-tutorial.md)

- [Apache Spark MLlib를 사용하여 기계 학습 모델 학습](../spark/apache-spark-machine-learning-mllib-notebook.md)
