---
title: '자습서: Azure Portal을 통해 PostgreSQL를 Azure DB for PostgreSQL online으로 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Portal를 통해 Azure Database Migration Service를 사용 하 여 PostgreSQL 온-프레미스에서 Azure Database for PostgreSQL로 온라인 마이그레이션을 수행 하는 방법을 알아봅니다.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 04/11/2020
ms.openlocfilehash: e01cc1c07d720c4743a03b5001e640f8b851dd5c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113995"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>자습서: Azure Portal을 통해 DMS를 사용 하 여 PostgreSQL online 용 Azure DB로 PostgreSQL 마이그레이션

Azure Database Migration Service를 사용 하 여 응용 프로그램의 가동 중지 시간을 최소화 하면서 온-프레미스 PostgreSQL 인스턴스에서 데이터베이스를 [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/) 마이그레이션할 수 있습니다. 이 자습서에서는 Azure Database Migration Service에서 온라인 마이그레이션 작업을 사용하여 **DVD 대여** 샘플 데이터베이스를 PostgreSQL 9.6의 온-프레미스 인스턴스에서 Azure Database for PostgreSQL로 마이그레이션합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
>
> * Pg_dump 유틸리티를 사용 하 여 샘플 스키마를 마이그레이션합니다.
> * Azure Database Migration Service 인스턴스를 만듭니다.
> * Azure Database Migration Service에서 마이그레이션 프로젝트를 만듭니다.
> * 마이그레이션을 실행합니다.
> * 마이그레이션을 모니터링합니다.
> * 마이그레이션을 수행 합니다.

> [!NOTE]
> Azure Database Migration Service를 사용하여 온라인 마이그레이션을 수행하려면 프리미엄 가격 책정 계층에 따라 인스턴스를 만들어야 합니다. 마이그레이션을 진행 하는 동안 데이터 도난을 방지 하기 위해 디스크를 암호화 합니다.

> [!IMPORTANT]
> 최적의 마이그레이션 환경을 위해 Microsoft는 대상 데이터베이스와 동일한 Azure 지역에서 Azure Database Migration Service의 인스턴스를 만드는 것을 권장합니다. 영역 또는 지역 간에 데이터를 이동하면 마이그레이션 프로세스 속도가 저하되고 오류가 발생할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

