---
title: Apache Spark를 사용 하 여 기계 학습 모델 학습
description: Azure Synapse Analytics에서 Apache Spark를 사용 하 여 기계 학습 모델 학습
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 09/13/2020
ms.openlocfilehash: 8250f7154cfb2739d065980284da67a200680d89
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95920136"
---
# <a name="train-machine-learning-models"></a>기계 학습 모델 학습
Azure Synapse Analytics에서 Apache Spark는 빅 데이터를 사용 하 여 기계 학습을 가능 하 게 하 고, 대량의 구조화 되 고 구조화 되지 않은 데이터 및 빠른 이동 데이터에서 중요 한 통찰력을 얻을 수 있는 기능을 제공 합니다. Azure Synapse Analytics에서 Azure Spark를 사용 하 여 기계 학습 모델을 학습 하는 경우 Apache Spark MLlib, Azure Machine Learning 및 기타 다양 한 오픈 소스 라이브러리가 있습니다. 

## <a name="apache-sparkml-and-mllib"></a>Apache SparkML 및 MLlib
Azure Synapse Analytics의 Apache Spark는 Microsoft가 구현한 클라우드의 Apache Spark 중 하나입니다. 이는 메모리 내 처리를 지원 하 여 빅 데이터 분석을 향상 시키는 통합 된 오픈 소스 병렬 데이터 처리 프레임 워크를 제공 합니다. 속도, 간편한 사용 및 정교한 분석을 위해 Spark 처리 엔진이 빌드되었습니다. Spark는 메모리 내 분산형 계산 기능을 지원하여 기계 학습 및 그래프 계산에 사용된 반복 알고리즘에 적합합니다. 

이 분산 환경에 알고리즘 모델링 기능을 제공하는 확장 가능한 두 가지 기계 학습 라이브러리로 MLlib와 SparkML이 있습니다. MLlib에는 RDD에 기반하여 빌드된 원래의 API가 포함되어 있습니다. SparkML은 ML 파이프라인을 생성하기 위해 데이터 프레임에 기반하여 빌드된 높은 수준의 고급 API를 제공하는 새로운 패키지입니다. SparkML는 MLlib의 모든 기능을 아직 지원 하지 않지만 MLlib를 Spark의 표준 기계 학습 라이브러리로 대체 합니다.

> [!NOTE]
> 
> 이 [자습서](../spark/apache-spark-azure-machine-learning-tutorial.md)에 따라 SparkML 모델을 만드는 방법에 대해 자세히 알아볼 수 있습니다.

