---
title: 느린 쿼리 로그 액세스 - Azure Portal - Azure Database for MySQL
description: 이 문서에서는 Azure Portal에서 Azure Database for MySQL의 느린 로그를 구성 및 액세스하는 방법을 설명합니다.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 3/15/2021
ms.openlocfilehash: 91569780aa71861e07c7e96bec5eac879642760d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103496221"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Azure Portal에서 느린 쿼리 로그 구성 및 액세스

Azure Portal에서 [Azure Database for MySQL 느린 쿼리 로그](concepts-server-logs.md)를 구성, 나열 및 다운로드할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소
이 문서의 단계를 수행하려면 [Azure Database for MySQL 서버](quickstart-create-mysql-server-database-using-azure-portal.md)가 있어야 합니다.

## <a name="configure-logging"></a>로깅 구성
MySQL 느린 쿼리 로그에 대한 액세스를 구성합니다. 

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. Azure Database for MySQL 서버를 선택합니다.

3. 사이드바의 **모니터링** 섹션에서 **서버 로그** 를 선택합니다. 
   :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="서버 로그 옵션의 스크린샷":::

4. 서버 매개 변수를 보려면 **여기를 클릭하여 로그를 사용하도록 설정하고 로그 매개 변수 구성** 을 선택합니다.

5. **slow_query_log** 를 **켜기** 로 설정합니다.

6. **log_output** 을 사용하여 로그를 출력할 위치를 선택합니다. 로컬 스토리지 및 Azure Monitor 진단 로그 모두에 로그를 전송하려면 **파일** 을 선택합니다.

7. 느린 쿼리 로그 파일에 수집될 쿼리의 쿼리 시간 임계값을 나타내는 "long_query_time"을 설정하는 것이 좋습니다. long_query_time의 최소값과 기본값은 각각 0과 10입니다.

8. log_slow_admin_statements와 같은 다른 매개 변수를 조정하여 관리문을 기록합니다. 기본적으로 관리문과 조회를 위해 인덱스를 사용하지 않는 쿼리는 기록되지 않습니다. 

9. **저장** 을 선택합니다. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="느린 쿼리 로그 매개 변수 및 저장의 스크린샷":::

**서버 매개 변수** 페이지에서 페이지를 닫아 로그 목록으로 돌아갈 수 있습니다.

## <a name="view-list-and-download-logs"></a>목록 보기 및 로그 다운로드
로깅이 시작된 후 사용 가능한 느린 쿼리 로그의 목록을 보고 개별 로그 파일을 다운로드할 수 있습니다.

1. Azure Portal을 엽니다.

2. Azure Database for MySQL 서버를 선택합니다.

3. 사이드바의 **모니터링** 섹션에서 **서버 로그** 를 선택합니다. 이 페이지에는 로그 파일의 목록이 표시됩니다.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/4-server-logs-list.png" alt-text="로그 목록이 강조 표시된 서버 로그 페이지의 스크린샷":::

   > [!TIP]
   > 로그의 명명 규칙은 **mysql-slow-< your server name>-yyyymmddhh.log** 입니다. 파일 이름에 사용된 날짜와 시간은 로그가 발행된 시간입니다. 로그 파일은 24시간이 지나거나 7.5GB를 초과할 때마다(먼저 해당되는 쪽) 회전됩니다. 

4. 필요한 경우 검색 상자를 사용하여 날짜 및 시간에 따라 특정 로그로 빠르게 범위를 좁힙니다. 로그의 이름이 검색됩니다.

5. 개별 로그 파일을 다운로드하려면 테이블 행의 각 로그 파일 옆에 있는 아래쪽 화살표 아이콘을 선택합니다.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/5-download.png" alt-text="아래쪽 화살표 아이콘이 강조 표시된 서버 로그 페이지의 스크린샷":::

## <a name="set-up-diagnostic-logs"></a>진단 로그 설정

1. 사이드바의 **모니터링** 섹션에서 **진단 설정**  >  **진단 설정 추가** 를 선택합니다.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png" alt-text="진단 설정 옵션의 스크린샷":::

2. 진단 설정의 이름을 제공합니다.

3. 느린 쿼리 로그를 보낼 데이터 싱크(스토리지 계정, 이벤트 허브 또는 Log Analytics 작업 영역)를 지정합니다.

4. 로그 형식으로 **MySqlSlowLogs** 를 선택합니다.
:::image type="content" source="./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png" alt-text="진단 설정 구성 옵션의 스크린샷":::

5. 느린 쿼리 로그를 파이프하도록 데이터 싱크를 구성한 후 **저장** 을 선택합니다.
:::image type="content" source="./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png" alt-text="저장이 강조 표시된 진단 설정 구성 옵션의 스크린샷":::

6. 구성된 데이터 싱크에서 탐색하여 느린 쿼리 로그에 액세스합니다. 로그가 표시되는 데에는 최대 10분이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계
- 느린 쿼리 로그를 프로그래밍 방식으로 다운로드하는 방법을 알아보려면 [CLI에서 느린 쿼리 로그 액세스](howto-configure-server-logs-in-cli.md)를 참조하세요.
- Azure Database for MySQL의 [느린 쿼리 로그](concepts-server-logs.md)에 대해 자세히 알아보세요.
- 매개 변수 정의 및 MySQL 로깅에 대한 자세한 내용은 [로그](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)의 MySQL 설명서를 참조하세요.