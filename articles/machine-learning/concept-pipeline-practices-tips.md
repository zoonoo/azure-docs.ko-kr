---
title: 기계 학습 파이프라인 반복 및 진화
titleSuffix: Azure Machine Learning
description: 빠른 개발을 위한 패턴, 사례 및 팁
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 05/01/2020
ms.openlocfilehash: 2ea353469ed111eebb591aa6ba86c652683cc2f0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82858185"
---
# <a name="iterating-and-evolving-machine-learning-pipelines"></a>기계 학습 파이프라인 반복 및 진화

Azure Machine Learning 파이프라인은 코드를 모듈화 하 고, 결과를 다시 사용 하 고, 계산 리소스를 최적화 하는 효율적인 방법을 제공 합니다. 파이프라인 사용에 대 한 몇 가지 실질적인 팁과 사례는 다음과 같습니다.

## <a name="how-do-you-get-started-with-pipelines"></a>파이프라인을 시작 하려면 어떻게 해야 하나요?

파이프라인을 처음 접하는 경우 시작 하기 위한 몇 가지 옵션이 있습니다.

* 생성 프로세스를 읽고 다시 만드는 방법에 대 한 자세한 내용은 [AZURE MACHINE LEARNING SDK를 사용 하 여 machine learning 파이프라인 만들기 및 실행](how-to-create-your-first-pipeline.md) 문서를 사용 하는 것이 좋습니다. 
* Jupyter 노트북에서 대화형으로 학습할 수 있는 경우 [Azure Machine Learning 파이프라인](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-getting-started.ipynb) 에서 개발한 파이프라인이 바로 시작할 수 있습니다.
* 코드 우선 상황을 선호 하는 경우 [Azure Machine Learning 파이프라인](https://github.com/microsoft/aml-pipelines-demo) 을 복제 하는 것이 좋은 시작점을 제공 합니다.

## <a name="how-do-you-modularize-pipeline-code"></a>파이프라인 코드를 어떻게 모듈화? 

모듈 및 `ModuleStep` 클래스는 ML 코드를 모듈화 수 있는 좋은 기회를 제공 합니다. 그러나 파이프라인 단계 간을 이동 하는 것은 함수 호출 보다 훨씬 더 비쌉니다. 확인 해야 하는 질문은 그리 많지 않습니다. "이러한 함수와 데이터는 다른 섹션에 있는 것과 개념적으로 다릅니다." 하지만 "이러한 함수와 데이터를 별도로 발전 시키려면 어떻게 해야 하나요?" 또는 "계산 비용이 많이 들고 출력을 다시 사용할 수 있습니까?" 자세한 내용은 thisn'tebook [How to Create Module, ModuleVersion, and use a 파이프라인 In ModuleStep](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb)항목을 참조 하세요.

앞서 설명한 것 처럼 학습에서 데이터 준비를 분리 하는 것은 종종 이러한 기회입니다. 경우에 따라 데이터 준비는 복잡 하 고 시간이 많이 걸리고 프로세스를 별도의 파이프라인 단계로 나눌 수 있습니다. 다른 기회에는 학습 후 테스트 및 분석이 포함 됩니다. 

## <a name="how-do-you-speed-pipeline-iteration"></a>파이프라인 반복 속도는 어떻게 되나요? 

파이프라인을 신속 하 게 반복 하는 일반적인 방법은 다음과 같습니다. 

- 파이프라인을 복제 (복사본 만들기) 하 고 파이프라인을 신속 하 게 다시 실행
- 시작 시간을 방지 하기 위해 계산 인스턴스를 계속 실행 합니다.
- 데이터 및 다시 사용을 허용 하는 단계를 구성 하면 파이프라인이 변경 되지 않은 데이터 다시 계산을 건너뛸 수 있습니다.

신속 하 게 반복 하려는 경우 파이프라인을 복제 하 여 파이프라인을 만들고 파이프라인을 다시 실행할 수 있습니다. 또 다른 유용한 기술은 계산 웜을 유지 하는 경우 새 계산을 계산 하는 비용이 발생 하지 않도록 하는 것입니다. 실행 결과를 다시 사용할 수 있도록 단계를 설정 하는 경우 반복 실행은 가능한 경우 (단계가 변경 되지 않은 경우) 결과를 다시 사용 합니다.

## <a name="how-do-you-collaborate-using-ml-pipelines"></a>ML 파이프라인을 사용 하 여 공동 작업을 수행 하려면 어떻게 하나요? 

별도의 파이프라인은 활동을 분할 하는 자연 스러운 선입니다. 여러 개발자나 여러 팀은 서로 다른 단계에서 작업을 수행할 수 있습니다. 즉, 단계 간에 이동 하는 데이터 및 인수에 대해 합의 하기만 하면 됩니다. 

활성 개발 중에 `PipelineRun` `StepRun` 작업 영역에서 결과를 검색 및 실행 하 고, 이러한 개체를 사용 하 여 최종 및 중간 출력을 다운로드 하 고, 사용자 고유의으로 모듈화 작업에 해당 아티팩트를 사용할 수 있습니다.

## <a name="use-pipelines-to-test-techniques-in-isolation"></a>파이프라인을 사용 하 여 격리에서 기술 테스트

실제 ML 솔루션은 일반적으로 모든 단계에 대 한 상당한 사용자 지정을 포함 합니다. 원시 데이터는 필터링, 변환 및 확대와 같은 방식으로 준비 해야 하는 경우가 많습니다. 학습 프로세스에는 몇 가지 잠재적인 아키텍처가 있을 수 있으며 심층 학습의 경우 계층 크기 및 활성화 기능에 대 한 여러 가지 가능한 변형이 있을 수 있습니다. 일관적인 아키텍처를 사용 하는 경우에도 하이퍼 매개 변수 검색은 심각한 wins를 생성할 수 있습니다.

[Automl](concept-automated-ml.md) 및 자동화 된 하이퍼 [매개 변수 검색과](how-to-tune-hyperparameters.md)같은 도구 뿐만 아니라 파이프라인은 A/B 테스트 솔루션에 대 한 중요 한 도구 일 수 있습니다. 파이프라인 단계에 대 한 여러 가지 변형이 있는 경우 변형을 시도 하는 별도의 실행을 쉽게 생성할 수 있습니다. 

```python
data_preparation_variants = [data1, data2, data3]
data_augmentation_variants = [aug1, aug2]
architecture_variants = [train1, train2, train3]

# Cartesian product
all_variants = np.array(np.meshgrid(data_preparation_variants,data_augmentation_variants,architecture_variants)).T.reshape(-1,3)

pipelines = [Pipeline(workspace=ws, steps=variant.tolist(), description=str(variant)) for variant in all_variants]

```

