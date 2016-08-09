<properties
   pageTitle="Azure SQL 데이터베이스 솔루션 빠른 시작 | Microsoft Azure"
   description="Azure SQL 데이터베이스 솔루션에 대한 자세한 정보"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-quickstart"
   ms.date="06/22/2016"
   ms.author="carlrab"/>

# Azure SQL 데이터베이스 솔루션 빠른 시작 탐색

이 문서에서는 Azure SQL 데이터베이스 솔루션 빠른 시작에 대해 대략적으로 설명합니다. 이러한 빠른 시작 설명서는 실제 시나리오를 기준으로 완전한 솔루션에서 SQL 데이터베이스를 사용하는 방법을 보여 줍니다. 특정 Azure SQL 데이터베이스 기능 사용을 보여 주는 간단한 단계별 자습서를 보려면 [Azure SQL 데이터베이스 자습서 탐색](sql-database-explore-tutorials.md)을 참조하세요.

## 여러 풀에 걸친 리소스 사용 데이터 수집 및 모니터링

이 솔루션 빠른 시작에서는 구독의 여러 풀에 걸쳐 Azure SQL 데이터베이스 리소스 사용량을 수집 및 모니터링하기 위한 솔루션을 제공합니다. 구독에 데이터베이스가 많은 경우 각 탄력적 풀을 개별적으로 모니터링하는 작업은 번거롭습니다. 이 문제를 해결하기 위해 리소스 사용량을 모니터링하고 분석하기 위해 여러 풀 및 해당 데이터베이스에서 리소스 사용 데이터를 수집하기 위해 SQL 데이터베이스 PowerShell cmdlet 및 T-SQL 쿼리를 결합할 수 있습니다.

GitHub SQL Server 예제 리포지토리의 [PowerShell 및 Power BI를 사용하여 SQL 데이터베이스에서 여러 탄력적 풀 관리](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools)에서는 powershell 스크립트 및 T-SQL 쿼리 집합과 이러한 기능이 수행하는 작업 및 사용 방법에 대한 설명서를 제공합니다.

## SaaS 시나리오에서 탄력적 풀을 사용하여 시작

이 솔루션 빠른 시작 설명서는 탄력적 풀을 활용하여 SaaS 응용 프로그램의 비용 효과적이고 확장 가능한 데이터베이스 백 엔드를 제공하는 SaaS(Softwware-as-a-Solution) 시나리오에 대한 솔루션을 제공합니다. 이 솔루션에서는 Azure 포털을 보완하는 사용자 지정 대시보드를 사용하여 부하 생성기가 탄력적 풀에서 생성하는 부하를 시각화할 수 있는 웹앱 구현 과정을 안내합니다.

GitHub SQL Server 샘플 리포지토리의 [Saas에 대한 탄력적 풀 사용자 지정 대시보드](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard)에서는 부하 생성기와 모니터링 웹앱 및 이러한 기능이 수행하는 작업과 사용 방법에 대한 설명서를 제공합니다.

## Entity Framework 및 Code First 개발을 사용하여 Azure SQL 데이터베이스 만들기

이 비디오 및 샘플은 새 데이터베이스를 대상으로 하는 Code First 개발에 대한 소개를 제공합니다. 이 시나리오는 존재하지 않고 Code First가 만드는 데이터베이스 또는 Code First가 새로운 테이블도 추가하는 빈 데이터베이스를 대상으로 하는 것을 포함합니다. Code First를 사용하면 C# 또는 VB.Net 클래스를 사용하여 모델을 정의할 수 있습니다. 추가 구성은 클래스 및 속성의 특성을 사용하거나 흐름 API를 사용하여 필요에 따라 수행할 수 있습니다. [새 데이터베이스에 대한 Code First](https://msdn.microsoft.com/data/jj193542.aspx)를 참조하세요.

## Entity Framework 응용 프로그램에 탄력적 데이터베이스 도구 통합

이 샘플에서는 [탄력적 데이터베이스 도구](sql-database-elastic-scale-get-started.md)의 기능을 통합하는 데 필요한 Entity Framework 응용 프로그램의 변경 내용을 보여 줍니다. 여기서는 [분할된 데이터베이스 맵 관리](sql-database-elastic-scale-shard-map-management.md) 및 [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md) 작성, Entity Framework Code First 접근 방식 사용을 중점적으로 다룹니다. 이 샘플 전체에서는 [EF용 Code First – New Database 샘플](http://msdn.microsoft.com/data/jj193542.aspx)을 실행 예제로 사용합니다. 이 문서와 함께 제공되는 샘플 코드는 Visual Studio 코드 샘플에 포함된 탄력적 데이터베이스 도구의 샘플 세트 일부입니다. [Entity Framework와 함께 탄력적 데이터베이스 클라이언트 라이브러리](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)를 참조하세요.

## 탄력적 데이터베이스 도구 및 행 수준 보안을 제공하는 다중 테넌트 응용 프로그램

이 샘플에서는 [행 수준 보안](https://msdn.microsoft.com/library/dn765131)으로 [탄력적 데이터베이스 도구](sql-database-elastic-scale-get-started.md)의 기능을 통합하는 데 필요한 Entity Framework 응용 프로그램의 변경 내용을 보여 줍니다. 이 샘플에서는 이러한 기술을 함께 사용하여 ADO.NET SqlClient 및/또는 Entity Framework를 통해 다중 테넌트 분할된 데이터베이스를 지원하는 우수한 확장성의 응용 프로그램을 구축하는 방법을 보여 줍니다. 이 샘플은 다중 테넌트 분할 데이터베이스에 대한 지원을 추가하여 [Entity Framework와 함께 탄력적 데이터베이스 클라이언트 라이브러리](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)를 확장합니다. 테넌트 네 명과 다중 테넌트 분할 데이터베이스 두 개를 사용하여 블로그 및 게시물을 만드는 간단한 콘솔 응용 프로그램을 구축하는 프로젝트입니다. [탄력적 데이터베이스 도구 및 행 수준 보안을 제공하는 다중 테넌트 응용 프로그램](sql-database-elastic-tools-multi-tenant-row-level-security.md)을 참조하세요.

## Tailspin 설문 조사 응용 프로그램

이 샘플은 사용자가 온라인 설문 조사를 만들 수 있도록 하는 설문 조사라는 다중 테넌트 웹 응용 프로그램입니다. 샘플은 등록, 인증, 권한 부여 및 앱 역할을 포함하는 다중 테넌트 응용 프로그램에서 사용자 ID를 관리할 때 몇 가지 주요 관심사를 보여 줍니다. 이 샘플을 실행하려면 [Tailspin 설문 조사 샘플 응용 프로그램을 실행하는 방법](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md)을 참조하세요.

## Contoso 클리닉 데모 응용 프로그램

이 샘플에서는 Azure SQL DB (V12)의 보안 기능을 보여 줍니다. 이 샘플을 실행하려면 [Contoso 클리닉 데모 응용 프로그램](https://github.com/Microsoft/azure-sql-security-sample)을 참조합니다.

## 다음 단계

[Azure SQL 데이터베이스 자습서 탐색](sql-database-explore-tutorials.md)

<!---HONumber=AcomDC_0727_2016-->