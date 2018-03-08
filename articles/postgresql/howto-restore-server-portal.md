---
title: "Azure Database for PostgreSQL에서 서버를 복원하는 방법"
description: "이 문서에서는 Azure Portal을 사용하여 PostgreSQL용 Azure Database에서 서버를 복원하는 방법을 설명합니다."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 7607a3e60eec39de61c785b8ff75a9f11fa02d0c
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2018
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Database for PostgreSQL에서 서버를 백업 및 복원하는 방법

## <a name="backup-happens-automatically"></a>자동으로 수행되는 백업
Azure Database for PostgreSQL 서버는 정기적으로 백업되어 복원 기능을 사용하도록 설정할 수 있습니다. 이 기능을 사용하면 서버 및 모든 데이터베이스를 이전 특정 시점으로 새 서버에 복원할 수 있습니다.

## <a name="set-backup-configuration"></a>백업 구성 설정

서버를 만들 때 **가격 책정 계층** 창에서 로컬 중복 백업 또는 지역 중복 백업을 위한 서버 구성 중에서 선택할 수 있습니다.

> [!NOTE]
> 서버가 만들어지면 지리적으로 중복되거나 로컬로 중복된 중복 형식은 전환할 수 없습니다.
>

Azure Portal을 통해 서버를 만드는 중에 **가격 책정 계층** 창에서 서버에 대해 **로컬 중복** 또는 **지역 중복** 백업을 선택합니다. 또한 이 창에서는 **백업 보존 기간**(서버 백업을 저장하려는 기간(일))도 선택할 수 있습니다.

   ![가격 책정 계층 - 백업 중복 선택](./media/howto-restore-server-portal/pricing-tier.png)

만드는 중에 이러한 값을 설정하는 방법에 대한 자세한 내용은 [Azure Database for PostgreSQL 서버 빠른 시작](quickstart-create-server-database-portal.md)을 참조하세요.

서버의 백업 보존 기간은 다음 단계를 통해 변경할 수 있습니다.
1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. Azure Database for PostgreSQL 서버를 선택합니다. 이 작업은 **개요** 페이지를 엽니다.
3. **설정** 아래의 메뉴에서 **가격 책정 계층**을 선택합니다. 슬라이더를 사용하여 **백업 보존 기간**을 7일에서 35일까지 원하는 대로 변경할 수 있습니다.
아래 스크린샷에서는 34일로 늘렸습니다.
![늘어난 백업 보존 기간](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. **확인**을 클릭하여 변경 내용을 확인합니다.

백업 보존 기간은 사용 가능한 백업을 기반으로 하기 때문에 특정 시점 복원을 검색할 수 있는 시간을 제어합니다. 특정 시점 복원은 다음 섹션에서 자세히 설명합니다. 

## <a name="point-in-time-restore-in-the-azure-portal"></a>Azure Portal의 특정 시점 복원
Azure Database for PostgreSQL을 사용하면 특정 시점의 서버를 서버의 새 복사본으로 다시 복원할 수 있습니다. 이 새 서버를 사용하여 데이터를 복구하거나 클라이언트 응용 프로그램에서 이 새 서버를 가리키도록 할 수 있습니다.

예를 들어 오늘 정오에 실수로 테이블을 삭제한 경우 정오 바로 전으로 복원하고 누락된 테이블과 데이터를 서버의 새로운 복사본에서 검색할 수 있습니다. 특정 시점 복원은 데이터베이스 수준이 아닌 서버 수준에 있습니다.

다음 단계는 샘플 서버를 특정 시점으로 복원합니다.
1. Azure Portal에서 Azure Database for PostgreSQL 서버를 선택합니다. 

2. 서버 **개요** 페이지의 도구 모음에서 **복원**을 선택합니다.

   ![PostgreSQL용 Azure Database - 개요 - 복원 단추](./media/howto-restore-server-portal/2-server.png)

3. 필요한 정보로 복원 양식을 채웁니다.

   ![PostgreSQL용 Azure Database - 정보 복원 ](./media/howto-restore-server-portal/3-restore.png)
  - **복원 지점**: 복원하려는 특정 시점을 선택합니다.
  - **대상 서버**: 새 서버에 대한 이름을 제공합니다.
  - **위치**: 하위 지역을 선택할 수 없습니다. 기본적으로 원본 서버와 동일합니다.
  - **가격 책정 계층**: 특정 시점 복원을 수행할 때 이러한 매개 변수는 변경할 수 없습니다. 원본 서버와 동일합니다. 

4. **확인**을 클릭하여 특정 시점으로 복원할 서버를 복원합니다. 

5. 복원이 완료되면 예상대로 데이터가 복원되었는지 확인하기 위해 생성하였던 새 서버를 찾습니다.

>[!Note]
>특정 시점 복원으로 만든 새 서버에는 선택한 특정 시점 당시의 기존 서버에 유효한 동일한 서버 관리자 로그인 이름과 암호가 있습니다. 암호는 새 서버의 **개요** 페이지에서 변경할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- 서비스의 [백업](concepts-backup.md)을 자세히 알아봅니다.
- [비즈니스 연속성](concepts-business-continuity.md) 옵션을 자세히 알아봅니다.
