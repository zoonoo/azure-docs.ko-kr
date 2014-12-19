<properties title="Add Azure SQL DB Elastic Scale References to a Visual Studio Project" pageTitle="Visual Studio 프로젝트에 Azure SQL DB 탄력적인 확장 참조 추가" description="How to add .NET references for Elastic Scale APIs to Visual Studio projects using Nuget." metaKeywords="Azure SQL Database, elastic scale, Nuget references" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#방법: Visual Studio 프로젝트에 Azure SQL DB 탄력적인 확장 참조 추가 

###필수 조건: 

- Visual Studio용 [NuGet Visual Studio Extension Gallery](http://docs.nuget.org/docs/start-here/installing-nuget) 설치

###Visual Studio에서 탄력적인 확장 참조를 추가하려면 

1. **파일** --> **새로 만들기** --> **프로젝트**에 있는 새 프로젝트 대화 상자를 통해 새 프로젝트를 만듭니다. 
2. 솔루션 탐색기에서 **참조**를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.
3. NuGet 패키지 관리 창의 왼쪽에 있는 메뉴에서 **온라인**을 선택한 다음 **nuget.org** 또는 "모두"를 선택합니다.
4. **온라인 검색** 대화 상자에서 **탄력적인 확장**을 입력하고 **탄력적인 확장 클라이언트 라이브러리**를 선택한 다음 **설치**를 클릭합니다.
4. 라이선스를 검토하고 **동의함**을 클릭합니다. 

이제 Azure SQL 데이터베이스 탄력적인 확장 참조가 프로젝트에 추가되었습니다. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
