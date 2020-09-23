---
title: 저속 쿼리 로그 액세스-Azure Portal-Azure Database for MySQL
description: 이 문서에서는 Azure Portal에서 Azure Database for MySQL의 저속 로그를 구성 하 고 액세스 하는 방법을 설명 합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 4/13/2020
ms.openlocfilehash: 69368500a99b47238e74a960fdd5381c0339430a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905860"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Azure Portal에서 느리게 쿼리 로그 구성 및 액세스

Azure Portal에서 [Azure Database for MySQL 저속 쿼리 로그](concepts-server-logs.md) 를 구성, 나열 및 다운로드할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
이 문서의 단계를 수행 하려면 [Azure Database for MySQL 서버가](quickstart-create-mysql-server-database-using-azure-portal.md)있어야 합니다.

## <a name="configure-logging"></a>로깅 구성
MySQL 느린 쿼리 로그에 대한 액세스를 구성합니다. 

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. Azure Database for MySQL 서버를 선택합니다.

3. 사이드바의 **모니터링** 섹션에서 **서버 로그**를 선택 합니다. 
   :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="서버 로그 옵션의 스크린샷":::

4. 서버 매개 변수를 보려면 로그를 **사용 하도록 설정 하 고 로그 매개 변수를 구성 하려면 여기를 클릭**하십시오 .를 선택 합니다.

5. **Slow_query_log** **를 켜기로 설정**합니다.

6. **Log_output**사용 하 여 로그를 출력할 위치를 선택 합니다. 로컬 저장소 및 Azure Monitor 진단 로그 모두에 로그를 전송 하려면 **파일**을 선택 합니다. 

7. 필요한 다른 매개 변수를 변경 합니다. 

8. **저장**을 선택합니다. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="저속 쿼리 로그 매개 변수 및 저장의 스크린샷":::

**서버 매개 변수** 페이지에서 페이지를 닫아 로그 목록으로 돌아갈 수 있습니다.

## <a name="view-list-and-download-logs"></a>목록 보기 및 로그 다운로드
로깅이 시작 된 후 사용 가능한 쿼리 로그의 목록을 보고 개별 로그 파일을 다운로드할 수 있습니다.

1. Azure Portal을 엽니다.

2. Azure Database for MySQL 서버를 선택합니다.

3. 사이드바의 **모니터링** 섹션에서 **서버 로그**를 선택 합니다. 이 페이지에는 로그 파일의 목록이 표시 됩니다.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/4-server-logs-list.png" alt-text="로그 목록이 강조 표시 된 서버 로그 페이지의 스크린샷":::

   > [!TIP]
   > 로그의 명명 규칙은 **mysql-slow-< your server name>-yyyymmddhh.log**입니다. 파일 이름에 사용 되는 날짜와 시간은 로그가 발급 된 시간입니다. 로그 파일은 24 시간 또는 7.5 GB 마다 회전 됩니다. 

4. 필요한 경우 검색 상자를 사용 하 여 날짜 및 시간에 따라 특정 로그에 빠르게 범위를 좁힙니다. 로그의 이름이 검색됩니다.

5. 개별 로그 파일을 다운로드 하려면 테이블 행의 각 로그 파일 옆에 있는 아래쪽 화살표 아이콘을 선택 합니다.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/5-download.png" alt-text="아래쪽 화살표 아이콘이 강조 표시 된 서버 로그 페이지의 스크린샷":::

## <a name="set-up-diagnostic-logs"></a>진단 로그 설정

1. 사이드바의 **모니터링** 섹션에서 **진단 설정**  >  **진단 설정 추가**를 선택 합니다.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png" alt-text="진단 설정 옵션의 스크린샷":::

1. 진단 설정 이름을 제공 합니다.

1. 저속 쿼리 로그를 보낼 데이터 싱크 (저장소 계정, 이벤트 허브 또는 Log Analytics 작업 영역)를 지정 합니다.

1. 로그 형식으로 **MySqlSlowLogs** 를 선택 합니다.
:::image type="content" source="./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png" alt-text="진단 설정 구성 옵션의 스크린샷":::

1. 저속 쿼리 로그를 파이프 하도록 데이터 싱크를 구성한 후 **저장**을 선택 합니다.
:::image type="content" source="./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png" alt-text="저장이 강조 표시 된 진단 설정 구성 옵션의 스크린샷":::

1. 구성 된 데이터 싱크에서 탐색 하 여 느리게 쿼리 로그에 액세스 합니다. 로그가 표시 되는 데 최대 10 분이 소요 될 수 있습니다.

## <a name="next-steps"></a>다음 단계
- 느리게 쿼리 로그를 프로그래밍 방식으로 다운로드 하는 방법을 알아보려면 [CLI에서 느리게 쿼리 로그 액세스](howto-configure-server-logs-in-cli.md) 를 참조 하세요.
- Azure Database for MySQL의 [저속 쿼리 로그](concepts-server-logs.md) 에 대해 자세히 알아보세요.
- 매개 변수 정의 및 MySQL 로깅에 대 한 자세한 내용은 [로그](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)에 대 한 mysql 설명서를 참조 하세요.