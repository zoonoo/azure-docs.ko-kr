---
title: 느린 쿼리 로그 에 액세스 - Azure 포털 - MariaDB용 Azure 데이터베이스
description: 이 문서에서는 Azure 포털에서 MariaDB에 대한 Azure 데이터베이스의 느린 쿼리 로그를 구성하고 액세스하는 방법을 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 89bdd209315445519c35f3ef2c2f1ad2555106ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531399"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Azure 포털에서 느린 쿼리 로그 구성 및 액세스

Azure 포털에서 [MariaDB 느린 쿼리 로그에 대한 Azure 데이터베이스를](concepts-server-logs.md) 구성, 목록 및 다운로드할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
이 문서의 단계에는 [MariaDB 서버에 대한 Azure 데이터베이스가](quickstart-create-mariadb-server-database-using-azure-portal.md)있어야 합니다.

## <a name="configure-logging"></a>로깅 구성
느린 쿼리 로그에 대한 액세스를 구성합니다. 

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.

2. Azure Database for MariaDB 서버를 선택합니다.

3. 사이드바의 **모니터링** 섹션에서 서버 **로그를 선택합니다.** 
   ![서버 로그 옵션의 스크린샷](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. 서버 매개 변수를 보려면 **여기를 클릭하여 로그를 활성화하고 로그 매개 변수를 구성합니다.**

5. **slow_query_log** **ON으로**전환하는 등 조정해야 하는 매개변수를 변경합니다. 이 세션에서 변경할 내용은 모두 자주색으로 강조 표시되어 있습니다. 

   매개 변수를 변경한 후 **저장을**선택합니다. 또는 변경 내용을 취소할 수 있습니다.

   ![서버 매개 변수 옵션의 스크린샷](./media/howto-configure-server-logs-portal/3-save-discard.png)

서버 **매개 변수** 페이지에서 페이지를 닫아 로그 목록으로 돌아갈 수 있습니다.

## <a name="view-list-and-download-logs"></a>목록 보기 및 로그 다운로드
로깅이 시작되면 사용 가능한 느린 쿼리 로그 목록을 보고 개별 로그 파일을 다운로드할 수 있습니다. 

1. Azure Portal을 엽니다.

2. Azure Database for MariaDB 서버를 선택합니다.

3. 사이드바의 **모니터링** 섹션에서 서버 **로그를 선택합니다.** 페이지에 로그 파일 목록이 표시됩니다.

   ![로그 목록이 강조 표시된 서버 로그 페이지의 스크린샷](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > 로그의 명명 규칙은 **mysql-slow-< your server name>-yyyymmddhh.log**입니다. 파일 이름에 사용된 날짜 및 시간은 로그가 발행된 시간입니다. 로그 파일은 24시간 또는 7.5GB마다 회전됩니다.

4. 필요한 경우 검색 상자를 사용하여 날짜와 시간에 따라 특정 로그로 빠르게 좁힐 수 있습니다. 로그의 이름이 검색됩니다.

5. 개별 로그 파일을 다운로드하려면 테이블 행의 각 로그 파일 옆에 있는 아래쪽 화살표 아이콘을 선택합니다.

   ![아래쪽 화살표 아이콘이 강조 표시된 서버 로그 페이지의 스크린샷](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>진단 로그 설정

1. 사이드바의 **모니터링** 섹션에서 진단 **설정** > **추가 진단 설정을**선택합니다.

   ![진단 설정 옵션의 스크린샷](./media/howto-configure-server-logs-portal/add-diagnostic-setting.png)

1. 진단 설정 이름을 제공합니다.

1. 느린 쿼리 로그(저장소 계정, 이벤트 허브 또는 Log Analytics 작업 영역)를 보낼 데이터 싱크를 지정합니다.

1. **MySqlSlowLogs를** 로그 유형으로 선택합니다.
![진단 설정 구성 옵션의 스크린샷](./media/howto-configure-server-logs-portal/configure-diagnostic-setting.png)

1. 느린 쿼리 로그를 파이프하도록 데이터 싱크를 구성한 후 **저장을**선택합니다.
![저장이 강조 표시된 진단 설정 구성 옵션의 스크린샷](./media/howto-configure-server-logs-portal/save-diagnostic-setting.png)

1. 구성한 데이터 싱크에서 검색하여 느린 쿼리 로그에 액세스합니다. 로그가 표시되는 데 최대 10분이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계
- 프로그래밍 방식으로 느린 쿼리 로그를 다운로드하는 방법을 알아보려면 [CLI의 느린 쿼리 로그 액세스를](howto-configure-server-logs-cli.md) 참조하십시오.
- MariaDB용 Azure 데이터베이스에서 [느린 쿼리 로그에](concepts-server-logs.md) 대해 자세히 알아봅니다.
- 매개 변수 정의 및 로깅에 대한 자세한 내용은 [로그에](https://mariadb.com/kb/en/library/slow-query-log-overview/)대한 MariaDB 설명서를 참조하십시오.