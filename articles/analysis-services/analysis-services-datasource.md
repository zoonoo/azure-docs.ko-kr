---
title: "Azure Analysis Services에서 지원되는 데이터 원본 | Microsoft Docs"
description: "Azure Analysis Services의 데이터 모델에 지원되는 데이터 원본에 대해 설명합니다."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 6ec63319-ff9b-4b01-a1cd-274481dc8995
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: 33115ee35670407c3b046f70a5fbebc47284b4b9
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Azure Analysis Services에서 지원되는 데이터 원본
Azure Analysis Services는 조직의 클라우드 및 온-프레미스 데이터 원본에 대한 연결을 지원합니다. 지원되는 데이터 원본이 항상 추가되고 있습니다. 자주 확인하세요. 

현재 지원되는 데이터 원본은 다음과 같습니다.

| 클라우드  |
|---|
| Azure Blob Storage*  |
| Azure SQL Database  |
| Azure Data Warehouse |


| 온-프레미스  |   |   |   |
|---|---|---|---|
| Access 데이터베이스  | 폴더* | Oracle 데이터베이스  | Teradata 데이터베이스 |
| Active Directory*  | JSON 문서*  | Postgre SQL Database*  |XML 테이블* |
| Analysis Services  | 이진의 줄*  | SAP HANA*  |
| 분석 플랫폼 시스템  | MySQL 데이터베이스  | SAP Business Warehouse*  | |
| Dynamics CRM*  | OData 피드*  | SharePoint*  |
| Excel 통합 문서  | ODBC 쿼리  | SQL Database  |
| Exchange*  | OLE DB  | Sybase 데이터베이스  |

\* 테이블 형식 1400 모델에만 해당합니다. 

> [!IMPORTANT]
> 온-프레미스 데이터 원본에 연결하려면 사용 중인 환경의 컴퓨터에 [온-프레미스 데이터 게이트웨이](analysis-services-gateway.md)가 설치되어 있어야 합니다.

## <a name="data-providers"></a>데이터 공급자

특정 데이터 원본에 연결할 때 Azure Analysis Service의 데이터 모델은 서로 다른 데이터 공급자가 필요할 수 있습니다. 경우에 따라 SQL Server Native Client(SQLNCLI11)와 같은 네이티브 공급자를 사용하여 데이터 원본에 연결하는 테이블 형식 모델은 오류를 반환할 수 있습니다.

Azure SQL Database 같은 클라우드 데이터 원본에 연결되는 데이터 모델의 경우 SQLOLEDB 이외의 네이티브 공급자를 사용하면 **"'SQLNCLI11.1'은 등록되지 않은 공급자입니다."**라는 오류 메시지가 나타날 수 있습니다. 또는 온-프레미스 데이터 원본에 DirectQuery 모델을 연결한 경우, 네이티브 공급자를 사용하는 경우 **"OLE DB 행 집합을 만드는 데 오류가 발생했습니다. 'LIMIT' 가까이에 잘못된 구문이 있습니다."**라는 오류 메시지가 나타날 수 있습니다.

다음 데이터 원본 공급자는 클라우드 또는 온-프레미스의 데이터 원본에 연결할 경우 메모리 내 또는 직접 쿼리 데이터 모델에 지원됩니다.

### <a name="cloud"></a>클라우드
| **데이터 원본** | **메모리 내** | **DirectQuery** |
|  --- | --- | --- |
| Azure SQL Data Warehouse |SQL Server용 .NET Framework 데이터 공급자 |SQL Server용 .NET Framework 데이터 공급자 |
| Azure SQL Database |SQL Server용 .NET Framework 데이터 공급자 |SQL Server용 .NET Framework 데이터 공급자 | |

### <a name="on-premises-via-gateway"></a>온-프레미스(게이트웨이 사용)
|**데이터 원본** | **메모리 내** | **DirectQuery** |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0 |SQL Server용 .NET Framework 데이터 공급자 |
| SQL Server |SQL Server용 Microsoft OLE DB Provider |SQL Server용 .NET Framework 데이터 공급자 | |
| SQL Server |SQL Server용 .NET Framework 데이터 공급자 |SQL Server용 .NET Framework 데이터 공급자 | |
| Oracle |Oracle용 Microsoft OLE DB Provider |.NET용 Oracle Data Provider | |
| Oracle |.NET용 Oracle Data Provider |.NET용 Oracle Data Provider | |
| Teradata |Teradata용 OLE DB Provider |.NET용 Teradata Data Provider | |
| Teradata |.NET용 Teradata Data Provider |.NET용 Teradata Data Provider | |
| 분석 플랫폼 시스템 |SQL Server용 .NET Framework 데이터 공급자 |SQL Server용 .NET Framework 데이터 공급자 | |

> [!NOTE]
> 온-프레미스 게이트웨이를 사용할 때 64비트 공급자가 설치되었는지 확인합니다.
> 
> 

온-프레미스 SQL Server Analysis Services 테이블 형식 모델을 Azure Analysis Services로 마이그레이션하는 경우 공급자를 변경해야 할 수도 있습니다.

**데이터 원본 공급자를 지정하려면**

1. SSDT > **테이블 형식 모델 탐색기** > **데이터 원본**에서 데이터 원본 연결을 마우스 오른쪽 단추로 클릭한 다음 **데이터 원본 편집**을 클릭합니다.
2. **연결 편집**에서 **고급**을 클릭하여 고급 속성 창을 엽니다.
3. **고급 속성 설정** > **공급자**에서 적절한 공급자를 선택합니다.

## <a name="impersonation"></a>가장
경우에 따라 다른 가장 계정을 지정해야 할 수도 있습니다. SSDT 또는 SSMS에서 가장 계정을 지정할 수 있습니다.

온-프레미스 데이터 원본의 경우:

* SQL 인증을 사용하는 경우 가장은 서비스 계정이어야 합니다.
* Windows 인증을 사용하는 경우 Windows 사용자/암호를 설정합니다. SQL Server의 경우 특정 가장 계정을 사용한 Windows 인증은 메모리 내 데이터 모델에 대해서만 지원됩니다.

클라우드 데이터 원본의 경우:

* SQL 인증을 사용하는 경우 가장은 서비스 계정이어야 합니다.

## <a name="next-steps"></a>다음 단계
온-프레미스 데이터 원본이 있는 경우 [온-프레미스 게이트웨이](analysis-services-gateway.md)를 설치해야 합니다.   
SSDT 또는 SSMS에서 서버를 관리하는 방법에 대해 알아보려면 [사용자 서버 관리](analysis-services-manage.md)를 참조하세요.

