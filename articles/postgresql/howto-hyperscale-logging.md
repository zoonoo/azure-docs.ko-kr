---
title: 로그 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL - 하이퍼스케일(Citus)에 대한 데이터베이스 로그에 액세스하는 방법
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: ca3cc2873fbc6db72b10c80daecddf1471e30ff4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100577064"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL의 로그 – 하이퍼스케일(Citus)

PostgreSQL 로그는 하이퍼스케일(Citus) 서버 그룹의 모든 노드에서 사용할 수 있습니다. 스토리지 서버 또는 분석 서비스에 로그를 제공할 수 있습니다. 해당 로그는 구성 오류 및 최적 상태가 아닌 성능 문제를 식별하고, 문제를 해결하고, 복구하는 데 사용될 수 있습니다.

## <a name="accessing-logs"></a>로그 액세스

하이퍼스케일(Citus) 코디네이터 또는 작업자 노드에 대한 PostgreSQL 로그에 액세스하려면 Azure Portal에서 다음 노드를 엽니다.

:::image type="content" source="media/howto-hyperscale-logging/choose-node.png" alt-text="노드 목록":::

선택한 노드에 대해 **진단 설정** 을 열고 **+ 진단 설정 추가** 를 클릭합니다.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="진단 설정 추가 단추":::

새 진단 설정의 이름을 선택하고 **PostgreSQLLogs** 상자를 확인합니다.  로그를 받을 대상을 선택합니다.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="PostgreSQL 로그 선택":::

## <a name="next-steps"></a>다음 단계

- [Log Analytics 쿼리 시작](../azure-monitor/logs/log-analytics-tutorial.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)에 대해 알아봅니다