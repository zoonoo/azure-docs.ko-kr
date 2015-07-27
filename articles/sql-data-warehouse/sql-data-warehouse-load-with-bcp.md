<properties
   pageTitle="bcp를 사용하여 SQL 데이터 웨어하우스로 데이터 로드 | Microsoft Azure"
   description="bcp 정의 및 데이터 웨어하우징 시나리오에 대해 사용하는 방법에 대해 알아봅니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/23/2015"
   ms.author="mausher;barbkess"/>


# bcp를 사용하여 데이터 로드
**[bcp][]**는 명령줄 대량 로드 유틸리티로, SQL Server, 데이터 파일 및 SQL 데이터 웨어하우스 간에 데이터를 복사할 수 있습니다. bcp 유틸리티를 사용하여 SQL 데이터 웨어하우스 테이블로 많은 수의 행을 가져오거나, 또는 SQL Server 테이블에서 데이터 파일로 데이터를 내보냅니다. Queryout 옵션을 사용하는 경우를 제외하고, bcp를 사용하려면 TRANSACT-SQL 지식이 없어도 됩니다.

bcp는 SQL 데이터 웨어하우스 데이터베이스 내부 및 외부로 더 작은 데이터 집합을 이동하는 빠르고 쉬운 방법입니다. bcp를 통한 로드/추출을 권장하는 정확한 크기의 데이터는 Azure 데이터 센터에 연결된 네트워크에 따라 다릅니다. 일반적으로 차원 테이블을 로드하고 추출할 수 있지만 매우 큰 팩트 테이블은 추출 하거나 로드하는 데 시간이 많이 걸릴 수 있습니다.

bcp를 사용하여 다음을 수행할 수 있습니다.- SQL 데이터 웨어하우스로 데이터를 로드하는 간단한 명령줄 유틸리티를 사용 합니다. - SQL 데이터 웨어하우스에서 데이터를 추출하는 간단한 명령줄 유틸리티를 사용합니다.

이 자습서에서는 다음 방법을 보여줍니다. - 명령에서 bcp를 사용하여 테이블로 데이터 가져오기 - bcp out 명령을 사용하여 테이블에서 데이터 내보내기

## 필수 조건
이 자습서를 실행하려면, SQL 데이터 웨어하우스 데이터베이스가 필요합니다. - bcp 명령줄 유틸리티가 설치됩니다. - SQLCMD 명령줄 유틸리티가 설치되어 있습니다.

>[AZURE.NOTE][Microsoft 다운로드 센터][]에서 bcp 및 sqlcmd 유틸리티를 다운로드할 수 있습니다.

##SQL 데이터 웨어하우스로 데이터 로드
이 자습서에서는 Azure SQL 데이터 웨어하우스에서 테이블을 만들고 테이블로 데이터를 가져옵니다.

### 1단계: Azure SQL 데이터 웨어하우스에서 테이블 만들기
명령 프롬프트에서 다음 명령을 사용하여 인스턴스에 연결하고 값을 적절하게 대체합니다.

```
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I
```
연결되면, sqlcmd 프롬프트에서 다음 테이블 스크립트를 복사한 다음 Enter 키를 누릅니다.

```
CREATE TABLE DimDate2 (DateId INT NOT NULL, CalendarQuarter TINYINT NOT NULL, FiscalQuarter TINYINT NOT NULL);
```

다음 줄에 GO 일괄 처리 종결자를 입력하고 Enter 키를 눌러 문을 실행합니다.

```
GO
```

### 2단계: 원본 데이터 파일 만들기

메모장을 열고 새 파일로 다음 데이터 줄을 복사합니다.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

로컬 임시 디렉터리, C:\\Temp\\DimDate2.txt로 저장합니다.

### 3단계: 데이터 연결 및 가져오기
bcp를 사용하여, 연결하고 값을 적절하게 대체하는 다음 명령을 사용하여 데이터를 가져올 수 있습니다.

```
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -w -t
```

앞에서와 같이 sqlcmd를 사용하여 연결하고 다음 TSQL 명령을 실행하여 데이터가 로드되었음을 확인할 수 있습니다.

```
SELECT * FROM DimDate2 ORDER BY 1;
GO
```

다음 결과를 반환해야 합니다.

DateId |CalendarQuarter |FiscalQuarter
----------- |--------------- |-------------
20150101 |1 |3
20150201 |1 |3
20150301 |1 |3
20150401 |2 |4
20150501 |2 |4
20150601 |2 |4
20150701 |3 |1
20150801 |3 |1
20150801 |3 |1
20151001 |4 |2
20151101 |4 |2
20151201 |4 |2

## SQL 데이터 웨어하우스에서 데이터 내보내기
이 자습서에서는 SQL 데이터 웨어하우스의 테이블에서 데이터 파일이 만들어집니다. 위에서 만든 데이터를 DimDate2_export.txt라는 새 데이터 파일로 내보냅니다.

### 1단계: 데이터 내보내기

bcp 유틸리티를 사용하여, 값을 적절하게 대체하는 다음 명령을 사용하여 연결하고 데이터를 내보낼 수 있습니다.

```
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -w -t
```
새 파일을 열어 데이터를 올바르게 내보냈는지 확인할 수 있습니다. 파일의 데이터는 아래 텍스트와 일치해야 합니다.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

>[AZURE.NOTE]분산된 시스템의 특성상 데이터 순서는 SQL 데이터 웨어하우스 데이터베이스에서 동일하지 않을 수 있습니다. 선택적으로 queryout 매개변수를 사용하여 실행할 TRANSACT-SQL 쿼리를 지정합니다.

## 다음 단계
로드 개요는 [SQL 데이터 웨어하우스로 데이터 로드][]를 참조하세요. 더 많은 개발 팁은 [SQL 데이터 웨어하우스 개발 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->

[SQL 데이터 웨어하우스로 데이터 로드]: ./sql-data-warehouse-overview-load/
[SQL 데이터 웨어하우스 개발 개요]: ./sql-data-warehouse-overview-develop/

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx


<!--Other Web references-->
[Microsoft 다운로드 센터]: http://www.microsoft.com/download/details.aspx?id=36433

<!---HONumber=July15_HO3-->