---
title: '자습서: Azure 포털을 통해 PostgreSQL을 위해 PostgreSQL을 Azure DB로 온라인으로 마이그레이션합니다.'
titleSuffix: Azure Database Migration Service
description: Azure 포털을 통해 Azure 데이터베이스 마이그레이션 서비스를 사용하여 PostgreSQL 온-프레미스에서 PostgreSQL용 Azure 데이터베이스로의 온라인 마이그레이션을 수행하는 방법을 알아봅니다.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: 4985c492c8ca71da87cf1a519ebc658c203d3952
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246979"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>자습서: Azure 포털을 통해 DMS를 사용 하 여 온라인 PostgreSQL에 대 한 Azure DB에 PostgreSQL 마이그레이션

Azure 데이터베이스 마이그레이션 서비스를 사용하여 응용 프로그램에 대한 가동 중지 시간을 최소화하면서 온-프레미스 PostgreSQL 인스턴스에서 [PostgreSQL용 Azure 데이터베이스로](https://docs.microsoft.com/azure/postgresql/) 데이터베이스를 마이그레이션할 수 있습니다. 이 자습서에서는 Azure Database Migration Service에서 온라인 마이그레이션 작업을 사용하여 **DVD 대여** 샘플 데이터베이스를 PostgreSQL 9.6의 온-프레미스 인스턴스에서 Azure Database for PostgreSQL로 마이그레이션합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
>
> * pg_dump 유틸리티를 사용하여 샘플 스키마를 마이그레이션합니다.
> * Azure Database Migration Service 인스턴스를 만듭니다.
> * Azure 데이터베이스 마이그레이션 서비스에서 마이그레이션 프로젝트를 만듭니다.
> * 마이그레이션을 실행합니다.
> * 마이그레이션을 모니터링합니다.
> * 마이그레이션 컷오버를 수행합니다.

> [!NOTE]
> Azure Database Migration Service를 사용하여 온라인 마이그레이션을 수행하려면 프리미엄 가격 책정 계층에 따라 인스턴스를 만들어야 합니다. 마이그레이션 과정에서 데이터 도난을 방지하기 위해 디스크를 암호화합니다.

> [!IMPORTANT]
> 최적의 마이그레이션 환경을 위해 Microsoft는 대상 데이터베이스와 동일한 Azure 지역에서 Azure Database Migration Service의 인스턴스를 만드는 것을 권장합니다. 영역 또는 지역 간에 데이터를 이동하면 마이그레이션 프로세스 속도가 저하되고 오류가 발생할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

* [PostgreSQL 커뮤니티 에디션](https://www.postgresql.org/download/) 9.4, 9.5, 9.6 또는 10을 다운로드하여 설치합니다. 소스 PostgreSQL 서버 버전은 9.4, 9.5, 9.6, 10 또는 11이어야 합니다. 자세한 내용은 [지원되는 PostgreSQL 데이터베이스 버전](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) 문서를 참조하세요.

    또한 온-프레미스 PostgreSQL 버전은 Azure Database for PostgreSQL 버전과 일치해야 합니다. 예를 들어 PostgreSQL 9.6은 PostgreSQL 9.6, 10 또는 11에 대해서만 Azure 데이터베이스로 마이그레이션할 수 있지만 PostgreSQL 9.5의 경우 Azure 데이터베이스로만 마이그레이션할 수 없습니다.

* [PostgreSQL 서버에 대한 Azure 데이터베이스를 만들거나](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) [PostgreSQL - 하이퍼스케일(Citus) 서버에 대한 Azure 데이터베이스 만들기.](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)
* [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 또는 [VPN을](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)사용하여 온-프레미스 소스 서버에 대한 사이트 간 연결을 제공하는 Azure 리소스 관리자 배포 모델을 사용하여 Azure 데이터베이스 마이그레이션 서비스에 대한 Microsoft Azure 가상 네트워크를 만듭니다. 가상 네트워크 만들기에 대한 자세한 내용은 [가상 네트워크 설명서](https://docs.microsoft.com/azure/virtual-network/)및 특히 단계별 세부 정보가 있는 빠른 시작 문서를 참조하십시오.

    > [!NOTE]
    > 가상 네트워크 설정 중에 Microsoft에 피어링하는 네트워크와 함께 ExpressRoute를 사용하는 경우 서비스를 프로비전할 서브넷에 다음 서비스 [끝점을](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) 추가합니다.
    >
    > * 대상 데이터베이스 엔드포인트(예: SQL 엔드포인트, Cosmos DB 엔드포인트 등)
    > * 스토리지 엔드포인트
    > * Service Bus 엔드포인트
    >
    > Azure Database Migration Service에는 인터넷 연결이 없으므로 이 구성이 필요합니다.

* 가상 네트워크에 대한 NSG(네트워크 보안 그룹) 규칙이 Azure 데이터베이스 마이그레이션 서비스(443, 53, 9354, 445, 12000)에 대한 다음 인바운드 통신 포트를 차단하지 않도록 합니다. 가상 네트워크 NSG 트래픽 필터링에 대한 자세한 내용은 [네트워크 보안 그룹과 네트워크 트래픽 필터링](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)문서를 참조하십시오.
* [데이터베이스 엔진 액세스를 위한 Windows 방화벽](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성합니다.
* Windows 방화벽을 열고 Azure Database Migration Service에서 기본적으로 5432 TCP 포트인 원본 PostgreSQL 서버에 액세스할 수 있도록 허용합니다.
* 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우, Azure Database Migration Service가 마이그레이션을 위해 원본 데이터베이스에 액세스할 수 있게 허용하는 방화벽 규칙을 추가해야 합니다.
* Azure Database Migration Service에서 대상 데이터베이스에 액세스할 수 있도록 Azure Database for PostgreSQL에 대한 서버 수준 [방화벽 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)을 만듭니다. Azure 데이터베이스 마이그레이션 서비스에 사용되는 가상 네트워크의 서브넷 범위를 제공합니다.
* postgresql.config 파일의 논리적 복제를 활성화하고 다음 매개 변수를 설정합니다.

  * wal_level = **논리적**
  * max_replication_slots = [number of slots], **5개 슬롯**으로 설정하는 것이 좋습니다.
  * max_wal_senders =[동시 작업 수] - max_wal_senders 매개 변수는 실행할 수 있는 동시 작업 수를 설정합니다. **10작업**으로 설정하는 것이 좋습니다.

> [!IMPORTANT]
> 기존 데이터베이스의 모든 테이블에는 변경 내용을 대상 데이터베이스에 동기화할 수 있도록 기본 키가 필요합니다.

## <a name="migrate-the-sample-schema"></a>샘플 스키마 마이그레이션

테이블 스키마, 인덱스 및 저장 프로시저와 같은 모든 데이터베이스 개체를 완료하려면 원본 데이터베이스에서 스키마를 추출하고 데이터베이스에 적용해야 합니다.

1. pg_dump -s 명령을 사용하여 데이터베이스에 대한 스키마 덤프 파일을 만듭니다.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    예를 들어 **dvdrental** 데이터베이스에 대한 스키마 덤프 파일을 만들려면 다음을 수행합니다.

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    pg_dump 유틸리티를 사용하는 방법에 대한 자세한 내용은 [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES) 자습서의 예제를 참조하세요.

2. Azure Database for PostgreSQL인 대상 환경에서 빈 데이터베이스를 만듭니다.

    데이터베이스를 연결하고 만드는 방법에 대한 자세한 내용은 Azure [포털에서 PostgreSQL 서버를 위한 Azure 데이터베이스 만들기](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) 또는 Azure [포털에서 PostgreSQL - 하이퍼스케일(Citus) 서버를 위한 Azure 데이터베이스 만들기](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)문서를 참조하십시오.

    > [!NOTE]
    > PostgreSQL에 대한 Azure 데이터베이스 의 인스턴스 - 하이퍼스케일(Citus)에는 하나의 데이터베이스인 **citus**.

3. 스키마 덤프 파일을 복원하여 만든 대상 데이터베이스에 스키마를 가져옵니다.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    예를 들어:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

4. 드롭 외래 키 스크립트를 추출하 고 대상에 추가 하려면 (PostgreSQL에 대 한 Azure 데이터베이스), PgAdmin 또는 psql에서 다음 스크립트를 실행 합니다.

   > [!IMPORTANT]
   > 스키마의 외래 키로 인해 마이그레이션의 초기 로드 및 연속 동기화가 실패합니다.

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

6. 대상 데이터베이스에서 트리거를 사용하지 않도록 설정하려면 아래 스크립트를 실행합니다.

   > [!IMPORTANT]
   > 원본에서 복제되는 데이터보다 먼저 대상에 데이터 무결성을 적용하여 데이터의 트리거(삽입 또는 업데이트)를 적용합니다. 따라서 마이그레이션 하는 동안 대상에 있는 모든 테이블에서 **트리거를** 사용 하지 않도록 설정 하 고 마이그레이션이 완료 된 후 트리거를 다시 활성화 하는 것이 좋습니다.

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration 리소스 공급자 등록

1. Azure Portal에 로그인하고, **모든 서비스**를 선택한 다음, **구독**을 선택합니다.

   ![포털 구독 표시](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Azure Database Migration Service의 인스턴스를 만들 구독을 선택한 다음, **리소스 공급자**를 선택합니다.

    ![리소스 공급자 보기](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. 마이그레이션을 검색한 다음 **Microsoft.DataMigration**의 오른쪽에서 **레지스터를**선택합니다.

    ![리소스 공급자 등록](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>DMS 인스턴스 만들기

1. Azure Portal에서 **+ 리소스 만들기**를 선택하고, Azure Database Migration Service를 검색한 다음, 드롭다운 목록에서 **Azure Database Migration Service**를 선택합니다.

    ![Azure Marketplace](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. **Azure Database Migration Service** 화면에서 **만들기**를 선택합니다.

    ![Azure Database Migration Service 인스턴스 만들기](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. 마이그레이션 **서비스 만들기** 화면에서 이름, 구독, 새 리소스 그룹 및 서비스의 위치를 지정합니다.

4. 기존 가상 네트워크를 선택하거나 새 가상 네트워크를 만듭니다.

    가상 네트워크는 Azure 데이터베이스 마이그레이션 서비스에 소스 PostgreSQL 서버 및 PostgreSQL 인스턴스에 대한 대상 Azure 데이터베이스에 대한 액세스 권한을 제공합니다.

    Azure 포털에서 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Azure 포털을 사용하여 가상 네트워크 만들기](https://aka.ms/DMSVnet)문서를 참조하십시오.

5. 가격 책정 계층을 선택합니다.

    비용 및 가격 책정 계층에 대한 자세한 내용은 [가격 책정 페이지를](https://aka.ms/dms-pricing)참조하십시오.

    ![Azure Database Migration Service 인스턴스 설정 구성](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. 검토를 선택 **+ 만들기를** 클릭하여 서비스를 만듭니다.

   서비스 생성은 약 10~15분 이내에 완료됩니다.

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기

서비스가 생성된 후 Azure Portal에서 서비스를 찾아 연 다음, 새로운 마이그레이션 프로젝트를 만듭니다.

1. Azure Portal에서 **모든 서비스**를 선택하고, Azure Database Migration Service를 검색하고 나서, **Azure Database Migration Services**를 선택합니다.

      ![Azure Database Migration Service의 모든 인스턴스 찾기](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. Azure **데이터베이스 마이그레이션 서비스** 화면에서 만든 Azure Database 마이그레이션 서비스 인스턴스의 이름을 검색한 다음 인스턴스를 선택한 다음 + **새 마이그레이션 프로젝트를**선택합니다.

3. 새 **마이그레이션 프로젝트** 화면에서 프로젝트 이름을 지정하고 소스 **서버 유형** 텍스트 상자에서 **PostgresSQL을**선택합니다. **Target server type** **Azure Database for PostgreSQL**

4. 활동 **유형 선택** 섹션에서 **온라인 데이터 마이그레이션을**선택합니다.

    ![Azure 데이터베이스 마이그레이션 서비스 프로젝트 만들기](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > 또는 **프로젝트만 만들기**를 선택하여 지금 마이그레이션 프로젝트를 만들고, 나중에 마이그레이션을 실행할 수도 있습니다.

5. **저장을**선택합니다. Azure 데이터베이스 마이그레이션 서비스를 성공적으로 사용하여 데이터를 마이그레이션한 다음 **활동 만들기 및 실행을**선택합니다.

## <a name="specify-source-details"></a>원본 세부 정보 지정

1. 소스 **세부 정보 추가** 화면에서 소스 PostgreSQL 인스턴스에 대한 연결 세부 정보를 지정합니다.

    ![원본 세부 정보 추가 화면](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. **저장**을 선택합니다.

## <a name="specify-target-details"></a>대상 세부 정보 지정

1. 대상 **세부 정보** 화면에서 pg_dump 사용하여 DVD 대여 스키마를 배포한 하이퍼스케일(Citus)의 미리 프로비저닝된 인스턴스인 대상 하이퍼스케일(Citus) 서버에 대한 연결 세부 정보를 **지정합니다.**

    ![대상 세부 정보 화면](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. **저장**을 선택한 다음, **대상 데이터베이스에 매핑** 화면에서 마이그레이션하기 위해 원본 및 대상 데이터베이스를 매핑합니다.

    대상 데이터베이스의 이름이 원본 데이터베이스와 동일하면 Azure Database Migration Service는 기본적으로 이 대상 데이터베이스를 선택합니다.

    ![대상 데이터베이스 화면에 매핑](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. **저장을**선택한 다음 **마이그레이션 설정** 화면에서 기본값을 수락합니다.

    ![마이그레이션 설정 화면](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. **저장**을 선택하고, **마이그레이션 요약** 화면의 **작업 이름** 텍스트 상자에서 마이그레이션 작업의 이름을 지정한 다음, 요약을 검토하여 원본 및 대상 세부 정보가 이전에 지정한 내용과 일치하는지 확인합니다.

    ![마이그레이션 요약 화면](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>마이그레이션 실행

* **마이그레이션 실행**을 선택합니다.

    마이그레이션 활동 창이 나타나고 활동 **상태가** 업데이트되어 **진행 중인 백업으로**표시되어야 합니다.

## <a name="monitor-the-migration"></a>마이그레이션 모니터링

1. 마이그레이션 작업 화면에서 **새로 고침**을 선택하여 마이그레이션 **상태**가 **완료**로 표시될 때까지 디스플레이를 업데이트합니다.

     ![마이그레이션 프로세스 모니터링](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. 마이그레이션이 완료되면 데이터베이스 **이름**에서 특정 데이터베이스를 선택하여 **전체 데이터 로드** 및 **증분 데이터 동기화** 작업에 대한 마이그레이션 상태로 이동합니다.

   > [!NOTE]
   > **전체 데이터 로드**는 초기 로드 마이그레이션 상태를 보여주고, **증분 데이터 동기화**는 CDC(변경 데이터 캡처) 상태를 보여줍니다.

     ![전체 데이터 로드 세부 정보](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![증분 데이터 동기화 세부 정보](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>마이그레이션 중단 수행

초기 전체 로드가 완료되면 데이터베이스가 **중단 준비 완료**로 표시됩니다.

1. 데이터베이스 마이그레이션을 완료할 준비가 되면 **중단 시작**을 선택합니다.

2. **보류 중인 변경 내용** 카운터가 **0으로** 표시될 때까지 기다렸다가 원본 데이터베이스에 들어오는 모든 트랜잭션이 중지되었는지 확인한 다음 **적용을** 선택합니다. **Apply**

    ![전체 컷오버 화면](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. 데이터베이스 마이그레이션 상태가 **완료됨을**표시하면 PostgreSQL용 Azure 데이터베이스의 새 대상 인스턴스에 응용 프로그램을 연결합니다.

## <a name="next-steps"></a>다음 단계

* Azure Database for PostgreSQL로 온라인 마이그레이션을 수행하는 경우와 관련하여 알려진 문제 및 제한 사항에 대한 자세한 내용은 [Azure Database for PostgreSQL 온라인 마이그레이션의 알려진 문제 및 해결 방법](known-issues-azure-postgresql-online.md) 문서를 참조하세요.
* Azure Database Migration Service에 대한 자세한 내용은 [Azure Database Migration Service란?](https://docs.microsoft.com/azure/dms/dms-overview) 문서를 참조하세요.
* Azure Database for PostgreSQL에 대한 자세한 내용은 [Azure Database for PostgreSQL이란?](https://docs.microsoft.com/azure/postgresql/overview) 문서를 참조하세요.
