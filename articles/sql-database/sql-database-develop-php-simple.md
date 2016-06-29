<properties
	pageTitle="Windows에서 PHP를 사용하여 SQL 데이터베이스에 연결 | Microsoft Azure"
	description="Windows 클라이언트에서 Azure SQL 데이터베이스에 연결하는 샘플 PHP 프로그램을 제시하고, 클라이언트에 필요한 필수 소프트웨어 구성 요소의 링크를 제공합니다."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="php"
	ms.topic="article"
	ms.date="04/27/2016"
	ms.author="meetb"/>


# Windows에서 PHP를 사용하여 SQL 데이터베이스에 연결


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


이 항목에서는 Windows에서 실행되는 PHP로 작성된 클라이언트 응용 프로그램으로 Azure SQL 데이터베이스에 연결하는 방법을 보여줍니다.

## 1단계: 개발 환경 구성

[PHP 개발을 위한 개발 환경 구성](https://msdn.microsoft.com/library/mt720663.aspx)

## 2단계: SQL 데이터베이스 만들기

샘플 데이터베이스를 만드는 방법을 알아보려면 [시작 페이지](sql-database-get-started.md)를 참조하세요. 안내에 따라 **AdventureWorks 데이터베이스 템플릿**을 만드는 것이 중요합니다. 아래 표시된 샘플은 **AdventureWorks 스키마**에서만 작동합니다.


## 3단계: 연결 정보 가져오기

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]


## 4단계: 샘플 코드 실행

* [PHP를 사용하여 SQL에 연결하는 개념 증명](https://msdn.microsoft.com/library/mt720665.aspx)
* [PHP로 SQL에 탄력적으로 연결](https://msdn.microsoft.com/library/mt720667.aspx)


## 다음 단계


PHP 설치 및 사용에 대한 자세한 내용은[PHP로 SQL Server 데이터베이스 액세스](http://social.technet.microsoft.com/wiki/contents/articles/1258.accessing-sql-server-databases-from-php.aspx)를 참조하세요.

## 추가 리소스

다중 테넌트 SaaS(software-as-a-service) 데이터베이스 응용 프로그램의 일반적인 데이터 아키텍처 패턴에 대한 정보는 [Azure SQL 데이터베이스를 사용한 다중 테넌트 SaaS 응용 프로그램의 설계 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md)을 참조하세요.

<!---HONumber=AcomDC_0615_2016-->