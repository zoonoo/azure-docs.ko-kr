---
title: Azure SQL Database를 사용하여 SSIS 마이그레이션을 데이터베이스 워크로드 대상으로 관리됨 인스턴스
description: Azure SQL Database를 사용하여 SSIS 마이그레이션을 데이터베이스 워크로드 대상으로 관리합니다.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 38010e3aaa2d0544dfbfe19135d25250d2b021a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929773"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>Azure SQL Database를 사용하여 SSIS 마이그레이션을 데이터베이스 워크로드 대상으로 관리됨 인스턴스

온프레미스에서 SQL Server로 데이터베이스 워크로드를 Azure SQL Database 관리 인스턴스로 마이그레이션할 [때는 Azure 데이터 마이그레이션](https://docs.microsoft.com/azure/dms/dms-overview)서비스(DMS) 및 [DMS를 사용하여 관리되는 Azure SQL Database 관리 인스턴스 마이그레이션에 대한 네트워크 토폴로지에](https://docs.microsoft.com/azure/dms/resource-network-topologies)대해 잘 알고 있어야 합니다.

이 문서에서는 SSIS 카탈로그(SSISDB) 및 SSIS 패키지 실행을 예약하는 SQL Server 에이전트 작업에 저장된 SSIS(SSIS) 패키지의 마이그레이션에 대해 중점합니다.

## <a name="migrate-ssis-catalog-ssisdb"></a>SSIS 카탈로그 마이그레이션(SSISDB)

SSISDB 마이그레이션은 문서에 설명된 대로 DMS를 사용하여 수행할 수 [있습니다.](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>Azure SQL Database 관리 인스턴스 에이전트에 대한 SSIS 작업

Azure SQL Database 관리 형 인스턴스에는 온-프레미스의 SQL Server 에이전트와 같은 네이티브 일류 스케줄러가 있습니다.  SSIS 작업에 대한 마이그레이션 도구를 아직 사용할 수 없으므로 스크립트/수동 복사본을 통해 온프레미스의 SQL Server 에이전트에서 Azure SQL Database 관리 인스턴스 에이전트로 마이그레이션해야 합니다.

## <a name="additional-resources"></a>추가 리소스

- [Azure 데이터 팩터리](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure SSIS 통합 런타임](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Azure SQL Database관리 인스턴스 마이그레이션에 대 한 네트워크 topologies DMS를 사용 하 여](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Azure SQL Database Managed Instance에 SSIS 패키지 마이그레이션](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>다음 단계

- [Azure에서 SSISDB에 연결](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Azure에 배포된 SSIS 패키지 실행](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
