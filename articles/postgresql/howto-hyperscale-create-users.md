---
title: 사용자 만들기-Hyperscale (Citus)-Azure Database for PostgreSQL
description: 이 문서에서는 Citus (Azure Database for PostgreSQL-Hyperscale)와 상호 작용 하기 위해 새 사용자 계정을 만드는 방법을 설명 합니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d093d4c23fcc44e7e9f3461f875607926f4b612d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977576"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL-Hyperscale (Citus)에서 사용자 만들기

이 문서에서는 Citus (Hyperscale) 서버 그룹 내에서 사용자를 만드는 방법을 설명 합니다. Azure 구독 사용자 및 해당 권한에 대 한 자세한 내용은 [AZURE RBAC (역할 기반 액세스 제어) 문서](../role-based-access-control/built-in-roles.md) 를 참조 하거나 [역할을 사용자 지정 하는 방법을](../role-based-access-control/custom-roles.md)검토 하세요.

## <a name="the-server-admin-account"></a>A kiszolgálói rendszergazdai fiók

새로 만든 Citus (Hyperscale) 서버 그룹에는 다음과 같은 몇 가지 역할이 미리 정의 되어 있습니다.

* [기본 PostgreSQL 역할](https://www.postgresql.org/docs/current/default-roles.html)
* *azure_pg_admin*
* *postgres*
* *citus*

PostgreSQL 엔진은 [PostgreSQL 제품 설명서](https://www.postgresql.org/docs/current/static/sql-createrole.html)에 설명 된 대로 권한을 사용 하 여 데이터베이스 개체에 대 한 액세스를 제어 합니다.
서버 관리자 사용자 *citus*는 *azure_pg_admin* 역할의 멤버입니다.
그러나 *postgres* (슈퍼 사용자) 역할의 일부가 아닙니다.  Hyperscale은 관리 되는 PaaS 서비스 이므로 Microsoft만 슈퍼 사용자 역할의 일부입니다. *Citus* 사용자는 제한 된 권한을 가지 며, 예를 들어 새 데이터베이스를 만들 수 없습니다.

## <a name="how-to-create-additional-users"></a>추가 사용자를 만드는 방법

*Citus* admin 계정에 추가 사용자를 만들 수 있는 권한이 부족 합니다. 사용자를 추가 하려면 Azure Portal 인터페이스를 사용 합니다.

1. 하이퍼 확장 서버 그룹에 대 한 **역할** 페이지로 이동 하 고 **+ 추가**를 클릭 합니다.

   ![역할 페이지](media/howto-hyperscale-create-users/1-role-page.png)

2. 역할 이름 및 암호를 입력 합니다. Kattintson a **Save** (Mentés) gombra.

   ![역할 추가](media/howto-hyperscale-create-users/2-add-user-fields.png)

사용자는 서버 그룹의 코디네이터 노드에 만들어지고 모든 작업자 노드에 전파 됩니다.

## <a name="how-to-delete-a-user-or-change-their-password"></a>사용자를 삭제 하거나 암호를 변경 하는 방법

하이퍼 확장 서버 그룹에 대 한 **역할** 페이지로 이동 하 여 사용자 옆에 있는 **줄임표 (...)** 를 클릭 합니다. 줄임표는 사용자를 삭제 하거나 암호를 다시 설정 하는 메뉴를 엽니다.

   ![역할 편집](media/howto-hyperscale-create-users/edit-role.png)

*Citus* 역할은 권한 있는 역할을 하며 삭제할 수 없습니다.

## <a name="how-to-modify-privileges-for-role"></a>역할에 대 한 권한을 수정 하는 방법

새 역할은 일반적으로 제한 된 권한으로 데이터베이스 액세스를 제공 하는 데 사용 됩니다. 사용자 권한을 수정 하려면 PgAdmin 또는 psql과 같은 도구를 사용 하 여 표준 PostgreSQL 명령을 사용 합니다. (Citus (Hyperscale)에서 [psql로 연결](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) 을 참조 하세요.)

예를 들어 *db_user* 에서 *mytable*을 읽도록 허용 하려면 권한을 부여 합니다.

```sql
GRANT SELECT ON mytable TO db_user;
```

Citus (hyperscale)는 전체 클러스터를 통해 단일 테이블 GRANT 문을 전파 하 여 모든 작업자 노드에 적용 합니다. 그러나 시스템 차원 (예: 스키마의 모든 테이블)은 모든 날짜 노드에서 실행 해야 합니다.  *Run_command_on_workers ()* 도우미 함수를 사용 합니다.

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="next-steps"></a>Következő lépések

새 사용자 컴퓨터의 IP 주소에 대 한 방화벽을 열어 연결 하도록 설정 합니다. [Azure Portal를 사용 하 여 Citus (Hyperscale) 방화벽 규칙을 만들고 관리](howto-hyperscale-manage-firewall-using-portal.md)합니다.

데이터베이스 사용자 계정 관리에 대 한 자세한 내용은 PostgreSQL 제품 설명서를 참조 하십시오.

* [데이터베이스 역할 및 권한](https://www.postgresql.org/docs/current/static/user-manag.html)
* [GRANT 구문](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Jogosultságok](https://www.postgresql.org/docs/current/static/ddl-priv.html)
