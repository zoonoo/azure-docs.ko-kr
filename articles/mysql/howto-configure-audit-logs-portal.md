---
title: 감사 로그 구성 및 액세스-Azure Database for MySQL
description: 이 문서에서는 Azure Portal에서 Azure Database for MySQL의 감사 로그를 구성 하 고 액세스 하는 방법을 설명 합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: 2a2d2a697f0e41fb296c61c01909a814678f8277
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350400"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Azure Portal에서 Azure Database for MySQL에 대 한 감사 로그 구성 및 액세스

Azure Portal에서 [Azure Database for MySQL 감사 로그](concepts-audit-logs.md) 및 진단 설정을 구성할 수 있습니다.

> [!IMPORTANT]
> 감사 로그 기능은 현재 미리 보기로 제공 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.

- [Azure Database for MySQL 서버](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>감사 로깅 구성

감사 로깅을 사용 하도록 설정 하 고 구성 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Azure Database for MySQL 서버를 선택합니다.

1. 사이드바의 **설정** 섹션에서 **서버 매개 변수**를 선택 합니다.
    ![서버 매개 변수](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. **Audit_log_enabled** 매개 변수를 ON으로 업데이트 합니다.
    ![ 감사 로그 사용 @ no__t-1

1. **Audit_log_events** 매개 변수를 업데이트 하 여 로깅할 [이벤트 유형을](concepts-audit-logs.md#configure-audit-logging) 선택 합니다.
    ![ 감사 로그 이벤트 @ no__t-1

1. **Audit_log_exclude_users** 매개 변수를 업데이트 하 여 로그에서 제외할 MySQL 사용자를 추가 합니다. MySQL 사용자 이름을 제공 하 여 사용자를 지정 합니다.
    ![ 감사 로그 제외 사용자 @ no__t-1

1. 매개 변수를 변경한 다음 **저장**을 클릭합니다. 또는 변경 사항을 **취소**할 수 있습니다.
    ![저장](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>진단 로그 설정

1. 사이드바의 **모니터링** 섹션에서 **진단 설정**을 선택 합니다.

1. "+ 진단 설정 추가" ![ 추가 진단 설정 @ no__t-1을 클릭 합니다.

1. 진단 설정 이름을 제공 합니다.

1. 감사 로그를 보낼 데이터 싱크 (저장소 계정, 이벤트 허브 및/또는 Log Analytics 작업 영역)를 지정 합니다.

1. 로그 형식으로 "MySqlAuditLogs"을 선택 합니다.
![ 진단 설정 구성 @ no__t-1

1. 감사 로그를 파이프 하도록 데이터 싱크를 구성한 후에는 **저장**을 클릭할 수 있습니다.
![ 진단 설정 저장 @ no__t-1

1. 구성 된 데이터 싱크에서 감사 로그를 탐색 하 여 액세스 합니다. 로그가 표시 되는 데 최대 10 분이 소요 될 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure Database for MySQL에서 [감사 로그](concepts-audit-logs.md) 에 대해 자세히 알아보세요.