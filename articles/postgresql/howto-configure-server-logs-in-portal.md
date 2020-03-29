---
title: 로그 관리 - Azure 포털 - PostgreSQL용 Azure 데이터베이스 - 단일 서버
description: 이 문서에서는 Azure Portal- Azure 포털의 단일 서버에 대한 Azure 데이터베이스의 서버 로그(.log 파일)를 구성하고 액세스하는 방법을 설명합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 3da48a9b6d3acb1f2811bc279de7963fa1d83918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763694"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>PostgreSQL - Azure 포털에서 단일 서버 로그에 대한 Azure 데이터베이스 구성 및 액세스

Azure 포털에서 [PostgreSQL 로그에 대한 Azure 데이터베이스를](concepts-server-logs.md) 구성, 목록 및 다운로드할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
이 문서의 단계에 는 [PostgreSQL 서버에 대한 Azure 데이터베이스가](quickstart-create-server-database-portal.md)있어야 합니다.

## <a name="configure-logging"></a>로깅 구성
쿼리 로그 및 오류 로그에 대한 액세스를 구성합니다. 

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.

2. Azure Database for PostgreSQL 서버를 선택합니다.

3. 사이드바의 **모니터링** 섹션에서 서버 **로그를 선택합니다.** 

   ![서버 로그 옵션의 스크린샷](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. 서버 매개 변수를 보려면 **여기를 클릭하여 로그를 활성화하고 로그 매개 변수를 구성합니다.**

5. 조정해야 하는 매개 변수를 변경합니다. 이 세션에서 변경할 내용은 모두 자주색으로 강조 표시되어 있습니다.

   매개 변수를 변경한 후 **저장을**선택합니다. 또는 변경 내용을 취소할 수 있습니다. 

   ![서버 매개 변수 옵션의 스크린샷](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

서버 **매개 변수** 페이지에서 페이지를 닫아 로그 목록으로 돌아갈 수 있습니다.

## <a name="view-list-and-download-logs"></a>목록 보기 및 로그 다운로드
로깅이 시작되면 사용 가능한 로그 목록을 보고 개별 로그 파일을 다운로드할 수 있습니다. 

1. Azure Portal을 엽니다.

2. Azure Database for PostgreSQL 서버를 선택합니다.

3. 사이드바의 **모니터링** 섹션에서 서버 **로그를 선택합니다.** 페이지에 로그 파일 목록이 표시됩니다.

   ![로그 목록이 강조 표시된 서버 로그 페이지의 스크린샷](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > 로그의 명명 규칙은 **postgresql-yyyy-mm-dd_hh0000.log**입니다. 파일 이름에 사용된 날짜 및 시간은 로그가 발행된 시간입니다. 로그 파일은 매시간 또는 100MB 중 먼저 회전합니다.

4. 필요한 경우 검색 상자를 사용하여 날짜와 시간에 따라 특정 로그로 빠르게 좁힐 수 있습니다. 로그의 이름이 검색됩니다.

   ![서버 로그 페이지의 스크린샷, 검색 상자 및 결과 강조 표시](./media/howto-configure-server-logs-in-portal/5-search.png)

5. 개별 로그 파일을 다운로드하려면 테이블 행의 각 로그 파일 옆에 있는 아래쪽 화살표 아이콘을 선택합니다.

   ![아래쪽 화살표 아이콘이 강조 표시된 서버 로그 페이지의 스크린샷](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>다음 단계
- 프로그래밍 방식으로 로그를 다운로드하는 방법을 알아보려면 [CLI의 서버 로그 액세스](howto-configure-server-logs-using-cli.md) 를 참조하십시오.
- PostgreSQL용 Azure 데이터베이스의 [서버 로그에](concepts-server-logs.md) 대해 자세히 알아봅니다. 
- 매개 변수 정의 및 PostgreSQL 로깅에 대한 자세한 내용은 [오류 보고 및 로깅에](https://www.postgresql.org/docs/current/static/runtime-config-logging.html)대한 PostgreSQL 설명서를 참조하십시오.

