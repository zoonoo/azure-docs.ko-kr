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
ms.openlocfilehash: 6ddff84de6b8ffd5bc8f7c7dcaa7cb4df3d71f81
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88703920"
---
**컴퓨팅 대상은 한 학습 작업에서 다음 학습 작업으로 재사용할 수 없습니다**. 예를 들어 원격 VM을 사용자의 작업 영역에 연결한 후에는 여러 작업에 다시 사용할 수 있습니다.  기계 학습 파이프라인의 경우 각 컴퓨팅 대상에 적절한 [파이프라인 단계](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py)를 사용합니다.

|학습 &nbsp;대상|[자동화된 ML](../articles/machine-learning/concept-automated-ml.md) | [ML 파이프라인](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning 디자이너](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[로컬 컴퓨터](../articles/machine-learning/how-to-set-up-training-targets.md#local)| 예 | &nbsp; | &nbsp; |
|[Azure Machine Learning 컴퓨팅 클러스터](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| 예 & <br/>하이퍼 매개 변수&nbsp;튜닝 | 예 | 예 |
|[Azure Machine Learning 컴퓨팅 인스턴스](../articles/machine-learning/how-to-set-up-training-targets.md#instance) | 예 & <br/>하이퍼 매개 변수 튜닝 | 예 |  |
|[원격 VM](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | 예 & <br/>하이퍼 매개 변수 튜닝 | 예 | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| 예(SDK 로컬 모드 전용) | 예 | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | 예 | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | 예 | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | 예 | &nbsp; |
