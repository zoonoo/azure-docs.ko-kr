<properties 
	pageTitle="Visual Studio 프로젝트에 Azure SQL DB 탄력적인 확장 참조 추가" 
	description="Nuget을 사용하여 Visual Studio 프로젝트에 탄력적인 확장 API에 대한 .NET 참조를 추가하는 방법입니다." 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/16/2015" 
	ms.author="sidneyh"/>

# Visual Studio 프로젝트에 탄력적 데이터베이스 클라이언트 라이브러리 참조를 추가하는 방법 

### 필수 조건: 

- Visual Studio용 [NuGet Visual Studio Extension Gallery](http://docs.nuget.org/docs/start-here/installing-nuget) 설치 

### Visual Studio에서 탄력적 데이터베이스 클라이언트 라이브러리 참조를 추가하기 

1. 기존 프로젝트를 열거나 **파일** --> **새로 만들기** -->**프로젝트**에서 새 프로젝트 대화 상자를 사용하여 새 프로젝트를 만듭니다. 
2. 솔루션 탐색기에서 **References**를 마우스 오른쪽 단추로 클릭하고 **Manage NuGet Packages**를 선택합니다.
3. NuGet 패키지 관리 창의 왼쪽에 있는 메뉴에서 **Online**을 선택한 다음 **nuget.org** 또는 “모두”를 선택합니다. 
4. **온라인 검색** 대화 상자에서 **탄력적 데이터베이스**를 입력하고 **탄력적 데이터베이스 클라이언트 라이브러리**를 선택한 다음 **설치**를 클릭합니다.

	![온라인 검색][1]
4. 라이선스를 검토하고 **동의함**을 클릭합니다. 

이제 클라이언트 라이브러리 참조가 프로젝트에 추가 되었습니다.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-add-references-visual-studio/search-online.png
<!--anchors-->

<!---HONumber=July15_HO2-->