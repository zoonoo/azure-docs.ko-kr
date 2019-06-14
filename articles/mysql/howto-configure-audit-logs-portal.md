---
title: 구성 하 고 Azure portal에서 MySQL 용 Azure Database에 대 한 감사 로그 액세스
description: 이 문서에 구성 하 고 Azure portal에서 MySQL 용 Azure Database에서 감사 로그에 액세스 하는 방법을 설명 합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: dfa57b3f597ba1c01dc78421cc04074f7b6a858e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063051"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>Azure portal에서 감사 로그 구성 및 액세스

구성할 수 있습니다 합니다 [Azure Database for MySQL 감사 로그](concepts-audit-logs.md) 및 Azure portal에서 진단 설정 합니다.

> [!IMPORTANT]
> 감사 로그 기능은 현재 미리 보기로 제공에서 됩니다.

## <a name="prerequisites"></a>필수 조건

이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.

- [Azure Database for MySQL 서버](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>감사 로깅 구성

사용 하도록 설정 하 고 감사 로깅을 구성 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Azure Database for MySQL 서버를 선택합니다.

1. 아래는 **설정을** 보충 선택 섹션 **서버 매개 변수**합니다.
    ![서버 매개 변수](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. 업데이트를 **audit_log_enabled** 매개 변수를 합니다.
    ![감사 로그를 사용 하도록 설정](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. 업데이트 하 여 로깅할 이벤트를 선택 합니다 **audit_log_events** 매개 변수입니다.
    ![감사 로그 이벤트](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. 업데이트 하 여 로깅에서 제외 하도록 MySQL 사용자를 추가 합니다 **audit_log_exclude_users** 매개 변수입니다. MySQL 사용자 이름을 제공 하 여 사용자를 지정 합니다.
    ![감사 로그 제외 사용자](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. 매개 변수를 변경한 다음 **저장**을 클릭합니다. 또는 변경 사항을 **취소**할 수 있습니다.
    ![저장](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>진단 로그 설정

1. 아래는 **모니터링** 보충 선택 섹션 **진단 설정**합니다.

1. "+ 진단 설정 추가" 클릭 ![진단 설정 추가](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. 진단 설정 이름을 지정 합니다.

1. 데이터 싱크 (저장소 계정, 이벤트 허브 및/또는 Log Analytics 작업 영역) 감사 로그를 보내도록 지정 합니다.

1. 로그 유형으로 "MySqlAuditLogs"를 선택 합니다.
![진단 설정 구성](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. 감사 로그를 사용 하는 파이프를 데이터 싱크로 구성한 경우 클릭할 수 **저장할**합니다.
![진단 설정 저장](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. 구성한 원하는 데이터 싱크에에서를 탐색 하 여 감사 로그에 액세스 합니다. 표시할 로그에 대 일 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 에 대해 자세히 알아보세요 [감사 로그](concepts-audit-logs.md) Azure Database for MySQL에서에서.