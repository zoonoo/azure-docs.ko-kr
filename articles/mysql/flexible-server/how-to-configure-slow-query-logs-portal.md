---
title: 느린 쿼리 로그 구성 - Azure Portal - Azure Database for MySQL - 유연한 서버
description: 이 문서에서는 Azure Portal에서 Azure Database for MySQL 유연한 서버에 대한 느린 쿼리 로그를 구성하고 이에 액세스하는 방법을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: a61c8e3451d661dae2e5ad56a0d4a947252ec873
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94540066"
---
# <a name="configure-and-access-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Database for MySQL - 유연한 서버에 대한 느린 쿼리 구성 및 액세스

> [!IMPORTANT]
> Azure Database for MySQL - 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

Azure Portal에서 Azure Database for MySQL 유연한 서버 [느린 쿼리 로그](concepts-slow-query-logs.md)를 구성하고 나열 및 다운로드할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
이 문서의 단계를 수행하려면 [유연한 서버](quickstart-create-server-portal.md)가 필요합니다.

## <a name="configure-logging"></a>로깅 구성
MySQL 느린 쿼리 로그에 대한 액세스를 구성합니다. 

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 유연한 서버를 선택합니다.

1. 사이드바의 **설정** 섹션에서 **서버 매개 변수** 를 선택합니다.
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/server-parameters.png" alt-text="서버 매개 변수 페이지.":::

1. **slow_query_log** 매개 변수를 **ON** 으로 업데이트 합니다.
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/slow-query-log-enable.png" alt-text="느린 쿼리 로그를 활성화합니다.":::

1. 필요한 다른 매개 변수를 변경합니다(예: `long_query_time`, `log_slow_admin_statements`). 더 많은 매개 변수는 [느린 쿼리 로그](./concepts-slow-query-logs.md#configure-slow-query-logging) 문서를 참조하세요.  
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/long-query-time.png" alt-text="느린 쿼리 로그 관련 매개 변수를 업데이트합니다.":::

1. **저장** 을 선택합니다. 
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/save-parameters.png" alt-text="느린 쿼리 로그 매개 변수를 저장합니다.":::

**서버 매개 변수** 페이지에서 페이지를 닫으면 로그 목록으로 돌아갈 수 있습니다.

## <a name="set-up-diagnostics"></a>진단 설정

느린 쿼리 로그는 Azure Monitor 진단 설정과 통합되어 로그를 Azure Monitor 로그, Event Hubs 또는 Azure Storage로 파이프할 수 있습니다.

1. 사이드바의 **모니터링** 섹션에서 **진단 설정** > **진단 설정 추가** 를 선택합니다.

   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/add-diagnostic-setting.png" alt-text="진단 설정 옵션의 스크린샷":::

1. 진단 설정의 이름을 입력합니다.

1. 느린 쿼리 로그를 보낼 대상(스토리지 계정, 이벤트 허브 또는 Log Analytics 작업 영역)을 지정합니다.

1. 로그 형식으로 **MySqlSlowLogs** 를 선택합니다.
    :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/configure-diagnostic-setting.png" alt-text="진단 설정 구성 옵션의 스크린샷":::

1. 느린 쿼리 로그를 전송하도록 데이터 싱크를 구성한 후 **저장** 을 선택합니다.
    :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/save-diagnostic-setting.png" alt-text="저장이 강조 표시된 진단 설정 구성 옵션의 스크린샷":::

1. 구성된 데이터 싱크에서 탐색하여 느린 쿼리 로그에 액세스합니다. 로그가 표시되는 데에는 최대 10분이 걸릴 수 있습니다.

로그를 Azure Monitor 로그(Log Analytics)로 파이프하는 경우, 분석에 사용할 수 있는 몇 가지 [샘플 쿼리](concepts-slow-query-logs.md#analyze-logs-in-azure-monitor-logs)를 참조하세요. 

## <a name="next-steps"></a>다음 단계
<!-- - See [Access slow query Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download slow query logs programmatically.-->
- [느린 쿼리 로그](concepts-slow-query-logs.md)에 대해 자세히 알아보기
- 매개 변수 정의 및 MySQL 로깅에 대한 자세한 내용은 [로그](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)의 MySQL 설명서를 참조하세요.
- [감사 로그](concepts-audit-logs.md)에 대해 알아보기
