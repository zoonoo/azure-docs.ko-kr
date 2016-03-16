<properties 
   pageTitle="Microsoft Azure 데이터 레이크 분석 개요 | Azure" 
   description="데이터 레이크 분석은 Azure 빅 데이터 계산 서비스로, 이를 통해 위치 및 크기에 관계없이 클라우드의 데이터에서 얻은 통찰력을 통해 데이터를 비즈니스 운영에 사용할 수 있습니다. 데이터 레이크 분석을 사용하면 가장 간단하고 확장성이 뛰어나면서도 가장 경제적인 방법으로 이 작업을 수행할 수 있습니다." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/06/2016"
   ms.author="jgao"/>

# Microsoft Azure 데이터 레이크 분석 개요

## Azure 데이터 레이크 분석이란?

Azure 데이터 레이크 분석은 빅 데이터 분석을 쉽게 수행하기 위해 구축된 새 서비스입니다. 이 서비스를 사용하면 분산된 인프라 작업보다는 작업 작성, 실행 및 관리에 초점을 맞출 수 있습니다. 하드웨어를 배포, 구성 및 조정하는 대신, 데이터를 변형하고 귀중한 통찰력을 얻기 위한 쿼리를 작성합니다. 이 분석 서비스는 필요한 전력 크기만큼 간단히 다이얼을 설정하여 어떤 크기의 작업도 즉시 처리할 수 있습니다. 실행할 때 작업 기준으로 비용이 부과되므로 비용 효과적일 수 있습니다. 이 분석 서비스는 온-프레미스 ID 시스템과 통합되어 액세스 및 역할을 간단히 관리할 수 있도록 하는 Azure Active Directory를 지원합니다. 또한 이 서비스에는 SQL의 장점을 사용자 코드의 표현 능력과 결합한 U-SQL 언어가 포함되어 있습니다. U-SQL의 확장 가능한 분산 런타임을 통해 저장소와 Azure, Azure SQL 데이터베이스 및 Azure SQL 데이터 웨어하우스의 SQL Server 간에 데이터를 효과적으로 분석할 수 있습니다.


## 주요 기능

- **동적 크기 조정** 

    데이터 레이크 분석은 클라우드 규모 및 성능에 맞게 처음부터 새롭게 설계되었습니다. 이 서비스를 통해 리소스를 동적으로 프로비전하고 테라바이트 또는 심지어 엑사바이트 단위의 데이터도 분석할 수 있습니다. 작업이 완료되면 리소스가 자동으로 사용 해제되며, 사용한 처리량에 대해서만 지불하면 됩니다. 저장된 데이터 크기 또는 사용된 계산량을 늘리거나 줄일 때 코드를 다시 작성할 필요가 없습니다. 따라서 대용량 데이터 집합을 처리하고 저장하는 방법 대신 비즈니스 논리에만 집중할 수 있습니다.

- **친숙한 도구를 사용하여 더 빠르게 개발하고 더 스마트하게 디버그 및 최적화 수행**

    데이터 레이크 분석은 Visual Studio에 깊이 통합되므로 친숙한 도구로 코드를 실행, 디버그 및 조정할 수 있습니다. U-SQL 작업의 시각화를 통해 코드가 실행되는 방식을 전체적으로 확인할 수 있으므로 성능 병목 현상을 쉽게 식별하고 비용을 최적화할 수 있습니다.

- **U-SQL: 간편하고 친숙하면서도 강력하고 확장 가능**

    데이터 레이크 분석은 SQL의 친숙하고 간단한 선언적 특성을 C#의 표현 능력으로 확장한 쿼리 언어인 U-SQL을 포함합니다. U-SQL 언어는 Microsoft 내의 빅 데이터 시스템을 지원하는 동일한 분산 런타임에 구축됩니다. 이제 수백만 명의 SQL 및 .NET 개발자가 이미 보유하고 있는 기술을 사용하여 모든 데이터를 처리하고 분석할 수 있습니다.

- **기존 IT 투자에 원활하게 통합**

    데이터 레이크 분석은 ID, 관리, 보안 및 데이터 웨어하우징에 대한 기존 IT 투자를 사용할 수 있습니다. 이를 통해 데이터 거버넌스가 간소화되며 현재 데이터 응용 프로그램을 쉽게 확장할 수 있습니다. 데이터 레이크 분석은 사용자 관리 및 권한에 대해 Active Directory와 통합되고 기본 제공 모니터링 및 감사와 함께 제공됩니다.

- **저렴하고 경제적**

    데이터 레이크 분석은 빅 데이터 작업을 실행하기 위한 경제적인 솔루션입니다. 데이터가 처리될 때 작업 단위로 비용을 지불합니다. 하드웨어, 라이선스 또는 서비스별 지원 계약이 필요하지 않습니다. 시스템은 작업이 시작하고 완료됨에 따라 자동으로 확장되고 축소되므로 필요 이상으로 비용을 지불하지 않아도 됩니다.

- **모든 Azure 데이터 작업**

    데이터 레이크 분석은 여러 Azure 데이터 원본 즉, Azure Blob 저장소 및 Azure SQL 데이터베이스에서 작업할 수 있습니다. 데이터 레이크 분석은 Azure 데이터 레이크 저장소에서 작업할 때 특히 최적화되어 빅 데이터 작업에 대한 최상의 성능, 처리량 및 병렬화를 제공합니다.

## 참고 항목

- 시작
    - [Azure 포털을 사용하여 데이터 레이크 분석 시작](data-lake-analytics-get-started-portal.md)
    - [Azure PowerShell을 사용하여 데이터 레이크 분석 시작](data-lake-analytics-get-started-powershell.md)
    - [Azure .NET SDK를 사용하여 데이터 레이크 분석 시작](data-lake-analytics-get-started-net-sdk.md)
    - [Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)
    - [Azure 데이터 레이크 분석 U-SQL 언어 시작](data-lake-analytics-u-sql-get-started.md)
    
- U-SQL 및 개발
    - [Azure 데이터 레이크 분석 U-SQL 언어 시작](data-lake-analytics-u-sql-get-started.md)
    - [Azure 데이터 레이크 분석 작업에 U-SQL 창 함수 사용](data-lake-analytics-use-window-functions.md)
    - [데이터 레이크 분석 작업을 위한 U-SQL 사용자 정의 연산자 개발](data-lake-analytics-u-sql-develop-user-defined-operators.md)
    
- 관리
    - [Azure 포털을 사용하여 Azure 데이터 레이크 분석 관리](data-lake-analytics-manage-use-portal.md)
    - [Azure PowerShell을 사용하여 Azure 데이터 레이크 분석 관리](data-lake-analytics-manage-use-powershell.md)
    - [Azure 포털을 사용하여 Azure 데이터 레이크 분석 작업 모니터링 및 문제 해결](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

- 종단간 자습서
    - [Azure 데이터 레이크 분석 대화형 자습서 사용](data-lake-analytics-use-interactive-tutorials.md)
    - [Azure 데이터 레이크 분석을 사용하여 웹 사이트 로그 분석](data-lake-analytics-analyze-weblogs.md)

- 의견 제시
    - [설명서 백로그에 대한 의견](data-lake-analytics-documentation-backlog.md)
    - [기능 요청 제출](http://aka.ms/adlafeedback)
    - [포럼에서 도움말 보기](http://aka.ms/adlaforums)

<!---HONumber=AcomDC_0302_2016-->