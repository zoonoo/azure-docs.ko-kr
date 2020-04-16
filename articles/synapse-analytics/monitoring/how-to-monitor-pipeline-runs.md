---
title: Azure 시냅스 스튜디오를 실행하는 모니터 파이프라인(미리 보기)
description: Azure Synapse Studio를 사용하여 작업 영역 파이프라인 실행을 모니터링합니다.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 712dc62e29229f03dec12afdf18edbf55667dbdf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430787"
---
# <a name="use-azure-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Azure Synapse Studio를 사용하여 작업 영역 파이프라인 실행을 모니터링합니다.

Azure Synapse Analytics를 사용하면 솔루션 내에서 데이터 이동, 데이터 변환 및 계산 활동을 자동화하고 오케스트레이션할 수 있는 복잡한 파이프라인을 만들 수 있습니다. Azure Synapse Studio(미리 보기)를 사용하여 이러한 파이프라인을 작성하고 모니터링할 수 있습니다.

이 문서에서는 파이프라인 실행을 모니터링하는 방법을 설명하며, 이를 통해 파이프라인의 최신 상태, 문제 및 진행 상황을 확인할 수 있습니다.

## <a name="access-the-list-of-pipeline-runs"></a>파이프라인 실행 목록에 액세스

작업 영역에서 실행되는 파이프라인 목록을 보려면 먼저 [Azure Synapse Studio를 열고](https://web.azuresynapse.net/) 작업 영역을 선택합니다.

![작업 영역에 로그인](./media/common/login-workspace.png)

작업 영역을 열면 왼쪽의 **모니터** 섹션을 선택합니다.

![모니터 허브 선택](./media/common/left-nav.png)

**파이프라인 실행을** 선택하여 파이프라인 실행 목록을 봅니다.

![파이프라인 실행 선택](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filtering-your-pipeline-runs"></a>파이프라인 실행 필터링

파이프라인 실행 목록을 관심 있는 파이프라인으로 필터링할 수 있습니다. 화면 상단의 필터를 사용하면 필터링할 필드를 지정할 수 있습니다.

예를 들어 뷰를 필터링하여 "holiday"라는 파이프라인에 대한 파이프라인 실행만 볼 수 있습니다.

![필터 단추](./media/common/filter-button.png)

![샘플 필터](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="viewing-details-about-a-specific-pipeline-run"></a>특정 파이프라인 실행에 대한 세부 정보 보기

파이프라인 실행에 대한 세부 정보를 보려면 파이프라인 실행을 선택합니다. 그런 다음 파이프라인 실행과 연결된 활동 실행을 봅니다. 파이프라인이 계속 실행 중인 경우 진행률을 모니터링할 수 있습니다. 
  
## <a name="next-steps"></a>다음 단계

응용 프로그램 모니터링에 대한 자세한 내용은 [모니터 아파치 스파크 응용 프로그램](how-to-monitor-spark-applications.md) 문서를 참조하십시오. 
