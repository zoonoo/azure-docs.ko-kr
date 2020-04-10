---
title: Azure 데이터 팩터리에서 온-프레미스 SSIS 워크로드를 SSIS로 마이그레이션
description: 온-프레미스 SSIS 워크로드를 ADF의 SSIS로 마이그레이션합니다.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: 6ad5bb26959916f60973a8c0274e17eee03aa7a1
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991469"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>온-프레미스 SSIS 워크로드를 ADF의 SSIS로 마이그레이션

## <a name="overview"></a>개요

온프레미스 SQL Server에서 Azure 데이터베이스 서비스( 즉 Azure SQL Database 또는 Azure SQL Database 관리 인스턴스)로 데이터베이스 워크로드를 마이그레이션할 때 기본 부가 가치 서비스 중 하나인 SSIS(SQL Server 통합 서비스)의 ETL 워크로드도 마이그레이션해야 합니다.

ADF(Azure 데이터 팩터리)의 Azure-SSIS 통합 런타임(IR)은 SSIS 패키지 실행을 지원합니다. Azure-SSIS IR이 프로비전되면 SQL Server 데이터 도구(SSDT)/SQL Server 관리 스튜디오(SSMS)와 같은 친숙한 도구와 dtinstall/dtutil/dtexec과 같은 명령줄 유틸리티를 사용하여 Azure에서 패키지를 배포하고 실행할 수 있습니다. 자세한 내용은 [Azure SSIS 리프트 앤 시프트 개요를](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)참조하십시오.

이 문서에서는 온-프레미스 SSIS에서 ADF의 SSIS로의 ETL 워크로드 마이그레이션 프로세스를 중대합니다. 마이그레이션 프로세스는 **평가** 및 **마이그레이션의**두 단계로 구성됩니다.

## <a name="assessment"></a>평가

전체 마이그레이션 계획을 수립하기 위해 철저한 평가를 통해 성공적인 마이그레이션을 방해하는 원본 SSIS 패키지의 문제를 식별하는 데 도움이 됩니다.

DMA(데이터 마이그레이션 도우미)는 로컬에서 설치하고 실행할 수 있는 이 목적을 위해 자유롭게 다운로드할 수 있는 도구입니다. 유형 **통합 서비스의** DMA 평가 프로젝트를 만들어 SSIS 패키지를 일괄 처리로 평가하고 다음 범주에 나와 있는 호환성 문제를 식별할 수 있습니다.

- 마이그레이션 차단기: Azure-SSIS IR에서 실행할 마이그레이션 원본 패키지를 차단하는 호환성 문제입니다. DMA는 이러한 문제를 해결하는 데 도움이 되는 지침을 제공합니다.

- 유익한 문제: 소스 패키지에 사용되는 부분적으로 지원되거나 사용되지 않는 기능입니다. DMA는 포괄적인 권장 사항 집합, Azure에서 사용할 수 있는 대체 방법 및 해결 단계 완화를 제공합니다.

### <a name="four-storage-types-for-ssis-packages"></a>SSIS 패키지를 위한 4가지 스토리지 유형

- SSIS 카탈로그(SSISDB). 이는 SQL Server 2012에서 도입되었으며 SSIS 프로젝트/패키지 작업에 사용되는 저장 프로시저, 뷰 및 테이블 값 함수 집합을 포함합니다.
- 파일 시스템
- SQL 서버 시스템 데이터베이스(MSDB)입니다.
- SSIS 패키지 스토어. 이 계층은 두 가지 하위 유형 위에 있는 패키지 관리 계층입니다.
  - SSIS 패키지를 저장하는 데 사용되는 SQL Server의 시스템 데이터베이스인 MSDB입니다.
  - SSIS 패키지를 저장하는 데 사용되는 SQL Server 설치 경로의 특정 폴더인 관리되는 파일 시스템입니다.

DMA는 현재 **DMA 버전 v5.0**이후 **파일 시스템,** **패키지 저장소**및 **SSIS 카탈로그에** 저장된 패키지의 일괄 평가를 지원합니다.

