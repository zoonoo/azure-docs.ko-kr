---
title: Apache Spark로 기계 학습 모델 학습
description: Azure Synapse Analytics에서 Apache Spark를 사용하여 기계 학습 모델 학습
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 09/13/2020
ms.openlocfilehash: 56b9a98eb72b375aacfeb7cb147997028d3d9ba7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98116809"
---
# <a name="train-machine-learning-models"></a>기계 학습 모델 학습
Azure Synapse Analytics의 Apache Spark는 빅 데이터로 기계 학습을 가능하게 하여 대량의 정형, 비정형 및 빠르게 이동하는 데이터에서 귀중한 통찰력을 얻을 수 있는 기능을 제공합니다. Azure Synapse Analytics에서 Azure Spark를 사용하여 기계 학습 모델을 교육할 때 Apache Spark MLlib, Azure Machine Learning 및 기타 다양한 오픈 소스 라이브러리와 같은 몇 가지 옵션이 있습니다. 

## <a name="apache-sparkml-and-mllib"></a>Apache SparkML 및 MLlib
Azure Synapse Analytics의 Apache Spark는 Microsoft가 구현한 클라우드의 Apache Spark 중 하나입니다. 메모리 내 처리를 지원하는 통합 오픈 소스 병렬 데이터 처리 프레임워크를 제공하여 빅 데이터 분석을 향상시킵니다. 속도, 간편한 사용 및 정교한 분석을 위해 Spark 처리 엔진이 빌드되었습니다. Spark는 메모리 내 분산형 계산 기능을 지원하여 기계 학습 및 그래프 계산에 사용된 반복 알고리즘에 적합합니다. 

이 분산 환경에 알고리즘 모델링 기능을 제공하는 확장 가능한 두 가지 기계 학습 라이브러리로 MLlib와 SparkML이 있습니다. MLlib에는 RDD에 기반하여 빌드된 원래의 API가 포함되어 있습니다. SparkML은 ML 파이프라인을 생성하기 위해 데이터 프레임에 기반하여 빌드된 높은 수준의 고급 API를 제공하는 새로운 패키지입니다. SparkML은 아직 MLlib의 모든 기능을 지원하지 않지만 Spark의 표준 기계 학습 라이브러리로 MLlib를 대체합니다.

> [!NOTE]
> 
> 이 [자습서](../spark/apache-spark-azure-machine-learning-tutorial.md)를 따라 SparkML 모델을 만드는 방법에 대해 자세히 알아볼 수 있습니다.

