<properties
   pageTitle="SQL 데이터 웨어하우스용 Visual Studio 및 SSDT 설치 | Microsoft Azure"
   description="Azure SQL 데이터 웨어하우스용 Visual Studio 및 SSDT(SQL Server 개발 도구) 설치"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="sonyama;barbkess"/>

# SQL 데이터 웨어하우스용 Visual Studio 2015 및 SSDT 설치

SQL 데이터 웨어하우스용 응용 프로그램을 개발하려면 최신 버전의 SSDT(SQL Server 데이터 도구)와 함께 Visual Studio 2015를 사용하는 것이 좋습니다. 이전 버전과의 호환성을 위해 SSDT와 함께 Visual Studio 2013 업데이트 5도 지원됩니다.

SSDT로 Visual Studio를 사용하면 시각적 탐색 테이블, 뷰, 저장된 프로시저 및 SQL 데이터 웨어하우스의 더 많은 개체 뿐만 아니라 쿼리 실행에 SQL Server 개체 탐색기를 사용할 수 있습니다.

> [AZURE.NOTE] SQL 데이터 웨어하우스는 아직 Visual Studio 데이터베이스 프로젝트를 지원하지 않습니다. 이 기능은 이후 버전에 추가됩니다.

## 1단계: Visual Studio 2015 설치

이 링크를 따라 Visual Studio 2015를 다운로드하고 설치합니다. Visual Studio 2013 또는 2015가 설치되어 있는 경우 2단계로 건너뛰어 SSDT를 설치할 수 있습니다.

1. [Visual Studio 2015 다운로드][].
2. MSDN의 [Visual Studio 설치][] 지침을 따르고 기본 구성을 선택합니다.

## 2단계: SSDT 설치

Visual Studio 용 SSDT를 설치하려면 다음 이 단계를 수행하여 Visual Studio 내에서 SSDT 업데이트를 확인하기만 하면 됩니다.

1. Visual Studio에서 **도구** / **확장 및 업데이트…** / **업데이트**를 클릭합니다.
2. **제품 업데이트**를 선택한 후 **데이터베이스 도구용 Microsoft SQL Server 업데이트**를 찾습니다.

업데이트가 없는 경우 최신 버전을 설치했어야 합니다. SSDT가 설치되었는지 확인하려면 **도움말** / **Microsoft Visual Studio 정보**를 클릭하여 목록에서 SQL Server 데이터 도구를 찾아봅니다. 최신 버전의 SSDT는 14.0.60525.0입니다. 설치 옵션을 Visual Studio에서 사용할 수 없는 경우 [SSDT 다운로드][] 페이지를 방문하여 SSDT를 수동으로 다운로드하고 설치할 수 있습니다.

## 다음 단계

이제 최신 버전의 SSDT가 설치되었으므로 SQL 데이터 웨어하우스에 [연결][]할 수 있습니다.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[연결]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Visual Studio 2015 다운로드]: https://www.visualstudio.com/downloads/
[Visual Studio 설치]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT 다운로드]: https://msdn.microsoft.com/library/mt204009.aspx

<!---HONumber=AcomDC_0817_2016-->