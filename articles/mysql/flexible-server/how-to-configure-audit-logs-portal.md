---
title: 감사 로그 구성-Azure Portal Azure Database for MySQL 유연한 서버
description: 이 문서에서는 Azure Portal에서 Azure Database for MySQL 유연한 서버에서 감사 로그를 구성 하 고 액세스 하는 방법을 설명 합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: b8fe32a079358fda48c6f5ee0c7eec9894a543a5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91295909"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure Database for MySQL 유연한 서버에 대 한 감사 로그 구성 및 액세스

> [!IMPORTANT]
> Azure Database for MySQL - 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

Azure Portal에서 Azure Database for MySQL 유연한 서버 [감사 로그](concepts-audit-logs.md) 및 진단 설정을 구성할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
이 문서의 단계를 수행 하려면 [유연한 서버가](quickstart-create-server-portal.md)필요 합니다.

## <a name="configure-audit-logging"></a>감사 로깅 구성

>[!IMPORTANT]
> 서버 성능이 크게 영향을 받지 않도록 감사 목적에 필요한 이벤트 유형과 사용자를 기록 하는 것이 좋습니다.

감사 로깅을 사용 하도록 설정 하 고 구성 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 유연한 서버를 선택 합니다.

1. 사이드바의 **설정** 섹션에서 **서버 매개 변수**를 선택 합니다.
    <!--:::image type="content" source="./media/howto-configure-audit-logs-portal/server-parameters.png" alt-text="Server parameters":::-->

1. **Audit_log_enabled** 매개 변수를 ON으로 업데이트 합니다.
    <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-enabled.png" alt-text="Enable audit logs":::-->

1. **Audit_log_events** 매개 변수를 업데이트 하 여 로깅할 [이벤트 유형을](concepts-audit-logs.md#configure-audit-logging) 선택 합니다.
    <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-events.png" alt-text="Audit log events":::-->

1. **Audit_log_exclude_users** 매개 변수를 업데이트 하 여 로그에서 제외할 MySQL 사용자를 추가 합니다. MySQL 사용자 이름을 제공 하 여 사용자를 지정 합니다.
    <!--:::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="Audit log exclude users":::-->

1. 매개 변수를 변경한 다음 **저장**을 클릭합니다. 또는 변경 사항을 **취소**할 수 있습니다.
    <!--:::image type="content" source="./media/howto-configure-audit-logs-portal/save-parameters.png" alt-text="Save":::-->

## <a name="set-up-diagnostics"></a>진단 설정

> [!NOTE]
> 로그에 액세스 하는 Azure Monitor 진단 설정과의 통합은 배포 중 이며 곧 전체 기능을 사용할 수 있습니다.

감사 로그는 로그를 Azure Monitor 로그, Event Hubs 또는 Azure Storage로 파이프 하는 데 사용할 수 있도록 Azure Monitor 진단 설정과 통합 됩니다.

1. 사이드바의 **모니터링** 섹션에서 **진단 설정**을 선택 합니다.

1. "+ 진단 설정 추가"를 클릭 합니다.  <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="Add diagnostic setting":::-->

1. 진단 설정 이름을 제공 합니다.

1. 감사 로그를 보낼 대상 (저장소 계정, 이벤트 허브 및/또는 Log Analytics 작업 영역)을 지정 합니다.

1. 로그 형식으로 **MySqlAuditLogs** 를 선택 합니다.
    <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="Configure diagnostic setting"::: -->

1. 감사 로그를 파이프 하도록 데이터 싱크를 구성한 후에는 **저장**을 클릭할 수 있습니다.
    <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="Save diagnostic setting":::-->

1. 구성 된 데이터 싱크에서 감사 로그를 탐색 하 여 액세스 합니다. 로그가 표시 되는 데 최대 10 분이 소요 될 수 있습니다.

감사 로그를 Azure Monitor 로그 (Log Analytics)로 파이프 하는 경우 분석에 사용할 수 있는 몇 가지 [샘플 쿼리](concepts-audit-logs.md#analyze-logs-in-azure-monitor-logs) 를 참조 하세요.  

## <a name="next-steps"></a>다음 단계

- [감사 로그](concepts-audit-logs.md) 에 대해 자세히 알아보기
- [저속 쿼리 로그](concepts-slow-query-logs.md) 에 대 한 자세한 정보
<!-- - Learn how to configure audit logs in the [Azure CLI](howto-configure-audit-logs-cli.md)-->