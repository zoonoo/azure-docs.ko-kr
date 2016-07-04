<properties
   pageTitle="Azure SQL 데이터 웨어하우스 복원(개요) | Microsoft Azure"
   description="Azure SQL 데이터 웨어하우스의 데이터베이스를 복구하기 위한 데이터베이스 복원 옵션 개요입니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/15/2016"
   ms.author="elfish;barbkess;sonyama"/>


# Azure SQL 데이터 웨어하우스 복원(개요)

> [AZURE.SELECTOR]
- [개요][]
- [포털][]
- [PowerShell][]
- [REST][]의 Azure SQL 데이터 웨어하우스는 로컬 중복 저장소 및 자동화된 백업을 사용하여 데이터를 보호합니다. 자동화된 백업은 별도의 관리 방법 없이 실수로 손상 또는 삭제되지 않도록 데이터베이스를 보호합니다. 사용자가 실수로 또는 우연하게 데이터를 수정하거나 삭제한 경우 이전 시점으로 데이터베이스를 복원하여 비즈니스 연속성을 보장할 수 있습니다. SQL 데이터 웨어하우스는 Azure 저장소 스냅숏을 사용하여 가동 중지 시간 없이 원활하게 데이터베이스를 백업합니다.

## 자동화된 백업

**활성** 데이터베이스는 자동으로 최소 8시간마다 백업되고 7일 동안 유지됩니다. 이 옵션을 사용하면 지난 7일 동안의 여러 복원 지점 중 하나로 활성 데이터베이스를 복원할 수 있습니다.

데이터베이스가 일시 중지되면 새 백업은 중지되고 이전 백업은 7일에 도달하면 없어집니다. 데이터베이스가 7일을 초과하여 일시 중지되는 경우 최소 하나의 백업이 항상 있도록 가장 최근 백업이 저장됩니다.

데이터베이스가 삭제되는 경우 마지막 백업이 7일 동안 저장됩니다.

마지막 백업이 수행된 때를 확인하려면 활성 SQL 데이터 웨어하우스에서 이 쿼리를 실행합니다.

```sql
select top 1 *
from sys.pdw_loader_backup_runs 
order by run_id desc;
```

7일 이상 백업을 유지해야 할 경우 복원 지점 중 하나를 새 데이터베이스로 복원한 다음 해당 백업의 저장 공간에 대한 비용만 부담하도록 필요에 따라 해당 데이터베이스를 일시 중지할 수 있습니다.

## 데이터 중복

백업 외에도 SQL 데이터 웨어하우스는 [로컬 중복(LRS)][] Azure 프리미엄 저장소를 사용하여 데이터를 보호합니다. 데이터의 여러 동기 복사본은 지역화된 오류 발생 시 투명 한 데이터 보호를 보장하기 위해 로컬 데이터 센터에 유지됩니다. 데이터 중복을 사용하면 데이터는 디스크 오류 등의 인프라 문제를 감당할 수 있습니다. 데이터 중복은 내결함성이 있는 비즈니스 연속성 및 고가용성 인프라를 보장합니다.

## 데이터베이스 복원

SQL 데이터 웨어하우스 복원은 Azure 포털에서 수행되거나 PowerShell 또는 REST API를 사용하여 자동화될 수 있는 간단한 작업입니다.


## 다음 단계
Azure SQL 데이터베이스 버전의 무중단 업무 방식 기능에 대해 알아보려면 [Azure SQL 데이터베이스 무중단 업무 방식 개요][]를 읽으세요.

<!--Image references-->

<!--Article references-->
[Azure SQL 데이터베이스 무중단 업무 방식 개요]: ./sql-database-business-continuity.md
[로컬 중복(LRS)]: ../storage/storage-redundancy.md
[개요]: ./sql-data-warehouse-restore-database-overview.md
[포털]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0622_2016-->