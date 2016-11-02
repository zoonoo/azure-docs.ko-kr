<properties
	pageTitle="SQL 데이터베이스 탄력적 풀 가격 및 성능"
	description="탄력적 데이터베이스 풀 관련 가격 정보입니다."
	services="sql-database"
	documentationCenter=""
	authors="srinia"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="05/27/2016"
	ms.author="srinia"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# 탄력적 데이터베이스 풀 대금 청구 및 가격 정보

탄력적 데이터베이스 풀은 다음 특성에 따라 대금이 청구됩니다.

- 탄력적 풀은 풀에 데이터베이스가 없더라도 생성 시부터 대금이 청구됩니다.
- 탄력적 풀은 시간당 대금이 청구됩니다. 이는 단일 데이터베이스의 성능 수준과 같은 계량 빈도입니다.
- 탄력적 풀이 새로운 크기의 eDTU로 조정된 경우, 크기 조정 작업이 완료될 때까지 새 eDTU 크기에 따른 대금이 청구되지 않습니다. 이는 독립 실행형 데이터베이스의 성능 수준을 변경하는 것과 동일한 패턴을 따릅니다.


- 탄력적 풀의 가격은 풀의 eDTU 수를 기준으로 책정됩니다. 탄력적 풀의 가격은 그 안의 탄력적 데이터베이스 사용률 및 수와 무관합니다.
- 가격은 (풀 eDTU 수)x(eDTU 당 단위 가격)으로 계산됩니다.

탄력적 풀의 단위 eDTU 가격은 동일한 서비스 계층에 있는 독립 실행형 데이터베이스의 단위 DTU 가격보다 높습니다. 자세한 내용은 [SQL 데이터베이스 가격](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.


eDTU 및 서비스 계층을 이해하려면 [SQL 데이터베이스 옵션 및 성능](sql-database-service-tiers.md)을 참조하세요.

## 다음 단계

- [탄력적 데이터베이스 풀 생성](sql-database-elastic-pool-create-portal.md)
- [탄력적 데이터베이스 풀 모니터링, 관리 및 크기 조정](sql-database-elastic-pool-manage-portal.md)
- [SQL 데이터베이스 옵션 및 성능: 각 서비스 계층에서 사용할 수 있는 항목 이해](sql-database-service-tiers.md)
- [탄력적 데이터베이스 풀에 적합한 데이터베이스를 식별하기 위한 PowerShell 스크립트](sql-database-elastic-pool-database-assessment-powershell.md)

<!---HONumber=AcomDC_0601_2016-->