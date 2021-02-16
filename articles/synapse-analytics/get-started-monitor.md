---
title: '자습서: Azure Synapse Analytics 시작 - Synapse 작업 영역 모니터링'
description: 이 자습서에서는 Synapse 작업 영역에서 작업을 모니터링하는 방법에 대해 알아봅니다.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: monitoring
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 8c0cdcad9a7803e0d8063362ca62887990045c86
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209817"
---
# <a name="monitor-your-synapse-workspace"></a>Synapse 작업 영역 모니터링

이 자습서에서는 Synapse 작업 영역에서 작업을 모니터링하는 방법에 대해 알아봅니다. SQL, Apache Spark 및 파이프라인에 대한 현재 및 과거의 작업을 모니터링할 수 있습니다. 

## <a name="introduction-to-the-monitor-hub"></a>모니터 허브 소개

Synapse Studio를 열고 **모니터** 허브로 이동합니다. 여기에서 작업 영역에서 수행되는 모든 작업의 기록과 현재 활성 상태인 작업을 확인할 수 있습니다. 

* **통합** 에서 파이프라인, 트리거 및 통합 런타임을 모니터링할 수 있습니다.
* **작업** 에서 Spark 및 SQL 작업을 모니터링할 수 있습니다. 

## <a name="integration"></a>통합

1. **통합 > 파이프라인** 으로 이동합니다. 이 보기에서는 파이프라인이 작업 영역에서 실행될 때마다 볼 수 있습니다. 
1. 이전 단계에서 실행한 파이프라인을 찾아 해당 **파이프라인 이름** 을 클릭하여 세부 정보를 확인합니다.
1. Synapse Studio 위쪽의 **이동 경로 탐색 막대** 를 클릭하고 **모든 파이프라인 실행** 을 클릭하여 이전 보기로 돌아갑니다.

## <a name="apache-spark-activities"></a>Apache Spark 작업

1. **작업 > Apache Spark 애플리케이션** 으로 이동합니다. 이제 실행 중이거나 작업 영역에서 실행되는 모든 Spark 애플리케이션을 볼 수 있습니다.
1. 더 이상 실행되지 않는 애플리케이션을 찾고 해당 **애플리케이션 이름** 를 클릭합니다. 이제 spark 애플리케이션의 세부 정보를 볼 수 있습니다.
1. Apache Spark에 대해 잘 알고 있는 경우 **Spark 기록 서버** 를 클릭하여 표준 Apache Spark 기록 서버 UI를 찾을 수 있습니다.

## <a name="sql-activities"></a>SQL 작업

1. **작업 > SQL 요청** 으로 이동합니다.
1. 이 보기에서는 SQL 요청을 볼 수 있습니다.
1. **풀** 필터에서 모니터링할 **풀** 을 선택합니다. 이제 실행 중이거나 해당 풀의 작업 영역에서 실행되는 모든 SQL 요청을 볼 수 있습니다.
1. 특정 SQL 요청을 찾아 해당 항목을 마우스로 가리킵니다. 가리키면 SQL 스크립트 아이콘이 표시됩니다.
1. SQL 스크립트 아이콘을 클릭하여 SQL 요청의 전체 텍스트를 확인합니다.

    > [!NOTE] 
    > 작업 영역이 활성화된 전용 SQL 풀(이전의 SQL DW)에서 Synapse Studio를 통해 제출된 SQL 요청은 Monitor 허브에서 볼 수 있습니다. 다른 모든 모니터링 활동의 경우 Azure Portal 전용 SQL 풀(이전의 SQL DW) 모니터링으로 이동할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [지식 센터 살펴보기](get-started-knowledge-center.md)
