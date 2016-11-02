<properties
	pageTitle="Windows에서 JDBC를 사용하여 SQL 데이터베이스 연결 | Microsoft Azure"
	description="Azure SQL 데이터베이스에 연결하는 데 사용할 수 있는 Java 코드 샘플을 제시합니다. 이 샘플에서는 JDBC를 사용하며, Windows 클라이언트 컴퓨터에서 실행됩니다."
	services="sql-database"
	documentationCenter=""
	authors="LuisBosquez"
	manager="jhubbard"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="drivers"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="lbosq"/>


# Windows에서 Java JDBC를 사용하여 SQL 데이터베이스에 연결합니다.


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


이 항목에서는 Azure SQL 데이터베이스에 연결하는 데 사용할 수 있는 Java 코드 샘플을 제시합니다. Java 샘플을 실행하려면 Java 개발 키트(JDK) 버전 1.8이 필요합니다. 이 샘플에서는 JDBC 드라이버를 사용하여 Azure SQL 데이터베이스에 연결합니다.

## 1단계: 개발 환경 구성

[Java 개발을 위한 개발 환경 구성](https://msdn.microsoft.com/library/mt720658.aspx)

## 2단계: SQL 데이터베이스 만들기

데이터베이스를 만드는 방법을 알아보려면 [시작 페이지](sql-database-get-started.md)를 참조하세요.

## 3단계: 연결 문자열 가져오기

[AZURE.INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [AZURE.NOTE] JTDS JDBC 드라이버를 사용하는 경우 연결 문자열의 URL에 "ssl=require"를 추가해야 하며, JVM "-Djsse.enableCBCProtection=false"에 대해 다음 옵션을 설정해야 합니다. 이 JVM 옵션은 보안 취약성에 대한 픽스를 사용하지 않도록 설정하므로 이 옵션을 설정하기 전에 관련된 위험을 이해해야 합니다.

## 4단계: 샘플 코드 실행

* [Java를 사용하여 SQL에 연결하는 개념 증명](https://msdn.microsoft.com/library/mt720656.aspx)

## 다음 단계

* [Java 개발자 센터](/develop/java/)를 방문합니다.
* [SQL 데이터베이스 개발 개요](sql-database-develop-overview.md)를 검토합니다.
* [SQL Server용 Microsoft JDBC Driver](https://msdn.microsoft.com/library/mt484311.aspx)에 대한 추가 정보

## 추가 리소스 

* [Azure SQL 데이터베이스를 사용한 다중 테넌트 SaaS 응용 프로그램 디자인 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* 모든 [SQL 데이터베이스의 기능](https://azure.microsoft.com/services/sql-database/)을 탐색합니다.

<!---HONumber=AcomDC_0622_2016-->