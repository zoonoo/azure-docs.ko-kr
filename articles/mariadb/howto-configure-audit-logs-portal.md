---
title: 감사 로그에 액세스 - Azure Portal - Azure Database for MariaDB
description: 이 문서에서는 Azure Portal에서 Azure Database for MariaDB의 감사 로그를 구성하고 액세스하는 방법을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/24/2020
ms.openlocfilehash: f1f3cda4ed9cdac6f6e03e88601b0a35d5c53faf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98663148"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>Azure Portal에서 감사 로그 구성 및 액세스

Azure Portal에서 [Azure Database for MariaDB 감사 로그](concepts-audit-logs.md) 및 진단 설정을 구성할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.

- [Azure Database for MariaDB 서버](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>감사 로깅 구성

>[!IMPORTANT]
> 서버 성능이 크게 영향을 받지 않도록 감사 목적에 필요한 이벤트 유형과 사용자만 로그하는 것이 좋습니다.

감사 로깅을 사용하도록 설정하고 구성합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Azure Database for MariaDB 서버를 선택합니다.

1. 사이드바의 **설정** 섹션에서 **서버 매개 변수** 를 선택합니다.
    ![서버 매개 변수](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. **Audit_log_enabled** 매개 변수를 ON으로 업데이트합니다.
    ![감사 로그 사용](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. **Audit_log_events** 매개 변수를 업데이트하여 로깅할 [이벤트 유형](concepts-audit-logs.md#configure-audit-logging)을 선택합니다.
    ![감사 로그 이벤트](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. **audit_log_exclude_users** 매개 변수를 업데이트하여 로깅에서 제외할 모든 MariaDB 사용자를 추가합니다. MariaDB 사용자 이름을 제공하여 사용자를 지정합니다.
    ![감사 로그 사용자 제외](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. 매개 변수를 변경한 다음 **저장** 을 클릭합니다. 또는 변경 사항을 **취소** 할 수 있습니다.
    ![저장](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>진단 로그 설정

1. 사이드바의 **모니터링** 섹션에서 **진단 설정** 을 선택합니다.

1. “+ 진단 설정 추가” ![ 진단 설정 추가](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)를 클릭합니다.

1. 진단 설정 이름을 입력합니다.

1. 감사 로그를 보낼 데이터 싱크(스토리지 계정, 이벤트 허브 및/또는 Log Analytics 작업 영역)를 지정합니다.

1. 로그 형식으로 “MySqlAuditLogs”를 선택합니다.
![진단 설정 구성](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. 감사 로그를 파이프하도록 데이터 싱크를 구성한 후에는 **저장** 을 클릭하면 됩니다.
![진단 설정 저장](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. 구성된 데이터 싱크에서 검색하여 감사 로그에 액세스합니다. 로그가 표시되는 데에는 최대 10분이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure Database for MariaDB의 [감사 로그](concepts-audit-logs.md)에 대해 자세히 알아보기
- [Azure CLI](howto-configure-audit-logs-cli.md)에서 감사 로그 구성 방법 알아보기