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
ms.date: 09/17/2020
ms.openlocfilehash: 5d312e5ccc0eeed3e17ffa855c496779c5769c5c
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2020
ms.locfileid: "91707741"
---
**컴퓨팅 대상은 한 학습 작업에서 다음 학습 작업으로 재사용할 수 없습니다**. 예를 들어 원격 VM을 사용자의 작업 영역에 연결한 후에는 여러 작업에 다시 사용할 수 있습니다.  기계 학습 파이프라인의 경우 각 컴퓨팅 대상에 적절한 [파이프라인 단계](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py&preserve-view=true)를 사용합니다.

대부분의 작업에 대한 학습 컴퓨팅 목표에 대해 아래 리소스를 사용할 수 있습니다.  그러나 모든 리소스를 자동화된 기계 학습, 기계 학습 파이프라인 또는 디자이너에 사용할 수 있는 것은 아닙니다.

|학습 &nbsp;대상|[자동화된 ML](../articles/machine-learning/concept-automated-ml.md) | [ML 파이프라인](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning 디자이너](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[로컬 컴퓨터](../articles/machine-learning/how-to-attach-compute-targets.md#local)| 예 | &nbsp; | &nbsp; |
|[Azure Machine Learning 컴퓨팅 클러스터](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| 예 | 예 | 예 |
|[Azure Machine Learning 컴퓨팅 인스턴스](../articles/machine-learning/how-to-create-manage-compute-instance.md) | 예(SDK를 통해)  | 예 |  |
|[원격 VM](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | 예  | 예 | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| 예(SDK 로컬 모드 전용) | 예 | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | 예 | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | 예 | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | 예 | &nbsp; |
