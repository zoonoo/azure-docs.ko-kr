---
title: "Microsoft Azure에서 개인 데이터 검색, 식별 및 분류 | Microsoft Docs"
description: "데이터를 검색, 분류, 검색, 식별하는 방법에 대해 자세히 알아보기"
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 0d99df534da4575f3c34ec6b3475cdd1bdc3308a
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2017
---
# <a name="discover-identify-and-classify-personal-data-in-microsoft-azure"></a>Microsoft Azure에서 개인 데이터 검색, 식별 및 분류

이 문서에서는 Azure Data Catalog, Azure Active Directory, SQL Database, Azure HDInsight의 Hadoop 클러스터에 대한 파워 쿼리, Azure Information Protection, Azure Search 및 Azure Cosmos DB용 SQL 쿼리를 비롯한 여러 Azure 도구와 서비스에서 개인 데이터를 검색, 식별 및 분류하는 방법에 대한 지침을 제공합니다.

## <a name="scenario-problem-statement-and-goal"></a>시나리오, 문제 설명 및 목표

미국 기반 스포츠 회사는 다양한 개인 데이터 및 기타 데이터를 수집합니다. 고객 및 직원 데이터를 포함합니다. 회사는 데이터를 여러 데이터베이스에 유지하고 해당 Azure 환경에서 여러 다른 위치에 저장합니다. 스포츠 장비 판매 외에도 EU를 비롯한 전 세계에서 유명한 운동 이벤트에 대한 등록을 호스트하고 관리하며 경우에 따라 수집한 고객 데이터는 의료 정보를 포함합니다.

회사가 매년 많은 국제 자전거 투어를 호스트하고 전 세계의 여러 위치에 직원을 파견하기 때문에 몇 가지 데이터 집합의 규모가 매우 큽니다. 회사에는 고객 및 직원이 모두 사용하는 개발자 빌드 응용 프로그램이 있습니다.

회사에서는 다음과 같은 문제를 해결하려고 합니다.

- 고객 및 직원 개인 데이터는 적절한 액세스 및 보안을 보장하기 위해 회사에서 수집하는 다른 데이터와 분류/구분해야 합니다.
- 데이터 관리자는 Azure 환경의 다양한 영역에 걸친 고객 개인 데이터의 위치를 쉽게 검색해야 합니다.
- 공유 문서 및 전자 메일 통신에 표시되는 고객 및 직원 개인 데이터의 보안을 유지할 수 있도록 레이블이 지정되어야 합니다.
- 회사의 앱 개발자는 웹 및 모바일 앱에서 고객 및 직원 개인 데이터를 쉽게 검색할 수 있는 방법이 필요합니다.
- 또한 개발자는 개인 데이터에 대한 해당 문서 데이터베이스를 쿼리해야 합니다.

### <a name="company-goals"></a>회사 목표

- 모든 고객 및 직원 개인 데이터를 쉽게 찾을 수 있도록 Azure Data Catalog에서 태그가 지정되거나 주석이 추가되어야 합니다. 이상적으로 고객 및 직원 개인 데이터는 별도로 태그가 지정되거나 주석이 추가됩니다.
- 고객 및 직원 사용자 프로필의 개인 데이터 및 Azure Active Directory에 있는 작업 정보는 쉽게 찾을 수 있어야 합니다.
- 여러 SQL Databases에 있는 개인 데이터를 쉽게 쿼리할 수 있어야 합니다. 
- 회사의 대규모 데이터 집합의 일부는 Azure HDInsight를 통해 관리되고 Hadoop에 저장됩니다. 해당 데이터는 개인 데이터를 쿼리할 수 있도록 Excel로 가져올 수 있어야 합니다.
- 문서 및 전자 메일 통신에서 공유된 개인 데이터는 Azure Information Protection에서 분류되고 레이블이 지정되고 안전하게 보호됩니다.
- 회사의 앱 개발자는 Azure Search에서와 같이 빌드된 앱에서 고객 및 직원 개인 데이터를 검색할 수 있어야 합니다.
- 개발자는 해당 문서 데이터베이스에서 개인 데이터를 찾을 수 있어야 합니다.

