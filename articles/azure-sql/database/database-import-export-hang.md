---
title: 데이터베이스 가져오기 및 내보내기 시간이 오래 걸림
description: Azure SQL Database와 Azure SQL Managed Instance 가져오기/내보내기 서비스는 데이터베이스를 가져오거나 내보내는 데 시간이 오래 걸립니다.
ms.custom: seo-lt-2019, sqldbrb=1
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: cawrites
ms.date: 09/27/2019
ms.openlocfilehash: a36fd722f38ec2253ada7e2f759597ea0df080a3
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110787374"
---
# <a name="azure-sql-database-and-managed-instance-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Azure SQL Database와 Managed Instance 가져오기/내보내기 서비스는 데이터베이스를 가져오거나 내보내는 데 시간이 오래 걸립니다.

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

가져오기/내보내기 서비스를 사용하는 경우 프로세스가 예상보다 오래 걸릴 수 있습니다. 이 문서에서는 이와 같은 지연의 가능한 원인과 대체 해결 방법을 설명합니다.

## <a name="azure-sql-database-importexport-service"></a>Azure SQL Database 가져오기/내보내기 서비스

Azure SQL Database 가져오기/내보내기 서비스는 모든 Azure 데이터 센터에서 실행되는 REST 기반 웹 서비스입니다. 이 서비스는 [데이터베이스 가져오기](database-import.md#using-azure-portal) 또는 [내보내기](./database-import.md#using-azure-portal) 옵션을 사용하여 Azure Portal에서 데이터베이스를 이동할 때 호출됩니다. 이 서비스는 Azure SQL Database와 Azure Blob Storage 간에 가져오기 및 내보내기를 수행하는 무료 요청 큐 및 계산 서비스를 제공합니다.

가져오기 및 내보내기 작업은 기존 물리적 데이터베이스 백업을 나타내지 않고, 특수 BACPAC 형식을 사용하는 데이터베이스의 논리적 백업을 나타냅니다. BACPAC 형식을 사용하면 Microsoft SQL Server, Azure SQL Database 및 Azure SQL Managed Instance의 버전마다 다를 수 있는 물리적 형식을 사용하지 않아도 됩니다.

## <a name="what-causes-delays-in-the-process"></a>프로세스에서 지연이 발생하는 이유는 무엇인가요?

Azure SQL Database 가져오기/내보내기 서비스는 가져오기 및 내보내기 작업을 처리하기 위해 지역당 제한된 수의 컴퓨팅 VM(가상 머신)을 제공합니다. 컴퓨팅 VM은 가져오기 또는 내보내기에서 지역 간 대역폭 지연 및 요금을 피하기 위해 지역별로 호스트됩니다. 동일한 지역에서 동시에 너무 많은 요청이 수행되는 경우 작업을 처리하는 동안 상당한 지연이 발생할 수 있습니다. 요청을 완료하는 데 필요한 시간은 몇 초에서 몇 시간까지 다양할 수 있습니다.

> [!NOTE]
> 요청이 4일 이내에 처리되지 않으면 서비스에서 자동으로 요청을 취소합니다.

## <a name="recommended-solutions"></a>권장 솔루션

데이터베이스 내보내기가 실수로 인한 데이터 삭제 복구에만 사용되는 경우 모든 Azure SQL Database 버전은 시스템 생성 백업에서 셀프 서비스 복원 기능을 제공합니다. 그러나 다른 이유로 해당 내보내기가 필요하고 지속적으로 빠르고 예측 가능한 가져오기/내보내기 성능이 필요한 경우에는 다음 옵션을 고려하세요.

* [SQLPackage 유틸리티를 사용하여 BACPAC 파일로 내보내기](./database-export.md#sqlpackage-utility)
* [SSMS(SQL Server Management Studio)를 사용하여 BACPAC 파일로 내보내기](./database-export.md#sql-server-management-studio-ssms)
* Microsoft SQL Server Data-Tier Application Framework(DacFx) API를 사용하여 코드에서 직접 BACPAC 가져오기 또는 내보내기 실행. 자세한 내용은 다음을 참조하세요.
  * [데이터 계층 애플리케이션 내보내기](/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Microsoft.SqlServer.Dac 네임스페이스](/dotnet/api/microsoft.sqlserver.dac)
  * [DACFx 다운로드](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-a-database"></a>데이터베이스를 내보내거나 가져올 때 고려해야 할 사항

* 이 문서에서 설명하는 모든 방법은 DTU(데이터베이스 트랜잭션 단위) 할당량을 모두 사용하여 Azure SQL Database 서비스에 의한 제한을 일으킵니다. [Azure Portal에서 데이터베이스에 대한 DTU 통계를 볼 수 있습니다](./monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring). 데이터베이스가 리소스 제한에 도달하면 [서비스 계층을 업그레이드](./scale-resources.md)하여 더 많은 리소스를 추가합니다.
* 이상적으로, 클라이언트 애플리케이션(예: sqlpackage 유틸리티 또는 사용자 지정 DAC 애플리케이션)을 데이터베이스와 동일한 지역에 있는 VM에서 실행해야 합니다. 그렇지 않으면 네트워크 대기 시간과 관련된 성능 문제가 발생할 수 있습니다.
* 클러스터형 인덱스가 없는 큰 테이블을 내보내면 매우 느리거나 오류가 발생할 수 있습니다. 이 동작은 테이블을 병렬로 분할하고 내보낼 수 없기 때문에 발생합니다. 대신 단일 트랜잭션으로 내보내야 하며 내보내기 중에 성능이 저하되고 잠재적으로 오류가 발생하는데, 큰 테이블의 경우 특히 더 그렇습니다.


## <a name="related-documents"></a>관련 문서

[데이터베이스를 내보내는 경우 고려 사항](./database-export.md#considerations)