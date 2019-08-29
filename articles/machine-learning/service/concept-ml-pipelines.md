---
title: ML 파이프라인 이란?
titleSuffix: Azure Machine Learning service
description: 이 문서에서는 Python용 Azure Machine Learning SDK로 빌드할 수 있는 기계 학습 파이프라인 및 파이프라인 사용의 장점에 대해 알아보겠습니다. ML(기계 학습) 파이프라인은 데이터 과학자가 해당 기계 학습 워크플로를 빌드 및 최적화하고 관리하는 데 사용합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 7d7333a9316e4d39fd550872c3df04024a75d21d
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128323"
---
# <a name="what-are-ml-pipelines-in-azure-machine-learning-service"></a>Azure Machine Learning 서비스의 ML 파이프라인 이란?

Azure Machine Learning 서비스를 사용 하 여 빌드 및 관리할 수 있는 machine learning 파이프라인에 대해 알아봅니다. 

ML(기계 학습) 파이프라인, 데이터 과학자, 데이터 엔지니어 및 IT 전문가를 사용하면 다음에 포함된 단계에서 공동 작업을 할 수 있습니다.
+ 정규화 및 변환 같은 데이터 준비
+ 모델 학습
+ 모델 평가
+ 배포

[첫 번째 파이프라인을 만드는](how-to-create-your-first-pipeline.md) 방법을 알아봅니다.

