<properties 
	pageTitle="Azure SQL 데이터베이스 탄력적인 확장 시작" 
	description="쉽게 실행할 수 있는 샘플 앱을 포함하여 Azure SQL 데이터베이스의 탄력적인 확장 기능에 대해 기본적으로 설명합니다." 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="sidneyh@microsoft.com"/>

# Azure SQL 데이터베이스 탄력적인 확장 미리 보기 시작

클라우드 컴퓨팅의 핵심 기능 중 하나는 요청 시 용량을 늘리고 줄이는 것입니다. 이전에는 클라우드 응용 프로그램의 데이터베이스 계층에 대해 이 기능을 제공하는 과정이 매우 번거롭고 복잡했습니다. 그러나 지난 몇 년간 해당 업계에서는 분할이라는 효율적으로 설정된 디자인 패턴을 중점적으로 사용하게 되었습니다. 일반적인 분할 패턴을 사용하면 위에서 설명한 문제점을 해결할 수는 있지만 분할을 사용하여 응용 프로그램을 빌드하고 관리하려면 응용 프로그램의 비즈니스 논리와는 관계없이 인프라에 막대한 투자를 해야 합니다. 

Azure SQL 데이터베이스의 탄력적인 확장(현재 미리 보기 상태)을 통해 업계 표준 분할 방식에 따라 응용 프로그램의 데이터 계층을 규모 확장 및 감축하는 동시에 분할된 클라우드 응용 프로그램 개발과 관리를 크게 간소화할 수 있습니다. 탄력적인 확장은 확장성이 뛰어난 응용 프로그램을 관리하기 위해 자체 Azure 구독에서 호스트할 수 있는 Azure 서비스 템플릿과 .NET 라이브러리 집합을 통해 제공되는 개발자 및 관리 기능을 모두 제공합니다. Azure DB 탄력적인 확장은 분할의 인프라 측면을 구현하므로 개발자는 응용 프로그램의 비즈니스 논리 개발에 주력할 수 있습니다. 

이 문서에서는 Azure SQL DB 탄력적인 확장의 개발자 환경을 소개합니다. 

