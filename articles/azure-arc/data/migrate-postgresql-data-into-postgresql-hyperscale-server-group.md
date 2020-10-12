---
title: PostgreSQL 데이터베이스의 데이터를 Azure Arc enabled PostgreSQL Hyperscale 서버 그룹으로 마이그레이션
titleSuffix: Azure Arc enabled database services
description: PostgreSQL 데이터베이스의 데이터를 Azure Arc enabled PostgreSQL Hyperscale 서버 그룹으로 마이그레이션
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 521fd61f18d6673e21c23dbca4cfc12d2ee4bf0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939641"
---
# <a name="migrate-postgresql-database-to-azure-arc-enabled-postgresql-hyperscale-server-group"></a>PostgreSQL database를 Azure Arc 사용 PostgreSQL Hyperscale 서버 그룹으로 마이그레이션

이 문서에서는 기존 PostgreSQL 데이터베이스 (Azure Arc 사용 Data Services에서 호스팅되지 않는 데이터베이스)를 Azure Arc enabled PostgreSQL Hyperscale 서버 그룹으로 가져오는 단계를 설명 합니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="considerations"></a>고려 사항

Azure Arc enabled PostgreSQL Hyperscale 서버 그룹은 PostgreSQL의 커뮤니티 버전 이며 CitusData 확장을 사용 하도록 설정 된 상태로 실행 됩니다. 따라서 Azure Arc 외부 PostgreSQL에서 작동 하는 모든 도구는 Azure Arc enabled PostgreSQL Hyperscale server 그룹에서 작동 해야 합니다.


따라서 Postgres에 대해 지금 사용 하는 도구 집합을 사용 하 여 다음을 수행할 수 있습니다.
1. Azure Arc 외부에 호스트 된 인스턴스에서 Postgres 데이터베이스를 백업 합니다.
2. Azure Arc enabled PostgreSQL Hyperscale 서버 그룹에서 복원

사용자가 수행할 수 있는 작업은 다음과 같습니다.
- 서버 매개 변수 다시 설정
- 보안 컨텍스트 다시 설정: 사용자, 역할 및 권한 다시 설정 ...

이 백업/복원 작업을 수행 하기 위해 Postgres에 대해 백업/복원을 수행할 수 있는 모든 도구를 사용할 수 있습니다. 예를 들면 다음과 같습니다.
- Azure Data Studio 및 해당 Postgres 확장
- `pgcli`
- `pgAdmin`
- `pg_dump`
- `pg_restore`
- `psql`
- ...

## <a name="example"></a>예제
도구를 사용 하 여 이러한 단계를 설명 해 보겠습니다 `pgAdmin` .
다음 설정을 고려 하십시오.
- **원본:**  
    운영 체제 미 설치 서버에서 온-프레미스로 실행 되 고 이름이 JEANYDSRV 인 Postgres server 버전 12이 고 1 개의 행 :::image type="content" source="media/postgres-hyperscale/migrate-pg-source.jpg" alt-text="마이그레이션-원본이"::: 있는 T1 테이블이 있는 MyOnPremPostgresDB 라는 데이터베이스를 호스팅합니다.

- **대상이**  
    Azure Arc 환경에서 실행 되 고 이름이 postgres01 인 Postgres server 버전 12입니다. 표준 Postgres 데이터베이스를 제외한 데이터베이스는 없습니다.  
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination.jpg" alt-text="마이그레이션-원본이":::


### <a name="take-a-backup-of-the-source-database-on-premises"></a>원본 데이터베이스의 백업을 온-프레미스로 가져오기

:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup.jpg" alt-text="마이그레이션-원본이":::

구성:
1. 파일 이름을 지정 합니다. **MySourceBackup**
2. 형식을 **사용자 지정** 
 :::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup2.jpg" alt-text="마이그레이션-원본이"::: 으로 설정 합니다.

백업이 성공적으로 완료 됩니다.  
:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup3.jpg" alt-text="마이그레이션-원본이":::

### <a name="create-an-empty-database-on-the-destination-system-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc enabled PostgreSQL Hyperscale 서버 그룹의 대상 시스템에서 빈 데이터베이스를 만듭니다.

> [!NOTE]
> 도구에 Postgres 인스턴스를 등록 하려면 `pgAdmin` Kubernetes 클러스터에서 인스턴스의 공용 IP를 사용 하 고 포트 및 보안 컨텍스트를 적절 하 게 설정 해야 합니다. `psql`다음 명령을 실행 한 후 끝점 줄에서 이러한 세부 정보를 찾을 수 있습니다.

```console
azdata arc postgres endpoint list -n postgres01
```
다음과 같은 출력을 반환 합니다.
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

대상 데이터베이스의 이름을 지정 **RESTORED_MyOnPremPostgresDB**  
:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg" alt-text="마이그레이션-대상-db-만들기"lightbox="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg":::

