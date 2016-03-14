<properties
	pageTitle="스트레치 데이터베이스에 대한 노출 영역 제한 사항 및 차단 문제 | Microsoft Azure"
	description="스트레치 데이터베이스를 사용하도록 설정하기 전에 먼저 해결해야 하는 차단 문제에 대해 알아봅니다."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglasl"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# 스트레치 데이터베이스에 대한 노출 영역 제한 사항 및 차단 문제

스트레치 데이터베이스를 사용하도록 설정하기 전에 먼저 해결해야 하는 차단 문제에 대해 알아봅니다.

## <a name="Limitations"></a>차단 문제
SQL Server 2016의 현재 미리 보기 릴리스에서 스트레치에 다음 항목으로 인해 부적합한 테이블이 만들어집니다.

**테이블 속성**
-   1,023개 이상의 열

-   998개 이상의 인덱스

-   FILESTREAM 데이터가 있는 테이블

-   FileTables

-   복제된 테이블

-   변경 내용 추적 또는 변경 데이터 캡처를 적극적으로 사용하는 테이블

-   메모리 최적화된 테이블

**데이터 형식 및 열 속성**
-   timestamp

-   sql\_variant

-   XML

-   geometry

-   geography

-   hierarchyid

-   CLR UDT(사용자 정의 형식)

**열 형식**
-   COLUMN\_SET

-   계산된 열

**제약 조건**
-   Check 제약 조건

-   기본 제약 조건

-   테이블을 참조하는 외래 키 제약 조건

**인덱스**
-   전체 텍스트 인덱스

-   XML 인덱스

-   공간 인덱스

-   테이블을 참조하는 인덱싱된 뷰

## <a name="Caveats"></a>스트레치 사용 테이블에 대한 제한 사항 및 주의 사항
SQL Server 2016의 현재 미리 보기 릴리스에서 스트레치 사용 테이블에 다음과 같은 제한 사항 또는 주의 사항이 있습니다.

-   스트레치 사용 테이블에서 UNIQUE 제약 조건 및 PRIMARY KEY 제약 조건에 대해 고유성이 적용되지 않습니다.

-   스트레치 사용 테이블에서 업데이트 또는 삭제 작업을 실행할 수 없습니다.

-   원격 Azure SQL 데이터베이스 테이블에 삽입할 수 없습니다.

-   스트레치 사용 테이블을 포함하는 뷰의 인덱스를 만들 수 없습니다.

-   스트레치 사용 테이블을 포함하는 뷰에서 업데이트 또는 삭제할 수 없습니다. 그러나 스트레치 사용 테이블을 포함하는 뷰에 삽입할 수 있습니다.

-   인덱스에 대한 필터는 원격 테이블에 전파되지 않습니다.

## 참고 항목
[스트레치 데이터베이스 관리자를 실행하여 스트레치 데이터베이스에 대한 데이터베이스 및 테이블 식별](sql-server-stretch-database-identify-databases.md) [Enable Stretch Database for a database(데이터베이스에 스트레치 데이터베이스를 사용하도록 설정)](sql-server-stretch-database-enable-database.md) [Enable Stretch Database for a table(테이블에 스트레치 데이터베이스를 사용하도록 설정)](sql-server-stretch-database-enable-table.md)

<!---HONumber=AcomDC_0302_2016-->