---
title: 사용자 만들기 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 이 문서에서는 Azure Database for PostgreSQL - 하이퍼스케일(Citus)을 조작할 새 사용자 계정을 만드는 방법을 설명합니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/8/2019
ms.openlocfilehash: 3772918d1a7e505b3994afa7849568bb309bd0cf
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109715415"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL-하이퍼스케일(Citus)에서 사용자 만들기

## <a name="the-server-admin-account"></a>서버 관리자 계정

PostgreSQL 엔진은 [역할](https://www.postgresql.org/docs/current/sql-createrole.html)을 사용하여 데이터베이스 개체에 대한 액세스를 제어하고, 새로 만든 하이퍼스케일(Citus) 서버 그룹에는 미리 정의된 여러 역할이 제공됩니다.

* [기본 PostgreSQL 역할](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

하이퍼스케일(Citus)은 관리형 PaaS 서비스이므로 Microsoft만 `postgres` 슈퍼 사용자 역할로 로그인 할 수 있습니다. 제한된 관리 액세스를 위해 하이퍼스케일(Citus)은 `citus` 역할을 제공합니다.

`citus` 역할의 권한:

* 일반적으로 슈퍼 사용자에게만 표시되는 변수를 포함하여 모든 구성 변수를 읽습니다.
* 일반적으로 슈퍼 사용자에게만 표시되는 뷰 또는 확장을 포함하여 모든 pg\_stat\_\* 뷰를 읽고 다양한 통계 관련 확장을 사용합니다.
* 테이블에 대한 액세스 공유 잠금을 장기간 수행할 수 있는 모니터링 기능을 실행합니다.
* [PostgreSQL 확장을 만듭니다](concepts-hyperscale-extensions.md)(역할이 `azure_pg_admin`의 멤버이기 때문임).

특히 `citus` 역할에는 다음과 같은 몇 가지 제한 사항이 있습니다.

* 역할을 만들 수 없음
* 데이터베이스를 만들 수 없음

## <a name="how-to-create-additional-user-roles"></a>추가 사용자 역할을 만드는 방법

앞서 언급했듯이 `citus` 관리자 계정에는 추가 사용자를 만들 수 있는 권한이 없습니다. 사용자를 추가하려면 Azure Portal 인터페이스를 사용합니다.

1. 하이퍼스케일(Citus) 서버 그룹의 **역할** 페이지로 이동한 다음 **+ 추가** 를 클릭합니다.

   :::image type="content" source="media/howto-hyperscale-create-users/1-role-page.png" alt-text="역할 페이지":::

2. 역할 이름 및 암호를 입력합니다. **저장** 을 클릭합니다.

   :::image type="content" source="media/howto-hyperscale-create-users/2-add-user-fields.png" alt-text="역할 추가":::

사용자는 서버 그룹의 코디네이터 노드에 생성되고 모든 작업자 노드에 전파됩니다. Azure Portal을 통해 만든 역할에는 `LOGIN` 특성이 있으며 데이터베이스에 로그인할 수 있는 진정한 사용자인 것을 의미합니다.

## <a name="how-to-modify-privileges-for-user-role"></a>사용자 역할의 권한을 수정하는 방법

새 사용자 역할은 일반적으로 제한된 권한을 가진 데이터베이스 액세스를 제공하는 데 사용됩니다. 사용자 권한을 수정하려면 PgAdmin 또는 psql과 같은 도구를 사용하여 표준 PostgreSQL 명령을 사용합니다. 하이퍼스케일(Citus) 빠른 시작에서 [psql로 연결](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql)을 참조하세요.

예를 들어 `db_user`가 `mytable`을 읽을 수 있도록 하려면 다음 권한을 부여합니다.

```sql
GRANT SELECT ON mytable TO db_user;
```

하이퍼스케일(Citus)은 단일 테이블 GRANT 문을 전체 클러스터에 전파하여 모든 작업자 노드에 적용합니다. 또한 시스템 차원(예: 스키마의 모든 테이블)에서 GRANT를 전파합니다.

```sql
-- applies to the coordinator node and propagates to workers
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>사용자 역할을 삭제하거나 암호를 변경하는 방법

사용자를 업데이트하려면 하이퍼스케일(Citus) 서버 그룹의 **역할** 페이지로 이동한 다음 사용자 옆에 있는 줄임표( **...** )를 클릭합니다. 줄임표를 클릭하면 사용자를 삭제하거나 암호를 재설정할 수 있는 메뉴가 열립니다.

   :::image type="content" source="media/howto-hyperscale-create-users/edit-role.png" alt-text="역할 편집":::

`citus` 역할은 권한이 있으며 삭제할 수 없습니다.

## <a name="next-steps"></a>다음 단계

새 사용자의 머신 IP 주소에 대해 방화벽을 열어 연결할 수 있도록 합니다. [Azure Portal을 사용하여 하이퍼스케일(Citus) 방화벽 규칙 만들기 및 관리](howto-hyperscale-manage-firewall-using-portal.md)를 참조하세요.

데이터베이스 사용자 계정 관리에 대한 자세한 내용은 PostgreSQL 제품 설명서를 참조하세요.

* [데이터베이스 역할 및 권한](https://www.postgresql.org/docs/current/static/user-manag.html)
* [GRANT 구문](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [권한](https://www.postgresql.org/docs/current/static/ddl-priv.html)
