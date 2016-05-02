<properties
   pageTitle="SQL 데이터 웨어하우스용 관리 도구 | Microsoft Azure"
   description="SQL 데이터 웨어하우스 관리 도구 소개"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="barbkess;sonyama;"/>

# SQL 데이터 웨어하우스 관리 도구
이 항목에서는 SQL 데이터 웨어하우스를 관리하기 위한 도구 및 옵션을 살펴보고 비교하므로 자신의 요구 사항에 맞는 도구를 선택할 수 있습니다. 적합한 도구를 선택하는 방법은 관리하는 데이터베이스 수, 작업 및 작업 수행 빈도에 따라 달라집니다.

## Azure 클래식 포털
[Azure 클래식 포털][]은 데이터베이스를 생성, 업데이트 및 삭제하고 데이터베이스 리소스를 모니터링할 수 있는 웹 기반의 클래식 포털입니다. Azure를 방금 시작했거나 관리하는 데이터웨어하우스 데이터베이스 수가 적거나 신속하게 작업을 수행해야 하는 경우에 매우 유용한 도구입니다.

포털에는 현재 및 과거 성능 DWU 설정, 사용 중인 저장소의 양, 성공적이고 실패한 SQL 연결 및 인스턴스에서 실행 중인 쿼리 및 이들 쿼리의 세부 정보에 대한 이해를 지원하는 일련의 시각화 및 데이터를 포괄하는 메트릭이 포함됩니다.

## Visual Studio에서 SQL Server 데이터 도구
[Visual Studio의 SSDT][](SQL Server Data Tools)는 사용자의 컴퓨터에서 실행되며 클라우드의 데이터베이스를 연결, 관리 및 개발해주는 클라이언트 도구입니다. Visual Studio 또는 다른 IDE(통합 개발 환경)에 익숙한 응용 프로그램 개발자라면 Visual Studio에서 SSDT를 사용해 보세요.

SSDT에는 SQL 데이터 웨어하우스 데이터베이스에 대해 스크립트를 시각화, 연결 및 실행할 수 있게 해주는 SQL 서버 탐색기가 포함됩니다. SQL 데이터 웨어하우스에 빠르게 연결하려면 Azure 클래식 포털에서 데이터베이스 세부 정보를 볼 때 명령 모음에서 **Visual Studio에서 열기** 단추를 클릭하기만 하면 됩니다.

SQL 데이터 웨어하우스에 대한 지원이 포함된 [SQL Server Data Tools][](SSDT)의 최신 버전을 다운로드할 수 있습니다.

## 명령줄 도구
한 가지 옵션은 PowerShell 또는 sqlcmd 명령줄 도구를 사용하여 SQL 데이터 웨어하우스를 관리하고 Azure 리소스 배포를 자동화하는 것입니다. 이들 도구는 필수 작업을 스크립트 및 자동화할 수 있는 프로덕션 환경에서 많은 수의 논리 서버를 관리하고 리소스 변경 사항을 배포할 때 권장합니다.

## 다음 단계
이러한 도구의 사용을 시작하려면 [연결][] 항목을 다시 읽으십시오.

<!--Image references-->

<!--Article references-->
[연결]: sql-data-warehouse-develop-connections.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/library/mt204009.aspx
[Visual Studio의 SSDT]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Azure 클래식 포털]: http://portal.azure.com/

<!---HONumber=AcomDC_0420_2016-->