[DMA를](https://docs.microsoft.com/sql/dma/dma-overview)얻고 [그것으로 패키지 평가를 수행합니다.](https://docs.microsoft.com/sql/dma/dma-assess-ssis)

## <a name="migration"></a>마이그레이션

원본 SSIS 패키지의 [저장소 유형과](#four-storage-types-for-ssis-packages) 데이터베이스 워크로드의 마이그레이션 대상에 따라 **SSIS 패키지 및 SSIS 패키지** 실행을 예약하는 SQL Server 에이전트 **작업을** 마이그레이션하는 단계가 다를 수 있습니다. 다음과 같은 두 가지 시나리오가 있습니다.

- [**Azure SQL Database는 데이터베이스** 워크로드 대상으로 인스턴스를 관리했습니다.](#azure-sql-database-managed-instance-as-database-workload-destination)
- [**Azure SQL 데이터베이스를** 데이터베이스 워크로드 대상으로](#azure-sql-database-as-database-workload-destination)

### <a name="azure-sql-database-managed-instance-as-database-workload-destination"></a>**Azure SQL Database는 데이터베이스** 워크로드 대상으로 인스턴스를 관리했습니다.

| **패키지 스토리지 유형** |SSIS 패키지를 일괄 마이그레이션하는 방법|SSIS 작업을 일괄 마이그레이션하는 방법|
|-|-|-|
|Ssisdb|[**SSISDB** 마이그레이션](scenario-ssis-migration-ssisdb-mi.md)|[SSIS 작업을 Azure SQL Database 관리 인스턴스 에이전트로 마이그레이션](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-azure-sql-database-managed-instance-agent)|
|파일 시스템|dtinstall/dtutil/수동 복사본을 통해 파일 공유/Azure 파일에 다시 배포하거나 VNet/자체 호스팅 IR을 통해 액세스할 수 있도록 파일 시스템에 보관합니다. 자세한 내용은 [dtutil 유틸리티](https://docs.microsoft.com/sql/integration-services/dtutil-utility)를 참조하십시오.|<li> [SSMS의 SSIS 작업 마이그레이션 마법사]로 마이그레이션합니다. (how-to-migrate-ssis-job-ssms.md) <li>스크립트/SSMS/ADF 포털을 통해 ADF 파이프라인/활동/트리거로 변환합니다. 자세한 내용은 [SSMS 예약 기능을](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)참조하십시오.|
|SQL 서버(MSDB)|SSMS/dtutil을 통해 파일 시스템/파일 공유/Azure 파일로 내보냅니다. 자세한 내용은 [SSIS 패키지 내보내기를](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service)참조하십시오.|스크립트/SSMS/ADF 포털을 통해 ADF 파이프라인/활동/트리거로 변환합니다. 자세한 내용은 [SSMS 예약 기능을](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)참조하십시오.|
|패키지 저장소|SSMS/dtutil을 통해 파일 시스템/파일 공유/Azure 파일로 내보내거나 dtinstall/dtutil/수동 복사본을 통해 파일 공유/Azure 파일에 다시 배포하거나 VNet/자체 호스팅 IR을 통해 액세스하기 위해 파일 시스템에 보관합니다. 자세한 내용은 dtutil 유틸리티를 참조하십시오. 자세한 내용은 [dtutil 유틸리티](https://docs.microsoft.com/sql/integration-services/dtutil-utility)를 참조하십시오.|스크립트/SSMS/ADF 포털을 통해 ADF 파이프라인/활동/트리거로 변환합니다. 자세한 내용은 [SSMS 예약 기능을](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)참조하십시오.|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Azure SQL 데이터베이스를** 데이터베이스 워크로드 대상으로

| **패키지 스토리지 유형** |SSIS 패키지를 일괄 마이그레이션하는 방법|작업을 일괄 마이그레이션하는 방법|
|-|-|-|
|Ssisdb|SSDT/SSMS를 통해 Azure-SSISDB로 재배포합니다. 자세한 내용은 [Azure에서 SSIS 패키지 배포를](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)참조하십시오.|스크립트/SSMS/ADF 포털을 통해 ADF 파이프라인/활동/트리거로 변환합니다. 자세한 내용은 [SSMS 예약 기능을](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)참조하십시오.|
|파일 시스템|dtinstall/dtutil/수동 복사본을 통해 파일 공유/Azure 파일에 다시 배포하거나 VNet/자체 호스팅 IR을 통해 액세스할 수 있도록 파일 시스템에 보관합니다. 자세한 내용은 [dtutil 유틸리티](https://docs.microsoft.com/sql/integration-services/dtutil-utility)를 참조하십시오.|<li> [SSMS의 SSIS 작업 마이그레이션 마법사]로 마이그레이션합니다. (how-to-migrate-ssis-job-ssms.md) <li> 스크립트/SSMS/ADF 포털을 통해 ADF 파이프라인/활동/트리거로 변환합니다. 자세한 내용은 [SSMS 예약 기능을](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)참조하십시오.|
|SQL 서버(MSDB)|SSMS/dtutil을 통해 파일 시스템/파일 공유/Azure 파일로 내보냅니다. 자세한 내용은 [SSIS 패키지 내보내기를](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service)참조하십시오.|스크립트/SSMS/ADF 포털을 통해 ADF 파이프라인/활동/트리거로 변환합니다. 자세한 내용은 [SSMS 예약 기능을](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)참조하십시오.|
|패키지 저장소|SSMS/dtutil을 통해 파일 시스템/파일 공유/Azure 파일로 내보내거나 dtinstall/dtutil/수동 복사본을 통해 파일 공유/Azure 파일에 다시 배포하거나 VNet/자체 호스팅 IR을 통해 액세스하기 위해 파일 시스템에 보관합니다. 자세한 내용은 dtutil 유틸리티를 참조하십시오. 자세한 내용은 [dtutil 유틸리티](https://docs.microsoft.com/sql/integration-services/dtutil-utility)를 참조하십시오.|스크립트/SSMS/ADF 포털을 통해 ADF 파이프라인/활동/트리거로 변환합니다. 자세한 내용은 [SSMS 예약 기능을](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)참조하십시오.|

## <a name="additional-resources"></a>추가 리소스

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [데이터베이스 마이그레이션 도우미](https://docs.microsoft.com/sql/dma/dma-overview)
- [SSIS 워크로드를 클라우드로 리프트 및 전환](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview?view=sql-server-2017)
- [SSIS 패키지를 Azure SQL Database 관리형 인스턴스로 마이그레이션](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [Azure SQL 데이터베이스에 패키지 재배포](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

## <a name="next-steps"></a>다음 단계

- [Azure에 배포된 SSIS 패키지 유효성 검사](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Azure에 배포된 SSIS 패키지 실행](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Azure-SSIS 통합 런타임 모니터링](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [Azure에서 SSIS 패키지 실행 예약](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
