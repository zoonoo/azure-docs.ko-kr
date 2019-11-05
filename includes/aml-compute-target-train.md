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
ms.date: 05/30/2019
ms.openlocfilehash: aa1120db5451583153928bd2cbc383a46781a267
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489565"
---
**계산 대상은 한 학습 작업에서 다음 학습 작업까지 다시 사용할 수 있습니다**. 예를 들어 원격 VM을 사용자의 작업 영역에 연결한 후에는 여러 작업에 다시 사용할 수 있습니다.

|학습 &nbsp;대상| GPU 지원 |[자동화 된 ML](../articles/machine-learning/service/concept-automated-ml.md) | [ML 파이프라인](../articles/machine-learning/service/concept-ml-pipelines.md) | [Azure Machine Learning 디자이너](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|:----:|
|[로컬 컴퓨터](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| 경우가 | 예 | &nbsp; | &nbsp; |
|[Azure Machine Learning 계산 인스턴스](../articles/machine-learning/service/concept-compute-instance.md)| 예 | | 예 |  |
|[Azure Machine Learning 계산 클러스터](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| 예 | 예 & <br/>하이퍼 매개 변수&nbsp;튜닝 | 예 | 예 |
|[원격 VM](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |예 | 예 & <br/>하이퍼 매개 변수 튜닝 | 예 | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | 예 | 예 | &nbsp; |
|[Azure 데이터 레이크 분석](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | 예 | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | 예 | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | 예 | &nbsp; |
