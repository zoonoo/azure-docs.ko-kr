---
title: 응용 프로그램 Apache Spark 모니터링
description: Azure Synapse Studio를 사용 하 여 Apache Spark 응용 프로그램을 모니터링 합니다.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: f231693fdcf3519e29eed38e47db52d92acc00fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430748"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Azure Synapse Studio (미리 보기)를 사용 하 여 Apache Spark 응용 프로그램 모니터링

Azure Synapse Analytics를 사용 하면 Spark 풀의 작업 영역에서 Spark를 사용 하 여 노트북, 작업 및 기타 종류의 응용 프로그램을 실행할 수 있습니다.

이 문서에서는 Spark 응용 프로그램을 모니터링 하 여 최신 상태, 문제 및 진행 상황을 파악할 수 있도록 하는 방법을 설명 합니다.

## <a name="accessing-the-list-of-spark-applications"></a>Spark 응용 프로그램 목록 액세스

작업 영역에서 Spark 응용 프로그램 목록을 보려면 먼저 [Azure Synapse Studio를 열고](https://web.azuresynapse.net/) 작업 영역을 선택 합니다.

  > [!div class="mx-imgBorder"]
  > ![작업 영역에 로그인](./media/common/login-workspace.png)

작업 영역을 연 후 왼쪽의 **모니터** 섹션을 선택 합니다.

  > [!div class="mx-imgBorder"]
  > ![모니터 허브 선택](./media/common/left-nav.png)

Spark 응용 프로그램 목록을 보려면 spark **응용 프로그램** 을 선택 합니다.

  > [!div class="mx-imgBorder"]
  > ![Spark 응용 프로그램 선택](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>Spark 응용 프로그램 필터링

Spark 응용 프로그램 목록을 원하는 항목으로 필터링 할 수 있습니다. 화면 위쪽의 필터를 사용 하 여 필터링 할 필드를 지정할 수 있습니다.

예를 들어 뷰를 필터링 하 여 이름이 "sales" 인 Spark 응용 프로그램만 볼 수 있습니다.

  > [!div class="mx-imgBorder"]
  > ![필터 단추](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![샘플 필터](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>특정 Spark 응용 프로그램에 대 한 세부 정보 보기

Spark 응용 프로그램 중 하나에 대 한 세부 정보를 보려면 Spark 응용 프로그램을 선택 하 고 세부 정보를 확인 합니다. Spark 응용 프로그램이 계속 실행 중인 경우 진행률을 모니터링할 수 있습니다.

## <a name="next-steps"></a>다음 단계

파이프라인 실행을 모니터링 하는 방법에 대 한 자세한 내용은 [Monitor 파이프라인 실행 Azure Synapse Studio](how-to-monitor-pipeline-runs.md) 문서를 참조 하세요.  
