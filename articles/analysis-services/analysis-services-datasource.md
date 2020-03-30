---
title: Azure Analysis Services에서 지원되는 데이터 원본 | Microsoft Docs
description: Azure 분석 서비스에서 테이블 형식 1200 및 상위 데이터 모델에 대해 지원되는 데이터 원본 및 커넥터에 대해 설명합니다.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f65d8fa2c2e522c718c637e32defc4c56fca8364
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461660"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Azure Analysis Services에서 지원되는 데이터 원본

분석 서비스 프로젝트를 사용하여 Visual Studio의 데이터 가져오기 마법사 또는 테이블 가져오기 마법사에 표시된 데이터 원본 및 커넥터는 Azure 분석 서비스 및 SQL Server 분석 서비스 모두에 대해 표시됩니다. 그러나 표시된 모든 데이터 원본 및 커넥터가 Azure 분석 서비스에서 지원되는 것은 아닙니다. 연결할 수 있는 데이터 원본의 유형은 모델 호환성 수준, 사용 가능한 데이터 커넥터, 인증 유형 및 온-프레미스 데이터 게이트웨이 지원과 같은 여러 요인에 따라 달라집니다. 다음 표는 Azure 분석 서비스에 대해 지원되는 데이터 원본에 대해 설명합니다.

## <a name="azure-data-sources"></a>Azure 데이터 원본

