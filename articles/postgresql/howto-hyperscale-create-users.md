---
title: 사용자 만들기 - 하이퍼스케일(Citus) - PostgreSQL용 Azure 데이터베이스
description: 이 문서에서는 PostgreSQL - 하이퍼스케일(Citus)에 대한 Azure 데이터베이스와 상호 작용하기 위해 새 사용자 계정을 만드는 방법에 대해 설명합니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 1/8/2019
ms.openlocfilehash: 674fd4372bdf7c3782d18aaf04b48eb0067a9b2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484930"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>PostgreSQL - 하이퍼스케일(Citus)에 대한 Azure 데이터베이스에서 사용자 만들기

> [!NOTE]
> "사용자"라는 용어는 하이퍼스케일(Citus) 서버 그룹 내의 사용자를 나타냅니다. Azure 구독 사용자 및 해당 권한에 대해 알아보려면 [AZURE 역할 기반 액세스 제어(RBAC) 문서를](../role-based-access-control/built-in-roles.md) 방문하거나 역할을 사용자 [지정하는 방법을](../role-based-access-control/custom-roles.md)검토합니다.

## <a name="the-server-admin-account"></a>서버 관리자 계정

PostgreSQL 엔진은 [역할을](https://www.postgresql.org/docs/current/sql-createrole.html) 사용하여 데이터베이스 개체에 대한 액세스를 제어하며 새로 생성된 하이퍼스케일(Citus) 서버 그룹에는 미리 정의된 몇 가지 역할이 제공됩니다.

* [기본 PostgreSQL 역할](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

하이퍼스케일은 관리되는 PaaS 서비스이므로 Microsoft만 수퍼 사용자 역할로 로그인할 `postgres` 수 있습니다. 제한된 관리 액세스의 경우 `citus` 하이퍼스케일은 역할을 제공합니다.

`citus` 역할에 대한 권한:

* 모든 구성 변수를 읽고, 일반적으로 수퍼 유저에게만 표시되는 변수도 읽습니다.
* 모든 pg\_\_ \* 통계 보기를 읽고 다양한 통계 관련 확장을 사용하여 일반적으로 수퍼유저만 볼 수 있는 보기 또는 확장도 사용할 수 있습니다.
* 테이블에 ACCESS SHARE 잠금을 취할 수 있는 모니터링 함수를 실행합니다.
* 역할이 멤버이기 `azure_pg_admin`때문에 [PostgreSQL 확장을 만듭니다.](concepts-hyperscale-extensions.md)

특히 `citus` 역할에는 몇 가지 제한 사항이 있습니다.

* 역할을 만들 수 없습니다.
* 데이터베이스를 만들 수 없습니다.

## <a name="how-to-create-additional-user-roles"></a>추가 사용자 역할을 만드는 방법

앞서 언급했듯이 `citus` 관리자 계정에는 추가 사용자를 만들 수 있는 권한이 없습니다. 사용자를 추가하려면 Azure 포털 인터페이스를 사용합니다.

1. 하이퍼스케일 서버 그룹의 **역할** 페이지로 이동하여 **+ 추가를**클릭합니다.

   ![역할 페이지](media/howto-hyperscale-create-users/1-role-page.png)

2. 역할 이름과 암호를 입력합니다. **저장**을 클릭합니다.

   ![역할 추가](media/howto-hyperscale-create-users/2-add-user-fields.png)

사용자는 서버 그룹의 코디네이터 노드에서 만들어지고 모든 작업자 노드에 전파됩니다. Azure 포털을 통해 생성된 `LOGIN` 역할에는 데이터베이스에 로그인할 수 있는 실제 사용자라는 특성이 있습니다.

## <a name="how-to-modify-privileges-for-user-role"></a>사용자 역할에 대한 권한을 수정하는 방법

새 사용자 역할은 일반적으로 제한된 권한으로 데이터베이스 액세스를 제공하는 데 사용됩니다. 사용자 권한을 수정하려면 PgAdmin 또는 psql과 같은 도구를 사용하여 표준 PostgreSQL 명령을 사용합니다. (하이퍼스케일(Citus) 퀵스타트에서 [psql으로 연결하기](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) 를 참조하십시오.)

예를 들어 읽을 `db_user` `mytable`수 있도록 하려면 권한을 부여합니다.

```sql
GRANT SELECT ON mytable TO db_user;
```

하이퍼스케일(Citus)은 전체 클러스터를 통해 단일 테이블 GRANT 문을 전파하여 모든 작업자 노드에 적용합니다. 그러나 시스템 전체(예: 스키마의 모든 테이블)인 GRANT는 모든 날짜 노드에서 실행되어야 합니다.  도우미 `run_command_on_workers()` 함수를 사용합니다.

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>사용자 역할을 삭제하거나 암호를 변경하는 방법

사용자를 업데이트하려면 하이퍼스케일 서버 그룹의 **역할** 페이지를 방문하고 사용자 **옆에** 있는 타원을 클릭합니다. 타원은 사용자를 삭제하거나 암호를 재설정하는 메뉴를 엽니다.

   ![역할 편집](media/howto-hyperscale-create-users/edit-role.png)

`citus` 역할은 권한이 있으며 삭제할 수 없습니다.

## <a name="next-steps"></a>다음 단계

새 사용자 컴퓨터의 IP 주소에 대한 방화벽을 열어 연결할 수 있도록 합니다: [Azure 포털을 사용하여 하이퍼스케일(Citus) 방화벽 규칙을 만들고 관리합니다.](howto-hyperscale-manage-firewall-using-portal.md)

데이터베이스 사용자 계정 관리에 대한 자세한 내용은 PostgreSQL 제품 설명서를 참조하십시오.

* [데이터베이스 역할 및 권한](https://www.postgresql.org/docs/current/static/user-manag.html)
* [그랜트 구문](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [권한](https://www.postgresql.org/docs/current/static/ddl-priv.html)
