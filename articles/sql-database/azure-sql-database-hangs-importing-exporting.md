---
title: Azure SQL Database Import/Export 서비스에서 데이터베이스를 가져오거나 내보내는 데 시간이 오래 걸립니다. Microsoft Docs
description: Azure SQL Database Import/Export 서비스는 데이터베이스를 가져오거나 내보내는 데 시간이 오래 걸립니다.
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: ed5f3d1cd505270eb91c9cfbd6fb5c38b908f33d
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974462"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Azure SQL Database Import/Export 서비스는 데이터베이스를 가져오거나 내보내는 데 시간이 오래 걸립니다.

Azure SQL Database Import/Export 서비스를 사용 하는 경우 프로세스를 완료 하는 데 시간이 오래 걸릴 수 있습니다. 이 문서에서는 이러한 문제를 해결 하는 데 사용할 수 있는 다른 방법 및 지연 원인에 대 한 추가 정보를 제공 합니다.

## <a name="azure-sql-database-importexport-service"></a>Azure SQL Database Import/Export 서비스

Azure SQL Database Import/Export 서비스는 모든 Microsoft Azure 데이터 센터에서 실행 되는 REST 기반 웹 서비스입니다. 이 서비스는 [데이터베이스 가져오기](https://docs.microsoft.com/azure/sql-database/sql-database-import#import-from-a-bacpac-file-in-the-azure-portal) 또는 [내보내기](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) 옵션을 사용 하 여 Microsoft Azure portal에서 SQL 데이터베이스를 이동할 때 호출 되는 서비스입니다. 이 서비스는 Microsoft Azure SQL database에서 Microsoft Azure BLOB (binary large object) 저장소로 가져오기 및 내보내기를 수행 하는 무료 요청 큐 서비스 및 무료 계산 서비스를 제공 합니다.

가져오기 및 내보내기 작업은 기존 물리적 데이터베이스 백업이 아니라 특수 BACPAC 형식을 사용 하는 데이터베이스의 논리적 백업입니다. 이 논리적 BACPAC 형식을 사용 하면 SQL Server와 SQL Database 버전에 따라 다를 수 있는 물리적 형식을 사용 하지 않아도 됩니다. 따라서이를 사용 하 여 데이터베이스를 SQL database 및 SQL Server 데이터베이스로 안전 하 게 복원할 수 있습니다.

## <a name="what-causes-the-process-to-take-a-long-time"></a>프로세스에 오랜 시간이 소요 되는 원인

Azure SQL Database 가져오기/내보내기 서비스는 가져오기 및 내보내기 작업을 처리 하기 위해 지역 당 제한 된 수의 계산 Vm (가상 머신)을 제공 합니다. 계산 VM은 지역 마다 호스트 되므로 가져오기 또는 내보내기에서 지역 간 대역폭 지연 및 요금을 피할 수 있습니다. 따라서 동일한 지역에서 너무 많은 요청을 동시에 수행 하는 경우 작업을 처리 하는 동안 상당한 지연이 발생 합니다. 요청을 완료 하는 데 필요한 시간은 몇 초에서 몇 시간까지 달라질 수 있습니다.

> [!NOTE]
> 요청이 4 일 이내에 처리 되지 않으면 서비스에서 자동으로 요청을 취소 합니다.

## <a name="recommended-solutions"></a>권장 해결 방법

데이터베이스 내보내기가 실수로 인 한 데이터 삭제를 복구 하는 데에만 사용 되는 경우 모든 Azure SQL Server 데이터베이스 버전은 시스템 생성 백업에서 셀프 서비스 복원 기능을 제공 합니다. 그러나 다른 이유로 이러한 내보내기가 필요 하 고 지속적으로 빠르고 예측 가능한 가져오기 또는 내보내기 성능이 필요한 경우에는 다음 옵션을 고려해 야 합니다.

* [SQLPackage 유틸리티를 사용 하 여 BACPAC 파일로 내보내기](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility)
* [SQL Server Management Studio (SSMS)를 사용 하 여 BACPAC 파일로 내보내기](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms)
* Microsoft® SQL Server® DacFx (데이터 계층 응용 프로그램 프레임 워크) API를 사용 하 여 코드에서 BACPAC 가져오기 또는 내보내기를 직접 실행 합니다. 추가 정보 검토
  * [데이터 계층 응용 프로그램 내보내기](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Microsoft SqlServer 네임 스페이스](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [다운로드 DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="considerations-when-exporting-or-importing-an-azure-sql-database"></a>Azure SQL database를 내보내거나 가져올 때의 고려 사항

* 이 문서에서 설명 하는 모든 메서드는 DTU 할당량을 사용 하 여 Azure SQLDB 서비스에 의해 제한 됩니다. [Azure Portal 데이터베이스에 대 한 DTU 통계를 볼](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#monitor-database-performance)수 있습니다. 데이터베이스가 리소스 제한에 도달 하는 경우 [서비스 계층을 업그레이드](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) 하 여 더 많은 리소스를 추가 합니다.
* 클라이언트 응용 프로그램 (예: sqlpackage 유틸리티 또는 사용자 지정 DAC 응용 프로그램)은 SQL database와 동일한 지역에 있는 VM (가상 머신)에서 실행 해야 합니다. 그렇지 않으면 네트워크 대기 시간으로 인해 성능 문제가 발생할 수 있습니다.
* 클러스터형 인덱스가 없는 큰 테이블을 내보내면 매우 느리거나 오류가 발생할 수 있습니다. 이는 테이블을 병렬로 분할 하 고 내보낼 수 없으며 단일 트랜잭션으로 내보내야 하므로 특히, 규모가 많은 테이블의 경우 내보내기 중에 속도 저하 및 잠재적 오류가 발생 하기 때문입니다. 


## <a name="related-documents"></a>관련 문서

[Azure SQL database를 내보낼 때의 고려 사항](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
