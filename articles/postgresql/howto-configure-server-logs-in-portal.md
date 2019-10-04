---
title: Azure Portal에서 Azure Database for PostgreSQL 단일 서버에서 서버 로그 구성 및 액세스
description: 이 문서에서는 Azure Portal에서 Azure Database for PostgreSQL 단일 서버에서 서버 로그를 구성 하 고 액세스 하는 방법을 설명 합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: c77e708e14d34545754ca38095aedb63ff0172a1
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841515"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>Azure Portal에서 Azure Database for PostgreSQL 단일 서버 로그 구성 및 액세스

Azure portal에서 [Azure Database for PostgreSQL 로그](concepts-server-logs.md)를 구성, 나열 및 다운로드합니다.

## <a name="prerequisites"></a>사전 요구 사항
이 문서의 단계를 수행 하려면 [Azure Database for PostgreSQL 서버가](quickstart-create-server-database-portal.md)있어야 합니다.

## <a name="configure-logging"></a>로깅 구성
쿼리 로그 및 오류 로그에 대한 액세스를 구성합니다. 

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. Azure Database for PostgreSQL 서버를 선택합니다.

3. 사이드바의 **모니터링** 섹션에서 **서버 로그**를 선택 합니다. 

   ![서버 로그 옵션의 스크린샷](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. 서버 매개 변수를 보려면 로그를 **사용 하도록 설정 하 고 로그 매개 변수를 구성 하려면 여기를 클릭**하십시오 .를 선택 합니다.

5. 조정해야 하는 매개 변수를 변경합니다. 이 세션에서 변경할 내용은 모두 자주색으로 강조 표시되어 있습니다.

   매개 변수를 변경한 후 **저장**을 선택 합니다. 또는 변경 내용을 취소할 수 있습니다. 

   ![서버 매개 변수 옵션의 스크린샷](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

**서버 매개 변수** 페이지에서 페이지를 닫아 로그 목록으로 돌아갈 수 있습니다.

## <a name="view-list-and-download-logs"></a>목록 보기 및 로그 다운로드
로깅이 시작 된 후 사용 가능한 로그 목록을 보고 개별 로그 파일을 다운로드할 수 있습니다. 

1. Azure Portal을 엽니다.

2. Azure Database for PostgreSQL 서버를 선택합니다.

3. 사이드바의 **모니터링** 섹션에서 **서버 로그**를 선택 합니다. 이 페이지에는 로그 파일의 목록이 표시 됩니다.

   ![로그 목록이 강조 표시 된 서버 로그 페이지의 스크린샷](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > 로그의 명명 규칙은 **postgresql-yyyy-mm-dd_hh0000.log**입니다. 파일 이름에 사용 되는 날짜와 시간은 로그가 발급 된 시간입니다. 로그 파일은 1 시간 마다 또는 100 MB 중 먼저 도달 하는 것으로 회전 합니다.

4. 필요한 경우 검색 상자를 사용 하 여 날짜 및 시간에 따라 특정 로그에 빠르게 범위를 좁힙니다. 로그의 이름이 검색됩니다.

   ![검색 상자 및 결과가 강조 표시 된 서버 로그 페이지의 스크린샷](./media/howto-configure-server-logs-in-portal/5-search.png)

5. 개별 로그 파일을 다운로드 하려면 테이블 행의 각 로그 파일 옆에 있는 아래쪽 화살표 아이콘을 선택 합니다.

   ![아래쪽 화살표 아이콘이 강조 표시 된 서버 로그 페이지의 스크린샷](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>다음 단계
- 프로그래밍 방식으로 로그를 다운로드 하는 방법을 알아보려면 [CLI에서 서버 로그 액세스](howto-configure-server-logs-using-cli.md) 를 참조 하세요.
- Azure Database for PostgreSQL의 [서버 로그](concepts-server-logs.md) 에 대해 자세히 알아보세요. 
- 매개 변수 정의 및 PostgreSQL 로깅에 대 한 자세한 내용은 [오류 보고 및 로깅](https://www.postgresql.org/docs/current/static/runtime-config-logging.html)에 대 한 PostgreSQL 설명서를 참조 하세요.