## <a name="popular-libraries"></a>인기 있는 라이브러리
Azure Synapse Analytics의 모든 Apache Spark 풀에는 미리 로드 되 고 인기 있는 기계 학습 라이브러리 집합이 제공 됩니다. 이러한 라이브러리는 프로그램 또는 프로젝트에 포함 시킬 수 있는 재사용 가능한 코드를 제공 합니다. 기본적으로 포함 된 관련 기계 학습 라이브러리 중 일부는 다음과 같습니다.
- [Scikit-](https://scikit-learn.org/stable/index.html) 기존 ML 알고리즘에 대해 가장 인기 있는 단일 노드 기계 학습 라이브러리 중 하나입니다. Scikit-학습 및 자율 학습 알고리즘의 대부분을 지원 하 고 데이터 마이닝 및 데이터 분석에도 사용할 수 있습니다.
  
- [Xgboost](https://xgboost.readthedocs.io/en/latest/) 는 의사 결정 트리와 임의 포리스트를 학습 하기 위한 최적화 된 알고리즘이 포함 된 인기 있는 기계 학습 라이브러리입니다. 
  
- [PyTorch](https://pytorch.org/)  &  [Tensorflow](https://www.tensorflow.org/) 은 강력한 Python 심층 학습 라이브러리입니다. Azure Synapse Analytics의 Apache Spark 풀 내에서 이러한 라이브러리를 사용 하 여 풀의 실행자 수를 0으로 설정 하 여 단일 컴퓨터 모델을 빌드할 수 있습니다. 이 구성에서 Apache Spark 작동 하지 않더라도 단일 컴퓨터 모델을 만드는 간단 하 고 비용 효율적인 방법입니다.

게시 된 [Azure Synapse Analytics 런타임을](../spark/apache-spark-version-support.md)확인 하 여 사용 가능한 라이브러리 및 관련 버전에 대해 자세히 알아볼 수 있습니다.

## <a name="mmlspark"></a>MMLSpark
[MMLSpark](https://github.com/Azure/mmlspark)는 Apache Spark용 Microsoft Machine Learning 라이브러리입니다. 이 라이브러리는 Spark에서 데이터 과학자의 생산성을 높이고 실험의 률을 높이고 광범위 한 데이터 집합에 대 한 심층 학습을 비롯 한 최첨단 기계 학습 기술을 활용 하도록 설계 되었습니다. 

MMLSpark는 확장 가능한 ML 모델(예: 인덱싱 문자열)을 작성하고, 데이터를 기계 학습 알고리즘에 필요한 레이아웃으로 강제 변환하고, 기능 벡터를 어셈블할 때 SparkML의 하위 수준 API에 기반한 계층을 제공합니다. MMLSpark 라이브러리는 PySpark에서 모델을 작성하기 위한 이러한 일반적인 작업을 간소화합니다.

## <a name="automated-ml-in-azure-machine-learning"></a>Azure Machine Learning의 자동화 된 ML 
Azure Machine Learning은 기계 학습 모델을 학습, 배포, 자동화, 관리 및 추적할 수 있는 클라우드 기반 환경입니다. Azure Machine Learning의 자동 ml (AutoML)은 학습 데이터 및 구성 설정을 수락 하 고, 최상의 모델에 도달할 수 있도록 다양 한 기능 정규화/표준화 방법, 모델 및 하이퍼 매개 변수 설정의 조합을 자동으로 반복 합니다. 

Azure Synapse Analytics 내에서 AutoML을 사용 하는 경우 다양 한 서비스 간의 긴밀 한 통합을 활용 하 여 인증 & 모델 교육을 간소화할 수 있습니다. 

> [!NOTE]
> 
> 이 [자습서](./spark/../apache-spark-azure-machine-learning-tutorial.md)를 수행 하 여 Azure Machine Learning automl 실험을 만드는 방법에 대해 자세히 알아볼 수 있습니다.

## <a name="azure-cognitive-services"></a>Azure Cognitive Services
[Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/what-are-cognitive-services) 는 감정적 감정의 텍스트 분석 또는 개체나 얼굴을 인식 하기 위해 이미지 분석과 같은 일반적인 문제를 해결 하는 기계 학습 기능을 제공 합니다. 이 서비스를 사용하기 위해 특별한 기계 학습 또는 데이터 과학 지식이 필요하지 않습니다. 인식 서비스는 machine learning 솔루션 (데이터, 알고리즘 및 학습 된 모델)의 구성 요소 중 일부 또는 전부를 제공 합니다. 이러한 서비스는 기계 학습 또는 데이터 과학을 사용 하지 않고도 데이터에 대 한 일반적인 지식이 필요 합니다. Azure Synapse Analytics 내에서 이러한 미리 학습 된 Cognitive Services를 자동으로 활용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Synapse Analytics의 Apache Spark 풀 내에서 기계 학습 모델을 학습 하는 다양 한 옵션에 대 한 개요를 제공 합니다. 아래 자습서에 따라 모델 학습에 대해 자세히 알아볼 수 있습니다.

- Azure Machine Learning 및 Azure Synapse Analytics를 사용 하 여 자동화 된 ML 실험 실행: [Automl 자습서](./spark/../apache-spark-autoscale.md) 
- SparkML 실험 실행: [Apache SparkML 자습서](../spark/apache-spark-azure-machine-learning-tutorial.md)
- 기본 라이브러리 보기: [Azure Synapse Analytics 런타임](../spark/apache-spark-version-support.md)