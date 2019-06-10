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
ms.openlocfilehash: 50905eb987defac612f1055b450b682726f0a56f
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752969"
---
|학습 &nbsp;대상| GPU 지원 |[자동화 된 기계 학습](../articles/machine-learning/service/concept-automated-ml.md) | [ML 파이프라인](../articles/machine-learning/service/concept-ml-pipelines.md) | [시각적 인터페이스](../articles/machine-learning/service/ui-concept-visual-interface.md)
|----|:----:|:----:|:----:|:----:|
|[로컬 컴퓨터](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| 아마도 | 예 | &nbsp; | &nbsp; |
|[Azure Machine Learning 컴퓨팅](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| 예 | 예 & <br/>하이퍼 매개 변수&nbsp;튜닝 | 예 | 예 |
|[원격 VM](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |예 | 예 & <br/>하이퍼 매개 변수 튜닝 | 예 | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | 예 | 예 | &nbsp; |
|[Azure 데이터 레이크 분석](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | 예 | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | 예 | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | 예 | &nbsp; |

**모든 컴퓨팅 대상을 여러 학습 작업에 다시 사용할 수 있습니다**. 예를 들어 원격 VM을 사용자의 작업 영역에 연결한 후에는 여러 작업에 다시 사용할 수 있습니다.