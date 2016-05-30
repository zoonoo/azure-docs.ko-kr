<properties
	pageTitle="스트레치 데이터베이스에 대한 제한 사항 | Microsoft Azure"
	description="스트레치 데이터베이스에 대한 제한 사항에 대해 알아봅니다."
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
	ms.date="05/17/2016"
	ms.author="douglasl"/>

# 스트레치 데이터베이스에 대한 제한

스트레치 사용 테이블에 대한 제한 사항 및 현재 테이블에 대해 스트레치를 사용하지 못하도록 하는 제한 사항에 대해 알아봅니다.

##  <a name="Caveats"></a> 스트레치 사용 테이블에 대한 제한 사항

스트레치 사용 테이블에는 다음 제한 사항이 있습니다.

### 제약 조건

-   마이그레이션된 데이터를 포함하는 Azure 테이블에서 UNIQUE 제약 조건 및 PRIMARY KEY 제약 조건에 대해 고유성이 적용되지 않습니다.

### DML 작업

-   스트레치 사용 테이블 또는 스트레치 사용 테이블을 포함하는 뷰에서 행을 업데이트 또는 삭제할 수 없습니다.

-   연결된 서버의 스트레치 사용 테이블에 행을 삽입할 수 없습니다.

### 인덱스

-   스트레치 사용 테이블을 포함하는 뷰의 인덱스를 만들 수 없습니다.

-   SQL Server 인덱스에 대한 필터는 원격 테이블에 전파되지 않습니다.

##  <a name="Limitations"></a> 현재 테이블에 대해 스트레치를 사용하지 못하도록 하는 제한 사항

다음 항목이 현재 테이블에 대해 스트레치를 사용하지 못하도록 합니다.

### 테이블 속성

-   1,023개보다 많은 열 또는 998개보다 많은 인덱스가 있는 테이블

-   FILESTREAM 데이터가 있는 테이블 또는 FileTables

-   복제되거나 변경 내용 추적 또는 변경 데이터 캡처를 적극적으로 사용하는 테이블

-   메모리 최적화된 테이블

### 데이터 형식

-   text, ntext 및 image

-   timestamp

-   sql\_variant

-   XML

-   geometry, geography, hierarchyid 및 CLR 사용자 정의 형식을 포함한 CLR 데이터 형식

### 열 형식

-   COLUMN\_SET

-   계산된 열

### 제약 조건

-   기본 제약 조건 및 Check 제약 조건

-   테이블을 참조하는 외래 키 제약 조건. 부모-자식 관계(예: Order 및 Order\_Detail)에서 자식 테이블(Order\_Detail)에는 스트레치를 사용할 수 있지만 부모 테이블(Order)에는 사용할 수 없습니다.

### 인덱스

-   전체 텍스트 인덱스

-   XML 인덱스

-   공간 인덱스

-   테이블을 참조하는 인덱싱된 뷰

## 참고 항목

[스트레치 데이터베이스 관리자를 실행하여 스트레치 데이터베이스에 대한 데이터베이스 및 테이블 식별](sql-server-stretch-database-identify-databases.md)

[데이터베이스에 대해 스트레치 데이터베이스를 사용하도록 설정](sql-server-stretch-database-enable-database.md)

[테이블에 대해 스트레치 데이터베이스를 사용하도록 설정](sql-server-stretch-database-enable-table.md)

<!---HONumber=AcomDC_0518_2016-->