## <a name="azure-active-directory-data-discovery"></a>Azure Active Directory: 데이터 검색

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)는 Microsoft의 클라우드 기반, 다중 테넌트 디렉터리 및 ID 관리 서비스입니다. [Azure Portal](https://portal.azure.com/)을 사용하여 [AAD(Azure Active Directory)](https://azure.microsoft.com/services/active-directory/) 환경에서 개인 데이터를 포함하는 고객 및 직원 사용자 프로필 및 사용자 작업 정보를 찾을 수 있습니다.

특정 사용자에 대한 개인 데이터를 찾거나 변경하려는 경우에 특히 유용합니다. 또한 사용자 프로필 및 작업 정보를 추가하거나 변경할 수 있습니다. 디렉터리에 대한 전역 관리자인 계정으로 로그인해야 합니다.

### <a name="how-do-i-locate-or-view-user-profile-and-work-information"></a>사용자 프로필 및 작업 정보를 찾고 보려면 어떻게 할까요?

1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **더 많은 서비스**를 선택하고 텍스트 상자에 **사용자 및 그룹**을 입력한 다음 **Enter**를 선택합니다.

   ![사용자 프로필 및 작업 정보를 찾으려면 어떻게 할까요?](media/how-to-discover-classify-personal-data-azure/user-profile.png)

3. **사용자 및 그룹** 블레이드에서 **사용자**를 선택합니다.

  ![사용자 및 그룹 열기](media/how-to-discover-classify-personal-data-azure/users-groups.png)

4. **사용자 및 그룹 - 사용자** 블레이드에서 목록의 사용자를 선택한 다음 선택한 사용자에 대한 블레이드에서 **프로필**을 선택하여 개인 데이터를 포함할 수 있는 사용자 프로필 정보를 볼 수 있습니다.

  ![사용자 선택](media/how-to-discover-classify-personal-data-azure/select-user.png)

5. 사용자 프로필 정보를 추가하거나 변경해야 하는 경우 명령 모음에서 수행한 후에 **저장**을 선택합니다.
6. 선택한 사용자에 대한 블레이드에서 **작업 정보**를 선택하여 개인 데이터를 포함할 수 있는 사용자 작업 정보를 볼 수 있습니다.

 ![작업 정보 보기](media/how-to-discover-classify-personal-data-azure/work-info.png)

7. 사용자 작업 정보를 추가하거나 변경해야 하는 경우 명령 모음에서 수행한 후에 **저장**을 선택합니다.

## <a name="azure-sql-database-data-discovery"></a>Azure SQL Database: 데이터 검색

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50)는 개발자가 응용 프로그램을 빌드하고 유지하는 데 유용한 클라우드 데이터베이스입니다. 표준 SQL 쿼리를 사용하여 개인 데이터를 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50)에서 찾을 수 있습니다. Azure SQL 탄력적 쿼리(미리 보기)를 사용하면 데이터베이스 간 쿼리를 수행할 수 있습니다.

자세한 [SQL Database](../sql-database/sql-database-technical-overview.md) 자습서에서는 데이터 쿼리를 빌드하고 실행하는 방법을 포함하여 SQL Database를 사용하는 여러 측면을 설명합니다. 특정 섹션에 대한 링크가 있는 자습서에서 사용할 수 있는 정보의 요약은 다음과 같습니다.

### <a name="how-do-i-build-a-sql-database"></a>SQL Database를 빌드하려면 어떻게 할까요?

수행할 수 있는 세 가지 방법이 있습니다.

