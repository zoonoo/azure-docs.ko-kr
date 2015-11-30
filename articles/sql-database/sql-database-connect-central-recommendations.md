<properties 
	pageTitle="SQL 데이터베이스에 연결: 모범 사례 | Microsoft Azure" 
	description="ADO.NET 및 PHP와 같은 기술에서 Azure SQL 데이터베이스에 연결하는 클라이언트 프로그램에 대한 링크 및 모범 사례 권장 사항을 수집하는 시작 지점 항목입니다." 
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
	ms.date="11/13/2015" 
	ms.author="genemi"/>


# SQL 데이터베이스에 연결: 모범 사례 및 설계 지침


이 항목은 Azure SQL 데이터베이스에 대한 클라이언트 연결을 시작할 좋은 기회입니다. 이 항목에서는 Azure SQL 데이터베이스에 연결하고 상호 작용하는 데 사용할 수 있는 여러 기술에 대한 코드 샘플의 링크를 제공합니다. 기술에는 Enterprise Library, JDBC, PHP 등이 있습니다. 제공된 정보는 SQL 데이터베이스에 연결하는 데 사용하는 특정 기술에 상관없이 적용됩니다.


<a id="a-tech-independent-recommend" name="a-tech-independent-recommend"></a>

## 기술 독립적 권장 사항


