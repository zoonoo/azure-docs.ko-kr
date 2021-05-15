---
title: Synapse Studio에서 SQL 요청을 모니터링하는 방법
description: Synapse Studio를 사용하여 SQL 요청을 모니터링하는 방법을 알아봅니다.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 7296fec91decaf1bd80829f9f3a743a518fbb7a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96467406"
---
# <a name="use-synapse-studio-to-monitor-your-sql-requests"></a>Synapse Studio를 사용하여 SQL 요청 모니터링

Synapse Studio를 사용하여 작업 영역의 SQL 풀에서 SQL 스크립트를 실행할 수 있습니다.

이 문서에서는 SQL 요청을 모니터링하여 실행 중인 요청의 상태를 확인하고 기록 요청의 세부 정보를 검색할 수 있도록 하는 방법을 설명합니다.

## <a name="access-sql-requests-list"></a>SQL 요청 목록 액세스

작업 영역에서 SQL 요청 목록을 보려면 먼저 [Synapse Studio를 열고](https://web.azuresynapse.net/) 작업 영역을 선택합니다.

![작업 영역에 로그인](./media/common/login-workspace.png)

작업 영역을 연 후 왼쪽의 **모니터** 섹션을 선택합니다.

![모니터 허브 선택](./media/common/left-nav.png)

**SQL 요청** 을 선택하여 SQL 요청 목록을 봅니다.

 ![SQL 요청 선택](./media/how-to-monitor-sql-requests/monitor-hub-nav-sql-requests.png)

## <a name="select-a-sql-pool"></a>SQL 풀 선택

기본적으로 이 보기에는 기본 제공되는 서버리스 SQL 풀의 SQL 요청 기록이 표시됩니다. 전용 SQL 풀 중 하나를 선택하여 해당 풀의 SQL 요청 기록을 볼 수 있습니다.

![SQL 풀 선택](./media/how-to-monitor-sql-requests/select-pool.png)

## <a name="filter-your-sql-requests"></a>SQL 요청 필터링

SQL 요청 목록을 관심 있는 요청으로 필터링할 수 있습니다. 화면 위쪽의 필터를 사용하여 필터링할 필드를 지정할 수 있습니다.

예를 들어 다음과 같이 보기를 필터링하여 `maria@contoso.com`에서 제출한 SQL 요청만 볼 수 있습니다.

![샘플 필터](./media/how-to-monitor-sql-requests/filter-example.png)

## <a name="view-details-about-a-specific-sql-request"></a>특정 SQL 요청에 관한 세부 정보 보기

SQL 요청 중 하나에 대한 세부 정보를 보려면 SQL 요청을 열어 세부 정보 보기로 이동합니다. 전용 SQL 풀에서 실행되는 복잡한 요청의 경우 진행률을 모니터링할 수 있습니다.

## <a name="next-steps"></a>다음 단계

파이프라인 실행을 모니터링하는 방법에 대한 자세한 내용은 [Synapse Studio에서 파이프라인 실행 모니터링](how-to-monitor-pipeline-runs.md) 문서를 참조하세요. 

Apache Spark 애플리케이션 모니터링에 대한 자세한 내용은 [Synapse Studio에서 Apache Spark 애플리케이션 모니터링](how-to-monitor-spark-applications.md) 문서를 참조하세요.