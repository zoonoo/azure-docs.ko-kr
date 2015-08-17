<properties 
	pageTitle="ADO.NET 4.5, ODBC 11 및 SQL 데이터베이스 V12에 대한 1433 이외의 포트 | Microsoft Azure"
	description="Azure SQL 데이터베이스 V12에 대한 클라이언트 연결이 프록시를 무시하고 데이터베이스와 직접 상호 작용하는 경우가 있습니다. 1433 이외의 포트가 중요해집니다."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor="" />


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/05/2015" 
	ms.author="genemi"/>


# ADO.NET 4.5, ODBC 11 및 SQL 데이터베이스 V12에 대한 1433 이외의 포트


이 항목은 Azure SQL 데이터베이스 V12 가 ADO.NET 4.5 이상을 사용하는 클라이언트의 연결 동작에 가져오는 변경 내용에 대해 설명합니다.


## 버전 확인


#### ADO.NET


- ADO.NET 4.0은 TDS 7.3 프로토콜을 지원하지만 7.4는 지원하지 않습니다.
- ADO.NET 4.5 이상은 TDS 7.4 프로토콜을 지원합니다.
- ADO.NET 4.5는 내부적으로 ODBC 11을 사용합니다.
 - ADO.NET 4.5에 적용되는 이 정보는 여ODBC 11에도 적용됩니다.


#### SQL 데이터베이스 V11 및 V12


이 항목에는 SQL 데이터베이스 V11 및 V12의 클라이언트 연결 차이점이 강조되어 있습니다.


*참고:* TRANSACT-SQL 문`SELECT @@version;`은 11. 또는 12.와 같은 숫자로 시작하는 값과 SQL 데이터베이스에 대한 V11 및 V12 의 버전 이름과 일치하는 값을 반환합니다.


## SQL 데이터베이스의 V11: 포트 1433


클라이언트 프로그램이 ADO.NET 4.5를 사용해 SQL 데이터베이스 V11에 연결하고 쿼리를 보낼 경우 내부 순서는 다음과 같습니다.


1. ADO.NET이 SQL 데이터베이스로 연결을 시도합니다.

2. ADO.NET이 포트 1433을 사용하여 미들웨어 모듈을 호출하고 해당 미들웨어가 SQL 데이터베이스에 연결합니다.

3. SQL 데이터베이스가 미들웨어로 응답을 다시 보내고 미들웨어는 ADO.NET에 대한 응답을 포트 1433으로 전달합니다.


**용어:** 이전 순서는 ADO.NET이 *프록시 경로*를 사용하여 SQL과 상호작용한다는 것으로 설명됩니다. 미들웨어가 관련되지 않은 경우 *직접 경로*가 사용되었다고 합니다.


## SQL 데이터베이스의 V12: 내부 vs 외부


V12로 연결을 위해 클라이언트 프로그램의 Azure 클라우드 경계의 *외부* 또는*내부* 실행 여부를 요청합니다. 하위 섹션에서는 일반적으로 두 가지 시나리오를 설명합니다.


#### *외부:* 클라이언트가 데스크톱 컴퓨터에서 실행됩니다.


포트 1433은 SQL 데이터베이스 클라이언트 응용 프로그램을 호스팅하는 데스크톱 컴퓨터에서 열어야 하는 유일한 포트입니다.


#### *내부:* 클라이언트가 Azure VM에서 실행됩니다.


클라이언트가 Azure 클라이언트 경계 내에서 실행되면 SQL 데이터베이스 서버와 상호작용하기 위해 *직접 경로*라는 것을 사용합니다. 연결이 설정된 후 클라이언트와 데이터베이스 사이의 추가 상호작용은 미들웨어 프록시를 관련시키지 않습니다.


순서는 다음과 같습니다.


1. ADO.NET 4.5 (또는 그 이상)는 Azure 클라우드와 간단한 상호작용을 시작하고, 동적으로 식별된 포트 번호를 받습니다.
 - 동적으로 식별된 포트 번호는 11000 \~ 11999 범위 사이입니다.

2. 그러면 ADO.NET은 미들웨어 없이 직접SQL 데이터베이스로 연결합니다.

3. 쿼리는 데이터베이스로 직접 전송되며 결과는 클라이언트에 직접 반환됩니다.


Azure 클라이언트 컴퓨터에 있는 11000 \~ 11999 범위 내의 포트가 ADO.NET 4.5와 SQL 데이터베이스 V12 사이의 클라이언트 상호작용에 사용 가능한지 확인합니다.

- 특히 해당 범위의 포트는 모든 다른 아웃바운드 차단으로부터 자유로워야 합니다.
- Azure VM에 있는 Windows 방화벽이 포트 설정을 제어합니다.


## 프록시 경로에 포함된 암시적 재시도 논리


프로덕션 환경에서 Azure SQL 데이터베이스 V11 또는 V12를 연결하는 클라이언트는 코드에 재시도 논리를 구현하는 것이 좋습니다. 사용자 지정 코드일 수도 있고, Enterprise Library 등의 API를 활용하는 코드일 수도 있습니다.


이 항목에서 앞서 설명한 프록시 경로는 재시도 논리 질문과 관련이 있습니다.


- V11에서 프록시로 작동하는 미들웨어 모듈은 일시적인 오류를 적절하게 처리하기 위해 적당한 수준의 재시도 논리를 제공했습니다.

- V12에서 프록시는 재시도 논리를 제공하지 않습니다.


두 시나리오 모두에서 클라이언트가 코드에 재시도 논리를 구현하는 것이 좋습니다. 재시도 논리를 제공하지 않는 최신 프록시 경로로 클라이언트에서의 재시도 논리 필요가 확실히 증가했습니다.


재시도 논리를 보여주는 코드 샘플은 [SQL 데이터베이스에 대한 클라이언트 빠른 시작 코드 샘플](sql-database-develop-quick-start-client-code-samples.md)을 참조하세요.


## 관련 링크


- [SQL 데이터베이스 V12의 새로운 기능](sql-database-v12-whats-new.md)

- 재시도 논리 고려 사항:[“SQL 데이터베이스에 연결: 링크, 모범 사례 및 디자인 지침” 항목의 “게이트웨이가 V12에서 재시도 논리를 더 이상 제공하지 않습니다” 섹션](sql-database-connect-central-recommendations.md#gatewaynoretry)

- ADO.NET 4.6은 2015년 7월 20일에 출시되었습니다. .NET 팀의 블로그 알림은 [여기](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx)에서 확인할 수 있습니다.

- ADO.NET 4.5는 2012년 8월 15일에 출시되었습니다. .NET 팀의 블로그 알림은 [여기](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx)에서 확인할 수 있습니다.
 - ADO.NET 4.5.1에 관한 블로그 게시물은 [여기](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx)에서 확인할 수 있습니다.

- [TDS 프로토콜 버전 목록](http://www.freetds.org/userguide/tdshistory.htm)

<!---HONumber=August15_HO6-->