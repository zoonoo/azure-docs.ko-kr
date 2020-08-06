---
title: 사용자 만들기-Hyperscale (Citus)-Azure Database for PostgreSQL
description: 이 문서에서는 Citus (Azure Database for PostgreSQL-Hyperscale)와 상호 작용 하기 위해 새 사용자 계정을 만드는 방법을 설명 합니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/8/2019
ms.openlocfilehash: 8a1b38b9f673669adb0b5fcf67d9d560c24d5c2a
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87825959"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL-Hyperscale (Citus)에서 사용자 만들기

> [!NOTE]
> "사용자" 라는 용어는 Citus (Hyperscale) 서버 그룹 내의 사용자를 의미 합니다. Azure 구독 사용자 및 해당 권한에 대 한 자세한 내용은 azure [역할 기반 access control (AZURE RBAC) 문서](../role-based-access-control/built-in-roles.md) 를 참조 하거나 [역할을 사용자 지정 하는 방법을](../role-based-access-control/custom-roles.md)검토 하세요.

## <a name="the-server-admin-account"></a>서버 관리자 계정

PostgreSQL 엔진은 [역할](https://www.postgresql.org/docs/current/sql-createrole.html) 을 사용 하 여 데이터베이스 개체에 대 한 액세스를 제어 하 고, 새로 만든 Citus (hyperscale) 서버 그룹에는 미리 정의 된 여러 역할이 있습니다.

* [기본 PostgreSQL 역할](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

Hyperscale은 관리 되는 PaaS 서비스 이므로 Microsoft만 슈퍼 사용자 역할을 사용 하 여 로그인 할 수 있습니다 `postgres` . 제한 된 관리 액세스의 경우 Hyperscale은 역할을 제공 합니다 `citus` .

역할에 대 한 사용 권한 `citus` :

* 일반적으로 superusers에만 표시 되는 변수를 포함 하 여 모든 구성 변수를 읽습니다.
* 모든 pg 상태 \_ \_ \* 뷰를 읽고 다양 한 통계 관련 확장 프로그램을 사용 합니다. 일반적으로 superusers에만 표시 되는 뷰 또는 확장도 있습니다.
* 테이블에 대 한 액세스 공유 잠금을 사용할 수 있는 모니터링 함수를 실행 합니다.
* [PostgreSQL 확장을 만듭니다](concepts-hyperscale-extensions.md) (역할은의 멤버 이기 때문 `azure_pg_admin` ).

특히 역할에는 `citus` 몇 가지 제한 사항이 있습니다.

* 역할을 만들 수 없음
* 데이터베이스를 만들 수 없음

## <a name="how-to-create-additional-user-roles"></a>추가 사용자 역할을 만드는 방법

앞서 언급 했 듯이 `citus` 관리자 계정에는 추가 사용자를 만들 수 있는 권한이 없습니다. 사용자를 추가 하려면 Azure Portal 인터페이스를 사용 합니다.

1. 하이퍼 확장 서버 그룹에 대 한 **역할** 페이지로 이동 하 고 **+ 추가**를 클릭 합니다.

   ![역할 페이지](media/howto-hyperscale-create-users/1-role-page.png)

2. 역할 이름 및 암호를 입력 합니다. **저장**을 클릭합니다.

   ![역할 추가](media/howto-hyperscale-create-users/2-add-user-fields.png)

사용자는 서버 그룹의 코디네이터 노드에 만들어지고 모든 작업자 노드에 전파 됩니다. Azure Portal를 통해 만든 역할에는 `LOGIN` 특성이 있습니다. 즉, 데이터베이스에 로그인 할 수 있는 진정한 사용자 임을 의미 합니다.

## <a name="how-to-modify-privileges-for-user-role"></a>사용자 역할에 대 한 권한을 수정 하는 방법

새 사용자 역할은 일반적으로 제한 된 권한으로 데이터베이스 액세스를 제공 하는 데 사용 됩니다. 사용자 권한을 수정 하려면 PgAdmin 또는 psql과 같은 도구를 사용 하 여 표준 PostgreSQL 명령을 사용 합니다. (Citus (Hyperscale)에서 [psql로 연결](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) 을 참조 하세요.)

예를 들어를 읽을 수 있도록 하려면 `db_user` `mytable` 사용 권한을 부여 합니다.

```sql
GRANT SELECT ON mytable TO db_user;
```

Citus (hyperscale)는 전체 클러스터를 통해 단일 테이블 GRANT 문을 전파 하 여 모든 작업자 노드에 적용 합니다. 또한 시스템 차원의 부여 (예: 스키마의 모든 테이블)를 전파 합니다.

```sql
-- applies to the coordinator node and propagates to workers
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>사용자 역할을 삭제 하거나 암호를 변경 하는 방법

사용자를 업데이트 하려면 하이퍼 확장 서버 그룹에 대 한 **역할** 페이지를 방문 하 여 사용자 옆에 **있는 줄임표 (...)** 를 클릭 합니다. 줄임표는 사용자를 삭제 하거나 암호를 다시 설정 하는 메뉴를 엽니다.

   ![역할 편집](media/howto-hyperscale-create-users/edit-role.png)

`citus`역할은 특권 이며 삭제할 수 없습니다.

## <a name="next-steps"></a>다음 단계

새 사용자 컴퓨터의 IP 주소에 대 한 방화벽을 열어 연결 하도록 설정 합니다. [Azure Portal를 사용 하 여 Citus (Hyperscale) 방화벽 규칙을 만들고 관리](howto-hyperscale-manage-firewall-using-portal.md)합니다.

데이터베이스 사용자 계정 관리에 대 한 자세한 내용은 PostgreSQL 제품 설명서를 참조 하십시오.

* [데이터베이스 역할 및 권한](https://www.postgresql.org/docs/current/static/user-manag.html)
* [GRANT 구문](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [부여](https://www.postgresql.org/docs/current/static/ddl-priv.html)
