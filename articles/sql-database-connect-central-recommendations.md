<properties 
	pageTitle="Azure SQL 데이터베이스 연결: 핵심 권장 사항 "
	description="Azure SQL 데이터베이스에 연결하기 위한 다양한 드라이버(예: ADO.NET 및 PHP)에 대한 보다 구체적인 항목의 링크를 제공하는 핵심 항목입니다. "
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/19/2015" 
	ms.author="genemi"/>


#SQL 데이터베이스 연결: 핵심 권장 사항


<!--
GeneMi , 2015-March-19 Thursday 15:41pm
sql-database-connect-central-recommendations.md
sql-database-connect-*.md

Re SqlException, not .HResult, rather .Number.
-->


이 항목에서는 Azure SQL 데이터베이스에 연결하고 상호 작용하는 데 사용할 수 있는 여러 기술에 대한 코드 샘플의 링크를 제공합니다. 기술에는 Enterprise Library, JDBC, PHP 등이 있습니다. 일반적으로 특정 연결 기술에 상관없이 적용되는 권장 사항이 제공됩니다.


##기술 독립적 권장 사항


이 섹션의 정보는 SQL 데이터베이스에 연결하는 데 사용하는 특정 기술에 상관없이 적용됩니다.


- [프로그래밍 방식으로 Azure SQL 데이터베이스에 연결하기 위한 지침](http://msdn.microsoft.com/library/azure/ee336282.aspx) - 다음 사항을 다룹니다.
 - 포트
 - 방화벽
 - 연결 문자열
- [Azure SQL 데이터베이스 리소스 관리](https://msdn.microsoft.com/library/azure/dn338083.aspx) - 다음 사항을 다룹니다.
 - 리소스 관리
 - 제한 적용
 - 제한


사용하는 연결 기술에 상관없이 SQL 데이터베이스 서버 및 개별 데이터베이스에 대한 특정 방화벽 설정은 다음과 관련이 있습니다.


- [Azure SQL 데이터베이스 방화벽](https://msdn.microsoft.com/library/azure/ee621782.aspx)


###빠른 권장 사항


####연결


- 클라이언트 연결 논리에서 기본 시간 제한을 30초로 재정의합니다.
 - 기본값 15초는 인터넷에 종속된 연결 시간으로 너무 짧습니다.
- [연결 풀](http://msdn.microsoft.com/library/8xx3tyca.aspx)을 사용하는 경우 프로그램에서 적극적으로 사용하지 않고 다시 사용할 준비를 하지 않는 경우 연결을 즉시 닫습니다.
 - 프로그램에서 다른 작업을 위해 일시 중지하지 않고 연결을 즉시 다시 사용하는 경우를 제외하고는 다음 패턴이 권장됩니다.
<br/><br/>연결을 엽니다.
<br/>연결을 통해 하나의 작업을 수행합니다.
<br/>연결을 닫습니다.<br/><br/>
- 연결 논리와 함께 다시 시도 논리를 사용하되, 일시적인 오류에 대해서만 사용합니다. SQL 데이터베이스를 사용할 때 여러 가지 이유로 인해 연결을 열거나 쿼리를 실행하려는 시도가 실패할 수 있습니다.
 - 한 가지 영구적인 실패 원인으로는 잘못된 형식의 연결 문자열을 들 수 있습니다.
 - 한 가지 일시적인 실패 원인으로는 Azure SQL 데이터베이스 시스템에 전체 부하 균형 조정이 필요한 경우를 들 수 있습니다. 일시적인 원인은 자체적으로 사라집니다. 즉, 프로그램에서 다시 시도해야 합니다.
 - 쿼리를 다시 시도할 때는 먼저 연결을 닫은 후 다른 연결을 엽니다.
- [Azure SQL 데이터베이스 방화벽](http://msdn.microsoft.com/library/ee621782.aspx)에서 포트 1433을 통해 나가는 TCP 통신을 허용해야 합니다.
 - SQL 데이터베이스 서버 또는 개별 데이터베이스에 대해 [방화벽](http://msdn.microsoft.com/library/azure/ee621782.aspx) 설정을 구성할 수 있습니다.


####인증


- Windows 인증이 아니라 SQL 데이터베이스 인증을 사용합니다.
- 기본  *master* 데이터베이스 대신 특정 데이터베이스를 지정합니다.
- 경우에 따라 사용자 이름에 접미사 *@yourservername*을 제공하거나 생략해야 합니다. 이는 사용자 도구 또는 API가 작성된 방법에 따라 결정됩니다.
 - 각 개별 기술에 대한 세부 정보를 확인하세요.
- [포함된 데이터베이스](http://msdn.microsoft.com/library/ff929071.aspx)에서 사용자를 지정하여 연결합니다.
 - 이 방법을 사용하면 마스터 데이터베이스에 로그인할 필요가 없으므로 성능 및 확장성이 향상됩니다.
 - SQL 데이터베이스에서는 Transact-SQL **USE myDatabaseName;** 문을 사용할 수 없습니다.


###일시적인 오류


일부 SQL 데이터베이스 연결 오류는 영구적이며 연결을 즉시 다시 시도할 이유가 없습니다. 다른 오류는 일시적이며, 프로그램에서 자동화된 다시 시도를 몇 번 시도하는 것이 좋습니다. 예제:


- *영구적:* 연결할 때 SQL 데이터베이스 서버 이름을 잘못 입력한 경우 다시 입력할 방법이 없습니다.
- *일시적:* SQL 데이터베이스 연결이 나중에 Azure 제한 또는 부하 분산 시스템으로 인해 강제로 종료된 경우 다시 연결을 시도하는 것이 좋습니다.


SQL 데이터베이스 호출에서 발생하는 [SqlException](https://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx)에는 해당 **Number** 속성에 숫자 오류 코드가 포함되어 있습니다. 오류 코드가 일시적 오류로 나열된 코드인 경우 프로그램에서 호출을 다시 시도해야 합니다.


- [오류 메시지(Azure SQL 데이터베이스)](http://msdn.microsoft.com/library/azure/ff394106.aspx) - **Connection-Loss Errors** 섹션은 다시 시도가 보증되는 일시적인 오류 목록입니다.
 - 예를 들어 다음과 유사한 오류 번호 40613이 발생한 경우 다시 시도합니다.<br/>*현재  'theserver' 서버의  'mydatabase' 데이터베이스를 사용할 수 없습니다.*


영구적 또는 일시적 연결 오류가 발생한 경우 추가 지원은 다음을 참조하세요.


- [Azure SQL 데이터베이스에 대한 연결 문제 해결](http://support.microsoft.com/ko-kr/kb/2980233/ko-kr)


##기술


다음 항목에는 여러 연결 기술에 대한 코드 샘플의 링크가 포함되어 있습니다.


- [Azure SQL 데이터베이스 개발: 방법 항목](http://msdn.microsoft.com/library/azure/ee621787.aspx)


엔터프라이즈 라이브러리 및 일시적인 오류 처리 클래스를 사용하는 ADO.NET은 다음을 참조하세요.


- [방법: Azure SQL 데이터베이스에 안정적으로 연결](http://msdn.microsoft.com/library/azure/dn864744.aspx)


탄력적인 확장은 다음을 참조하세요.


- [Azure SQL 데이터베이스 탄력적인 확장 미리 보기 시작](sql-database-elastic-scale-get-started.md)
- [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md)


##완전하지 않거나 오래된 게시물


이 섹션의 링크는 블로그 게시물 또는 이와 유사한 원본의 링크이므로 불완전하거나 오래되었을 수 있습니다. 그러나 일부 배경 값이 있을 수 있습니다.


- [Microsoft Azure SQL 데이터베이스에서 일시적인 오류에 대해 논리 다시 시도](http://social.technet.microsoft.com/wiki/contents/articles/4235.retry-logic-for-transient-failures-in-windows-azure-sql-database.aspx)

<!-- -->


<!--HONumber=49-->