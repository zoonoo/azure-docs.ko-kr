---
title: 가져오기/내보내기 서비스가 시간이 오래 걸립니다.
description: Azure SQL 데이터베이스 가져오기/내보내기 서비스는 데이터베이스를 가져오거나 내보내는 데 시간이 오래 걸립니다.
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
manager: dcscontentpm
ms.openlocfilehash: cf2d9b218fe63414af2446b8562d3ba187b2d395
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535768"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Azure SQL 데이터베이스 가져오기/내보내기 서비스는 데이터베이스를 가져오거나 내보내는 데 시간이 오래 걸립니다.

Azure SQL 데이터베이스 가져오기/내보내기 서비스를 사용하는 경우 프로세스가 예상보다 오래 걸릴 수 있습니다. 이 문서에서는 이 지연 및 대체 해결 방법에 대한 잠재적인 원인을 설명합니다.

## <a name="azure-sql-database-importexport-service"></a>Azure SQL 데이터베이스 가져오기/내보내기 서비스

Azure SQL 데이터베이스 가져오기/내보내기 서비스는 모든 Azure 데이터 센터에서 실행되는 REST 기반 웹 서비스입니다. 이 서비스는 [가져오기 데이터베이스](sql-database-import.md#using-azure-portal) 또는 [내보내기](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) 옵션을 사용하여 Azure 포털에서 SQL 데이터베이스를 이동할 때 호출됩니다. 이 서비스는 Azure SQL 데이터베이스와 Azure Blob 저장소 간의 가져오기 및 내보내기를 수행하기 위한 무료 요청 대기열 및 계산 서비스를 제공합니다.

가져오기 및 내보내기 작업은 기존의 물리적 데이터베이스 백업이 아니라 특수 BACPAC 형식을 사용하는 데이터베이스의 논리적 백업을 나타냅니다. BACPAC 형식을 사용하면 Microsoft SQL Server 및 Azure SQL Database 버전마다 다를 수 있는 물리적 형식을 사용하지 않아도 됩니다. 따라서 데이터베이스를 SQL Server 데이터베이스와 SQL 데이터베이스에 안전하게 복원하는 데 사용할 수 있습니다.

## <a name="what-causes-delays-in-the-process"></a>프로세스지연의 원인은 무엇입니까?

Azure SQL 데이터베이스 가져오기/내보내기 서비스는 가져오기 및 내보내기 작업을 처리하는 지역당 제한된 수의 VM(계산 가상 머신)을 제공합니다. 계산 VM은 지역별 호스트되어 가져오기 또는 내보내기에서 지역 간 대역폭 지연 및 요금이 발생하지 않도록 합니다. 동일한 리전에서 동시에 너무 많은 요청이 수행되는 경우 작업을 처리하는 데 상당한 지연이 발생할 수 있습니다. 요청을 완료하는 데 필요한 시간은 몇 초에서 여러 시간에 이르기까지 다양할 수 있습니다.

> [!NOTE]
> 4일 이내에 요청이 처리되지 않으면 서비스가 자동으로 요청을 취소합니다.

## <a name="recommended-solutions"></a>권장 솔루션

데이터베이스 내보내기가 실수로 데이터 삭제에서 복구하는 데만 사용되는 경우 모든 Azure SQL Database 버전은 시스템 생성 백업에서 셀프 서비스 복원 기능을 제공합니다. 그러나 다른 이유로 이러한 내보내기가 필요하고 일관되게 더 빠르거나 예측 가능한 가져오기/내보내기 성능이 필요한 경우 다음 옵션을 고려하십시오.

* [SQLPackage 유틸리티를 사용하여 BACPAC 파일로 내보내기.](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility)
* [SQL 서버 관리 스튜디오(SSMS)를 사용하여 BACPAC 파일로 내보내기.](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms)
* Microsoft SQL 서버 데이터 계층 응용 프로그램 프레임워크(DacFx) API를 사용하여 코드에서 직접 BACPAC 가져오기 또는 내보내기를 실행합니다. 자세한 내용은 다음을 참조하세요.
  * [데이터 계층 응용 프로그램 내보내기](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [마이크로소프트.SqlServer.Dac 네임스페이스](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [DACFx 다운로드](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-an-azure-sql-database"></a>Azure SQL 데이터베이스를 내보내거나 가져올 때 고려해야 할 사항

* 이 문서에서 설명하는 모든 메서드는 DTU(데이터베이스 트랜잭션 단위) 할당량을 사용하므로 Azure SQL Database 서비스에 의해 제한이 발생합니다. Azure [포털 에서 데이터베이스에 대한 DTU 통계를 볼](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#sql-database-resource-monitoring)수 있습니다. 데이터베이스가 리소스 제한에 도달하면 [서비스 계층을 업그레이드하여](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) 리소스를 추가합니다.
* 이상적으로는 SQL 데이터베이스와 동일한 지역의 VM에서 클라이언트 응용 프로그램(예: sqlpackage 유틸리티 또는 사용자 지정 DAC 응용 프로그램)을 실행하는 것이 좋습니다. 그렇지 않으면 네트워크 대기 시간과 관련된 성능 문제가 발생할 수 있습니다.
* 클러스터된 인덱스없이 큰 테이블을 내보내는 것은 매우 느리거나 실패를 일으킬 수 있습니다. 이 문제는 테이블을 분할하고 병렬로 내보낼 수 없기 때문에 발생합니다. 대신 단일 트랜잭션으로 내보내야 하며 특히 큰 테이블의 경우 내보내기 중에 성능이 저하되고 오류가 발생할 수 있습니다.


## <a name="related-documents"></a>관련 문서

[Azure SQL 데이터베이스를 내보낼 경우 고려 사항](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
