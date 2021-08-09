---
title: Synapse Studio에서 Apache Spark 애플리케이션을 모니터링하는 방법
description: Synapse Studio를 사용하여 Apache Spark 애플리케이션을 모니터링하는 방법을 알아봅니다.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 5f9733866e85d79bdb85b8a24d1878e1169c2479
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586043"
---
# <a name="how-to-use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Synapse Studio를 사용하여 Apache Spark 애플리케이션을 모니터링하는 방법

Azure Synapse Analytics에서 Spark를 사용하여 작업 영역의 Spark 풀에서 Notebook, 작업 및 기타 종류의 애플리케이션을 실행할 수 있습니다.

이 문서에서는 Apache Spark 애플리케이션을 모니터링하여 최신 상태, 문제 및 진행 상황을 파악할 수 있도록 하는 방법을 설명합니다.

## <a name="access-apache-spark-applications-list"></a>Apache Spark 애플리케이션 목록에 대한 액세스

작업 영역에서 Apache Spark 애플리케이션 목록을 보려면 먼저 [Synapse Studio를 열고](https://web.azuresynapse.net/) 작업 영역을 선택합니다.

![작업 영역에 로그인](./media/common/login-workspace.png)

작업 영역을 연 후 왼쪽의 **모니터** 섹션을 선택합니다.

![모니터 허브 선택](./media/common/left-nav.png)

**Apache Spark** 애플리케이션을 선택하여 Apache Spark 애플리케이션 목록을 확인합니다.

 ![Apache Spark 애플리케이션](./media/how-to-monitor-spark-applications/monitor-hub-nav-spark-applications.png)

## <a name="filter-your-apache-spark-applications"></a>Apache Spark 애플리케이션 필터링

Apache Spark 애플리케이션 목록을 원하는 애플리케이션으로 필터링할 수 있습니다. 화면 위쪽의 필터를 사용하여 필터링할 필드를 지정할 수 있습니다.

예를 들어 뷰를 필터링하여 “sales”라는 이름이 포함된 Apache Spark 애플리케이션만을 확인할 수 있습니다.

![샘플 필터](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="view-details-about-a-specific-apache-spark-application"></a>특정 Apache Spark 애플리케이션에 대한 세부 정보 보기

Apache Spark 애플리케이션 중 하나에 대한 세부 정보를 보려면 Apache Spark 애플리케이션을 선택하고 세부 정보를 확인합니다. Apache Spark 애플리케이션이 여전히 실행 중인 경우 진행률을 모니터링할 수 있습니다. [자세히 알아봅니다](apache-spark-applications.md).

## <a name="next-steps"></a>다음 단계

파이프라인 실행을 모니터링하는 방법에 대한 자세한 내용은 [Synapse Studio에서 파이프라인 실행 모니터링](how-to-monitor-pipeline-runs.md) 문서를 참조하세요. 

Apache Spark 애플리케이션 디버깅에 대한 자세한 내용은 [Synapse Studio에서 Apache Spark 애플리케이션 모니터링](apache-spark-applications.md) 문서를 참조하세요.