### <a name="restore-the-database-in-your-arc-setup"></a>Arc 설정에서 데이터베이스 복원
:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore.jpg" alt-text="마이그레이션-원본이":::

복원 구성:
1. 복원할 백업이 포함 된 파일을 가리킵니다. **MySourceBackup**
2. **사용자 지정 또는 tar** 
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore2.jpg" alt-text="마이그레이션-원본이"::: 으로 설정 된 서식 유지

3. **복원**을 클릭합니다.  

   복원에 성공 했습니다.  
   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore3.jpg" alt-text="마이그레이션-원본이":::

### <a name="verify-that-the-database-was-successfully-restored-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>데이터베이스가 Azure Arc enabled PostgreSQL Hyperscale 서버 그룹에서 성공적으로 복원 되었는지 확인 합니다.

다음 방법 중 하나를 사용 합니다.

**시작 `pgAdmin` :**  

Azure Arc 설정에서 호스트 되는 Postgres 인스턴스를 확장 합니다. 복원 된 데이터베이스에 테이블이 표시 되 고 데이터를 선택할 때 온-프레미스 인스턴스에 있는 것과 동일한 행을 표시 합니다.

   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestoreverif.jpg" alt-text="마이그레이션-원본이"
     },
     {
       "Description": "Log Search Dashboard",
       "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
     },
     {
       "Description": "Metrics Dashboard",
       "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
     }
   ]
   ```

1. `psql`연결 문자열에서 `-d` 매개 변수를 사용 하 여 데이터베이스 이름을 표시 합니다. 아래의 명령을 사용 하 여 암호를 입력 하 라는 메시지가 표시 됩니다.

   ```console
   psql -d RESTORED_MyOnPremPostgresDB -U postgres -h 10.0.0.4 -p 32639
   ```

   `psql` 연결.

   ```output
   Password for user postgres:
   psql (10.12 (Ubuntu 10.12-0ubuntu0.18.04.1), server 12.3 (Debian 12.3-1.pgdg100+1))
   WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
   SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
   Type "help" for help.

   RESTORED_MyOnPremPostgresDB=#   
   ```

1. 테이블을 선택 하면 온-프레미스 Postgres 인스턴스에서 복원한 데이터가 표시 됩니다.

   ```console
   RESTORED_MyOnPremPostgresDB=# select * from t1;
   ```

   ```output
    col1 |    col2
   ------+-------------
       1 | BobbyIsADog
   (1 row)
   ```

> [!NOTE]
> - 규모를 확장 하 고 PostgreSQL Hyperscale 서버 그룹의 작업자 노드에 데이터를 분할/분산 시킬 때까지 Azure Arc PostgreSQL Hyperscale에서 실행 하는 경우 성능에 많은 이점이 표시 되지 않습니다. [다음 단계](#next-steps)를 참조 하세요.
>
> - 현재 온-프레미스 또는 기타 클라우드에서 실행 되는 기존 Postgres 인스턴스에 "Azure Arc에 등록" 할 수 없습니다. 즉, 기존 Postgres 인스턴스에 일종의 "Azure Arc agent"를 설치 하 여 Azure Arc에서 사용 하도록 설정 하는 Postgres를 설정 하는 것은 불가능 합니다. 대신 새 Postgres 인스턴스를 만들고 여기에 데이터를 전송 해야 합니다. 위에 표시 된 기법을 사용 하 여이 작업을 수행 하거나 원하는 ETL 도구를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure Database for PostgreSQL Hyperscale의 개념 및 방법 가이드를 읽고 여러 PostgreSQL Hyperscale 노드에 데이터를 분산 하 고 Azure Database for PostgreSQL 하이퍼 규모의 모든 기능을 활용 합니다.
    * [노드 및 테이블](../../postgresql/concepts-hyperscale-nodes.md)
    * [애플리케이션 유형 확인](../../postgresql/concepts-hyperscale-app-type.md)
    * [배포 열 선택](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [테이블 공동 배치](../../postgresql/concepts-hyperscale-colocation.md)
    * [테이블 분산 및 수정](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [다중 테 넌 트 데이터베이스 디자인](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [실시간 분석 대시보드 디자인](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> *이 문서에서는 **Azure Portal에 로그인**하는 섹션을 건너뛰고 **Postgres-Hyperscale (Citus)에 대 한 Azure 데이터베이스를 만듭니다**. Azure Arc 배포의 나머지 단계를 구현 합니다. 이러한 섹션은 Azure 클라우드에서 PaaS 서비스로 제공 되는 Citus (Azure Database for PostgreSQL Hyperscale)에 고유 하지만, 문서의 다른 부분은 Azure Arc enabled PostgreSQL Hyperscale에 직접 적용할 수 있습니다.

- [Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 스케일 아웃](scale-out-postgresql-hyperscale-server-group.md)