![Azure Machine Learning Service의 기계 학습 파이프라인](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>어떤 Azure 파이프라인 기술을 사용 해야 하나요?

Azure 클라우드는 각각 다른 용도로 여러 다른 파이프라인을 제공 합니다. 다음 표에서는 다양 한 파이프라인 및 사용 되는 방법을 보여 줍니다.

| 파이프라인 | 수행하는 작업 | 정식 파이프 |
| ---- | ---- | ---- |
| 파이프라인 Azure Machine Learning | 기계 학습 시나리오에 대 한 템플릿으로 사용할 수 있는 재사용 가능한 기계 학습 워크플로를 정의 합니다. | 데이터 > 모델 |
| [Azure Data Factory 파이프라인](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | 작업을 수행 하는 데 필요한 데이터 이동, 변환 및 제어 작업을 그룹화 합니다.  | 데이터 > 데이터 |
| [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | 모든 플랫폼/모든 클라우드에 응용 프로그램의 지속적인 통합 및 전달  | 코드 > 앱/서비스 |

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 파이프라인을 빌드하는 이유는 무엇인가요?

Machine learning 파이프라인은 인프라 및 자동화 대신 전문 지식, 기계 학습에 집중할 수 있도록 속도, 이식성 및 재사용을 통해 워크플로를 최적화 합니다.

파이프라인은 파이프라인에서 고유한 계산 단위인 여러 **단계**에서 생성 됩니다. 각 단계는 독립적으로 실행 되 고 격리 된 계산 리소스를 사용할 수 있습니다.
독립적인 단계를 통해 여러 데이터 과학자가 과도 하 게 처리 시간이 소모 된 계산 리소스 없이 동일한 파이프라인에서 동시에 작업할 수 있으며 각 단계에 대해 다른 계산 형식/크기를 쉽게 사용할 수 있습니다.

파이프라인 설계 후에는 종종 파이프라인의 학습 루프를 미세 조정합니다. 파이프라인을 다시 실행 하면 실행이 업데이트 된 학습 스크립트와 같이 다시 실행 해야 하는 고유한 단계로 이동 하 여 변경 되지 않은 항목을 건너뜁니다. 단계를 실행하는 데 사용된 변경되지 않은 스크립트에도 동일한 패러다임이 적용됩니다. 이러한 재사용 기능을 사용 하면 기본 데이터가 변경 되지 않은 경우 데이터 수집 및 변환과 같은 비용이 많이 들고 시간이 많이 걸리는 단계를 실행 하지 않아도 됩니다.

Azure Machine Learning를 사용 하 여 파이프라인의 각 단계에 대 한 다양 한 도구 키트와 프레임 워크 (예: PyTorch 또는 TensorFlow)를 사용할 수 있습니다. Azure는 중간 데이터가 다운스트림 컴퓨팅 대상과 간편하게 공유될 수 있도록 사용하는 다양한 [컴퓨팅 대상](concept-azure-machine-learning-architecture.md) 간에 조정합니다.

Azure Portal에서 직접 [파이프라인 실험에 대한 메트릭을 추적](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments). 파이프라인이 게시 된 후에는 모든 플랫폼 또는 스택에서 파이프라인을 다시 실행할 수 있는 REST 끝점을 구성할 수 있습니다.

## <a name="key-advantages"></a>주요 장점

기계 학습 워크플로에 파이프라인을 사용 하는 경우의 주요 이점은 다음과 같습니다.

|주요 장점|Description|
|:-------:|-----------|
|**무인&nbsp;실행**|안정적이 고 무인 방식으로 병렬로 실행 하거나 순서 대로 실행 하는 단계를 예약 합니다. 데이터 준비 및 모델링은 마지막 일 또는 몇 주, 그리고 파이프라인을 사용 하 여 프로세스가 실행 되는 동안 다른 작업에 집중할 수 있습니다. |
|**다른 유형의 계산**|다른 유형의 확장 가능한 계산 리소스 및 저장소 위치에서 안정적으로 조정 된 여러 파이프라인을 사용 합니다. HDInsight, GPU 데이터 과학 Vm 및 Databricks 같은 다양 한 계산 대상에서 개별 파이프라인 단계를 실행 하 여 사용 가능한 계산 리소스를 효율적으로 사용 합니다.|
|**재사용 가능**|재 학습 및 일괄 처리 점수 매기기와 같은 특정 시나리오에 대 한 파이프라인 템플릿을 만듭니다. 간단한 REST 호출을 통해 외부 시스템에서 게시 된 파이프라인을 트리거합니다.|
|**추적 및 버전 관리**|반복하면서 데이터 및 결과 경로를 수동으로 추적하는 대신, Pipelines SDK를 사용하여 데이터 원본, 입력 및 출력의 이름과 버전을 명시적으로 지정합니다. 생산성 향상을 위해 스크립트와 데이터를 별도로 관리할 수도 있습니다.|
|**작업할**|파이프라인을 통해 데이터 과학자는 기계 학습 디자인 프로세스의 모든 영역에서 공동 작업을 수행할 수 있으며 파이프라인 단계에서 동시에 작업할 수 있습니다.|

## <a name="the-python-sdk-for-pipelines"></a>파이프라인용 Python SDK

[PYTHON SDK를 사용](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) 하 여 선호 하는 IDE (통합 개발 환경) 또는 Jupyter 노트북에서 ML 파이프라인을 만들 수 있습니다. Azure Machine Learning SDK는 데이터 종속성이 없을 때 파이프라인의 단계를 순차적 방식 및 병렬 방식으로 처리하는 명령적 구문을 제공합니다. 

선언적 데이터 종속성을 사용하여 작업을 최적화할 수 있습니다. 이 SDK에는 데이터 전송, 모델 게시 등의 일반적인 작업을 위해 사전 빌드된 모듈의 프레임워크가 포함되어 있습니다. 파이프라인에서 재사용 가능한 사용자 지정 단계를 구현 하 여 사용자 고유의 규칙을 모델링 하도록 프레임 워크를 확장할 수 있습니다. SDK에서 직접 컴퓨팅 대상 및 스토리지 리소스를 관리할 수도 있습니다.

파이프라인을 템플릿으로 저장 하 고 일괄 처리 점수 매기기 또는 재 학습 작업을 위해 REST 끝점에 배포 합니다.

파이프라인에 대한 Python 패키지에는 두 가지 [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) 및 [azureml-pipeline-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)가 있습니다. 신속 하 게 시작 하려면 다음과 같이 미리 작성 된 모듈 중 하나를 사용 합니다.

* [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep) 를 사용 하 여 한 단계에서 Python 스크립트 실행
* [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep) 를 사용 하 여 저장소 옵션 간 데이터 전송
* [AutoMLStep](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlstep) 를 사용 하 여 automl 파이프라인 단계 만들기

## <a name="next-steps"></a>다음 단계

+ [첫 번째 파이프라인을 만드는](how-to-create-your-first-pipeline.md) 방법을 알아봅니다.

+ [대량 데이터에서 일괄 처리 예측을 실행](how-to-run-batch-predictions.md)하는 방법에 대해 알아봅니다.

+ [파이프라인에 대 한 SDK 참조 문서](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py)를 참조 하세요.

+ 예제 Jupyter 노트북 보여주는 [Azure Machine Learning 파이프라인](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)을 사용해 보세요. 노트북을 실행 하 여 [이 서비스를 탐색](samples-notebooks.md)하는 방법을 알아봅니다.
