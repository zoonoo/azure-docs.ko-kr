---
title: "Microsoft Azure Data Lake Analytics 개요 | Microsoft Docs"
description: "Data Lake Analytics은 Azure Big Data 서비스로, 이를 통해 크기나 위치에 관계없이 클라우드의 데이터에서 얻은 통찰력을 통해 데이터를 비즈니스 운영에 사용할 수 있습니다."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 1e1d443a-48a2-47fb-bc00-bf88274222de
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/23/2017
ms.author: saveenr
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 8817b511d779029421491194b50120d51ec9dbad
ms.contentlocale: ko-kr
ms.lasthandoff: 06/26/2017


---
# Microsoft Azure Data Lake Analytics 개요
<a id="overview-of-microsoft-azure-data-lake-analytics" class="xliff"></a>
## Azure Data Lake Analytics이란?
<a id="what-is-azure-data-lake-analytics" class="xliff"></a>
Azure Data Lake Analytics는 빅 데이터 분석을 간소화하는 주문형 분석 작업 서비스입니다. 분산된 인프라 작업보다는 작업 작성, 실행 및 관리에 초점을 맞출 수 있습니다. 하드웨어를 배포, 구성 및 조정하는 대신, 데이터를 변형하고 귀중한 통찰력을 얻기 위한 쿼리를 작성합니다. 이 분석 서비스는 필요한 전력 크기만큼 다이얼을 설정하여 어떤 크기의 작업도 즉시 처리할 수 있습니다. 실행할 때 작업 기준으로 비용이 부과되므로 비용 효과적일 수 있습니다. 이 분석 서비스는 온-프레미스 ID 시스템과 통합되어 액세스 및 역할을 관리할 수 있도록 하는 Azure Active Directory를 지원합니다. 또한 이 서비스에는 SQL의 장점을 사용자 코드의 표현 능력과 결합한 U-SQL 언어가 포함되어 있습니다. U-SQL의 확장 가능한 분산 런타임을 통해 저장소와 Azure, Azure SQL Database 및 Azure SQL Data Warehouse의 SQL Server 간에 데이터를 효과적으로 분석할 수 있습니다.

## 주요 기능
<a id="key-capabilities" class="xliff"></a>
* **동적 크기 조정**
  
    Data Lake Analytics은 클라우드 규모 및 성능에 맞게 설계되었습니다.  이 서비스를 통해 리소스를 동적으로 프로비전하고 테라바이트 또는 심지어 엑사바이트 단위의 데이터도 분석할 수 있습니다. 작업이 완료되면 리소스가 자동으로 사용 해제되며, 사용한 처리량에 대해서만 지불하면 됩니다. 저장된 데이터 크기 또는 사용된 계산 리소스의 양을 늘리거나 줄일 때 코드를 다시 작성할 필요가 없습니다. 대용량 데이터 집합을 처리하고 저장하는 방법 대신 비즈니스 논리에만 집중할 수 있습니다.
* **친숙한 도구를 사용하여 더 빠르게 개발하고 더 스마트하게 디버그 및 최적화 수행**
  
    Data Lake Analytics은 Visual Studio에 깊이 통합되므로 친숙한 도구로 코드를 실행, 디버그 및 조정할 수 있습니다. U-SQL 작업의 시각화를 통해 코드가 실행되는 방식을 전체적으로 확인할 수 있으므로 성능 병목 현상을 쉽게 식별하고 비용을 최적화할 수 있습니다.
* **U-SQL: 간편하고 친숙하면서도 강력하고 확장 가능**
  
    Data Lake Analytics은 SQL의 친숙하고 간단한 선언적 특성을 C#의 표현 능력으로 확장한 쿼리 언어인 U-SQL을 포함합니다. U-SQL 언어는 Microsoft 내의 빅 데이터 시스템을 지원하는 동일한 분산 런타임에 구축됩니다. 이제 수백만 명의 SQL 및 .NET 개발자가 이미 보유하고 있는 기술을 사용하여 데이터를 처리하고 분석할 수 있습니다.
* **기존 IT 투자에 원활하게 통합**
  
    Data Lake Analytics은 ID, 관리, 보안 및 데이터 웨어하우징에 대한 기존 IT 투자를 사용할 수 있습니다. 이러한 방법으로 데이터 거버넌스가 간소화되며 현재 데이터 응용 프로그램을 쉽게 확장할 수 있습니다. Data Lake Analytics은 사용자 관리 및 권한에 대해 Active Directory와 통합되고 기본 제공 모니터링 및 감사와 함께 제공됩니다.
* **저렴하고 경제적**
  
    Data Lake Analytics은 빅 데이터 작업을 실행하기 위한 경제적인 솔루션입니다. 데이터가 처리될 때 작업 단위로 비용을 지불합니다. 하드웨어, 라이선스 또는 서비스별 지원 계약이 필요하지 않습니다. 시스템은 작업이 시작되고 완료됨에 따라 자동으로 확장되고 축소되므로 필요 이상으로 비용을 지불하지 않아도 됩니다.
* **모든 Azure 데이터 작업**
  
    Data Lake Analytics은 Azure Data Lake에서 작업할 때 최적화되어 빅 데이터 작업에 대한 최상의 성능, 처리량 및 병렬화를 제공합니다.  또한 Data Lake Analytics는 Azure Blob Storage 및 Azure SQL Database에서 작업할 수도 있습니다.

## 다음 단계
<a id="next-steps" class="xliff"></a>
 
  * [Azure Portal](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI](data-lake-analytics-get-started-cli2.md)를 사용하여 Data Lake Analytics 시작
  * [Azure Portal](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) | [Azure .NET SDK](data-lake-analytics-manage-use-dotnet-sdk.md) | [Node.js](data-lake-analytics-manage-use-nodejs.md)를 사용하여 Azure Data Lake Analytics 관리
  * [Azure Portal을 사용하여 Azure Data Lake Analytics 작업 모니터링 및 문제 해결](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md) 