## <a name="popular-libraries"></a>인기 있는 라이브러리
Azure Synapse Analytics의 모든 Apache Spark 풀에는 미리 로드된 인기 있는 기계 학습 라이브러리 집합이 함께 제공됩니다. 이러한 라이브러리는 프로그램이나 프로젝트에 포함할 수 있는 재사용 가능한 코드를 제공합니다. 기본적으로 포함된 관련 기계 학습 라이브러리 중 일부는 다음과 같습니다.
- [Scikit-learn](https://scikit-learn.org/stable/index.html)은 기존 ML 알고리즘을 위한 가장 인기 있는 단일 노드 기계 학습 라이브러리 중 하나입니다. Scikit-learn은 대부분의 지도 및 비지도 학습 알고리즘을 지원하며 데이터 마이닝 및 데이터 분석에도 사용할 수 있습니다.
  
- [XGBoost](https://xgboost.readthedocs.io/en/latest/)는 의사 결정 트리 및 임의 포레스트 학습을 위한 최적화된 알고리즘이 포함된 인기 있는 기계 학습 라이브러리입니다. 
  
- [PyTorch](https://pytorch.org/) & [Tensorflow](https://www.tensorflow.org/)는 강력한 Python 딥 러닝 라이브러리입니다. Azure Synapse Analytics의 Apache Spark 풀 내에서 이러한 라이브러리를 사용하여 풀의 실행기 수를 0으로 설정하여 단일 컴퓨터 모델을 빌드할 수 있습니다. 이 구성에서는 Apache Spark가 작동하지 않지만 단일 컴퓨터 모델을 만드는 간단하고 비용 효율적인 방법입니다.

게시된 [Azure Synapse Analytics 런타임](../spark/apache-spark-version-support.md)을 보면 사용 가능한 라이브러리 및 관련 버전에 대해 자세히 알아볼 수 있습니다.

## <a name="mmlspark"></a>MMLSpark
[MMLSpark](https://github.com/Azure/mmlspark)는 Apache Spark용 Microsoft Machine Learning 라이브러리입니다. 이 라이브러리는 Spark에서 데이터 과학자의 생산성을 높이고, 실험 속도를 높이고, 큰 데이터 세트에서 최첨단 기계 학습 기술(딥 러닝 포함)을 활용하도록 설계되었습니다. 

MMLSpark는 확장 가능한 ML 모델(예: 인덱싱 문자열)을 작성하고, 데이터를 기계 학습 알고리즘에 필요한 레이아웃으로 강제 변환하고, 기능 벡터를 어셈블할 때 SparkML의 하위 수준 API에 기반한 계층을 제공합니다. MMLSpark 라이브러리는 PySpark에서 모델을 작성하기 위한 이러한 일반적인 작업을 간소화합니다.

## <a name="automated-ml-in-azure-machine-learning"></a>Azure Machine Learning의 자동화된 ML 
Azure Machine Learning은 기계 학습 모델을 학습, 배포, 자동화, 관리 및 추적할 수 있는 클라우드 기반 환경입니다. Azure Machine Learning의 자동화된 ML은 학습 데이터 및 구성 설정을 적용하고, 다양한 기능 정규화/표준화 방법, 모델 및 하이퍼 매개 변수 설정의 조합을 자동으로 반복하여 최상의 모델에 도달합니다. 

Azure Synapse Analytics 내에서 자동화된 ML을 사용하는 경우 서로 다른 서비스 간의 긴밀한 통합을 활용하여 인증과 모델 학습을 간소화할 수 있습니다. 

> [!NOTE]
> 
> 이 [자습서](./spark/../apache-spark-azure-machine-learning-tutorial.md)를 따라 Azure Machine Learning 자동화 ML 실험을 만드는 방법에 대해 자세히 알아볼 수 있습니다.

## <a name="azure-cognitive-services"></a>Azure Cognitive Services
[Azure Cognitive Services](../../cognitive-services/what-are-cognitive-services.md)는 텍스트 분석을 통한 정서적 감정 분석이나 이미지 분석을 통한 개체 또는 얼굴 인식과 같은 일반적인 문제를 해결하기 위한 기계 학습 기능을 제공합니다. 이 서비스를 사용하기 위해 특별한 기계 학습 또는 데이터 과학 지식이 필요하지 않습니다. Cognitive Service는 기계 학습 솔루션의 구성 요소(데이터, 알고리즘 및 학습된 모델)의 일부 또는 전체를 제공합니다. 이러한 서비스를 사용하려면 기계 학습 또는 데이터 과학에 대한 경험은 없어도 되지만 데이터에 대한 일반적인 지식은 필요합니다. Azure Synapse Analytics 내에서 이러한 사전 학습된 Cognitive Services를 자동으로 활용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Synapse Analytics의 Apache Spark 풀 내에서 기계 학습 모델을 학습하는 다양한 옵션에 대한 개요를 제공합니다. 아래 자습서를 따라 모델 학습에 대해 자세히 알아볼 수 있습니다.

- Azure Machine Learning 및 Azure Synapse Analytics를 사용하여 자동화된 ML 실험 실행: [자동화된 ML 자습서](../spark/apache-spark-azure-machine-learning-tutorial.md) 
- SparkML 실험 실행: [Apache SparkML 자습서](../spark/apache-spark-machine-learning-mllib-notebook.md)
- 기본 라이브러리 보기: [Azure Synapse Analytics 런타임](../spark/apache-spark-version-support.md)