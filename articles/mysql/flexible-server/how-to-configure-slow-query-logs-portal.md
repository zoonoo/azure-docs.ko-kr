---
title: 저속 쿼리 로그 구성-Azure Portal Azure Database for MySQL 유연한 서버
description: 이 문서에서는 Azure Portal에서 Azure Database for MySQL 유연한 서버에서 느리게 쿼리 로그를 구성 하 고 액세스 하는 방법을 설명 합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: e2046673cda17c58153ceb12eee31edb83365092
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91565739"
---
# <a name="configure-and-access-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure Database for MySQL 유연한 서버에 대 한 느리게 쿼리 로그 구성 및 액세스

> [!IMPORTANT]
> Azure Database for MySQL - 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

Azure Portal에서 Azure Database for MySQL 유연한 서버 [저속 쿼리 로그](concepts-slow-query-logs.md) 를 구성, 나열 및 다운로드할 수 있습니다.

## <a name="prerequisites"></a>사전 준비 사항
이 문서의 단계를 수행 하려면 [유연한 서버가](quickstart-create-server-portal.md)필요 합니다.

## <a name="configure-logging"></a>로깅 구성
MySQL 느린 쿼리 로그에 대한 액세스를 구성합니다. 

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 유연한 서버를 선택 합니다.

1. 사이드바의 **설정** 섹션에서 **서버 매개 변수**를 선택 합니다.
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/server-parameters.png" alt-text="서버 매개 변수 페이지.":::

1. **Slow_query_log** 매개 변수를 **ON**으로 업데이트 합니다.
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/slow-query-log-enable.png" alt-text="서버 매개 변수 페이지.":::

1. 필요한 다른 매개 변수를 변경 합니다 (예: `long_query_time`, `log_slow_admin_statements`). 더 많은 매개 변수는 [저속 쿼리 로그](./concepts-slow-query-logs.md#configure-slow-query-logging) 문서를 참조 하세요.  
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/long-query-time.png" alt-text="서버 매개 변수 페이지.":::

1. **저장**을 선택합니다. 
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/save-parameters.png" alt-text="서버 매개 변수 페이지.":::

**서버 매개 변수** 페이지에서 페이지를 닫아 로그 목록으로 돌아갈 수 있습니다.

## <a name="set-up-diagnostics"></a>진단 설정

저속 쿼리 로그는 로그를 Azure Monitor 로그, Event Hubs 또는 Azure Storage로 파이프 하는 데 사용할 수 있는 Azure Monitor 진단 설정에 통합 됩니다.

1. 사이드바의 **모니터링** 섹션에서 **진단 설정**  >  **진단 설정 추가**를 선택 합니다.

   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/add-diagnostic-setting.png" alt-text="서버 매개 변수 페이지.":::

1. 진단 설정 이름을 제공 합니다.

1. 저속 쿼리 로그를 보낼 대상 (저장소 계정, 이벤트 허브 또는 Log Analytics 작업 영역)을 지정 합니다.

1. 로그 형식으로 **MySqlSlowLogs** 를 선택 합니다.
    :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/configure-diagnostic-setting.png" alt-text="서버 매개 변수 페이지.":::

1. 저속 쿼리 로그를 파이프 하도록 데이터 싱크를 구성한 후 **저장**을 선택 합니다.
    :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/save-diagnostic-setting.png" alt-text="서버 매개 변수 페이지.":::

1. 구성 된 데이터 싱크에서 탐색 하 여 느리게 쿼리 로그에 액세스 합니다. 로그가 표시 되는 데 최대 10 분이 소요 될 수 있습니다.

로그를 Azure Monitor 로그 (Log Analytics)로 파이프 하는 경우 분석에 사용할 수 있는 몇 가지 [샘플 쿼리](concepts-slow-query-logs.md#analyze-logs-in-azure-monitor-logs) 를 참조 하세요. 

## <a name="next-steps"></a>다음 단계
<!-- - See [Access slow query Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download slow query logs programmatically.-->
- [저속 쿼리 로그](concepts-slow-query-logs.md) 에 대 한 자세한 정보
- 매개 변수 정의 및 MySQL 로깅에 대 한 자세한 내용은 [로그](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)에 대 한 mysql 설명서를 참조 하세요.
- [감사 로그](concepts-audit-logs.md) 에 대해 알아보기
