---
title: BCDR (비즈니스 연속성 및 재해 복구)를 위한 Azure SSIS 통합 런타임 구성
description: 이 문서에서는 BCDR (비즈니스 연속성 및 재해 복구)를 위해 Azure SQL Database/Managed Instance 장애 조치 (failover) 그룹을 사용 하 여 Azure Data Factory에서 Azure SSIS 통합 런타임을 구성 하는 방법을 설명 합니다.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/05/2021
ms.openlocfilehash: a426ee39ba3c0f50b9a6c1fb9c7de1ef8e7291b2
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566356"
---
# <a name="configure-azure-ssis-integration-runtime-for-business-continuity-and-disaster-recovery-bcdr"></a>BCDR (비즈니스 연속성 및 재해 복구)를 위한 Azure SSIS 통합 런타임 구성 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory (ADF)의 SSIS (Azure SQL Database/Managed Instance 및 SQL Server Integration Services)는 SQL Server 마이그레이션을 위해 권장 되는 모든 PaaS (Platform as a Service) 솔루션으로 결합 될 수 있습니다. Azure SQL Database/Managed Instance에 의해 호스트 되는 ssis 카탈로그 데이터베이스 (SSISDB)에 SSIS 프로젝트를 배포 하 고 ADF의 Azure SSIS IR (통합 런타임)에서 SSIS 패키지를 실행할 수 있습니다.

BCDR (비즈니스 연속성 및 재해 복구)의 경우 읽기/쓰기 액세스 권한이 있는 기본 Azure 지역의 SSISDB (주 역할)가 읽기 전용 액세스 (보조 역할)를 사용 하 여 보조 지역으로 계속 복제 되는 [지역 복제/장애 조치 (failover) 그룹](../azure-sql/database/auto-failover-group-overview.md)을 사용 하 여 Azure SQL Database/Managed Instance 구성할 수 있습니다. 주 지역에서 재해가 발생 하면 기본 및 보조 SSISDBs 역할을 교환 하는 장애 조치 (failover)가 트리거됩니다.

BCDR의 경우 Azure SQL Database/Managed Instance 장애 조치 (failover) 그룹과 동기화 된 상태에서 작동 하는 이중 대기 Azure SSIS IR 쌍을 구성할 수도 있습니다. 이렇게 하면 지정 된 시간에 하나의 Azure-SSIS IRs를 실행 하는 한 쌍을 가질 수 있습니다. 단, 패키지 실행 로그 (주 역할)는 물론 패키지 실행 로그 (주 역할)는 물론 패키지 실행 로그 (주 역할)의 경우와 같은 다른 Azure Files 위치에 배포 된 패키지에 대해서만 동일한 작업을 수행할 수 있습니다. SSISDB 장애 조치 (failover)가 발생 하면 기본 및 보조 Azure SSIS IRs 역할을 교환 하 고 둘 다 실행 중인 경우 가동 중지 시간이 거의 발생 하지 않습니다.

이 문서에서는 BCDR에 대해 Azure SQL Database/Managed Instance 장애 조치 그룹을 사용 하 여 Azure-SSIS IR를 구성 하는 방법을 설명 합니다.

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-database-failover-group"></a>Azure SQL Database 장애 조치 (failover) 그룹을 사용 하 여 이중 대기 Azure-SSIS IR 쌍 구성

Azure SQL Database 장애 조치 (failover) 그룹과 동기화 된 상태에서 작동 하는 이중 대기 Azure-SSIS IR 쌍을 구성 하려면 다음 단계를 완료 합니다.

1. Azure Portal/ADF UI를 사용 하 여 주 지역에서 SSISDB를 호스트 하기 위해 주 Azure SQL Database 서버와 함께 새 Azure-SSIS IR을 만들 수 있습니다. 기본 Azure SQL Database 서버에서 호스트 하는 SSIDB에 이미 연결 되어 있는 기존 Azure-SSIS IR가 있고 계속 실행 중인 경우 다시 구성 하려면 먼저 중지 해야 합니다. 이는 기본 Azure-SSIS IR입니다.

   **Integration runtime 설정** 창의 **배포 설정** 페이지에서 [ssisdb를 사용 하도록 선택](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb) 하는 경우 **ssisdb 장애 조치 (failover)와 함께 이중 대기 Azure-SSIS Integration Runtime 쌍 사용** 확인란을 선택 합니다. **이중 대기 쌍 이름** 에 기본 및 보조 AZURE-SSIS IRs의 쌍을 식별 하는 이름을 입력 합니다. 기본 Azure-SSIS IR 만들기를 완료 하면 읽기/쓰기 액세스를 사용 하 여 사용자를 대신 하 여 생성 되는 기본 SSISDB가 시작 되 고 연결 됩니다. 방금 다시 구성한 경우 다시 시작 해야 합니다.

