<properties
   pageTitle="Azure SQL 데이터 웨어하우스에서 데이터베이스 복원(개요) | Microsoft Azure"
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
   ms.date="06/02/2016"
   ms.author="elfish;barbkess;sonyama"/>


# Azure SQL 데이터 웨어하우스에서 데이터베이스 복원(개요)

> [AZURE.SELECTOR]
- [개요](sql-data-warehouse-overview-manage-database-restore.md)
- [포털](sql-data-warehouse-manage-database-restore-portal.md)
- [PowerShell](sql-data-warehouse-manage-database-restore-powershell.md)
- [REST (영문)](sql-data-warehouse-manage-database-restore-rest-api.md)

이 문서에서는 Azure SQL 데이터 웨어하우스에서 데이터베이스를 복원하기 위한 옵션을 설명합니다. 여기에는 라이브 데이터 웨어하우스 및 삭제된 데이터 웨어하우스 복원 작업이 포함됩니다. 라이브 및 삭제된 데이터 웨어하우스는 모든 데이터 웨어하우스에서 만든 자동 스냅숏에서 복원됩니다.

## 복구 시나리오

**인프라 오류로부터 복구:** 디스크 오류 등의 인프라 문제를 복구하는 데 이 시나리오를 참조합니다. 고객은 내결함성이 있는 비즈니스 연속성 및 고가용성 인프라를 확인하고자 합니다.

**사용자 오류에서 복구:** 이 시나리오에서는 의도 하지 않거나 부수적 데이터 손상 또는 삭제로부터의 복구를 가리킵니다. 사용자가 실수로 또는 우연하게 데이터를 수정하거나 삭제한 경우, 고객은 이전 시점으로 데이터베이스를 복원하여 비즈니스 연속성을 보장하고자 합니다.

## 스냅숏 정책

[AZURE.INCLUDE [SQL 데이터 웨어하우스 백업 보존 정책](../../includes/sql-data-warehouse-backup-retention-policy.md)]


## 데이터베이스 복원 기능

SQL 데이터 웨어하우스가 데이터베이스의 안정성을 개선하고 앞에서 언급한 시나리오에서의 복구 기능 및 연속 작업을 허용하는 방법을 살펴보겠습니다.


### 데이터 중복

SQL 데이터 웨어하우스는 3개의 데이터 사본을 포함하는 [로컬 중복(LRS)](../storage/storage-redundancy.md) Azure 프리미엄 저장소에 모든 데이터를 저장합니다.

### 데이터베이스 복원

데이터베이스 복원은 데이터베이스를 이전 시점으로 복원하도록 설계되었습니다. Azure SQL 데이터 웨어하우스 서비스는 적어도 8시간마다 자동 저장소 스냅숏을 사용하여 모든 데이터베이스를 보호하고 7일 동안 복원 지점의 불연속 집합 제공을 유지합니다. 자동 스냅숏 및 복원 기능은 별도의 관리 방법 없이 실수로 손상 또는 삭제되지 않도록 데이터베이스를 보호합니다. 데이터베이스를 복원하는 방법에 대한 자세한 내용은 [데이터베이스 복원 작업][]을 참조하세요.

## 다음 단계
기타 중요한 관리 작업은 [관리 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[Azure storage redundancy options]: ../storage/storage-redundancy.md#read-access-geo-redundant-storage
[Backup and restore tasks]: sql-data-warehouse-database-restore-portal.md
[관리 개요]: sql-data-warehouse-overview-management.md
[데이터베이스 복원 작업]: sql-data-warehouse-manage-database-restore-portal.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0608_2016-->