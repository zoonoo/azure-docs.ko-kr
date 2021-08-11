---
title: Synapse Studio를 사용하여 파이프라인 실행 모니터링
description: Synapse Studio를 사용하여 작업 영역 파이프라인 실행을 모니터링합니다.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: dbd890117c78274392d5745e0563332371b404c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96452576"
---
# <a name="use-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Synapse Studio를 사용하여 작업 영역 파이프라인 실행 모니터링

Azure Synapse Analytics를 사용하면 솔루션 내에서 데이터 이동, 데이터 변환 및 컴퓨팅 활동을 자동화하고 통합할 수 있는 복잡한 파이프라인을 만들 수 있습니다. Synapse Studio를 사용하여 이러한 파이프라인을 작성하고 모니터링할 수 있습니다.

이 문서에서는 파이프라인 실행을 모니터링하여 파이프라인의 최신 상태, 이슈 및 진행률을 확인할 수 있는 방법을 설명합니다.

## <a name="access-pipeline-runs-list"></a>파이프라인 실행 목록 액세스

작업 영역에서 파이프라인 실행 목록을 보려면 먼저 [Synapse Studio를 열고](https://web.azuresynapse.net/) 작업 영역을 선택합니다.

![작업 영역에 로그인](./media/common/login-workspace.png)

작업 영역을 연 후 왼쪽의 **모니터** 섹션을 선택합니다.

![모니터 허브 선택](./media/common/left-nav.png)

**파이프라인 실행** 을 선택하여 파이프라인 실행 목록을 봅니다.

![파이프라인 실행 선택](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filter-your-pipeline-runs"></a>파이프라인 실행 필터링

파이프라인 실행 목록을 원하는 애플리케이션으로 필터링할 수 있습니다. 화면 위쪽의 필터를 사용하여 필터링할 필드를 지정할 수 있습니다.

예를 들어, 보기를 필터링하여 "휴일"이라는 파이프라인의 파이프라인 실행만 볼 수 있습니다.

![샘플 필터](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="view-details-about-a-specific-pipeline-run"></a>특정 파이프라인 실행에 대한 세부 정보 보기

파이프라인 실행에 대한 세부 정보를 보려면 파이프라인 실행을 선택합니다. 그러면 파이프라인 실행과 연결된 활동 실행이 표시됩니다. 파이프라인이 아직 실행 중인 경우 진행률을 모니터링할 수 있습니다. 
  
## <a name="next-steps"></a>다음 단계

애플리케이션 모니터링에 대한 자세한 내용은 [Apache Spark 애플리케이션 모니터링](how-to-monitor-spark-applications.md) 문서를 참조하세요. 
