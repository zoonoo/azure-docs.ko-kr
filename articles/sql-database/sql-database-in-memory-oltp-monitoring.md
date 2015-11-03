<properties
	pageTitle="XTP 메모리 내 저장소 모니터링 | Microsoft Azure"
	description="XTP 메모리 내 저장소 사용, 용량을 예측 및 모니터링합니다. 41805 용량 오류를 해결합니다."
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/28/2015"
	ms.author="jodebrui"/>


# XTP 메모리 내 저장소 모니터링

서비스 계층 및 저장소 정보는 [SQL 데이터베이스 서비스 계층 문서](sql-database-service-tiers.md)를 참조합니다.

[메모리 내](sql-database-in-memory.md)를 사용하는 경우 메모리에 최적화된 테이블 및 테이블 변수에 있는 데이터는 XTP 메모리 내 저장소에 상주합니다. Premium 서비스 계층에는 최대 메모리 내 저장소 크기가 있습니다. 이 제한이 초과되면 삽입 및 업데이트 작업이 실패하기 시작할 수 있습니다.(오류 41805로) 해당 시점에서 데이터를 삭제하여 하나에 메모리를 회수하거나 데이터베이스의 성능 계층을 업그레이드해야 합니다.

## 데이터가 메모리 내 저장소 용량에 맞는지 여부 결정

메모리에 최적화된 테이블에 대한 메모리 요구 사항을 추정하면 Azure SQL 데이터베이스에서 SQL Server가 작동과 동일한 방식으로 작동합니다. [MSDN](https://msdn.microsoft.com/library/dn282389.aspx)의 항목을 검토하는 데 몇 분이 걸립니다.

테이블 및 테이블 변수 행 뿐만 아니라 인덱스가 사용자 데이터 크기를 계산합니다. 또한 ALTER TABLE은 전체 테이블 및 인덱스의 새 버전을 만들기 위해 충분한 공간이 필요합니다.

##### Premium 서비스 계층에 대한 최대 XTP 메모리 내 저장소 크기:



Azure [포털](http://portal.azure.com/)의 메모리 내 저장소 사용을 모니터링할 수 있습니다.

- 데이터베이스 블레이드에서 리소스 사용률 상자를 찾고 편집을 클릭합니다.
- 그런 다음 메트릭 XTP 메모리 내 저장소 비율을 선택합니다.

또는 다음 쿼리를 사용하여 메모리 내 저장소 사용률을 표시합니다.

    select xtp_storage_percent from sys.dm_db_resource_stats

## 메모리 부족 상황을 수정합니다 - 41805 오류

메모리가 부족하면 41805 오류 메시지와 함께 삽입, 업데이트 및 만들기 작업이 실패합니다.

오류 메시지 41805는 메모리에 최적화된 테이블 및 테이블 변수가 최대 크기를 초과했음을 나타냅니다.

이 오류를 해결하려면


- 잠재적으로 데이터를 기존의 디스크 기반 테이블에 오프로드딩하여 메모리에 최적화된 테이블에서 데이터를 삭제합니다. 또는
- 메모리에 최적화된 테이블에서 유지하는 데 필요한 데이터에 대한 충분한 메모리 내 저장소가 있는 서비스 계층을 업그레이드합니다.

## 다음 단계
[동적 관리 뷰를 사용하여 Azure SQL 데이터베이스 모니터링](sql-database-monitoring-with-dmvs.md)에 대해 자세히 알아봅니다.

<!---HONumber=Nov15_HO1-->