* [PostgreSQL community edition](https://www.postgresql.org/download/) 9.4, 9.5, 9.6 또는 10을 다운로드 하 여 설치 합니다. 원본 PostgreSQL 서버 버전은 9.4, 9.5, 9.6, 10 또는 11 이어야 합니다. 자세한 내용은 [지원되는 PostgreSQL 데이터베이스 버전](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) 문서를 참조하세요.

    또한 대상 Azure Database for PostgreSQL 버전은 온-프레미스 PostgreSQL 버전과 같거나 그 이상 이어야 합니다. 예를 들어 PostgreSQL 9.6은 Azure Database for PostgreSQL 9.6, 10 또는 11로 마이그레이션할 수 있지만 Azure Database for PostgreSQL 9.5으로는 마이그레이션할 수 없습니다.

* [Azure Database for PostgreSQL 서버를 만들거나](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) [Citus (Azure Database for PostgreSQL-hyperscale) 서버를 만듭니다](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal).
* [Express](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 경로 또는 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)을 사용 하 여 온-프레미스 원본 서버에 대 한 사이트 간 연결을 제공 하는 Azure Resource Manager 배포 모델을 사용 하 여 Azure Database Migration Service에 대 한 Microsoft Azure Virtual Network를 만듭니다. 가상 네트워크를 만드는 방법에 대 한 자세한 내용은 [Virtual Network 설명서](https://docs.microsoft.com/azure/virtual-network/)와 특히 단계별 정보를 포함 하는 빠른 시작 문서를 참조 하세요.

    > [!NOTE]
    > 가상 네트워크를 설정 하는 동안 Microsoft에 네트워크 피어 링을 사용 하는 Express 경로를 사용 하는 경우 서비스가 프로 비전 될 서브넷에 다음 서비스 [끝점](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) 을 추가 합니다.
    >
    > * 대상 데이터베이스 엔드포인트(예: SQL 엔드포인트, Cosmos DB 엔드포인트 등)
    > * 스토리지 엔드포인트
    > * Service Bus 엔드포인트
    >
    > Azure Database Migration Service에는 인터넷 연결이 없으므로 이 구성이 필요합니다.

* 가상 네트워크에 대 한 NSG (네트워크 보안 그룹) 규칙이 Azure Database Migration Service에 대해 443, 53, 9354, 445, 12000 인바운드 통신 포트를 차단 하지 않는지 확인 합니다. Virtual network NSG 트래픽 필터링에 대 한 자세한 내용은 [네트워크 보안 그룹을 사용 하 여 네트워크 트래픽 필터링](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)문서를 참조 하세요.
* [데이터베이스 엔진 액세스를 위한 Windows 방화벽](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성합니다.
* Windows 방화벽을 열고 Azure Database Migration Service에서 기본적으로 5432 TCP 포트인 원본 PostgreSQL 서버에 액세스할 수 있도록 허용합니다.
* 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우, Azure Database Migration Service가 마이그레이션을 위해 원본 데이터베이스에 액세스할 수 있게 허용하는 방화벽 규칙을 추가해야 합니다.
* Azure Database Migration Service에서 대상 데이터베이스에 액세스할 수 있도록 Azure Database for PostgreSQL에 대한 서버 수준 [방화벽 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)을 만듭니다. Azure Database Migration Service에 사용 되는 가상 네트워크의 서브넷 범위를 제공 합니다.
* postgresql.config 파일의 논리적 복제를 활성화하고 다음 매개 변수를 설정합니다.

  * wal_level = **논리적**
  * max_replication_slots = [number of slots], **5개 슬롯**으로 설정하는 것이 좋습니다.
  * max_wal_senders =[동시 작업 수] - max_wal_senders 매개 변수는 실행할 수 있는 동시 작업 수를 설정합니다. **10작업**으로 설정하는 것이 좋습니다.

> [!IMPORTANT]
> 기존 데이터베이스의 모든 테이블에는 변경 내용을 대상 데이터베이스와 동기화 할 수 있도록 기본 키가 필요 합니다.

## <a name="migrate-the-sample-schema"></a>샘플 스키마 마이그레이션

테이블 스키마, 인덱스 및 저장 프로시저와 같은 모든 데이터베이스 개체를 완료하려면 원본 데이터베이스에서 스키마를 추출하고 데이터베이스에 적용해야 합니다.

1. pg_dump -s 명령을 사용하여 데이터베이스에 대한 스키마 덤프 파일을 만듭니다.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    예를 들어 **dvdrental** 데이터베이스에 대 한 스키마 덤프 파일을 만들려면 다음을 수행 합니다.

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    pg_dump 유틸리티를 사용하는 방법에 대한 자세한 내용은 [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES) 자습서의 예제를 참조하세요.

2. Azure Database for PostgreSQL인 대상 환경에서 빈 데이터베이스를 만듭니다.

    데이터베이스를 연결 하 고 만드는 방법에 대 한 자세한 내용은 [Azure Portal에서 Azure Database for PostgreSQL 서버 만들기](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) 또는 [Azure Portal에서 Azure Database for PostgreSQL-Hyperscale (Citus) 서버 만들기](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)문서를 참조 하세요.

    > [!NOTE]
    > Citus (Azure Database for PostgreSQL-Hyperscale)의 인스턴스에는 **Citus**데이터베이스가 하나만 있습니다.

3. 스키마 덤프 파일을 복원하여 만든 대상 데이터베이스에 스키마를 가져옵니다.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    예를 들면 다음과 같습니다.

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Drop foreign key 스크립트를 추출 하 여 대상에 추가 하려면 (Azure Database for PostgreSQL) PgAdmin 또는 psql에서 다음 스크립트를 실행 합니다.

   > [!IMPORTANT]
   > 스키마의 외래 키로 인해 마이그레이션의 초기 로드 및 연속 동기화가 실패 합니다.

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ', foreignkey), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ', foreignkey, ' FOREIGN KEY (', column_name, ')', ' REFERENCES ', foreign_table_schema, '.', foreign_table_name, '(', foreign_column_name, ')' ), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name
        ) Q
        GROUP BY Q.table_schema, Q.table_name;
    ```

5. 쿼리 결과에서 외래 키 삭제(두 번째 열)를 실행합니다.

6. 대상 데이터베이스에서 트리거를 사용 하지 않도록 설정 하려면 아래 스크립트를 실행 합니다.

   > [!IMPORTANT]
   > 데이터의 트리거 (삽입 또는 업데이트)는 원본에서 복제 되는 데이터 앞에 대상의 데이터 무결성을 적용 합니다. 따라서 마이그레이션하는 동안 **대상의** 모든 테이블에서 트리거를 사용 하지 않도록 설정 하 고 마이그레이션이 완료 된 후에 트리거를 다시 사용 하도록 설정 하는 것이 좋습니다.

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration 리소스 공급자 등록

1. Azure Portal에 로그인하고, **모든 서비스**를 선택한 다음, **구독**을 선택합니다.

   ![포털 구독 표시](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Azure Database Migration Service의 인스턴스를 만들 구독을 선택한 다음, **리소스 공급자**를 선택합니다.

    ![리소스 공급자 보기](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. 마이그레이션을 검색 한 다음 **microsoft.datamigration**의 오른쪽에서 **등록**을 선택 합니다.

    ![리소스 공급자 등록](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>DMS 인스턴스 만들기

1. Azure Portal에서 **+ 리소스 만들기**를 선택하고, Azure Database Migration Service를 검색한 다음, 드롭다운 목록에서 **Azure Database Migration Service**를 선택합니다.

    ![Azure Marketplace](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. **Azure Database Migration Service** 화면에서 **만들기**를 선택합니다.

    ![Azure Database Migration Service 인스턴스 만들기](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. **마이그레이션 서비스 만들기** 화면에서 이름, 구독, 새 리소스 그룹 또는 기존 리소스 그룹을 지정 하 고 서비스의 위치를 지정 합니다.

4. 기존 가상 네트워크를 선택 하거나 새 가상 네트워크를 만드세요.

    가상 네트워크는 원본 PostgreSQL 서버 및 대상 Azure Database for PostgreSQL 인스턴스에 대 한 액세스 권한을 Azure Database Migration Service 제공 합니다.

    Azure Portal에서 가상 네트워크를 만드는 방법에 대 한 자세한 내용은 [Azure Portal를 사용 하 여 가상 네트워크 만들기](https://aka.ms/DMSVnet)문서를 참조 하세요.

5. 가격 책정 계층을 선택합니다.

    비용 및 가격 책정 계층에 대 한 자세한 내용은 [가격 책정 페이지](https://aka.ms/dms-pricing)를 참조 하세요.

    ![Azure Database Migration Service 인스턴스 설정 구성](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. **검토 + 만들기** 를 선택 하 여 서비스를 만듭니다.

   서비스 만들기는 약 10 ~ 15 분 이내에 완료 됩니다.

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기

서비스가 생성된 후 Azure Portal에서 서비스를 찾아 연 다음, 새로운 마이그레이션 프로젝트를 만듭니다.

1. Azure Portal에서 **모든 서비스**를 선택하고, Azure Database Migration Service를 검색하고 나서, **Azure Database Migration Services**를 선택합니다.

      ![Azure Database Migration Service의 모든 인스턴스 찾기](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. **Azure Database Migration Services** 화면에서 사용자가 만든 Azure Database Migration Service 인스턴스의 이름을 검색 하 고, 인스턴스를 선택한 다음, + **새 마이그레이션 프로젝트**를 선택 합니다.

3. **새 마이그레이션 프로젝트** 화면에서 프로젝트의 이름을 지정 하 고, **원본 서버 유형** 텍스트 상자에서 **PostgresSQL**를 선택 하 고, **대상 서버 유형** 텍스트 상자에서 **Azure Database for PostgreSQL**를 선택 합니다.

4. **작업 유형 선택** 섹션에서 **온라인 데이터 마이그레이션**을 선택 합니다.

    ![Azure Database Migration Service 프로젝트 만들기](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > 또는 **프로젝트만 만들기**를 선택하여 지금 마이그레이션 프로젝트를 만들고, 나중에 마이그레이션을 실행할 수도 있습니다.

5. **저장**을 선택 하 고 Azure Database Migration Service를 사용 하 여 데이터를 마이그레이션하기 위한 요구 사항을 확인 한 다음 **작업 만들기 및 실행**을 선택 합니다.

## <a name="specify-source-details"></a>원본 세부 정보 지정

1. **원본 세부 정보 추가** 화면에서 원본 PostgreSQL 인스턴스에 대 한 연결 세부 정보를 지정 합니다.

    ![원본 세부 정보 추가 화면](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. **저장**을 선택합니다.

## <a name="specify-target-details"></a>대상 세부 정보 지정

1. **대상 세부 정보** 화면에서 Citus (대상 hyperscale) 서버에 대 한 연결 세부 정보를 지정 합니다 .이 서버에는 pg_dump를 사용 하 여 **DVD 대 여** 스키마를 배포한 Citus (hyperscale)의 미리 프로 비전 된 인스턴스입니다.

    ![대상 세부 정보 화면](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. **저장**을 선택한 다음, **대상 데이터베이스에 매핑** 화면에서 마이그레이션하기 위해 원본 및 대상 데이터베이스를 매핑합니다.

    대상 데이터베이스의 이름이 원본 데이터베이스와 동일하면 Azure Database Migration Service는 기본적으로 이 대상 데이터베이스를 선택합니다.

    ![대상 데이터베이스에 매핑 화면](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. **저장**을 선택 하 고 **마이그레이션 설정** 화면에서 기본값을 적용 합니다.

    ![마이그레이션 설정 화면](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. **저장**을 선택하고, **마이그레이션 요약** 화면의 **작업 이름** 텍스트 상자에서 마이그레이션 작업의 이름을 지정한 다음, 요약을 검토하여 원본 및 대상 세부 정보가 이전에 지정한 내용과 일치하는지 확인합니다.

    ![마이그레이션 요약 화면](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>마이그레이션 실행

* **마이그레이션 실행**을 선택합니다.

    마이그레이션 작업 창이 표시 되 고 작업 **상태가** **백업 중**으로 표시 되도록 업데이트 되어야 합니다.

## <a name="monitor-the-migration"></a>마이그레이션 모니터링

1. 마이그레이션 작업 화면에서 **새로 고침**을 선택하여 마이그레이션 **상태**가 **완료**로 표시될 때까지 디스플레이를 업데이트합니다.

     ![마이그레이션 프로세스 모니터링](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. 마이그레이션이 완료 되 면 **데이터베이스 이름**에서 특정 데이터베이스를 선택 하 여 **전체 데이터 로드** 및 **증분 데이터 동기화** 작업에 대 한 마이그레이션 상태를 가져옵니다.

   > [!NOTE]
   > **전체 데이터 로드**는 초기 로드 마이그레이션 상태를 보여주고, **증분 데이터 동기화**는 CDC(변경 데이터 캡처) 상태를 보여줍니다.

     ![전체 데이터 로드 정보](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![증분 데이터 동기화 세부 정보](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>마이그레이션 중단 수행

초기 전체 로드가 완료되면 데이터베이스가 **중단 준비 완료**로 표시됩니다.

1. 데이터베이스 마이그레이션을 완료할 준비가 되면 **중단 시작**을 선택합니다.

2. **보류 중인 변경 내용** 카운터가 **0** 으로 표시 될 때까지 기다린 후 원본 데이터베이스에 들어오는 모든 트랜잭션이 중지 되었는지 확인 하 고 **확인** 확인란을 선택한 다음 **적용**을 선택 합니다.

    ![화면에서 전체를 건너뜁니다.](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. 데이터베이스 마이그레이션 상태가 **완료 됨**으로 표시 되 면 응용 프로그램을 Azure Database for PostgreSQL의 새 대상 인스턴스에 연결 합니다.

## <a name="next-steps"></a>다음 단계

* Azure Database for PostgreSQL로 온라인 마이그레이션을 수행하는 경우와 관련하여 알려진 문제 및 제한 사항에 대한 자세한 내용은 [Azure Database for PostgreSQL 온라인 마이그레이션의 알려진 문제 및 해결 방법](known-issues-azure-postgresql-online.md) 문서를 참조하세요.
* Azure Database Migration Service에 대한 자세한 내용은 [Azure Database Migration Service란?](https://docs.microsoft.com/azure/dms/dms-overview) 문서를 참조하세요.
* Azure Database for PostgreSQL에 대한 자세한 내용은 [Azure Database for PostgreSQL이란?](https://docs.microsoft.com/azure/postgresql/overview) 문서를 참조하세요.