탄력적인 확장의 작동 방식에 대한 자세한 내용은 [탄력적인 확장 개요](http://go.microsoft.com/?linkid=9862592)를 참조하세요.

탄력적인 확장과 관련된 모든 항목의 목록은 [탄력적인 확장 설명서 맵](sql-database-elastic-scale-documentation-map.md)을 참조하세요.

## 탄력적인 확장 샘플 응용 프로그램

이 문서의 샘플에서는 간단한 분할 응용 프로그램을 만들고 탄력적인 확장의 주요 기능을 살펴봅니다. 응용 프로그램을 다운로드하여 실행하려면 아래의 단계를 수행하거나 [탄력적인 확장 - 시작](http://go.microsoft.com/?linkid=9862983) 비디오를 시청하세요. 

### 필수 조건
샘플 앱을 실행하려면 Visual Studio를 사용해야 하며 Azure에서 실행 중인 Azure SQL 데이터베이스 액세스 권한이 있어야 합니다. Azure 구독이 아직 없으면 [평가판 구독](http://azure.microsoft.com/pricing/free-trial/)을 신청하세요.
#### Visual Studio 및 Nuget

1. C#가 포함된 Visual Studio 2012 이상이 필요합니다. [Visual Studio 다운로드](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)에서 무료 버전을 다운로드하세요.
2. Nuget 2.7 이상. 최신 버전을 설치하려면 [NuGet 설치](http://docs.nuget.org/docs/start-here/installing-nuget)를 참조하세요.
#### Azure SQL 데이터베이스 만들기

* [Microsoft Azure SQL 데이터베이스 시작](http://azure.microsoft.com/documentation/articles/sql-database-get-started/)을 참조하세요.

## 샘플 앱 다운로드 및 실행
**Azure SQL 데이터베이스의 탄력적인 확장 - 시작** 샘플 응용 프로그램은 Azure SQL DB 탄력적인 확장을 사용하는 분할된 응용 프로그램용 개발 환경의 가장 중요한 측면을 보여 주며 [분할된 데이터베이스 맵 관리](http://go.microsoft.com/?linkid=9862595), [데이터 종속 라우팅](http://go.microsoft.com/?linkid=9862596) 및 [다중 분할된 데이터베이스 쿼리](http://go.microsoft.com/?linkid=9862597)의 주요 사용 사례를 중점적으로 소개합니다. 샘플을 다운로드하고 실행하려면 다음 단계를 수행합니다. 

1. Visual Studio를 열고 **파일 -> 새로 만들기 -> 프로젝트**를 선택합니다.
2. 대화 상자에서 **온라인**을 클릭합니다.

    ![New Project>Online][2]
3. 그런 다음 **샘플** 아래에서 **Visual C#**을 클릭합니다.

    ![Click Visual C#][3]
4. 검색 상자에 **탄력적인 확장**을 입력하여 샘플을 검색합니다. 그러면 **Azure SQL 데이터베이스의 탄력적인 확장 - 시작**이라는 제목이 표시됩니다.

    ![Search Box][1]
 
5. 샘플을 선택하고 새 프로젝트의 이름과 위치를 선택한 다음 **확인**을 눌러 프로젝트를 만듭니다.
6. 샘플 프로젝트의 솔루션에서 **app.config** 파일을 열고 파일의 지침에 따라 Azure SQL 데이터베이스 서버 이름 및 로그인 정보(사용자 이름과 암호)를 추가합니다.
7. 응용 프로그램을 빌드 및 실행합니다. 메시지가 표시되면 Visual Studio가 솔루션의 NuGet 패키지를 복원하도록 허용합니다. 그러면 NuGet에서 최신 버전의 탄력적인 확장 클라이언트 라이브러리가 다운로드됩니다.
8. 여러 옵션을 적용해 보면서 탄력적인 확장의 기능에 대해 자세히 살펴봅니다. 응용 프로그램이 콘솔 출력에서 수행하는 단계와 백그라운드의 코드도 확인해 보세요.

    ![progress][4]

축하합니다. 이제 Azure SQL DB에서 첫 번째 탄력적인 확장 응용 프로그램을 빌드하고 실행했습니다. SQL Server Management Studio를 통해 Azure DB Server에 연결하여 샘플에서 작성된 분할을 확인해 보세요. 샘플에서 작성된 분할 맵 관리자 데이터베이스와 새 샘플 분할 데이터베이스를 확인할 수 있습니다.

**참고**   SQL Server Management Studio가 없으면 해당 도구를 얻는 지침이 포함된 [SQL Server Management Studio를 사용하여 Azure SQL 데이터베이스 관리](http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/)를 참조하세요.  

### 코드 샘플의 주요 부분

1. **분할된 데이터베이스 및 분할된 데이터베이스 맵 관리**: **ShardMapManagerSample.cs** 파일의 코드에서 분할된 데이터베이스, 범위 및 매핑으로 작업하는 방법을 보여 줍니다. 이 항목에 대한 자세한 내용은 [분할된 데이터베이스 맵 관리](http://go.microsoft.com/?linkid=9862595)를 참조하세요.  
2. **데이터 종속 라우팅**: 적절한 분할로의 트랜잭션 라우팅이 **DataDependentRoutingSample.cs**에 나와 있습니다. 자세한 내용은 [데이터 종속 라우팅](http://go.microsoft.com/?linkid=9862596)을 참조하세요. 
3. **여러 분할된 데이터베이스 쿼리**: 여러 분할된 데이터베이스에 대한 쿼리 방법이 **MultiShardQuerySample.cs** 파일에 나와 있습니다. 자세한 내용은 [다중 분할된 데이터베이스 쿼리](http://go.microsoft.com/?linkid=9862597)를 참조하세요.
4. **비어 있는 분할된 데이터베이스 추가**: 파일
**AddNewShardsSample.cs**의 코드를 사용하여 비어 있는 새 분할된 데이터베이스를 반복적으로 추가합니다. 이 항목에 대한 자세한 내용은 [분할된 데이터베이스 맵 관리](http://go.microsoft.com/?linkid=9862595)를 참조하세요.

### 기타 탄력적인 확장 작업

1. **기존 분할된 데이터베이스 분할**: **분할/병합 서비스**를 통해 분할된 데이터베이스를 분할하는 기능이 제공됩니다. 이 서비스에 대한 자세한 내용은 [분할/병합 서비스](http://go.microsoft.com/?linkid=9862795)를 참조하세요.
2. **기존의 분할된 데이터베이스 병합**: 분할된 데이터베이스 병합도 **분할/병합 서비스**를 사용하여 수행합니다. 자세한 내용은 [분할/병합 서비스](http://go.microsoft.com/?linkid=9862795)를 참조하세요.   


## 비용

탄력적인 확장 라이브러리와 서비스 템플릿은 무료입니다. 즉, 탄력적인 확장을 사용해도 Azure 사용 비용 이외에 추가 비용은 부과되지 않습니다. 

예를 들어 샘플 응용 프로그램이 새 데이터베이스를 만드는 경우 해당 비용은 선택한 Azure SQL DB 데이터베이스 버전과 응용 프로그램의 Azure 사용량에 따라 부과됩니다.

가격 정보는 [SQL 데이터베이스 가격 정보](http://azure.microsoft.com/pricing/details/sql-database/)를 참조하세요.

## 다음 단계
탄력적인 확장 기능에 대한 자세한 내용은 다음 항목을 참조하세요.

* [탄력적인 확장 학습 페이지](sql-database-elastic-scale-documentation-map.md) 
-    코드 샘플: 
    -    [Azure SQL 데이터베이스의 탄력적인 확장 - 시작](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
    -    [Azure SQL 데이터베이스의 탄력적인 확장 - Entity Framework와 통합](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
    -    [스크립트 센터의 분할된 데이터베이스 탄력성](http://go.microsoft.com/?linkid=9862617)
-    블로그: [탄력적인 확장 발표](http://go.microsoft.com/?linkid=9862608)
-    Channel 9: [탄력적인 확장 개요 비디오](http://go.microsoft.com/?linkid=9862609)
-    토론 포럼: [Azure SQL 데이터베이스 포럼](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)


<!--Anchors-->
[탄력적인 확장 샘플 응용 프로그램]: #The-Elastic-Scale-Sample-Application
[샘플 앱 다운로드 및 실행]: #Download-and-Run-the-Sample-App
[비용]: #Cost
[다음 단계]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png

<!--HONumber=47-->
 