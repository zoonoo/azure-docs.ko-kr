<properties
	pageTitle="스트레치 데이터베이스 관리 및 문제 해결 | Microsoft Azure"
	description="스트레치 데이터베이스를 관리하고 문제를 해결하는 방법을 알아봅니다."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# 스트레치 데이터베이스 관리 및 문제 해결

스트레치 데이터베이스를 관리하고 문제를 해결하려면 이 항목에 설명된 도구 및 메서드를 사용합니다.

## <a name="LocalInfo"></a>스트레치 데이터베이스를 사용하는 로컬 데이터베이스 및 테이블에 대한 정보 가져오기
스트레치 지원 SQL Server 데이터베이스 및 테이블에 대한 정보를 보려면 카탈로그 뷰 **sys.databases** 및 **sys.tables**를 엽니다. 자세한 내용은 [sys.databases(TRANSACT-SQL)](https://msdn.microsoft.com/library/ms178534.aspx) 및 [sys.tables(TRANSACT-SQL)](https://msdn.microsoft.com/library/ms187406.aspx)를 참조하세요.

## <a name="RemoteInfo"></a>스트레치 데이터베이스에서 사용하는 원격 데이터베이스 및 테이블에 대한 정보 가져오기
마이그레이션된 데이터가 저장된 원격 데이터베이스 및 테이블에 대한 정보를 보려면 카탈로그 뷰 **sys.remote\_data\_archive\_databases** 및 **sys.remote\_data\_archive\_tables**를 엽니다. 자세한 내용은 [sys.remote\_data\_archive\_databases(Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx) 및 [sys.remote\_data\_archive\_tables(Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx)를 참조하세요.

## 테이블에 적용된 필터 조건자 확인
카탈로그 뷰 **sys.remote\_data\_archive\_tables**를 열고 **filter\_predicate** 열의 값을 확인합니다. 값이 null이면 전체 테이블을 마이그레이션할 수 있습니다. 자세한 내용은 [sys.remote\_data\_archive\_tables(TRANSACT-SQL)](https://msdn.microsoft.com/library/dn935003.aspx)를 참조하세요.

## <a name="Migration"></a>데이터 마이그레이션 상태 확인
스트레치 데이터베이스 모니터에서 데이터 마이그레이션을 모니터링하려면 SQL Server Management Studio에서 데이터베이스에 대한 **작업 | 스트레치 | 모니터**를 선택합니다. 자세한 내용은 [데이터 마이그레이션 모니터링 및 문제 해결(스트레치 데이터베이스)](sql-server-stretch-database-monitor.md)을 참조하세요.

또는 동적 관리 뷰 **sys.dm\_db\_rda\_migration\_status**를 열고 마이그레이션된 배치 수 및 데이터 행 수를 확인합니다.

## 인덱싱 등 리소스를 많이 사용하는 작업에 대한 Azure 성능 수준 증가
스트레치 데이터베이스에 대해 구성된 큰 테이블에서 인덱스를 빌드, 다시 빌드 또는 다시 구성하는 경우 작업 기간 동안 해당 원격 데이터베이스의 성능 수준을 높이는 것이 좋습니다.

## 원격 테이블의 스키마 변경 금지
스트레치 데이터베이스에 대해 구성된 SQL Server 테이블과 연결된 원격 Azure 테이블의 스키마를 변경하지 마세요. 특히 열의 데이터 형식 또는 이름을 수정해서는 안 됩니다. 스트레치 데이터베이스 기능에서는 SQL Server 테이블의 스키마와 관련하여 원격 테이블의 스키마에 대해 다양한 가정을 합니다. 원격 스키마를 변경하면 변경된 테이블에 대해 스트레치 데이터베이스의 작동이 중지됩니다.

## <a name="Firewall"></a>데이터 마이그레이션 문제 해결
문제 해결 제안 사항은 [데이터 마이그레이션 모니터링 및 문제 해결(스트레치 데이터베이스)](sql-server-stretch-database-monitor.md)을 참조하세요.

## 쿼리 성능 문제 해결
**스트레치 지원 테이블이 포함된 쿼리 속도가 느립니다.** 스트레치 지원 테이블이 포함된 쿼리는 테이블에서 스트레치를 사용하기 전보다 실행 속도가 느립니다. 쿼리 성능이 크게 저하된 경우 다음과 같은 문제를 검토하세요.

-   Azure 서버가 SQL Server와 다른 지리적 지역에 있습니까? 네트워크 대기 시간을 줄이기 위해 SQL Server와 동일한 지리적 지역에 있도록 Azure 서버를 구성합니다.

-   네트워크 상태가 저하될 수 있습니다. 최근에 발생한 문제 또는 중단에 대한 정보는 네트워크 관리자에게 문의하세요.

## 참고 항목

[스트레치 데이터베이스 모니터링](sql-server-stretch-database-monitor.md)

[스트레치 지원 데이터베이스 백업 및 복원](sql-server-stretch-database-backup.md)

<!---HONumber=AcomDC_0316_2016-->