---
title: 액세스 감사 로그 - Azure 포털 - MySQL용 Azure 데이터베이스
description: 이 문서에서는 Azure 포털에서 MySQL용 Azure 데이터베이스의 감사 로그를 구성하고 액세스하는 방법을 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: fcd436f87b24d8cdc246d2f1d33a49311e72d46b
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380297"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Azure 포털에서 MySQL용 Azure 데이터베이스에 대한 감사 로그 구성 및 액세스

Azure 포털에서 [MySQL 감사 로그](concepts-audit-logs.md) 및 진단 설정에 대한 Azure 데이터베이스를 구성할 수 있습니다.

> [!IMPORTANT]
> 감사 로그 기능은 현재 미리 보기 상태입니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.

- [MySQL 서버에 대한 Azure 데이터베이스](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>감사 로깅 구성

감사 로깅을 활성화하고 구성합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Azure Database for MySQL 서버를 선택합니다.

1. 사이드바의 **설정** 섹션에서 서버 **매개 변수를 선택합니다.**
    ![서버 매개 변수](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. **audit_log_enabled** 매개 변수를 ON으로 업데이트합니다.
    ![감사 로그 사용](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. **audit_log_events** 매개 변수를 업데이트하여 기록할 [이벤트 유형을](concepts-audit-logs.md#configure-audit-logging) 선택합니다.
    ![로그 이벤트 감사](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. **audit_log_exclude_users** 매개 변수를 업데이트하여 로깅에서 제외할 MySQL 사용자를 추가합니다. MySQL 사용자 이름을 제공하여 사용자를 지정합니다.
    ![감사 로그 사용자 제외](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. 매개 변수를 변경한 다음 **저장**을 클릭합니다. 또는 변경 사항을 **취소**할 수 있습니다.
    ![저장](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>진단 로그 설정

1. 사이드바의 **모니터링** 섹션에서 진단 **설정을**선택합니다.

1. "+ 진단 설정 추가"를 ![클릭합니다진단 설정 추가](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. 진단 설정 이름을 제공합니다.

1. 감사 로그(저장소 계정, 이벤트 허브 및/또는 Log Analytics 작업 영역)를 보낼 데이터 싱크를 지정합니다.

1. "MySqlAuditLogs"를 로그 유형으로 선택합니다.
![진단 설정 구성](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. 감사 로그를 파이프하도록 데이터 싱크를 구성한 후에는 **저장을**클릭할 수 있습니다.
![진단 설정 저장](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. 구성한 데이터 싱크에서 감사 로그를 탐색하여 감사 로그에 액세스합니다. 로그가 표시되는 데 최대 10분이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

- MySQL용 Azure 데이터베이스에서 [감사 로그에](concepts-audit-logs.md) 대해 자세히 알아보기
- [Azure CLI에서](howto-configure-audit-logs-cli.md) 감사 로그를 구성하는 방법에 대해 알아봅니다.