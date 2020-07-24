---
title: Synapse Studio를 사용 하 여 파이프라인 실행 모니터링
description: Synapse Studio를 사용 하 여 작업 영역 파이프라인 실행을 모니터링 합니다.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 385b06dd7756c3bebf6557f9ea0b518473a37d49
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075907"
---
# <a name="use-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Synapse Studio를 사용 하 여 작업 영역 파이프라인 실행 모니터링

Azure Synapse Analytics를 사용 하면 솔루션 내에서 데이터 이동, 데이터 변환 및 계산 작업을 자동화 하 고 오케스트레이션 할 수 있는 복잡 한 파이프라인을 만들 수 있습니다. Synapse Studio (미리 보기)를 사용 하 여 이러한 파이프라인을 작성 하 고 모니터링할 수 있습니다.

이 문서에서는 파이프라인 실행을 모니터링 하 여 파이프라인의 최신 상태, 문제 및 진행률을 확인할 수 있는 방법을 설명 합니다.

## <a name="access-the-list-of-pipeline-runs"></a>파이프라인 실행 목록 액세스

작업 영역에서 파이프라인 실행 목록을 보려면 먼저 [Synapse Studio를 열고](https://web.azuresynapse.net/) 작업 영역을 선택 합니다.

![작업 영역에 로그인](./media/common/login-workspace.png)

작업 영역을 연 후 왼쪽의 **모니터** 섹션을 선택 합니다.

![모니터 허브 선택](./media/common/left-nav.png)

파이프라인 **실행** 을 선택 하 여 파이프라인 실행 목록을 봅니다.

![파이프라인 실행 선택](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filtering-your-pipeline-runs"></a>파이프라인 실행 필터링

파이프라인 실행 목록을 관심 있는 항목으로 필터링 할 수 있습니다. 화면 위쪽의 필터를 사용 하 여 필터링 할 필드를 지정할 수 있습니다.

예를 들어 뷰를 필터링 하 여 "휴일" 이라는 파이프라인에 대 한 파이프라인 실행만 볼 수 있습니다.

![필터 단추](./media/common/filter-button.png)

![샘플 필터](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="viewing-details-about-a-specific-pipeline-run"></a>특정 파이프라인 실행에 대 한 세부 정보 보기

파이프라인 실행에 대 한 세부 정보를 보려면 파이프라인 실행을 선택 합니다. 그런 다음 파이프라인 실행과 연결 된 활동 실행을 확인 합니다. 파이프라인이 아직 실행 중인 경우 진행률을 모니터링할 수 있습니다. 
  
## <a name="next-steps"></a>다음 단계

응용 프로그램 모니터링에 대해 자세히 알아보려면 [Apache Spark 응용 프로그램 모니터링](how-to-monitor-spark-applications.md) 문서를 참조 하세요. 
