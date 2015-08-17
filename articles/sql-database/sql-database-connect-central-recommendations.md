<properties 
	pageTitle="SQL 데이터베이스 연결: 링크, 모범 사례, 설계 지침" 
	description="시작지점 항목은 Ado.net 및 php같은 기술에서 Azure SQL 데이터베이스에 연결해주는 클라이언트 프로그램과 링크를 수집합니다." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/05/2015" 
	ms.author="genemi"/>


# SQL 데이터베이스 연결: 링크, 모범 사례, 설계 지침


이 항목은 Azure SQL 데이터베이스에 대한 클라이언트 연결을 시작할 좋은 기회입니다. 이 항목에서는 Azure SQL 데이터베이스에 연결하고 상호 작용하는 데 사용할 수 있는 여러 기술에 대한 코드 샘플의 링크를 제공합니다. 기술에는 Enterprise Library, JDBC, PHP 등이 있습니다. 일반적으로 특정 연결 기술에 상관없이 적용되는 권장 사항이 제공됩니다.


## 기술 독립적 권장 사항


이 섹션의 정보는 SQL 데이터베이스에 연결하는 데 사용하는 특정 기술에 상관없이 적용됩니다.


- [Azure SQL 데이터베이스 프로그래밍 방식으로 연결 하기 위한 지침](http://msdn.microsoft.com/library/azure/ee336282.aspx) -다음 사항을 다룹니다.
 - 포트
 - 방화벽
 - 연결 문자열
- [Azure SQL 데이터베이스 리소스 관리](https://msdn.microsoft.com/library/azure/dn338083.aspx) -다음 사항을 다룹니다.
 - 리소스 관리
 - 제한 적용
 - 제한


사용하는 연결 기술에 상관없이 SQL 데이터베이스 서버 및 개별 데이터베이스에 대한 특정 방화벽 설정은 다음과 관련이 있습니다.


- [Azure SQL 데이터베이스 방화벽](https://msdn.microsoft.com/library/azure/ee621782.aspx)


## 인증 권장 사항


- Windows 인증이 아니라 SQL 데이터베이스 인증을 사용합니다.
- *마스터* 데이터의 기본값 대신 특정데이터 베이스를 지정합니다.
- 경우에 따라 사용자 이름에*@yourservername*을 붙이거나 생략해야 합니다. 이는 사용자 도구 또는 API가 작성된 방법에 따라 결정됩니다.
 - 각 개별 기술에 대한 세부 정보를 확인하세요.
- [포함 된 데이터베이스](http://msdn.microsoft.com/library/ff929071.aspx)에서 사용자를 지정하여 연결합니다.
 - 이 방법을 사용하면 마스터 데이터베이스에 로그인할 필요가 없으므로 성능 및 확장성이 향상됩니다.
 - SQL 데이터베이스에서는 Transact-SQL **USE myDatabaseName;** 문장을 사용할 수 없습니다.


## 연결 권장 사항


- 클라이언트 연결 논리에서 기본 시간 제한을 30초로 재정의합니다.
 - 기본값 15초는 인터넷에 종속된 연결 시간으로 너무 짧습니다.
- [Azure SQL 데이터베이스 방화벽](http://msdn.microsoft.com/library/ee621782.aspx) 에서 포트 1433을 통해 나가는 TCP 통신을 허용해야 합니다.
 - SQL 데이터베이스 서버 또는 개별 데이터베이스에 대해 [방화벽](http://msdn.microsoft.com/library/azure/ee621782.aspx) 설정을 구성할 수 있습니다.
- [연결 풀](http://msdn.microsoft.com/library/8xx3tyca.aspx)을 사용하는 경우 프로그램에서 적극적으로 사용하지 않고 다시 사용할 준비를 하지 않는 경우 연결을 즉시 닫습니다.
 - 프로그램에서 다른 작업을 위해 일시 중지하지 않고 연결을 즉시 다시 용하는 경우를 제외하고는 다음패턴이 권장됩니다: <br/><br/>연결을 엽니다. <br/>연결을 통해 하나의 작업을 수행합니다. <br/>연결을 닫습니다.<br/><br/>
- 연결 논리와 함께 다시 시도 논리를 사용하되, 일시적인 오류에 대해서만 사용합니다. SQL 데이터베이스를 사용할 때 여러 가지 이유로 인해 연결을 열거나 쿼리를 실행하려는 시도가 실패할 수 있습니다.
 - 한 가지 영구적인 실패 원인으로는 잘못된 형식의 연결 문자열을 들 수 있습니다.
 - 한 가지 일시적인 실패 원인으로는 Azure SQL 데이터베이스 시스템에 전체 부하 균형 조정이 필요한 경우를 들 수 있습니다. 일시적인 원인은 자체적으로 사라집니다. 즉, 프로그램에서 다시 시도해야 합니다.
 - 쿼리를 다시 시도할 때는 먼저 연결을 닫은 후 다른 연결을 엽니다.


### V12에서 1433 이외의 포트


Azure SQL 데이터베이스 V12에 대한 클라이언트 연결이 프록시를 무시하고 데이터베이스와 직접 상호 작용하는 경우가 있습니다. 1433 이외의 포트가 중요해집니다. 자세한 내용은 <br/>[ADO.NET 4.5, ODBC 11 및 SQL 데이터베이스 V12에 대한 1433 이외의 포트](sql-database-develop-direct-route-ports-adonet-v12.md)를 참조하세요.


다음 섹션에서는 재시도 논리와 일시적인 오류 처리에 대해 더 설명합니다.


## 일시적인 오류 및 재시도 논리


Azure 같은 클라우드 서비스와 해당 SQL 데이터베이스 서비스는 작업 로드 균형 조정 및 관리 리소스의 무한 .작업을 해결 합니다. 동일한 컴퓨터에서 제공하는 두 데이터베이스가 동시에 지나치게 많은 처리를 하게 되면 관리 시스템은 한 데이터베이스의 작업 부하를 용량이 여유가 있는 다른 리소스로 이동시켜야 할 필요성을 감지합니다.


이동하는 동안 데이터베이스 일시적으로 사용할 수 있습니다. 새 연결을 차단 하거나, 클라이언트의 연결을 끊는 원인을 발생시킵니다. 리소스 시프트는 일시적이지만, 몇 초 또는 몇 분만에 자동으로 해결할 수 있습니다. 이동이 완료된 후, 클라이언트 프로그램의 연결을 다시 설정하고 작업을 계속할 수 있습니다. 처리 과정에서 일시 중지는 클라이언트 프로그램의 피할 수 있는 오류보다 좋습니다.


SQL 데이터베이스에서 오류가 발생하면 [SqlException](https://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx)이 발생합니다. `SqlException`은 해당 **Number** 속성에 숫자 오류 코드를 포함합니다. 오류 코드가 일시적 오류로 나열된 코드인 경우 프로그램에서 호출을 다시 시도해야 합니다.


- [SQL 데이터베이스 클라이언트 프로그램에 대한 오류 메시지](sql-database-develop-error-messages.md)
 - **일시적인 오류, 연결 손실 오류** 섹션은 자동으로 재시도가 보증되는 일시적인 오류 목록입니다.
 - 예를 들어 <br/>과 유사한 오류 번호 40613이 발생한 경우 *'theserver' 서버의 'mydatabase' 데이터베이스가 현재 사용 불가능합니다.*


일시적인 *오류*는 일시적인 *장애*라고도 합니다. 이 항목에서는 이 두 용어를 동의어라고 간주합니다.


일시적이던 일시적이지 않던 연결 오류가 발생한 경우 추가 지원은 다음을 참조하세요.


- [Azure SQL 데이터베이스에 대한 연결 문제 해결](http://support.microsoft.com/kb/2980233/)


재시도 논리를 보여주는 코드 샘플 항목에 대한 링크는 다음을 참조하세요.


- [SQL 데이터베이스에 대한 클라이언트 빠른 시작 코드 샘플](sql-database-develop-quick-start-client-code-samples.md)


<a id="gatewaynoretry" name="gatewaynoretry">&nbsp;</a>


## 미들웨어 프록시 및 재시도 논리


V11 및 ADO.NET 4.5 클라이언트 사이를 중재하는 미들웨어 프록시는 재시도 논리를 사용하여 일시적인 오류의 작은 하위 집합을 정상적으로 처리합니다. 프록시가 두 번째 시도에 성공적으로 연결하는 경우 클라이언트 프로그램은 첫 번째 시도가 실패했음을 알지 못합니다.


V12 프록시는 일시적인 오류의 작은 하위 집합을 처리합니다. 다른 V12의 경우 SQL 데이터베이스에 직접 연결하는 뛰어난 속도를 위해 프록시가 무시됩니다. 이러한 변경 내용 때문에 클라이언트 ADO.NET 4.5 프로그램에서 Azure SQL 데이터베이스 V12는 Microsoft SQL Server처럼 보입니다.


재시도 논리를 보여 주는 코드 샘플은 <br/>[SQL 데이터베이스에 대한 클라이언트 빠른 시작 코드 샘플](sql-database-develop-quick-start-client-code-samples.md)을 참조하세요.


> [AZURE.TIP]프로덕션 환경에서 Azure SQL 데이터베이스 V11 또는 V12에 연결하는 클라이언트는 해당 코드에서 재시도 논리를 구현하는 것이 좋습니다. 사용자 지정 코드일 수도 있고, Enterprise Library 등의 API를 활용하는 코드일 수도 있습니다.


## 기술


다음 항목에서는 여러 언어 및 클라이언트 프로그램에서 Azure SQL 데이터베이스에 연결할 때 사용할 수 있는 드라이버 기술에 대한 코드 샘플에 대한 링크를 포함 합니다.


Windows, Linux 및 Mac OS X 모두에서 실행 되는 클라이언트에 대한 다양한 코드 샘플이 제공 됩니다.


**일반 샘플:** 프로그래밍 언어, PHP, Python, Node.js 및 c#.NET의 다양한 코드 샘플입니다. 또한, Windows, LInux 및 Mac OS X에서 실행되는 클라이언트에 샘플이 제공됩니다.


- [클라이언트 개발 및 SQL 데이터베이스에 대한 빠른 시작 코드 샘플](sql-database-develop-quick-start-client-code-samples.md)
- [Azure SQL 데이터베이스 개발: 방법 도움말 항목](http://msdn.microsoft.com/library/azure/ee621787.aspx)


**탄력적인 확장:** 탄력적인 확장 데이터베이스의 연결에 대한 자세한 내용은 다음을 참조하세요.


- [Azure SQL 데이터베이스 탄력적인 확장 미리 보기 시작](sql-database-elastic-scale-get-started.md)
- [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md)


**드라이버 라이브러리:** 권장 버전을 포함한 연결 드라이버 라이브러리에 대한 자세한 내용은 다음을 참조하세요.


- [SQL 데이터베이스 및 SQL Server에 대한 연결 라이브러리](sql-database-libraries.md)


## 참고 항목


- [첫 Azure SQL 데이터베이스 만들기](sql-database-get-started.md)

 

<!---HONumber=August15_HO6-->