|데이터 원본  |메모리 내  |DirectQuery  |메모 |
|---------|---------|---------|---------|
|Azure SQL Database      |   yes      |    yes      |<sup>[2,](#azprovider)</sup> <sup> [3](#azsqlmanaged)</sup>|
|Azure 시냅스 분석(SQL 데이터 웨어하우스)      |   yes      |   yes       |<sup>[2](#azprovider)</sup>|
|Azure Blob Storage      |   yes       |    예      | <sup>[1](#tab1400a)</sup> |
|Azure Table Storage     |   yes       |    예      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  yes        |  예        |<sup>[1](#tab1400a)</sup> |
|Azure 데이터 레이크 스토어 Gen1      |   yes       |    예      |<sup>[1](#tab1400a)</sup> |
|Azure 데이터 레이크 스토어 Gen2       |   yes       |    예      |<sup>[1,](#tab1400a)</sup> <sup> [5](#gen2)</sup>|
|Azure HDInsight HDFS    |     yes     |   예       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   yes       |   예       |<sup>[1,](#tab1400a)</sup> <sup> [4](#databricks)</sup>|
||||

**노트:**   
<a name="tab1400a">1</a> - 테이블 형식 1400 이상 모델에만 해당됩니다.  
<a name="azprovider">2</a> - 테이블 형식 1200 이상의 모델에서 *공급자* 데이터 원본으로 지정하면 메모리 내 및 DirectQuery 모델 모두 SQL Server MSOLEDBSQL(권장), SQL Server 네이티브 클라이언트 11.0 또는 SQL Server용 .NET 프레임워크 데이터 공급자용 Microsoft OLE DB 드라이버가 필요합니다.    
<a name="azsqlmanaged">3</a> - Azure SQL 데이터베이스 관리 인스턴스가 지원됩니다. 관리되는 인스턴스는 개인 IP 주소가 있는 Azure VNet 내에서 실행되므로 인스턴스에서 공용 끝점을 사용하도록 설정해야 합니다. 활성화되지 않은 경우 [온-프레미스 데이터 게이트웨이가](analysis-services-gateway.md) 필요합니다.    
<a name="databricks">4</a> - Spark 커넥터를 사용하는 Azure Databricks는 현재 지원되지 않습니다.   
<a name="gen2">5</a> - ADLS Gen2 커넥터는 현재 지원되지 않습니다, 그러나, Azure Blob 스토리지 커넥터는 ADLS Gen2 데이터 원본과 함께 사용할 수 있습니다.   

## <a name="other-data-sources"></a>기타 데이터 원본

|데이터 원본 | 메모리 내 | DirectQuery |메모   |
|  --- | --- | --- | --- |
|Access 데이터베이스     |  yes | 예 |  |
|Active Directory     |  yes | 예 | <sup>[6](#tab1400b)</sup>  |
|Analysis Services     |  yes | 예 |  |
|분석 플랫폼 시스템     |  yes | 예 |  |
|CSV 파일  |yes | 예 |  |
|Dynamics 365     |  yes | 예 | <sup>[6](#tab1400b)</sup> |
|Excel 통합 문서     |  yes | 예 |  |
|Exchange      |  yes | 예 | <sup>[6](#tab1400b)</sup> |
|폴더      |yes | 예 | <sup>[6](#tab1400b)</sup> |
|IBM Informix  |yes | 예 |  |
|JSON 문서      |  yes | 예 | <sup>[6](#tab1400b)</sup> |
|바이너리에서 줄      | yes | 예 | <sup>[6](#tab1400b)</sup> |
|MySQL 데이터베이스     | yes | 예 |  |
|OData 피드      |  yes | 예 | <sup>[6](#tab1400b)</sup> |
|ODBC 쿼리     | yes | 예 |  |
|OLE DB     |   yes | 예 |  |
|Oracle  | yes  |yes  | <sup>[9](#oracle)</sup> |
|PostgreSQL 데이터베이스   | yes | 예 | <sup>[6](#tab1400b)</sup> |
|Salesforce 개체|  yes | 예 | <sup>[6](#tab1400b)</sup> |
|Salesforce 보고서 |yes | 예 | <sup>[6](#tab1400b)</sup> |
|SAP HANA     |  yes | 예 |  |
|SAP Business Warehouse    |  yes | 예 | <sup>[6](#tab1400b)</sup> |
|SharePoint 목록      |   yes | 예 | <sup>[6](#tab1400b)</sup>, <sup> [11](#filesSP)</sup> |
|SQL Server |yes   | yes  | <sup>[7,](#sqlim)</sup> <sup> [8](#instgw)</sup> | 
|SQL Server 데이터 웨어하우스 |yes   | yes  | <sup>[7,](#sqlim)</sup> <sup> [8](#instgw)</sup> |
|Sybase 데이터베이스     |  yes | 예 |  |
|Teradata | yes  | yes  | <sup>[10](#teradata)</sup> |
|TXT 파일  |yes | 예 |  |
|XML 테이블    |  yes | 예 | <sup>[6](#tab1400b)</sup> |
| | | |

**노트:**   
<a name="tab1400b">6</a> - 테이블 형식 1400 이상 모델만.  
<a name="sqlim">7</a> - 테이블 형식 1200 이상의 모델에서 *공급자* 데이터 원본으로 지정하는 경우 SQL Server MSOLEDBSQL(권장), SQL Server 네이티브 클라이언트 11.0 또는 SQL Server용 .NET 프레임워크 데이터 공급자에 대한 Microsoft OLE DB 드라이버를 지정합니다.  
<a name="instgw">8</a> - MSOLEDBSQL을 데이터 공급자로 지정하는 경우 온-프레미스 데이터 게이트웨이와 동일한 컴퓨터에 [SQL Server용 Microsoft OLE DB 드라이버를](https://docs.microsoft.com/sql/connect/oledb/oledb-driver-for-sql-server) 다운로드하여 설치해야 할 수 있습니다.  
<a name="oracle">9</a> - 테이블 형식 1200 모델의 경우 또는 테이블 형식 1400+ 모델의 *공급자* 데이터 원본으로 .NET에 대한 Oracle 데이터 공급자를 지정합니다.  
<a name="teradata">10</a> - 테이블 형식 1200 모델의 경우 또는 테이블 형식 1400+ 모델의 *공급자* 데이터 원본으로 .NET에 대한 Teradata 데이터 공급자를 지정합니다.   
<a name="filesSP">11</a> - 온-프레미스 SharePoint의 파일은 지원되지 않습니다.

Azure Analysis Services 서버의 온-프레미스 데이터 원본에 연결하려면 [온-프레미스 게이트웨이가](analysis-services-gateway.md)필요합니다. 게이트웨이를 사용하는 경우 64비트 공급자는 필수입니다. 

## <a name="understanding-providers"></a>공급자 이해

Visual Studio에서 테이블 형식 1400 이상의 모델 프로젝트를 만들 때 기본적으로 **데이터 받기를**사용하여 데이터 원본에 연결할 때 데이터 공급자를 지정하지 않습니다. Tabular 1400 이상의 모델은 [Power Query](/power-query/power-query-what-is-power-query) 커넥터를 사용하여 데이터 원본과 분석 서비스 간의 연결, 데이터 쿼리 및 매시업을 관리합니다. 이러한 연결을 연결 속성 설정에서 *구조화 된* 데이터 원본 연결이라고도 합니다. 그러나 레거시 데이터 원본을 활성화할 수 있습니다. **활성화된 경우 테이블 가져오기 마법사를** 사용하여 테이블 형식 1200에서 전통적으로 지원되는 특정 데이터 원본에 연결하고 하위 모델을 *레거시*또는 *공급자* 데이터 원본으로 연결할 수 있습니다. 공급자 데이터 원본으로 지정하면 특정 데이터 공급자 및 기타 고급 연결 속성을 지정할 수 있습니다. 예를 들어 온-프레미스 SQL Server 데이터 웨어하우스 또는 Azure SQL 데이터베이스에 레거시 데이터 원본으로 연결할 수 있습니다. 그런 다음 SQL Server MSOLEDBSQL 데이터 공급자에 대한 OLE DB 드라이버를 선택할 수 있습니다. 이 경우 OLE DB 데이터 공급자를 선택하면 Power Query 커넥터를 통해 향상된 성능을 제공할 수 있습니다. 

Visual Studio에서 테이블 가져오기 마법사를 사용하는 경우 모든 데이터 원본에 대한 연결에는 데이터 공급자가 필요합니다. 기본 데이터 공급자가 선택됩니다. 필요한 경우 데이터 공급자를 변경할 수 있습니다. 선택한 공급자 유형은 모델이 메모리 내 저장소 또는 DirectQuery를 사용하고 있는지 여부, 모델을 배포하는 Analysis Services 플랫폼에 따라 성능에 따라 달라질 수 있습니다.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>테이블 형식 1400 및 상위 모델 프로젝트에서 공급자 데이터 원본 지정

공급자 데이터 원본을 활성화하려면 Visual Studio에서 **도구** > **옵션** > **분석 서비스 테이블 형식** > **데이터 가져오기를**클릭하고 **레거시 데이터 원본 활성화를**선택합니다.

![레거시 데이터 원본 사용](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

레거시 데이터 원본을 사용하도록 설정한 경우 **테이블 모델 탐색기에서** **데이터 원본** > **가져오기 데이터 원본(레거시)을**마우스 오른쪽 단추로 클릭합니다.

![테이블 모델 탐색기의 레거시 데이터 원본](media/analysis-services-datasource/aas-import-legacy-datasources.png)

테이블 형식 1200 모델 프로젝트와 마찬가지로 **테이블 가져오기 마법사를** 사용하여 데이터 원본에 연결합니다. 연결 페이지에서 고급 을 **클릭합니다.** **고급 속성 설정에서**데이터 공급자 및 기타 연결 설정을 지정합니다.

![레거시 데이터 원본 고급 속성](media/analysis-services-datasource/aas-import-legacy-advanced.png)


## <a name="impersonation"></a>가장
경우에 따라 다른 가장 계정을 지정해야 할 수도 있습니다. 가장 계정은 Visual Studio 또는 SSMS에서 지정할 수 있습니다.

온-프레미스 데이터 원본의 경우:

* SQL 인증을 사용하는 경우 가장은 서비스 계정이어야 합니다.
* Windows 인증을 사용하는 경우 Windows 사용자/암호를 설정합니다. SQL Server의 경우 특정 가장 계정을 사용한 Windows 인증은 메모리 내 데이터 모델에 대해서만 지원됩니다.

클라우드 데이터 원본의 경우:

* SQL 인증을 사용하는 경우 가장은 서비스 계정이어야 합니다.

## <a name="oauth-credentials"></a>OAuth 자격 증명

메모리 내 모드, Azure SQL Database, Azure 시냅스 분석(SQL 데이터 웨어하우스), 역학 365 및 SharePoint List를 사용하여 1400 이상의 호환성 수준에서 테이블 형식 모델의 경우 OAuth 자격 증명을 지원합니다. Azure Analysis Services는 OAuth 데이터 원본에 대한 토큰 새로 고침을 관리하여 장기 실행 새로 고침 작업에 대한 시간 시간을 방지합니다. 유효한 토큰을 생성하려면 SSMS를 사용하여 자격 증명을 설정합니다.

직접 쿼리 모드는 OAuth 자격 증명으로 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계
[온-프레미스 게이트웨이](analysis-services-gateway.md)   
[서버 관리](analysis-services-manage.md)   