- [Azure Portal](https://portal.azure.com/)에서 Azure SQL Database를 만들 수 있습니다. 이 자습서에서는 리소스 그룹 및 논리 서버 내에서 계산 및 저장소 리소스의 특정 집합을 사용합니다. AdventureWorks라는 가상 회사의 샘플 데이터를 사용합니다. 서버 수준 방화벽 규칙도 만듭니다. 이를 수행하는 방법을 알아보려면 [Azure Portal에서 Azure SQL Database 만들기](../sql-database/sql-database-get-started-portal.md) 자습서를 방문합니다.

  ![Azure SQL Database 만들기](media/how-to-discover-classify-personal-data-azure/create-database.png)
- SQL Database는 브라우저 기반 명령줄 도구인 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) CLI에서 만들 수도 있습니다. 이 도구는 Azure Portal에서 사용할 수 있으며 거기서 직접 실행할 수 있습니다. 이 자습서에서는 도구를 시작하고, 스크립트 변수를 정의하고, 리소스 그룹 및 논리 서버를 만들고, 서버 방화벽 규칙을 구성합니다. 샘플 데이터로 데이터베이스를 만듭니다. 이러한 방식으로 데이터베이스를 만드는 방법을 알아보려면 [Azure CLI를 사용하여 단일 Azure SQL Database 만들기](../sql-database/sql-database-get-started-cli.md) 자습서를 방문합니다.

  ![CLIT 자습서](media/how-to-discover-classify-personal-data-azure/cli-tutorial.png)

>[!NOTE]
Linux 관리자와 개발자는 일반적으로 Azure CLI를 사용합니다. 일부 사용자는 Azure CLI가 세 번째 옵션인 PowerShell보다 더 쉽고 직관적이라고 생각합니다.

- 마지막으로 Azure 및 기타 리소스를 만들고 관리하는 데 사용하는 명령줄/스크립트 도구인 PowerShell을 사용하여 SQL Database를 만들 수 있습니다. 이 자습서에서는 도구를 시작하고, 스크립트 변수를 정의하고, 리소스 그룹 및 논리 서버를 만들고, 서버 방화벽 규칙을 구성합니다. 샘플 데이터로 데이터베이스를 만듭니다.

자습서에는 Azure PowerShell 모듈 버전 4.0 이상이 필요합니다. Get-module-ListAvailable AzureRM을 실행하여 버전을 확인합니다. 설치 또는 업그레이드해야 하는 경우 Azure PowerShell 모듈 설치를 참조하세요.

