---
title: Azure Analysis Services에서 지원되는 데이터 원본 | Microsoft Docs
description: Azure Analysis Services의 데이터 모델에 지원되는 데이터 원본에 대해 설명합니다.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: cc47ff50aa7bb6dce58ed83f75f69fb600ad040b
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73146930"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Azure Analysis Services에서 지원되는 데이터 원본

Visual Studio에서 데이터 가져오기 또는 마법사 가져오기에 표시된 데이터 원본 및 커넥터는 Azure Analysis Services 및 SQL Server Analysis Services에 표시됩니다. 그러나 표시된 일부 데이터 원본 및 커넥터는 Azure Analysis Services에서 지원됩니다. 연결할 수 있는 데이터 원본의 형식은 모델 호환성 수준, 사용 가능한 데이터 커넥터, 인증 형식, 공급자 및 온-프레미스 데이터 게이트웨이 지원과 같은 많은 요인에 따라 달라집니다. 

## <a name="azure-data-sources"></a>Azure 데이터 원본

|데이터 원본  |메모리 내  |DirectQuery  |
|---------|---------|---------|
|Azure SQL Database<sup>[2](#azsqlmanaged)</sup>     |   yes      |    yes      |
|Azure SQL Data Warehouse     |   yes      |   yes       |
|Azure Blob Storage<sup>[1](#tab1400a)</sup>     |   yes       |    아닙니다.      |
|Azure Table Storage<sup>[1](#tab1400a)</sup>    |   yes       |    아닙니다.      |
|Azure Cosmos DB<sup>[1](#tab1400a)</sup>     |  yes        |  아닙니다.        |
|Azure Data Lake Store(Gen1)<sup>[1](#tab1400a)</sup>, <sup>[4](#gen2)</sup>      |   yes       |    아닙니다.      |
|Azure HDInsight HDFS<sup>[1](#tab1400a)</sup>     |     yes     |   아닙니다.       |
|Azure HDInsight Spark<sup>[1](#tab1400a)</sup>, <sup>[3](#databricks)</sup>     |   yes       |   아닙니다.       |
||||

<a name="tab1400a">1</a> - 테이블 형식 1400 이상 모델에만 해당됩니다.   
<a name="azsqlmanaged">2</a> - Azure SQL Database Managed Instance가 지원됩니다. 관리 되는 인스턴스는 개인 IP 주소를 사용 하 여 Azure VNet 내에서 실행 되므로 인스턴스에서 공용 끝점을 사용 하도록 설정 해야 합니다. 사용 하도록 설정 하지 않으면 온-프레미스 데이터 게이트웨이가 필요 합니다.    
<a name="databricks">3</a> - Spark 커넥터를 사용하는 Azure Databricks는 현재 지원되지 않습니다.   
<a name="gen2">4</a> - ADLS Gen2는 현재 지원되지 않습니다.


**공급자**   
Azure 데이터 원본에 연결된 메모리 내 모델 및 DirectQuery 모델은 .NET Framework Data Provider for SQL Server를 사용합니다.

## <a name="other-data-sources"></a>기타 데이터 원본

온-프레미스 데이터 원본 및 Azure AS 서버에 연결하려면 온-프레미스 게이트웨이가 필요합니다. 게이트웨이를 사용하는 경우 64비트 공급자는 필수입니다.

### <a name="in-memory-and-directquery"></a>메모리 내 및 DirectQuery

|데이터 원본 | 메모리 내 공급자 | DirectQuery 공급자 |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0, Microsoft OLE DB Provider for SQL Server, .NET Framework Data Provider for SQL Server | SQL Server용 .NET Framework 데이터 공급자 |
| SQL Server 데이터 웨어하우스 |SQL Server Native Client 11.0, Microsoft OLE DB Provider for SQL Server, .NET Framework Data Provider for SQL Server | SQL Server용 .NET Framework 데이터 공급자 |
| Oracle | Oracle에 대 한 OLE DB 공급자, .NET 용 Oracle Data Provider |.NET용 Oracle Data Provider |
| Teradata |Teradata용 OLE DB Provider, .NET용 Teradata Data Provider |.NET용 Teradata Data Provider |
| | | |

### <a name="in-memory-only"></a>메모리 내 전용

|데이터 원본  |  
|---------|
|Access 데이터베이스     |  
|Active Directory<sup>[1](#tab1400b)</sup>     |  
|Analysis Services     |  
|분석 플랫폼 시스템     |  
|CSV 파일  |
|Dynamics CRM<sup>[1](#tab1400b)</sup>     |  
|Excel 통합 문서     |  
|Exchange<sup>[1](#tab1400b)</sup>     |  
|Folder<sup>[1](#tab1400b)</sup>     |
|IBM Informix<sup>[1](#tab1400b)</sup>(베타) |
|JSON 문서<sup>[1](#tab1400b)</sup>     |  
|이진의 줄<sup>[1](#tab1400b)</sup>     | 
|MySQL Database     | 
|OData 피드<sup>[1](#tab1400b)</sup>     |  
|ODBC 쿼리     | 
|OLE DB     |   
|PostgreSQL 데이터베이스<sup>[1](#tab1400b)</sup>    | 
|Salesforce 개체<sup>[1](#tab1400b)</sup> |  
|Salesforce 보고서<sup>[1](#tab1400b)</sup> |
|SAP HANA<sup>[1](#tab1400b)</sup>    |  
|SAP Business Warehouse<sup>[1](#tab1400b)</sup>    |  
|SharePoint 목록<sup>[1](#tab1400b)</sup>, <sup>[2](#filesSP)</sup>     |   
|Sybase 데이터베이스     |  
|TXT 파일  |
|XML 테이블<sup>[1](#tab1400b)</sup>    |  
||
 
<a name="tab1400b">1</a> - 테이블 형식 1400 이상 모델에만 해당됩니다.   
<a name="filesSP">2</a> -온-프레미스 SharePoint의 파일은 지원 되지 않습니다.

## <a name="specifying-a-different-provider"></a>다른 공급자 지정

특정 데이터 원본에 연결할 때 Azure Analysis Service의 데이터 모델은 서로 다른 데이터 공급자가 필요할 수 있습니다. 경우에 따라 SQL Server Native Client(SQLNCLI11)와 같은 네이티브 공급자를 사용하여 데이터 원본에 연결하는 테이블 형식 모델은 오류를 반환할 수 있습니다. SQLOLEDB 이외의 네이티브 공급자를 사용하는 경우 **공급자 'SQLNCLI11.1'이 등록되지 않았습니다.** 라는 오류 메시지가 표시될 수 있습니다. 또는 온-프레미스 데이터 원본에 연결 하는 DirectQuery 모델이 있고 네이티브 공급자를 사용 하는 경우, **OLE DB 행 집합을 만드는 동안 오류 메시지가 표시 될 수 있습니다. ' LIMIT ' 근처의 구문이 잘못 되었습니다**.

온-프레미스 SQL Server Analysis Services 테이블 형식 모델을 Azure Analysis Services로 마이그레이션하는 경우 공급자를 변경해야 할 수도 있습니다.

**공급자를 지정하려면**

1. Visual Studio > **테이블 형식 모델 탐색기** 에서 **데이터 원본 > 데이터**원본 연결을 마우스 오른쪽 단추로 클릭 한 다음 **데이터 원본 편집**을 클릭 합니다.
2. **연결 편집**에서 **고급**을 클릭하여 고급 속성 창을 엽니다.
3. **고급 속성 설정** > **공급자**에서 적절한 공급자를 선택합니다.

## <a name="impersonation"></a>가장
경우에 따라 다른 가장 계정을 지정해야 할 수도 있습니다. Visual Studio 또는 SSMS에서 가장 계정을 지정할 수 있습니다.

온-프레미스 데이터 원본의 경우:

* SQL 인증을 사용하는 경우 가장은 서비스 계정이어야 합니다.
* Windows 인증을 사용하는 경우 Windows 사용자/암호를 설정합니다. SQL Server의 경우 특정 가장 계정을 사용한 Windows 인증은 메모리 내 데이터 모델에 대해서만 지원됩니다.

클라우드 데이터 원본의 경우:

* SQL 인증을 사용하는 경우 가장은 서비스 계정이어야 합니다.

## <a name="oauth-credentials"></a>OAuth 자격 증명

1400 이상의 호환성 수준에서 테이블 형식 모델의 경우 Azure SQL Database, Azure SQL Data Warehouse, Dynamics 365 및 SharePoint 목록에서 OAuth 자격 증명을 지원 합니다. Azure Analysis Services는 장기 실행 새로 고침 작업에 대 한 시간 제한을 방지 하기 위해 OAuth 데이터 원본에 대 한 토큰 새로 고침을 관리 합니다. 유효한 토큰을 생성 하려면 SSMS를 사용 하 여 자격 증명을 설정 합니다.

## <a name="next-steps"></a>다음 단계
[온-프레미스 게이트웨이](analysis-services-gateway.md)   
[서버 관리](analysis-services-manage.md)   

