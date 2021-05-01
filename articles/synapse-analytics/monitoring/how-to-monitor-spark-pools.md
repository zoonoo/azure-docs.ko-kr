---
title: Synapse Studio에서 Apache Spark 풀을 모니터링하는 방법
description: Synapse Studio를 사용하여 Apache Spark 풀을 모니터링하는 방법을 알아봅니다.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 8d95897e0c2d58b2a3955918be945800eed9ba56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96467246"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-pools"></a>Synapse Studio를 사용하여 Apache Spark 풀 모니터링

Azure Synapse Analytics에서 Spark를 사용하여 작업 영역의 Spark 풀에서 노트북, 작업 및 기타 종류의 애플리케이션을 실행할 수 있습니다.

이 문서에서는 다양한 작업 영역 사용자가 사용하고 있는 vCores 수를 포함하여 사용자의 풀 상태를 파악할 수 있도록 Apache Spark 풀을 모니터링하는 방법을 설명합니다.

## <a name="access-spark-pools-list"></a>Spark 풀 목록 액세스

작업 영역에서 Apache Spark 풀 목록을 보려면 먼저 [Synapse Studio를 열고](https://web.azuresynapse.net/) 작업 영역을 선택합니다.

![작업 영역에 로그인](./media/common/login-workspace.png)

작업 영역을 연 후 왼쪽의 **모니터** 섹션을 선택합니다.

![모니터 허브 선택](./media/common/left-nav.png)

**Apache Spark 풀** 을 선택하면 Apache Spark 풀 목록을 볼 수 있습니다.

 ![Apache Spark 풀 선택](./media/how-to-monitor-spark-pools/monitor-hub-nav-spark-pools.png)

## <a name="filter-your-spark-pools"></a>Spark 풀 필터링

Spark 풀 목록을 관심 있는 풀로 필터링할 수 있습니다. 화면 위쪽의 필터를 사용하여 필터링할 필드를 지정할 수 있습니다.

예를 들어 뷰를 필터링하여 이름이 "dataprep"인 Spark 풀만 볼 수 있습니다.

![샘플 필터](./media/how-to-monitor-spark-pools/filter-example.png)

## <a name="view-details-about-a-specific-spark-pool"></a>특정 Spark 풀 세부 정보 보기

Spark 풀 중 하나에 대한 세부 정보를 보려면 Spark 풀을 선택하여 세부 정보를 확인합니다.

![Apache Spark 풀 세부 정보](./media/how-to-monitor-spark-pools/spark-pool-details.png)

## <a name="next-steps"></a>다음 단계

파이프라인 실행을 모니터링하는 방법에 대한 자세한 내용은 [Synapse Studio에서 파이프라인 실행 모니터링](how-to-monitor-pipeline-runs.md) 문서를 참조하세요. 

Apache Spark 애플리케이션 모니터링에 대한 자세한 내용은 see the [Synapse Studio에서 Apache Spark 애플리케이션 모니터링](how-to-monitor-spark-applications.md) 문서를 참조하세요.
