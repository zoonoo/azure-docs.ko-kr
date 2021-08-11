---
title: 감사 로그 액세스 - Azure Portal - Azure Database for MySQL
description: 이 문서에서는 Azure Portal에서 Azure Database for MySQL의 감사 로그를 구성 및 액세스하는 방법을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: a73a3c77310c0e19792758c0586975e14cfaebf8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541660"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Azure Portal에서 Azure Database for MySQL에 대한 감사 로그 구성 및 액세스

Azure Portal에서 [Azure Database for MySQL 감사 로그](concepts-audit-logs.md) 및 진단 설정을 구성할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.

- [Azure Database for MySQL 서버](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>감사 로깅 구성

>[!IMPORTANT]
> 서버 성능이 크게 영향을 받지 않도록 감사 목적에 필요한 이벤트 유형과 사용자만 로그하는 것이 좋습니다.

감사 로깅을 사용하도록 설정하고 구성합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Azure Database for MySQL 서버를 선택합니다.

1. 사이드바의 **설정** 섹션 아래에서 **서버 매개 변수** 를 선택합니다.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/server-parameters.png" alt-text="서버 매개 변수":::

1. **Audit_log_enabled** 매개 변수를 ON으로 업데이트합니다.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-enabled.png" alt-text="감사 로그 사용":::

1. **audit_log_events** 매개 변수를 업데이트하여 [이벤트 유형](concepts-audit-logs.md#configure-audit-logging)을 로그되도록 선택합니다.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-events.png" alt-text="감사 로그 이벤트":::

1. **Audit_log_exclude_users** 및 **audit_log_include_users** 매개 변수를 업데이트하여 로깅에 포함하거나 제외할 MySQL 사용자를 추가합니다. MySQL 사용자 이름을 제공하여 사용자를 지정합니다.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="감사 로그 사용자 제외":::

1. 매개 변수를 변경한 다음 **저장** 을 클릭합니다. 또는 변경 사항을 **취소** 할 수 있습니다.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/save-parameters.png" alt-text="저장":::

## <a name="set-up-diagnostic-logs"></a>진단 로그 설정

1. 사이드바의 **모니터링** 섹션에서 **진단 설정** 을 선택합니다.

1. "+ 진단 설정 추가" :::image type="content" source="./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="진단 설정 추가":::를 클릭합니다.

1. 진단 설정의 이름을 제공합니다.

1. 감사 로그를 보낼 데이터 싱크(스토리지 계정, 이벤트 허브 및/또는 Log Analytics 작업 영역)를 지정합니다.

1. 로그 형식으로 "MySqlAuditLogs"를 선택합니다.
:::image type="content" source="./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="진단 설정 구성":::

1. 감사 로그를 파이프하도록 데이터 싱크를 구성한 후에는 **저장** 을 클릭하면 됩니다.
:::image type="content" source="./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="진단 설정 저장":::

1. 구성된 데이터 싱크에서 탐색하여 감사 로그에 액세스합니다. 로그가 표시되는 데에는 최대 10분이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure Database for MySQL의 [감사 로그](concepts-audit-logs.md)에 대해 자세히 알아봅니다.
- [Azure CLI](howto-configure-audit-logs-cli.md)에서 감사 로그를 구성하는 방법을 알아봅니다.