```PowerShell
New-AzureRmSQLDatabase -ResourceGroupName $resourcegroupname `
-ServerName $servername `
-DatabaseName $databasename `
-RequestedServiceObjectiveName "s0"
```

이러한 방식으로 데이터베이스를 만드는 방법을 알아보려면 [Powershell을 사용하여 단일 Azure SQL Database 만들기](../sql-database/sql-database-get-started-powershell.md) 자습서를 방문합니다.

>[!Note]
Windows 관리자는 PowerShell을 사용하는 경향이 있지만 일부는 Azure CLI를 사용합니다.

### <a name="how-do-i-search-for-personal-data-in-sql-database-in-the-azure-portal"></a>Azure Portal의 SQL Database에서 개인 데이터를 검색하려면 어떻게 할까요?**

개인 데이터를 검색하기 위해 Azure Portal 내에서 기본 제공 쿼리 편집기 도구를 사용할 수 있습니다. SQL Server 관리자 로그인 및 암호를 사용하여 도구에 로그인하고 쿼리를 입력합니다.

  ![포털을 사용하여 SQL 검색](media/how-to-discover-classify-personal-data-azure/search-sql-portal.png)

자습서의 5단계에서는 쿼리 편집기 창에서 예제 쿼리를 보여주지만 개인 정보나 중요한 정보에 집중하지 않습니다.(또한 두 테이블의 데이터를 결합하고 반환되는 데이터 집합에서 원본 열의 별칭을 만듭니다.) 다음 스크린샷에서는 반환되는 결과 창뿐만 아니라 5단계의 쿼리를 보여줍니다.

  ![쿼리 편집기](media/how-to-discover-classify-personal-data-azure/query-editor.png)

데이터베이스가 MyTable이라고 하는 경우 개인 정보의 샘플 쿼리는 이름, 사회 보장 번호 및 ID 번호를 포함하고 다음과 같이 표시될 수 있습니다.

“SELECT Name, SSN, ID number FROM MyTable”

쿼리를 실행한 후 **결과** 창에서 결과를 확인합니다.

Azure Portal의 SQL Database를 쿼리하는 방법에 대한 자세한 내용은 자습서의 [SQL Database 쿼리](../sql-database/sql-database-get-started-portal.md) 섹션을 방문합니다.

### <a name="how-do-i-search-for-data-across-multiple-databases"></a>여러 데이터베이스 간에 데이터를 검색하려면 어떻게 할까요?

SQL 탄력적인 쿼리(미리 보기)를 사용하면 데이터베이스 및 여러 데이터베이스 쿼리 간에 작업을 수행하고 단일 결과 반환할 수 있습니다. [자습서 개요](../sql-database/sql-database-elastic-query-overview.md)는 시나리오에 대한 자세한 설명을 포함하고 수직 및 행 데이터베이스 분할 간에 차이점을 설명합니다. 행 분할은 “분할”이라고 합니다.

  ![수직 분할](media/how-to-discover-classify-personal-data-azure/vertical-partition.png)

  ![행 분할](media/how-to-discover-classify-personal-data-azure/horizontal.png)

시작하려면 [Azure SQL Database 탄력적 쿼리 개요(미리 보기)](../sql-database/sql-database-elastic-query-overview.md) 페이지를 방문합니다.

#### <a name="power-query-for-importing-azure-hdinsight-hadoop-clusters-data-discovery-for-large-data-sets"></a>파워 쿼리(Azure HDInsight Hadoop 클러스터 가져오기): 큰 데이터 집합의 데이터 검색

Hadoop는 오픈 소스 Apache 저장소이며 Hadoop 클러스터에서 분석되고 저장되는 대규모 데이터 집합에 대한 처리 서비스입니다. [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)를 사용하면 Azure에서 Hadoop 클러스터를 사용할 수 있습니다. 파워 쿼리는 Excel 추가 기능으로 무엇보다도 사용자가 다양한 소스에서 데이터를 검색할 수 있도록 합니다.

파워 쿼리를 사용하여 Azure HDInsight의 Hadoop 클러스터와 연결된 개인 데이터를 Excel로 가져올 수 있습니다. 데이터가 Excel 형식이면 쿼리를 사용하여 식별할 수 있습니다.

#### <a name="how-do-i-use-excel-power-query-to-import-hadoop-clusters-in-azure-hdinsight-into-excel"></a>Excel 파워 쿼리를 사용하여 Azure HDInsight의 Hadoop 클러스터를 Excel로 가져오려면 어떻게 할까요?

HDInsight 자습서에서는 이 전체 과정을 설명합니다. 필수 구성 요소를 설명하고 [Azure HDInsight 시작](../hdinsight/hadoop/apache-hadoop-linux-tutorial-get-started.md) 자습서에 대한 링크를 포함합니다. 지침은 Excel 2016, 2013 및 2010을 다룹니다(단계는 이전 버전의 Excel와 약간 다름). Excel 파워 쿼리 추가 기능이 설치되지 않은 경우 이 자습서에서는 가져오는 방법을 보여줍니다. Excel에서 자습서를 시작하고 클러스터와 연결된 Azure Blob Storage 계정이 필요합니다.

  ![Excel에서 쿼리](media/how-to-discover-classify-personal-data-azure/excel.png)

이 작업을 수행하는 방법을 알아보려면 [파워 쿼리를 사용하여 Hadoop에 Excel 연결](../hdinsight/hadoop/apache-hadoop-connect-excel-power-query.md) 자습서를 방문합니다.

소스: [파워 쿼리를 사용하여 Hadoop에 Excel 연결](../hdinsight/hadoop/apache-hadoop-connect-excel-power-query.md)

## <a name="azure-information-protection-personal-data-classification-for-documents-and-email"></a>Azure Information Protection: 문서 및 전자 메일에 대한 개인 데이터 분류

[Azure Information Protection](https://www.microsoft.com/cloud-platform/azure-information-protection)을 통해 Azure 고객이 내부나 외부에서 공유 문서 및 전자 메일 통신을 분류하고 보호하는 레이블을 적용할 수 있습니다. 이러한 항목 중 일부는 고객 또는 직원 개인 정보를 포함할 수 있습니다. 관리자 또는 사용자가 자동 또는 수동으로 규칙 및 조건을 정의할 수 있습니다. 예를 들어 사용자가 신용 카드 정보를 포함하는 문서를 저장하는 경우 관리자가 구성한 레이블 권장 사항을 확인하게 됩니다.

### <a name="how-do-i-try-it"></a>시도하려면 어떻게 할까요?

조직에는 적합한지 확인하기 위해 Azure Information Protection을 사용해 보려는 경우 [빠른 시작 자습서](https://docs.microsoft.com/information-protection/get-started/infoprotect-quick-start-tutorial)를 방문합니다. 설치, 정책 구성, 분류 확인, 레이블 지정, 작업 중 공유 등 다섯 가지 기본 단계를 30분 이내에 설명해야 합니다.

### <a name="how-do-i-deploy-it"></a>배포하려면 어떻게 할까요?

조직에 Azure Information Protection을 배포하려는 경우 [분류, 레이블 지정 및 보호를 위한 배포 로드맵](https://docs.microsoft.com/information-protection/plan-design/deployment-roadmap)을 방문합니다.

### <a name="is-there-anything-else-i-should-know"></a>알아야 할 다른 항목이 있나요?

설정하는 방법을 검토하는 데 도움이 되는 상호 보완적인 정보는 [준비, 설정, 보호](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/21/azure-information-protection-ready-set-protect/)
블로그 게시물을 방문합니다. Azure Information Protection에 대한 자세한 내용은 아래 나열된 링크 자세히 알아보기를 확인합니다.

## <a name="azure-search-data-discovery-for-developer-apps"></a>Azure Search: 개발자 앱에 대한 데이터 검색

[Azure Search](https://azure.microsoft.com/services/search/)는 클라우드 검색 솔루션 개발자를 위한 기능이며 응용 프로그램에 다양한 데이터 검색 환경을 제공합니다. Azure Search를 사용하면 Azure Cosmo DB, Azure SQL Database, Azure Blob Storage, Azure Table 저장소 또는 사용자 지정 고객 JSON 데이터에서 소싱된 사용자 정의 인덱스에서 데이터를 찾을 수 있습니다. 개인 데이터 형식 또는 특정 개인의 개인 데이터를 검색하도록 Azure Search REST API를 사용하여 Lucene 쿼리를 구성할 수도 있습니다. 기능에는 전체 텍스트 검색, 단순 쿼리 구문 및 Lucene 쿼리 구문이 포함됩니다. 

## <a name="how-do-i-use-sql-to-query-data"></a>SQL을 사용하여 데이터를 쿼리하려면 어떻게 할까요?

기본 사항을 시작하려면 [Azure CosmosD DB: SQL을 사용하여 쿼리하는 방법](../cosmos-db/tutorial-query-documentdb.md) 자습서를 방문합니다. 이 자습서에서는 샘플 문서 및 두 가지 샘플 SQL 쿼리와 결과를 제공합니다.

SQL 쿼리를 빌드하는 방법에 대한 자세한 지침은 [Azure Cosmos DB Document DB API에 대한 SQL 쿼리](../cosmos-db/sql-api-sql-query.md)를 방문합니다.

Azure Cosmos DB 처음 사용하고 데이터베이스 만들기, 컬렉션 추가, 데이터 추가 방법을 알아보려는 경우 [Azure Cosmos DB: SQL API 웹앱 빌드](../cosmos-db/create-sql-api-dotnet.md) 빠른 시작 자습서를 방문합니다. Java, Python과 같은 .NET 이외의 언어로 작업을 수행하려는 경우 사이트를 가져오면 기본 설정된 언어를 선택하기만 합니다.

## <a name="next-steps"></a>다음 단계

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50)

[SQL Database 정의](../sql-database/sql-database-technical-overview.md)

[Azure Portal에서 사용할 수 있는 SQL Database 쿼리 편집기](https://azure.microsoft.com/blog/t-sql-query-editor-in-browser-azure-portal/)

[Azure Information Protection이란?](https://docs.microsoft.com/information-protection/understand-explore/what-is-information-protection)

[Azure Rights Management란?](https://docs.microsoft.com/information-protection/understand-explore/what-is-azure-rms)

[Azure Information Protection: 준비, 설정, 보호](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/21/azure-information-protection-ready-set-protect/)
