---
title: 감사 로그 구성 - Azure Portal - Azure Database for MySQL - 유연한 서버
description: 이 문서에서는 Azure Portall에서 Azure Database for MySQL 유연한 서버의 감사 로그를 구성 및 액세스하는 방법을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: 68ee7528eb7b8d91d4346b5cf7fa2f92841255b7
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122642191"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Database for MySQL 유연한 서버의 감사 로그 구성 및 액세스

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL - 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

Azure Portal에서 Azure Database for MySQL 유연한 서버 [감사 로그](concepts-audit-logs.md) 및 진단 설정을 구성할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 수행하려면 [유연한 서버](quickstart-create-server-portal.md)가 필요합니다.

## <a name="configure-audit-logging"></a>감사 로깅 구성

>[!IMPORTANT]
> 서버 성능이 크게 영향을 받지 않도록 감사 목적에 필요한 이벤트 유형과 사용자만 로그하는 것이 좋습니다.

감사 로깅을 사용하도록 설정하고 구성합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 유연한 서버를 선택합니다.

1. 사이드바의 **설정** 섹션 아래에서 **서버 매개 변수** 를 선택합니다.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/server-parameters.png" alt-text="서버 매개 변수":::

1. **Audit_log_enabled** 매개 변수를 ON으로 업데이트합니다.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-enabled.png" alt-text="감사 로그 사용":::

1. **audit_log_events** 매개 변수를 업데이트하여 [이벤트 유형](concepts-audit-logs.md#configure-audit-logging)을 로그되도록 선택합니다.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-events.png" alt-text="감사 로그 이벤트":::

1. **Audit_log_exclude_users** 및 **audit_log_include_users** 매개 변수를 업데이트하여 로깅에 포함하거나 제외할 MySQL 사용자를 추가합니다. MySQL 사용자 이름을 제공하여 사용자를 지정합니다.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="감사 로그 사용자 제외":::

1. 매개 변수를 변경한 다음 **저장** 을 클릭합니다. 또는 변경 사항을 **취소** 할 수 있습니다.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/save-parameters.png" alt-text="저장":::

## <a name="set-up-diagnostics"></a>진단 설정

감사 로그는 Azure Monitor 진단 설정과 통합되어 로그를 Azure Monitor 로그, Event Hubs 또는 Azure Storage로 파이프할 수 있습니다.

1. 사이드바의 **모니터링** 섹션에서 **진단 설정** 을 선택합니다.

1. "+ 진단 설정 추가" :::image type="content" source="./media/how-to-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="진단 설정 추가":::를 클릭합니다.

1. 진단 설정 이름을 입력합니다.

1. 감사 로그를 보낼 대상(스토리지 계정, 이벤트 허브 및/또는 Log Analytics 작업 영역)을 지정합니다.

1. 로그 형식으로 **MySqlAuditLogs** 를 선택합니다.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="진단 설정 구성":::

1. 감사 로그를 파이프하도록 데이터 싱크를 구성한 후에는 **저장** 을 클릭하면 됩니다.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="진단 설정 저장":::

1. 구성된 데이터 싱크에서 탐색하여 감사 로그에 액세스합니다. 로그가 표시되는 데에는 최대 10분이 걸릴 수 있습니다.

감사 로그를 Azure Monitor 로그(Log Analytics)로 파이프하는 경우 분석에 사용할 수 있는 몇 가지 [샘플 쿼리](concepts-audit-logs.md#analyze-logs-in-azure-monitor-logs)를 참조하세요.  

## <a name="next-steps"></a>다음 단계

- [감사 로그](concepts-audit-logs.md)에 대한 자세한 정보
- [느린 쿼리 로그](concepts-slow-query-logs.md)에 대한 정보
<!-- - Learn how to configure audit logs in the [Azure CLI](howto-configure-audit-logs-cli.md)-->