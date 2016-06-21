<properties
	pageTitle="C# 쿼리로 SQL 데이터베이스에 연결 | Microsoft Azure"
	description="C#에 프로그램을 작성하여 SQL 데이터베이스에 쿼리하고 연결합니다. IP 주소, 연결 문자열, 보안 로그인 및 무료 Visual Studio에 대한 정보입니다."
	services="sql-database"
	keywords="c# 데이터베이스 쿼리, c# 쿼리, 데이터베이스 연결, SQL C#"
	documentationCenter=""
	authors="MightyPen"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="04/25/2016"
	ms.author="annemill"/>


# Visual Studio를 사용하여 SQL 데이터베이스에 연결

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Visual Studio에서 Azure SQL 데이터베이스에 연결하는 방법을 알아봅니다.

## 필수 조건


Visual Studio를 사용하여 SQL 데이터베이스에 연결하려면 다음이 있어야 합니다.


- Azure 계정 및 구독 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.


- Azure SQL 데이터베이스 서비스에서 **AdventureWorksLT** 데모 데이터베이스입니다.
 - 몇 분 안에 [데모 데이터베이스를 만듭니다.](sql-database-get-started.md)


- Visual Studio 2013 업데이트 4(이후) Microsoft는 이제 Visual Studio 커뮤니티를 *무료* 로 제공합니다.
 - [Visual Studio Community, 다운로드](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [무료 Visual Studio에 대한 더 많은 옵션](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)
 - 이 항목의 뒷부분에 나오는 [단계](#InstallVSForFree)는 [Azure 포털](https://portal.azure.com/)이 Visual Studio의 설치를 어떻게 안내하는지를 설명합니다.


<a name="InstallVSForFree" id="InstallVSForFree"></a>

&nbsp;

## 1단계: Visual Studio Community를 무료로 설치


Visual Studio를 설치해야 하는 경우 다음을 수행할 수 있습니다.

- 무료 다운로드 및 기타 옵션을 제공하는 Visual Studio 제품 웹 페이지에 브라우저를 탐색하여 Visual Studio 커뮤니티를 무료로 설치합니다.
- [Azure 포털](https://portal.azure.com/)은 다음에 설명할 다운로드 웹 페이지를 안내합니다.


### Azure 포털을 통한 Visual Studio


1. [Azure 포털](https://portal.azure.com/)(http://portal.azure.com/)을 통해 로그인합니다.

2. **모두*찾아보기** > **SQL 데이터베이스**를 클릭합니다. 블레이드는 데이터베이스에 대한 해당 검색을 엽니다.

3. 위쪽의 텍스트 상자 시작에서 **AdventureWorksLT** 데이터베이스의 이름을 입력하기 시작합니다.

4. 서버에서 데이터베이스에 대한 행을 보려면 행을 클릭합니다. 데이터베이스에 블레이드가 열립니다.

5. 편의를 위해 이전 블레이드 각각에서 최소화 컨트롤을 클릭합니다.

6. 데이터베이스 블레이드의 위쪽에서 **Visual Studio에서 열기** 단추를 클릭합니다. Visual Studio에 대한 블레이드가 링크로 열려서 Visual Studio에 위치를 설치합니다.

	![Visual Studio에서 열기 단추][20-OpenInVisualStudioButton]

7. **Community(무료)** 링크 또는 유사한 링크를 클릭합니다. 새 웹 페이지를 추가합니다.

8. 새 웹 페이지에서 링크를 사용하여 Visual Studio를 설치합니다.

9. Visual Studio를 설치한 후에 **Visual Studio에서 열기** 블레이드에서 **Visual Studio에서 열기** 단추를 클릭합니다. Visual Studio를 엽니다.

10. 해당 **SQL Server 개체 탐색기** 창의 혜택을 위해 Visual Studio가 대화 상자에서 연결 문자열 필드를 입력하도록 요청합니다.
 - **Windows 인증**이 아니라 **SQL 서버 인증**을 선택합니다.
 - **AdventureWorksLT** 데이터베이스를 지정해야 합니다.(대화 상자의 **옵션** > **연결 속성**)

11. **SQL Server 개체 탐색기**에서 데이터베이스에 대한 노드를 확장합니다.


## 2단계: 예제 쿼리 실행

논리적 서버에 연결한 후 데이터베이스에 연결하고 샘플 쿼리를 실행할 수 있습니다.

1. **개체 탐색기**에서 **AdventureWorks** 샘플 데이터베이스와 같은 권한이 있는 서버의 데이터베이스로 이동합니다.
2. 데이터베이스를 마우스 오른쪽 단추로 클릭한 다음 **새 쿼리**를 선택합니다.

	![새 쿼리 SQL 데이터베이스 서버에 연결: SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)

3. 쿼리 창에서 다음 코드를 복사하여 붙여넣습니다.

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. **실행** 단추를 클릭합니다. 다음 스크린샷에서는 성공적인 쿼리를 보여 줍니다.

	![성공. SQL 데이터베이스 서버에 연결: SVisual Studio](./media/sql-database-connect-query-ssms/5-success.png)

## 다음 단계

[.NET(C#)을 사용하여 SQL 데이터베이스에 연결](sql-database-develop-dotnet-simple.md)


<!-- Image references. -->

[20-OpenInVisualStudioButton]: ./media/sql-database-connect-query/connqry-free-vs-e.png

<!---HONumber=AcomDC_0615_2016-->