---
title: 포함 파일
description: 포함 파일
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 12/04/2019
ms.openlocfilehash: 6106d4e0801500b0429e634651f3de342646b754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77156281"
---
**계산 대상은 한 교육 작업에서 다음 작업으로 다시 사용할 수 있습니다.** 예를 들어 원격 VM을 사용자의 작업 영역에 연결한 후에는 여러 작업에 다시 사용할 수 있습니다.  기계 학습 파이프라인의 경우 각 계산 대상에 대해 적절한 [파이프라인 단계를](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) 사용합니다.

|교육 &nbsp;대상|[자동화된 ML](../articles/machine-learning/concept-automated-ml.md) | [ML 파이프라인](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning 디자이너](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[로컬 컴퓨터](../articles/machine-learning/how-to-set-up-training-targets.md#local)| 예 | &nbsp; | &nbsp; |
|[Azure 기계 학습 계산 클러스터](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| 그래 & <br/>하이퍼매개&nbsp;변수 튜닝 | 예 | 예 |
|[원격 VM](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | 그래 & <br/>하이퍼 매개 변수 튜닝 | 예 | &nbsp; |
|[Azure&nbsp;데이터 브릭](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| 예(SDK 로컬 모드만) | 예 | &nbsp; |
|[Azure 데이터 레이크 분석](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | 예 | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | 예 | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | 예 | &nbsp; |
