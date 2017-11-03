---
title: "PostgreSQL용 Azure Database에서 서버를 복원하는 방법 | Microsoft Docs"
description: "이 문서에서는 Azure Portal을 사용하여 PostgreSQL용 Azure Database에서 서버를 복원하는 방법을 설명합니다."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 07/20/2017
ms.openlocfilehash: 3f1ffa20b58c52558e880ed6a0ef4ca674173973
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2017
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Database for PostgreSQL에서 서버를 백업 및 복원하는 방법

## <a name="backup-happens-automatically"></a>자동으로 수행되는 백업
Azure Database for PostgreSQL을 사용할 경우 데이터베이스 서비스가 자동으로 5분마다 서버를 백업합니다. 

백업은 기본 계층을 사용하는 경우 7일, 표준 계층을 사용하는 경우 35일 동안 사용할 수 있습니다. 자세한 내용은 [PostgreSQL용 Azure Database 서비스 계층](concepts-service-tiers.md)을 참조하세요.

이 자동 백업 기능을 사용하면 서버 및 모든 데이터베이스를 새 서버에 이전 특정 시점으로 복원할 수도 있습니다.

## <a name="restore-in-the-azure-portal"></a>Azure Portal에서 복원
PostgreSQL용 Azure Database를 사용하면 서버를 다시 특정 시점으로 서버의 새 복사본에 복원할 수 있습니다. 이 새 서버를 사용하여 데이터를 복구할 수 있습니다. 

예를 들어 오늘 정오에 실수로 테이블을 삭제한 경우 정오 바로 전으로 복원하고 누락된 테이블과 데이터를 서버의 새로운 복사본에서 검색할 수 있습니다.

다음 단계는 샘플 서버를 특정 시점으로 복원합니다.
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. PostgreSQL용 Azure 데이터베이스 서버를 찾습니다. Azure Portal에서 왼쪽 메뉴의 **모든 리소스**를 클릭하고 서버 이름(예: **mypgserver-20170401**)을 입력하여 기존 서버를 검색합니다. 검색 결과에 나열된 서버 이름을 클릭합니다. 서버에 대한 **개요** 페이지가 열리고 추가 구성을 위한 옵션이 제공됩니다.

   ![Azure Portal - 검색으로 서버 찾기](media/postgresql-howto-restore-server-portal/1-locate.png)

3. 서버 개요 블레이드의 도구 모음에서 **복원**을 클릭합니다. 복원 블레이드가 열립니다.

   ![PostgreSQL용 Azure Database - 개요 - 복원 단추](./media/postgresql-howto-restore-server-portal/2_server.png)

4. 필요한 정보로 복원 양식을 채웁니다.

   ![PostgreSQL용 Azure Database - 정보 복원 ](./media/postgresql-howto-restore-server-portal/3_restore.png)
  - **복원 지점**: 서버를 변경하기 전 발생한 특정 시점을 선택합니다.
  - **대상 서버:** 복원해 두려는 새 서버의 이름을 제공합니다.
  - **위치**: 하위 지역을 선택할 수 없습니다. 기본적으로 원본 서버와 동일합니다.
  - **가격 책정 계층**: 이 값은 서버를 복원할 때 변경할 수 없습니다. 원본 서버와 동일합니다. 

5. **확인**을 클릭하여 특정 시점으로 복원할 서버를 복원합니다. 

6. 복원이 완료되면 예상대로 데이터가 복원되었는지 확인하기 위해 생성하였던 새 서버를 찾습니다.

## <a name="next-steps"></a>다음 단계
- [PostgreSQL용 Azure Database에 대한 연결 라이브러리](concepts-connection-libraries.md)
