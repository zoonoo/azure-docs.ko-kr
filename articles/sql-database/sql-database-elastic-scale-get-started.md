<properties 
	pageTitle="탄력적 데이터베이스 도구 시작하기" 
	description="쉽게 실행할 수 있는 샘플 앱을 포함하여 Azure SQL 데이터베이스의 탄력적 데이터베이스 도구 기능에 대해 기본적으로 설명합니다." 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="ddove" 
	editor="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/04/2015" 
	ms.author="ddove;sidneyh"/>

# 탄력적 데이터베이스 도구 시작하기

이 문서에서는 샘플 앱을 실행하여 개발자 환경을 소개합니다. 이 문서의 샘플에서는 간단한 분할 응용 프로그램을 만들고 탄력적 데이터베이스 도구의 주요 기능을 살펴봅니다. 이 샘플은 [탄력적 데이터베이스 클라이언트 라이브러리](sql-database-elastic-database-client-library.md)의 기능을 보여줍니다.

## 필수 조건

1. C#가 포함된 Visual Studio 2012 이상이 필요합니다. [Visual Studio 다운로드](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)에서 무료 버전을 다운로드하세요.
2. Nuget 2.7 이상. 최신 버전을 설치하려면 [NuGet 설치](http://docs.nuget.org/docs/start-here/installing-nuget)를 참조하세요.

## 샘플 앱 다운로드 및 실행

**Azure SQL와 탄력적 DB - 시작** 샘플 응용 프로그램은 Azure SQL 탄력적 데이터베이스 도구를 사용하는 분할된 응용 프로그램용 개발 환경의 가장 중요한 측면을 설명합니다. [분할 맵 관리](sql-database-elastic-scale-shard-map-management.md), [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md) 및 [다중 분할 쿼리](sql-database-elastic-scale-multishard-querying.md)의 주요 사용 사례를 중점적으로 소개합니다. 샘플을 다운로드하고 실행하려면 다음 단계를 수행합니다.

1. Visual Studio를 열고 **파일 -> 새로 만들기 -> 프로젝트**를 선택합니다.
2. 대화 상자에서 **온라인**을 클릭합니다.

    ![새 프로젝트>온라인][2]
3. 그런 다음 **샘플** 아래에서 **Visual C#**을 클릭합니다.

    ![Visual C# 클릭][3]
4. 검색 상자에 **탄력적 db**를 입력하여 샘플을 검색합니다. 그러면 **Azure SQL용 탄력적 DB 도구 - 시작**이라는 제목이 표시됩니다.

    ![검색 상자][1]
 
5. 샘플을 선택하고 새 프로젝트의 이름과 위치를 선택한 다음 **확인**을 눌러 프로젝트를 만듭니다.
6. 샘플 프로젝트의 솔루션에서 **app.config** 파일을 열고 파일의 지침에 따라 Azure SQL 데이터베이스 서버 이름 및 로그인 정보(사용자 이름과 암호)를 추가합니다.
7. 응용 프로그램을 빌드 및 실행합니다. 메시지가 표시되면 Visual Studio가 솔루션의 NuGet 패키지를 복원하도록 허용합니다. 그러면 NuGet에서 최신 버전의 탄력적 데이터베이스 클라이언트 라이브러리가 다운로드됩니다.
8. 여러 옵션을 적용해 보면서 클라이언트 라이브러리 기능에 대해 자세히 살펴봅니다. 응용 프로그램이 콘솔 출력에서 수행하는 단계와 백그라운드의 코드도 확인해 보세요.

    ![진행률][4]

축하합니다. 이제 Azure SQL 데이터베이스에서 탄력적 데이터베이스 도구를 사용하는 첫 번째 분할 응용 프로그램을 빌드하고 실행했습니다. Visual Studio 또는 SQL Server Management Studio를 통해 Azure DB Server에 연결하여 샘플에서 작성된 분할을 확인해 보세요. 샘플에서 작성된 분할된 데이터베이스 맵 관리자 데이터베이스와 새 샘플 분할 데이터베이스를 확인할 수 있습니다.

**참고** SQL Server Management Studio가 없으면 해당 도구를 얻는 지침이 포함된 [SQL Server Management Studio를 사용하여 Azure SQL 데이터베이스 관리](sql-database-manage-azure-ssms.md)를 참조하세요.

### 코드 샘플의 주요 부분

1. **분할된 데이터베이스 및 분할된 데이터베이스 맵 관리**: 코드는 **ShardMapManagerSample.cs** 파일에서 분할된 데이터베이스, 범위 및 매핑으로 작업하는 방법을 보여 줍니다. 이 항목에 대한 자세한 내용은 다음을 참조하세요. [분할된 데이터베이스 맵 관리](http://go.microsoft.com/?linkid=9862595).  
2. **데이터 종속 라우팅**: 적절한 분할로의 트랜잭션 라우팅은 **DataDependentRoutingSample.cs**에 나와 있습니다. 자세한 내용은 [데이터 종속 라우팅](http://go.microsoft.com/?linkid=9862596)을 참조하세요. 
3. **여러 분할된 데이터베이스에 대한 쿼리**: 여러 분할된 데이터베이스에 대한 쿼리 방법은 **MultiShardQuerySample.cs** 파일에서 설명합니다. 자세한 내용은 [다중 분할 쿼리](http://go.microsoft.com/?linkid=9862597)를 참조하세요.
4. **비어 있는 분할 추가**: **AddNewShardsSample.cs** 파일의 코드를 사용하여 비어 있는 새 분할을 반복적으로 추가할 수 있습니다. 이 항목에 대한 자세한 내용은 다음에서 다룹니다. [분할된 데이터베이스 맵 관리](http://go.microsoft.com/?linkid=9862595).

### 기타 탄력적인 확장 작업

1. **기존의 분할된 데이터베이스 분할**: **분할/병합 도구**를 통해 분할된 데이터베이스를 분할하는 기능이 제공됩니다. 이 도구에 대한 자세한 내용은 다음을 참조하세요. [분할/병합 도구 개요](sql-database-elastic-scale-overview-split-and-merge.md).
2. **기존의 분할된 데이터베이스 병합**: 분할 병합도 **분할/병합 도구**를 사용하여 수행할 수 있습니다. 자세한 내용은 [분할/병합 도구 개요](sql-database-elastic-scale-overview-split-and-merge.md)를 참조하세요.   


## 비용

탄력적 데이터베이스 도구는 무료로 제공됩니다. 탄력적 데이터베이스 도구 사용해도 Azure 사용 비용 이외에 추가 비용은 부과되지 않습니다.

예를 들어 샘플 응용 프로그램이 새 데이터베이스를 만드는 경우 해당 비용은 선택한 Azure SQL DB 데이터베이스 버전과 응용 프로그램의 Azure 사용량에 따라 부과됩니다.

가격 정보는 [SQL 데이터베이스 가격 정보](http://azure.microsoft.com/pricing/details/sql-database/)를 참조하세요.

## 다음 단계
탄력적 데이터베이스 도구에 대한 자세한 내용은 다음을 참조하세요.

* [탄력적 데이터베이스 도구 설명서 맵](sql-database-elastic-scale-documentation-map.md) 
-    코드 샘플: 
    -    [Azure SQL과 탄력적 DB - 시작](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
    -    [Azure SQL과 탄력적 DB - Entity Framework와 통합](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
    -    [스크립트 센터의 분할된 데이터베이스 탄력성](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
-    블로그: [탄력적인 확장 발표](http://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
-    채널 9: [탄력적인 확장 개요 비디오](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
-    토론 포럼: [Azure SQL 데이터베이스 포럼](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)


<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
 

<!---HONumber=AcomDC_0114_2016-->