- [Azure SQL 데이터베이스 프로그래밍 방식으로 연결 하기 위한 지침](http://msdn.microsoft.com/library/azure/ee336282.aspx) -다음 사항을 다룹니다.
 - [포트 및 방화벽](sql-database-configure-firewall-settings.md)
 - 연결 문자열
- [Azure SQL 데이터베이스 리소스 관리](http://msdn.microsoft.com/library/azure/dn338083.aspx) - 다음 사항을 다룹니다.
 - 리소스 관리
 - 제한 적용
 - 제한


<a id="b-authentication-recommend" name="b-authentication-recommend"></a>

## 인증 권장 사항


- Azure SQL 데이터베이스에서 사용할 수 없는 Windows 인증이 아닌 Azure SQL 데이터베이스 인증을 사용합니다.
- *마스터* 데이터의 기본값 대신 특정데이터 베이스를 지정합니다.
 - SQL 데이터베이스에서는 Transact-SQL **USE myDatabaseName;** 문장을 사용하여 다른 데이터베이스로 전환할 수 없습니다.


### 포함된 사용자


SQL 데이터베이스에 사용자로 사용자를 추가할 때 옵션이 있습니다.

- 암호를 사용하여 *로그인*을 **마스터** 데이터베이스에 추가한 다음 해당 *사용자*를 동일한 서버의 하나 이상의 다른 데이터베이스에 추가합니다.

- 암호를 사용하여 *포함된 사용자*를 하나 이상의 데이터베이스에 **마스터**의 모든 *로그인*에 연결하지 않고 추가합니다.


포함된 사용자 접근 방식은 모두 장점과 단점이 있습니다.

- 장점은 데이터베이스의 모든 사용자가 포함된 사용자일 때 한 Azure SQL 데이터베이스 서버에서 다른 데이터베이스로 데이터베이스를 쉽게 이동할 수 있는 것입니다.

- 단점은 일상적인 관리의 어려움입니다. 예:
 - 하나의 로그인을 삭제하는 것 보다 여러 포함된 사용자를 삭제하는 것이 더 어렵습니다.
 - 여러 데이터베이스의 포함된 사용자인 사람은 기억하거나 업데이트할 더 많은 암호가 있을 수 있습니다.


자세한 정보는 - [포함된 데이터베이스 사용자 - 데이터베이스를 이식 가능하게 만들기](http://msdn.microsoft.com/library/ff929188.aspx)를 참조하세요.


<a id="c-connection-recommend" name="c-connection-recommend"></a>

## 연결 권장 사항


- 클라이언트 연결 논리에서 기본 시간 제한을 30초로 재정의합니다.
 - 기본값 15초는 인터넷에 종속된 연결 시간으로 너무 짧습니다.


- 클라이언트 프로그램을 호스팅하는 컴퓨터에서 방화벽이 포트 1433에서 발신 TCP 통신을 허용하는지 확인합니다.


- Azure VM(가상 컴퓨터)에서 클라이언트가 실행되는 동안 클라이언트 프로그램이 SQL 데이터베이스 V12에 연결하는 경우 VM에서 포트 범위 11000-11999 및 14000-14999를 열어야 합니다. 자세한 내용을 보려면 [여기](sql-database-develop-direct-route-ports-adonet-v12.md)를 클릭하세요.


- *일시적인 오류* 를 처리하려면 Azure SQL 데이터베이스와 상호 작용하는 클라이언트 프로그램에 [ *다시 시도* 논리](#TransientFaultsAndRetryLogicGm)를 추가합니다.


### 연결 풀


[연결 풀](http://msdn.microsoft.com/library/8xx3tyca.aspx)을 사용하는 경우 프로그램에서 활발하게 사용하지 않고 다시 사용할 준비를 하지 않으면 연결을 즉시 닫습니다.

프로그램에서 다른 작업을 위해 일시 중지하지 않고 연결을 즉시 다시 사용하는 경우를 제외하고는 다음 패턴이 권장됩니다.

- 연결을 엽니다.
- 연결을 통해 하나의 작업을 수행합니다.
- 연결을 닫습니다.


### V12에서 1433 이외의 포트


Azure SQL 데이터베이스 V12에 대한 클라이언트 연결이 프록시를 무시하고 데이터베이스와 직접 상호 작용하는 경우가 있습니다. 1433 이외의 포트가 중요해집니다. 자세한 내용은 <br/>
[ADO.NET 4.5 및 SQL 데이터베이스 V12에 대한 1433 이외의 포트](sql-database-develop-direct-route-ports-adonet-v12.md)를 참조하세요.


다음 섹션에서는 재시도 논리와 일시적인 오류 처리에 대해 더 설명합니다.



<a name="TransientFaultsAndRetryLogicGm" id="TransientFaultsAndRetryLogicGm"></a>

&nbsp;

## 일시적인 오류 및 재시도 논리


SQL 데이터베이스 서비스에 과도한 워크로드 부하가 발생하는 경우 Azure 시스템은 서버를 동적으로 다시 구성 하는 기능이 있습니다.

그러나 재구성 중 클라이언트 프로그램의 SQL 데이터베이스에 대한 연결이 손실될 수 있습니다. 이 오류를 *일시적 장애*라고 합니다.

클라이언트 프로그램에 재시도 논리가 있는 경우, 자체 해결을 위한 일시적 오류 시간이 지난 후 연결을 다시 시도할 수 있습니다.

첫 번째 재시도 전에 5초간 지연하는 것이 좋습니다. 5초보다 짧은 지연 후 재시도는 클라우드 서비스에 많은 위험이 있습니다. 각 후속 재시도에 대해 지연 시간은 최대 60초까지 기하급수적으로 증가해야 합니다.

ADO.NET을 사용하는 클라이언트에 대한 *차단 기간*의 설명은 [SQL Server 연결 풀링(ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)에서 사용 가능합니다.


재시도 논리를 보여 주는 코드 샘플은 [SQL 데이터베이스에 대한 클라이언트 빠른 시작 코드 샘플](sql-database-develop-quick-start-client-code-samples.md)을 참조하세요.


### 일시적인 오류에 대한 오류 번호


SQL 데이터베이스에서 오류가 발생하면 [SqlException](http://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx)이 발생합니다. **SqlException**은 해당 **Number**속성에 숫자 오류 코드를 포함합니다. 오류 코드가 일시적 오류로 나열된 코드인 경우 프로그램에서 호출을 다시 시도해야 합니다.


- [SQL 데이터베이스 클라이언트 프로그램에 대한 오류 메시지](sql-database-develop-error-messages.md#bkmk_connection_errors)
 - **일시적인 오류, 연결 손실 오류** 섹션은 자동으로 재시도가 보증되는 일시적인 오류 목록입니다.
 - 예를 들어 다음과 유사한 오류 번호 40613이 발생한 경우 다시 시도합니다.<br/>*'theserver' 서버의 'mydatabase' 데이터베이스를 현재 사용할 수 없습니다.*


자세한 내용은 다음을 참조하세요.
- [Azure SQL 데이터베이스 개발: 방법 도움말 항목](http://msdn.microsoft.com/library/azure/ee621787.aspx)
- [Azure SQL 데이터베이스에 대한 연결 문제 해결](http://support.microsoft.com/kb/2980233/)


<a id="e-technologies" name="e-technologies"></a>

## 기술


다음 항목에서는 여러 언어 및 클라이언트 프로그램에서 Azure SQL 데이터베이스에 연결할 때 사용할 수 있는 드라이버 기술에 대한 코드 샘플에 대한 링크를 포함 합니다.


Windows, Linux 및 Mac OS X 모두에서 실행 되는 클라이언트에 대한 다양한 코드 샘플이 제공 됩니다.


**일반 샘플:** PHP, Python, Node.js 및 .NET C#을 비롯한 다양한 프로그래밍 언어에 대한 [코드 샘플](sql-database-develop-quick-start-client-code-samples.md)이 있습니다. 또한, Windows, LInux 및 Mac OS X에서 실행되는 클라이언트에 샘플이 제공됩니다.


**탄력적인 확장:** 탄력적인 확장 데이터베이스의 연결에 대한 자세한 내용은 다음을 참조하세요.

- [Azure SQL 데이터베이스 탄력적인 확장 미리 보기 시작](sql-database-elastic-scale-get-started.md)
- [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md)


**드라이버 라이브러리:** 권장 버전을 포함한 연결 드라이버 라이브러리에 대한 자세한 내용은 다음을 참조하세요.

- [SQL 데이터베이스 및 SQL Server에 대한 연결 라이브러리](sql-database-libraries.md)

<!---HONumber=Nov15_HO4-->