1. Azure Portal를 사용 하 여 주 SSISDB가 주 Azure SQL Database 서버의 **개요** 페이지에 만들어졌는지 여부를 확인할 수 있습니다. 만들어진 후에는 [기본 및 보조 Azure SQL Database 서버에 대 한 장애 조치 (failover) 그룹을 만들고](../azure-sql/database/failover-group-add-single-database-tutorial.md?tabs=azure-portal#2---create-the-failover-group) **장애 조치 (failover) 그룹** 페이지에서 SSISDB를 추가할 수 있습니다. 장애 조치 (failover) 그룹을 만든 후에는 보조 Azure SQL Database 서버의 **개요** 페이지에서 읽기 전용 액세스 권한으로 주 SSISDB가 보조 데이터베이스에 복제 되었는지 여부를 확인할 수 있습니다.

1. Azure Portal/ADF UI를 사용 하 여 보조 지역에서 SSISDB를 호스트 하는 보조 Azure SQL Database 서버를 사용 하 여 다른 Azure-SSIS IR을 만들 수 있습니다. 이는 보조 Azure-SSIS IR입니다. 전체 BCDR에 종속 된 모든 리소스 (예: 사용자 지정 설치 스크립트/파일을 저장 하기 위한 Azure Storage, 오케스트레이션/예약 패키지 실행을 위한 ADF 등)가 보조 지역에도 생성 되었는지 확인 합니다.

   **Integration runtime 설정** 창의 **배포 설정** 페이지에서 [ssisdb를 사용 하도록 선택](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb) 하는 경우 **ssisdb 장애 조치 (failover)와 함께 이중 대기 Azure-SSIS Integration Runtime 쌍 사용** 확인란을 선택 합니다. **이중 대기 쌍 이름** 에 대해 동일한 이름을 입력 하 여 기본 및 보조 AZURE-SSIS IRs 쌍을 식별 합니다. 보조 Azure-SSIS IR 만들기를 완료 하면 시작 되 고 보조 SSISDB에 연결 됩니다.

1. SSISDB 장애 조치 (failover)가 발생할 때 가동 중지 시간이 거의 발생 하지 않도록 하려면 두 Azure SSIS IRs를 실행 합니다. 주 Azure-SSIS IR만 기본 SSISDB에 액세스 하 여 패키지 실행 로그를 가져오고 실행할 수 있을 뿐만 아니라 패키지 실행 로그를 기록 하 고 실행할 수 있으며, 보조 Azure-SSIS IR는 Azure Files와 같은 다른 위치에 배포 된 패키지에 대해서만 동일한 작업을 수행할 수 있습니다.

   실행 비용을 최소화 하려면 보조 Azure-SSIS IR를 만든 후 중지할 수 있습니다. SSISDB 장애 조치 (failover)가 발생 하면 기본 및 보조 Azure SSIS IRs에서 역할을 교환 합니다. 기본 Azure-SSIS IR 중지 된 경우 다시 시작 해야 합니다. 사용 되는 가상 네트워크 및 삽입 방법에 삽입 되었는지 여부에 따라 실행 하는 데 5 분 또는 약 20-30 분이 소요 됩니다.

1. [오케스트레이션/일정 예약 실행을 위해 ADF를 사용](./how-to-invoke-ssis-package-ssis-activity.md)하는 경우 SSIS 패키지 작업 및 연결 된 트리거를 실행 하는 모든 관련 ADF 파이프라인이 처음에는 트리거를 사용 하지 않도록 설정 하 여 보조 ADF로 복사 되었는지 확인 합니다. SSISDB 장애 조치 (failover)가 발생 하는 경우이를 사용 하도록 설정 해야 합니다.

1. [Azure SQL Database 장애 조치 (failover) 그룹을 테스트](../azure-sql/database/failover-group-add-single-database-tutorial.md?tabs=azure-portal#3---test-failover) 하 고 [ADF 포털에서 Azure-SSIS IR 모니터링 페이지](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) 를 선택 하 여 기본 및 보조 Azure SSIS IRs에서 역할을 교환 했는지 여부를 확인할 수 있습니다. 

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-managed-instance-failover-group"></a>Azure SQL Managed Instance 장애 조치 (failover) 그룹을 사용 하 여 이중 대기 Azure-SSIS IR 쌍 구성

Azure SQL Managed Instance 장애 조치 (failover) 그룹과 동기화 할 수 있는 이중 대기 Azure-SSIS IR 쌍을 구성 하려면 다음 단계를 완료 합니다.

1. Azure Portal를 사용 하 여 기본 Azure SQL Managed Instance의 **장애 조치 (Failover) 그룹** 페이지에서 [기본 및 보조 Azure sql 관리 되는 인스턴스에 대해 장애 조치 (failover) 그룹을 만들](../azure-sql/managed-instance/failover-group-add-instance-tutorial.md?tabs=azure-portal) 수 있습니다.

1. Azure Portal/ADF UI를 사용 하 여 기본 지역에서 SSISDB를 호스트 하는 기본 Azure SQL Managed Instance를 사용 하 여 새 Azure-SSIS IR을 만들 수 있습니다. 기본 Azure SQL Managed Instance에서 호스트 하는 SSIDB에 이미 연결 되어 있는 기존 Azure-SSIS IR 이미 실행 중인 경우 다시 구성 하려면 먼저 중지 해야 합니다. 이는 기본 Azure-SSIS IR입니다.

   **Integration runtime 설정** 창의 **배포 설정** 페이지에서 [ssisdb를 사용 하도록 선택](./create-azure-ssis-integration-runtime.md#creating-ssisdb) 하는 경우 **ssisdb 장애 조치 (failover)와 함께 이중 대기 Azure-SSIS Integration Runtime 쌍 사용** 확인란을 선택 합니다. **이중 대기 쌍 이름** 에 기본 및 보조 AZURE-SSIS IRs의 쌍을 식별 하는 이름을 입력 합니다. 기본 Azure-SSIS IR 만들기를 완료 하면 읽기/쓰기 액세스를 사용 하 여 사용자를 대신 하 여 생성 되는 기본 SSISDB가 시작 되 고 연결 됩니다. 방금 다시 구성한 경우 다시 시작 해야 합니다. 보조 Azure SQL Managed Instance의 **개요** 페이지에 있는 읽기 전용 액세스 권한으로 주 SSISDB가 보조 데이터베이스에 복제 되었는지 여부를 확인할 수도 있습니다.

1. Azure Portal/ADF UI를 사용 하 여 보조 지역에서 SSISDB를 호스트 하는 보조 Azure SQL Managed Instance를 사용 하 여 다른 Azure-SSIS IR을 만들 수 있습니다. 이는 보조 Azure-SSIS IR입니다. 전체 BCDR에 종속 된 모든 리소스 (예: 사용자 지정 설치 스크립트/파일을 저장 하기 위한 Azure Storage, 오케스트레이션/예약 패키지 실행을 위한 ADF 등)가 보조 지역에도 생성 되었는지 확인 합니다.

   **Integration runtime 설정** 창의 **배포 설정** 페이지에서 [ssisdb를 사용 하도록 선택](./create-azure-ssis-integration-runtime.md#creating-ssisdb) 하는 경우 **ssisdb 장애 조치 (failover)와 함께 이중 대기 Azure-SSIS Integration Runtime 쌍 사용** 확인란을 선택 합니다. **이중 대기 쌍 이름** 에 대해 동일한 이름을 입력 하 여 기본 및 보조 AZURE-SSIS IRs 쌍을 식별 합니다. 보조 Azure-SSIS IR 만들기를 완료 하면 시작 되 고 보조 SSISDB에 연결 됩니다.

1. Azure SQL Managed Instance는 DMK (데이터베이스 마스터 키)를 사용 하 여 데이터를 암호화 하 여 SSISDB와 같은 데이터베이스의 중요 한 데이터를 보호할 수 있습니다. DMK 자체는 기본적으로 SMK (서비스 마스터 키)를 사용 하 여 암호화 됩니다. 작성 시점에 Azure SQL Managed Instance 장애 조치 (failover) 그룹은 기본 Azure SQL Managed Instance에서 SMK을 복제 하지 않으므로 장애 조치 (failover)가 발생 한 후에는 보조 Azure SQL Managed Instance에서 SSISDB를 암호 해독할 수 없습니다. 이 문제를 해결 하려면 DMK에 대 한 암호 암호화를 추가 하 여 보조 Azure SQL Managed Instance에서 암호를 해독할 수 있습니다. SSMS를 사용 하 여 다음 단계를 완료 합니다.

   1. DMK 암호화를 위한 암호를 추가 하려면 기본 Azure SQL Managed Instance SSISDB에 대해 다음 명령을 실행 합니다.

      ```sql
      ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'YourPassword'
      ```
   
   1. 기본 및 보조 Azure SQL 관리 되는 인스턴스의 SSISDB에 대해 다음 명령을 실행 하 여 DMK 암호를 해독 하기 위한 새 암호를 추가 합니다.

      ```sql
      EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB', @password = N'YourPassword', @action = N'add'
      ```

1. SSISDB 장애 조치 (failover)가 발생할 때 가동 중지 시간이 거의 발생 하지 않도록 하려면 두 Azure SSIS IRs를 실행 합니다. 주 Azure-SSIS IR만 기본 SSISDB에 액세스 하 여 패키지 실행 로그를 가져오고 실행할 수 있을 뿐만 아니라 패키지 실행 로그를 기록 하 고 실행할 수 있으며, 보조 Azure-SSIS IR는 Azure Files와 같은 다른 위치에 배포 된 패키지에 대해서만 동일한 작업을 수행할 수 있습니다.

   실행 비용을 최소화 하려면 보조 Azure-SSIS IR를 만든 후 중지할 수 있습니다. SSISDB 장애 조치 (failover)가 발생 하면 기본 및 보조 Azure SSIS IRs에서 역할을 교환 합니다. 기본 Azure-SSIS IR 중지 된 경우 다시 시작 해야 합니다. 사용 되는 가상 네트워크 및 삽입 방법에 삽입 되었는지 여부에 따라 실행 하는 데 5 분 또는 약 20-30 분이 소요 됩니다.

1. [오케스트레이션/일정 예약 실행을 위해 AZURE SQL Managed Instance 에이전트를 사용](./how-to-invoke-ssis-package-managed-instance-agent.md)하는 경우 처음에는 일정을 사용 하지 않도록 설정 하 여 해당 작업 단계 및 관련 일정이 포함 된 모든 관련 SSIS 작업이 보조 Azure SQL Managed Instance에 복사 되었는지 확인 합니다. SSMS를 사용 하 여 다음 단계를 완료 합니다.

   1. 각 SSIS 작업에 대해 마우스 오른쪽 단추를 클릭 하 고 **작업 스크립팅**, **만들기** 및 **새 쿼리 편집기 창** 드롭다운 메뉴 항목을 선택 하 여 해당 스크립트를 생성 합니다.

      ![SSIS 작업 스크립트 생성](media/configure-bcdr-azure-ssis-integration-runtime/generate-ssis-job-script.png)

   1. 생성 된 각 SSIS 작업 스크립트에 대해 저장 프로시저를 실행 하 `sp_add_job` 고 필요에 따라 인수에 대 한 값 할당을 수정/제거 하는 명령을 찾습니다 `@owner_login_name` .

   1. 업데이트 된 각 SSIS 작업 스크립트에 대해 보조 Azure SQL Managed Instance에서 실행 하 여 작업 단계 및 관련 일정으로 작업을 복사 합니다.

   1. 다음 스크립트를 사용 하 여 기본/보조 Azure SQL 관리 되는 인스턴스 모두에서 기본/보조 SSISDB 역할을 기반으로 SSIS 작업 일정을 설정/해제 하 고 정기적으로 실행 하는 새 T-sql 작업을 만듭니다. SSISDB 장애 조치 (failover)가 발생 하면 사용 하지 않도록 설정 된 SSIS 작업 일정이 활성화 되 고 그 반대의 경우도 마찬가지입니다.

      ```sql
      IF (SELECT Top 1 role_desc FROM SSISDB.sys.dm_geo_replication_link_status WHERE partner_database = 'SSISDB') = 'PRIMARY'
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 0
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 1
         END
      ELSE
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 1
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 0
         END
      ```

1. [오케스트레이션/일정 예약 실행을 위해 ADF를 사용](./how-to-invoke-ssis-package-ssis-activity.md)하는 경우 SSIS 패키지 작업 및 연결 된 트리거를 실행 하는 모든 관련 ADF 파이프라인이 처음에는 트리거를 사용 하지 않도록 설정 하 여 보조 ADF로 복사 되었는지 확인 합니다. SSISDB 장애 조치 (failover)가 발생 하는 경우이를 사용 하도록 설정 해야 합니다.

1. [AZURE SQL Managed Instance 장애 조치 (failover) 그룹을 테스트](../azure-sql/managed-instance/failover-group-add-instance-tutorial.md?tabs=azure-portal#test-failover) 하 고 [ADF 포털에서 Azure-SSIS IR 모니터링 페이지](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) 를 선택 하 여 기본 및 보조 Azure SSIS IRs에서 역할을 교환 했는지 여부를 확인할 수 있습니다. 

## <a name="attach-a-new-azure-ssis-ir-to-existing-ssisdb-hosted-by-azure-sql-databasemanaged-instance"></a>Azure SQL Database/Managed Instance에서 호스트 하는 기존 SSISDB에 새 Azure-SSIS IR을 연결 합니다.

재해가 발생 하 고 기존 Azure-SSIS IR에 영향을 주지만 동일한 지역에 Azure SQL Database/Managed Instance 되지 않은 경우 다른 지역의 새 항목으로 바꿀 수 있습니다. Azure SQL Database/Managed Instance에서 호스트 하는 기존 SSISDB를 새 Azure-SSIS IR에 연결 하려면 다음 단계를 완료 합니다.

1. 기존 Azure-SSIS IR 아직 실행 중인 경우 Azure Portal/ADF UI 또는 Azure PowerShell를 사용 하 여 먼저 중지 해야 합니다. 재해가 동일한 지역의 ADF에도 영향을 주는 경우이 단계를 건너뛸 수 있습니다.

1. SSMS를 사용 하 여 Azure SQL Database/Managed Instance의 SSISDB에 대해 다음 명령을 실행 하 여 새 ADF/Azure-SSIS IR의 연결을 허용 하는 메타 데이터를 업데이트 합니다.

   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name = 'YourNewADF', @integration_runtime_name = 'YourNewAzureSSISIR'
   ```

1. [Azure Portal/adf UI](./create-azure-ssis-integration-runtime.md#use-the-azure-portal-to-create-an-integration-runtime) 또는 [Azure PowerShell](./create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime)를 사용 하 여 다른 지역에서 각각 새 adf/ *Azure-SSIS IR 라는 새 adf/* 를 만듭니다 / . Azure Portal/ADF UI를 사용 하는 경우 **Integration runtime 설정** 창의 **배포 설정** 페이지에서 연결 테스트 오류를 무시할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure-SSIS IR에 대해 다음과 같은 기타 구성 옵션을 고려할 수 있습니다.

- [Azure-SSIS IR에 대 한 패키지 저장소 구성](./create-azure-ssis-integration-runtime.md#creating-azure-ssis-ir-package-stores)

- [Azure-SSIS IR에 대 한 사용자 지정 설정 구성](./how-to-configure-azure-ssis-ir-custom-setup.md)

- [Azure-SSIS IR에 대 한 가상 네트워크 주입 구성](./join-azure-ssis-integration-runtime-virtual-network.md)

- [자체 호스팅 IR을 Azure-SSIS IR 프록시로 구성](./self-hosted-integration-runtime-proxy-ssis.md)