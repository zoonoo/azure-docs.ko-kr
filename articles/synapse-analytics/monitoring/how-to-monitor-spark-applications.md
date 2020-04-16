---
title: 아파치 스파크 애플리케이션 모니터링
description: Azure 시냅스 스튜디오를 사용하여 아파치 스파크 응용 프로그램을 모니터링합니다.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: f231693fdcf3519e29eed38e47db52d92acc00fa
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430748"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Azure 시냅스 스튜디오(미리 보기)를 사용하여 아파치 스파크 응용 프로그램을 모니터링합니다.

Azure Synapse 분석을 사용하면 Spark를 사용하여 작업 공간의 Spark 풀에서 전자 필기장, 작업 및 기타 종류의 응용 프로그램을 실행할 수 있습니다.

이 문서에서는 Spark 응용 프로그램을 모니터링하는 방법을 설명하여 최신 상태, 문제 및 진행 상황을 감시할 수 있습니다.

## <a name="accessing-the-list-of-spark-applications"></a>스파크 응용 프로그램 목록 액세스

작업 영역에서 Spark 응용 프로그램 목록을 보려면 먼저 [Azure Synapse Studio를 열고](https://web.azuresynapse.net/) 작업 영역을 선택합니다.

  > [!div class="mx-imgBorder"]
  > ![작업 영역에 로그인](./media/common/login-workspace.png)

작업 영역을 열면 왼쪽의 **모니터** 섹션을 선택합니다.

  > [!div class="mx-imgBorder"]
  > ![모니터 허브 선택](./media/common/left-nav.png)

**스파크 응용 프로그램을** 선택하여 스파크 응용 프로그램 목록을 봅니다.

  > [!div class="mx-imgBorder"]
  > ![스파크 애플리케이션 선택](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>스파크 애플리케이션 필터링

Spark 응용 프로그램 목록을 관심 있는 응용 프로그램으로 필터링할 수 있습니다. 화면 상단의 필터를 사용하면 필터링할 필드를 지정할 수 있습니다.

예를 들어 뷰를 필터링하여 "sales"라는 이름이 포함된 Spark 응용 프로그램만 볼 수 있습니다.

  > [!div class="mx-imgBorder"]
  > ![필터 단추](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![샘플 필터](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>특정 스파크 응용 프로그램에 대한 세부 정보 보기

Spark 응용 프로그램 중 하나에 대한 세부 정보를 보려면 Spark 응용 프로그램을 선택하고 세부 정보를 봅니다. Spark 응용 프로그램이 계속 실행 중인 경우 진행 률을 모니터링할 수 있습니다.

## <a name="next-steps"></a>다음 단계

파이프라인 실행 모니터링에 대한 자세한 내용은 [모니터 파이프라인 실행 Azure Synapse Studio](how-to-monitor-pipeline-runs.md) 문서를 참조하세요.  
