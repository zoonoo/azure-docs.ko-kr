---
title: 기계 학습 파이프라인 빌드 - Azure Machine Learning 서비스
description: 이 문서에서는 Python용 Azure Machine Learning SDK로 빌드할 수 있는 기계 학습 파이프라인 및 파이프라인 사용의 장점에 대해 알아보겠습니다. ML(Machine Learning) 파이프라인은 데이터 과학자가 기계 학습 워크플로를 빌드, 최적화 및 관리하는 데 사용됩니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 11/07/2018
ms.openlocfilehash: 099b59cde4ee438f16b9d7e77bd81c004006cb71
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684871"
---
# <a name="pipelines-and-azure-machine-learning"></a>파이프라인 및 Azure Machine Learning

이 문서에서는 Python용 Azure Machine Learning SDK로 빌드할 수 있는 기계 학습 파이프라인 및 파이프라인 사용의 장점에 대해 알아보겠습니다.

## <a name="what-are-machine-learning-pipelines"></a>기계 학습 파이프라인이란?

ML(기계 학습) 파이프라인, 데이터 과학자, 데이터 엔지니어 및 IT 전문가를 사용하면 다음에 포함된 단계에서 공동 작업을 할 수 있습니다.
+ 정규화 및 변환 같은 데이터 준비
+ 모델 학습
+ 모델 평가
+ 배포 

다음 다이어그램은 예제 파이프라인을 보여줍니다.

[ ![Azure Machine Learning 서비스의 기계 학습 파이프라인](./media/concept-ml-pipelines/pipelines.png) ] (./media/concept-ml-pipelines/machine-learning-pipelines-big.png#lightbox)

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 파이프라인을 빌드하는 이유는 무엇인가요?

[Python용 Azure Machine Learning SDK](#the-python-sdk-for-pipelines)는 ML 파이프라인을 만들고 개별 파이프라인 실행을 제출 및 추적하는 데 사용할 수 있습니다.

파이프라인을 사용하면 단순성, 속도, 이식성 및 재사용을 통해 워크플로를 최적화할 수 있습니다. Azure Machine Learning을 사용하여 파이프라인을 빌드하면 인프라 대신 여러분이 가장 잘 알고 있는 기계 학습에 집중할 수 있습니다.

워크플로를 조정하고 테스트할 때 별도의 단계를 사용하여 필요한 단계만 다시 실행할 수 있습니다. 단계는 파이프라인의 계산 단위입니다. 위의 다이어그램에서 볼 수 있듯이, 데이터를 준비하는 작업에는 정규화, 변환, 유효성 검사, 기능화를 포함한(이에 국한되지 않음) 여러 단계가 관련될 수 있습니다. 데이터 원본 및 중간 데이터를 파이프라인을 통해 다시 사용하면 계산 시간 및 리소스가 절약됩니다. 

파이프라인 설계를 마친 후에는 종종 파이프라인의 교육 루프를 미세 조정합니다. 파이프라인을 다시 실행하면 재실행이 필요한 단계(예: 업데이트된 교육 스크립트)로 넘어가며, 변경되지 않은 부분을 건너뜁니다. 단계를 실행하는 데 사용된 변경되지 않은 스크립트에도 동일한 패러다임이 적용됩니다. 

Azure Machine Learning을 사용하면 파이프라인의 각 단계에서 Microsoft Cognitive Toolkit, TensorFlow 등의 다양한 도구 키트와 프레임워크를 사용할 수 있습니다. Azure는 개발자가 중간 데이터를 계산 대상과 간편하게 공유하기 위해 사용하는 다양한 [계산 대상](concept-azure-machine-learning-architecture.md)을 조정합니다. 

Azure Portal에서 직접 [파이프라인 실험에 대한 메트릭을 추적](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments). 

## <a name="key-advantages"></a>주요 장점

기계 학습 워크플로에 대한 파이프라인을 빌드하여 얻을 수 있는 데 주요 장점은 다음과 같습니다.

|주요 장점|설명|
|:-------:|-----------|
|**무인&nbsp;실행**|몇 가지 단계가 안정적인 무인 방식을 통해 병렬로 또는 순차적으로 실행되도록 예약합니다. 데이터 준비 및 모델링 작업이 며칠 또는 몇 주간 지속될 수 있기 때문에, 이제는 파이프라인이 실행되는 동안 다른 작업에 집중할 수 있습니다. |
|**다양한 혼합형 계산**|확장 가능한 이기종 계산 및 저장소에 안정적으로 조정되는 여러 파이프라인을 사용합니다. 개별 파이프라인 단계를 HDInsight, GPU Data Science VM, Databricks 등의 다른 계산 대상에서 실행하여 가용 계산 옵션을 효율적으로 사용할 수 있습니다.|
|**재사용 가능**|재교육 및 일괄 처리 점수 매기기 같은 특정 시나리오에 사용할 수 있도록 파이프라인을 템플릿화할 수 있습니다.  간단한 REST 호출을 통해 외부 시스템에서 파이프라인을 트리거할 수 있습니다.|
|**추적 및 버전 관리**|작업을 반복할 때 데이터 및 결과 경로를 수동으로 추적하는 대신, SDK 파이프라인을 사용하여 명시적으로 데이터 원본, 입력 및 출력의 이름을 지정하고 버전을 관리하는 한편 스크립트와 데이터를 개별적으로 관리하여 생산성을 높일 수 있습니다.|

## <a name="the-python-sdk-for-pipelines"></a>파이프라인용 Python SDK

Python을 사용하여 ML 파이프라인을 만듭니다. Azure Machine Learning SDK는 데이터 종속성이 없을 때 파이프라인의 단계를 순차적 방식 및 병렬 방식으로 처리하는 명령적 구문을 제공합니다. Jupyter Notebook 또는 선호하는 다른 IDE에서 이 SDK와 상호 작용 수 있습니다. 

선언적 데이터 종속성을 사용하여 작업을 최적화할 수 있습니다. 이 SDK에는 데이터 전송, 모델 게시 등의 일반적인 작업을 위해 미리 빌드된 모듈의 프레임워크가 포함됩니다. 파이프라인에서 다시 사용할 수 있는 사용자 지정 단계를 구현하여 개발자 고유의 규칙을 모델링하도록 프레임워크를 확장할 수 있습니다. SDK에서 직접 계산 대상 및 스토리지 리소스를 관리할 수도 있습니다.

일괄 처리 점수 매기기 또는 재교육 작업을 예약할 수 있도록 파이프라인을 템플릿으로 저장하고 REST 엔드포인트에 배포할 수 있습니다.

다음 섹션의 [파이프라인용 Python SDK 참조 문서](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) 및 Notebook을 통해 빌드 방법을 알아보세요.

## <a name="example-notebooks"></a>노트북 예제
 
다음 Notebook은 Azure Machine Learning을 사용하는 파이프라인을 설명합니다. [pipeline/pipeline-batch-scoring.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/pipeline/pipeline-batch-scoring.ipynb).
 
이 Notebook을 다운로드하려면 다음 단계를 수행합니다.
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
