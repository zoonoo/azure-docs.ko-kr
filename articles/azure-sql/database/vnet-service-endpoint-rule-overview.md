---
title: Azure SQL Database의 데이터베이스에 대 한 가상 네트워크 끝점 및 규칙
description: 서브넷을 가상 네트워크 서비스 끝점으로 표시 합니다. 그런 다음 데이터베이스에 대 한 ACL에 끝점을 가상 네트워크 규칙으로 추가 합니다. 그런 다음 데이터베이스는 모든 가상 컴퓨터 및 서브넷의 다른 노드에서 보낸 통신을 수락 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: d480239c0eb99ed48c13ec2fdb5b052574acc318
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97092502"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Azure SQL Database의 서버에 대 한 가상 네트워크 서비스 끝점 및 규칙 사용

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

*가상 네트워크 규칙* 은 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 의 데이터베이스 및 탄력적 [Azure SQL Database](sql-database-paas-overview.md) 풀에 대 한 서버가 가상 네트워크의 특정 서브넷에서 보낸 통신을 수락 하는지 여부를 제어 하는 방화벽 보안 기능입니다. 이 문서에서는 가상 네트워크 규칙이 때때로 SQL Database 및 Azure Synapse Analytics에서 데이터베이스에 대 한 통신을 안전 하 게 허용 하는 가장 좋은 옵션을 설명 합니다.

> [!NOTE]
> 이 문서는 SQL Database 및 Azure Synapse 분석 모두에 적용 됩니다. 간단히 하기 위해 *데이터베이스* 라는 용어는 SQL Database 및 Azure Synapse Analytics의 두 데이터베이스를 모두 나타냅니다. 마찬가지로 *서버* 에 대 한 모든 참조는 SQL Database 및 Azure Synapse Analytics를 호스팅하는 [논리 SQL server](logical-servers.md) 를 참조 합니다.

가상 네트워크 규칙을 만들려면 먼저 참조할 규칙에 대한 [가상 네트워크 서비스 엔드포인트][vm-virtual-network-service-endpoints-overview-649d]가 있어야 합니다.

## <a name="create-a-virtual-network-rule"></a>가상 네트워크 규칙 만들기

가상 네트워크 규칙만 만들려면 [이 문서의 뒷부분에](#anchor-how-to-by-using-firewall-portal-59j)나오는 단계 및 설명으로 건너뛸 수 있습니다.

## <a name="details-about-virtual-network-rules"></a>가상 네트워크 규칙에 대한 세부 정보

이 섹션에서는 가상 네트워크 규칙에 대한 여러 가지 세부 정보를 설명합니다.

### <a name="only-one-geographic-region"></a>단 하나의 지리적 지역

각 가상 네트워크 서비스 끝점은 하나의 Azure 지역에만 적용 됩니다. 끝점은 다른 지역이 서브넷의 통신을 수락 하도록 설정 하지 않습니다.

가상 네트워크 규칙은 기본 엔드포인트가 적용되는 지역으로 제한됩니다.

### <a name="server-level-not-database-level"></a>데이터베이스 수준이 아닌 서버 수준

각 가상 네트워크 규칙은 서버에 있는 하나의 특정 데이터베이스 뿐만 아니라 전체 서버에 적용 됩니다. 즉, 가상 네트워크 규칙은 데이터베이스 수준이 아니라 서버 수준에서 적용 됩니다.

이와 달리 IP 규칙은 각 수준에서 적용될 수 있습니다.

### <a name="security-administration-roles"></a>보안 관리 역할

가상 네트워크 서비스 끝점의 관리에서 보안 역할의 분리가 있습니다. 다음과 같은 각 역할의 작업이 필요합니다.

- **네트워크 관리자 ([네트워크 참가자](../../role-based-access-control/built-in-roles.md#network-contributor) 역할):** &nbsp; 끝점을 켭니다.
- **데이터베이스 관리자 ([SQL Server 참가자](../../role-based-access-control/built-in-roles.md#sql-server-contributor) 역할):** &nbsp; ACL (액세스 제어 목록)을 업데이트 하 여 지정 된 서브넷을 서버에 추가 합니다.

#### <a name="azure-rbac-alternative"></a>Azure RBAC 대체

네트워크 관리자 및 데이터베이스 관리자 역할에는 가상 네트워크 규칙을 관리하는 데 필요한 것보다 많은 기능이 포함됩니다. 해당 기능의 하위 집합만 필요합니다.

Azure에서 [RBAC(역할 기반 액세스 제어)][rbac-what-is-813s]를 사용하여 기능의 필요한 하위 집합만 포함된 단일 사용자 지정 역할을 만들 수도 있습니다. 네트워크 관리자 또는 데이터베이스 관리자를 포함 하는 대신 사용자 지정 역할을 사용할 수 있습니다. 사용자를 사용자 지정 역할에 추가 하 고 다른 두 개의 주요 관리자 역할에 사용자를 추가 하는 경우 보안 노출의 노출 영역이 낮습니다.

> [!NOTE]
> 경우에 따라 SQL Database 데이터베이스와 가상 네트워크 서브넷은 서로 다른 구독에 있습니다. 이러한 경우 다음 구성을 확인 해야 합니다.
>
> - 두 구독은 동일한 Azure Active Directory (Azure AD) 테 넌 트에 있어야 합니다.
> - 서비스 끝점을 사용 하도록 설정 하 고 지정 된 서버에 가상 네트워크 서브넷을 추가 하는 등의 작업을 시작 하는 데 필요한 권한이 사용자에 게 있습니다.
> - 두 구독 모두 Microsoft.Sql 공급자에 등록되어야 합니다.

## <a name="limitations"></a>제한 사항

SQL Database 가상 네트워크 규칙 기능에는 다음과 같은 제한 사항이 있습니다.

- SQL Database의 데이터베이스에 대 한 방화벽에서 각 가상 네트워크 규칙은 서브넷을 참조 합니다. 이러한 모든 참조 된 서브넷은 데이터베이스를 호스트 하는 동일한 지역에서 호스팅되어야 합니다.
- 각 서버에는 가상 네트워크에 대해 최대 128 개의 ACL 항목이 있을 수 있습니다.
- 가상 네트워크 규칙은 [클래식 배포 모델][arm-deployment-model-568f] 네트워크가 아닌 Azure Resource Manager 가상 네트워크에만 적용 됩니다.
- SQL Database에 대 한 가상 네트워크 서비스 끝점을 켜면 Azure Database for MySQL 및 Azure Database for PostgreSQL에 대 한 끝점도 활성화 됩니다. 끝점이 **ON** 으로 설정 되어 있으면 끝점에서 Azure Database for MySQL 또는 Azure Database for PostgreSQL 인스턴스로 연결 하려고 하면 실패할 수 있습니다.
  - 기본적인 이유는 Azure Database for MySQL 및 Azure Database for PostgreSQL에 가상 네트워크 규칙이 구성 되어 있지 않을 수 있기 때문입니다. Azure Database for MySQL 및 Azure Database for PostgreSQL에 대 한 가상 네트워크 규칙을 구성 해야 합니다. 그러면 연결에 성공 합니다.
  - 이미 개인 끝점으로 구성 된 SQL 논리 서버에서 가상 네트워크 방화벽 규칙을 정의 하려면 **공용 네트워크 액세스 거부** 를 **아니요** 로 설정 합니다.
- 방화벽에서 IP 주소 범위는 다음 네트워킹 항목에 적용 되지만 가상 네트워크 규칙은 그렇지 않습니다.
  - [S2S (사이트 간) VPN (가상 사설망)][vpn-gateway-indexmd-608y]
  - [Azure express](../../expressroute/index.yml) 경로를 통한 온-프레미스

### <a name="considerations-when-you-use-service-endpoints"></a>서비스 끝점을 사용할 때의 고려 사항

SQL Database에 대 한 서비스 끝점을 사용 하는 경우 다음 고려 사항을 검토 합니다.

- **Azure SQL Database 공용 Ip에 대 한 아웃 바운드가 필요 합니다.** Ip를 SQL Database 하 여 연결을 허용 하려면 NSGs (네트워크 보안 그룹)를 열어야 합니다. SQL Database에 대 한 NSG [서비스 태그](../../virtual-network/network-security-groups-overview.md#service-tags) 를 사용 하 여이 작업을 수행할 수 있습니다.

### <a name="expressroute"></a>ExpressRoute

온-프레미스에서 [express](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 경로를 사용 하는 경우 공용 피어 링 또는 Microsoft 피어 링의 경우 사용 되는 NAT IP 주소를 식별 해야 합니다. 공용 피어링의 경우 기본적으로 각 ExpressRoute 회로는 트래픽이 Microsoft Azure 네트워크 백본으로 들어갈 때 Azure 서비스 트래픽에 적용되는 두 개의 NAT IP 주소를 사용합니다. Microsoft 피어 링의 경우 사용 되는 NAT IP 주소는 고객 또는 서비스 공급자가 제공 합니다. 서비스 리소스에 대한 액세스를 허용하려면 리소스 IP 방화벽 설정에서 이러한 공용 IP 주소를 허용해야 합니다. ExpressRoute 회로 IP 주소를 찾으려면 Azure Portal을 통해 [ExpressRoute에서 지원 티켓을 엽니다](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). Express 경로 공용 및 Microsoft 피어 링에 대 한 NAT에 대 한 자세한 내용은 [Azure 공용 피어 링에 대 한 nat 요구 사항](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)을 참조 하세요.

회로에서 SQL Database로의 통신을 허용 하려면 NAT의 공용 IP 주소에 대 한 IP 네트워크 규칙을 만들어야 합니다.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-virtual-network-service-endpoints-with-azure-storage"></a>Azure Storage에서 virtual network 서비스 끝점을 사용 하는 경우의 영향

Azure Storage는 사용자가 Azure Storage 계정에 대한 연결성을 제한하도록 허용하는 동일한 기능을 구현했습니다. SQL Database를 사용 하는 Azure Storage 계정에서이 기능을 사용 하도록 선택 하는 경우 문제가 발생할 수 있습니다. 다음은이에 의해 영향을 받는 SQL Database 및 Azure Synapse Analytics 기능을 나열 하 고 설명 합니다.

### <a name="azure-synapse-analytics-polybase-and-copy-statement"></a>Azure Synapse Analytics PolyBase 및 COPY 문

PolyBase와 COPY 문은 일반적으로 처리량이 높은 데이터 수집을 위해 Azure Storage 계정에서 Azure Synapse Analytics로 데이터를 로드 하는 데 사용 됩니다. 데이터를 로드 하는 Azure Storage 계정이 한 가상 네트워크 서브넷 집합에만 액세스 하는 경우 PolyBase를 사용 하는 경우 연결이 중단 되 고 저장소 계정에 대 한 COPY 문이 중단 됩니다. 가상 네트워크에 대 한 보안을 유지 하는 Azure Storage에 연결 하는 Azure Synapse Analytics에서 복사 및 PolyBase를 사용 하 여 가져오기 및 내보내기 시나리오를 사용 하도록 설정 하려면이 섹션의 단계를 따르세요.

#### <a name="prerequisites"></a>필수 구성 요소

- [이 가이드](/powershell/azure/install-az-ps)를 사용 하 여 Azure PowerShell를 설치 합니다.
- 범용 v1 또는 Azure Blob Storage 계정이 있는 경우 범용 [v2 저장소 계정으로 업그레이드](../../storage/common/storage-account-upgrade.md)의 단계를 수행 하 여 먼저 범용 v2로 업그레이드 해야 합니다.
- Azure Storage 계정 **방화벽 및 가상 네트워크** 설정 메뉴에서 **신뢰할 수 있는 Microsoft 서비스가이 저장소 계정에 액세스 하도록 허용** 해야 합니다. 이 구성을 사용 하도록 설정 하면 Azure 백본에 네트워크 트래픽이 남아 있는 강력한 인증을 사용 하 여 PolyBase와 COPY 문이 저장소 계정에 연결할 수 있습니다. 자세한 내용은 [이 가이드](../../storage/common/storage-network-security.md#exceptions)를 참조 하세요.

> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 SQL Database에서 계속 지원 되지만 모든 향후 개발은 Az. Sql 모듈에 대 한 것입니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 계속 수신할 예정입니다. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다. 호환성에 대한 자세한 내용은 [새로운 Azure PowerShell Az 모듈 소개](/powershell/azure/new-azureps-module-az)를 참조하세요.

#### <a name="steps"></a>단계

1. 독립 실행형 전용 SQL 풀을 사용 하는 경우 PowerShell을 사용 하 여 SQL server를 Azure AD에 등록 합니다.

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Azure Synapse Analytics 작업 영역 내의 전용 SQL 풀에는이 단계가 필요 하지 않습니다.

1. Azure Synapse Analytics 작업 영역이 있는 경우 작업 영역 시스템 관리 id를 등록 합니다.

   1. Azure Portal Azure Synapse Analytics 작업 영역으로 이동 합니다.
   2. **관리 되는 id** 창으로 이동 합니다.
   3. **파이프라인 허용** 옵션을 사용 하도록 설정 했는지 확인 합니다.
   
1. [저장소 계정 만들기](../../storage/common/storage-account-create.md)의 단계를 수행 하 여 **범용 v2 저장소 계정을** 만듭니다.

   > [!NOTE]
   >
   > - 범용 v1 또는 Blob Storage 계정이 있는 경우 먼저 범용 [v2 저장소 계정으로 업그레이드](../../storage/common/storage-account-upgrade.md)의 단계에 따라 *v2로 업그레이드* 해야 합니다.
   > - Azure Data Lake Storage Gen2에 대 한 알려진 문제는 [Azure Data Lake Storage Gen2의 알려진 문제](../../storage/blobs/data-lake-storage-known-issues.md)를 참조 하세요.

1. 저장소 계정에서 **Access Control (IAM)** 로 이동 하 여 **역할 할당 추가** 를 선택 합니다. **저장소 Blob 데이터 참가자** azure 역할을 azure AD에 등록 한 전용 SQL 풀을 호스트 하는 서버 또는 작업 영역에 할당 합니다.

   > [!NOTE]
   > 저장소 계정에 대 한 소유자 권한이 있는 멤버만이 단계를 수행할 수 있습니다. 다양 한 Azure 기본 제공 역할에 대 한 자세한 내용은 [azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조 하세요.
  
1. Azure Storage 계정에 대 한 PolyBase 연결을 설정 하려면:

   1. 이전에 데이터베이스 [마스터 키](/sql/t-sql/statements/create-master-key-transact-sql) 를 만들지 않은 경우 데이터베이스 마스터 키를 만듭니다.

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. **Id = ' 관리 서비스 ID '** 인 데이터베이스 범위 자격 증명을 만듭니다.

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - 이 메커니즘은 내부적으로 [관리 되는 id](../../active-directory/managed-identities-azure-resources/overview.md) 를 사용 하기 때문에 Azure Storage 액세스 키로 암호를 지정할 필요가 없습니다.
       > - 가상 네트워크에 대해 보호 되는 Azure Storage 계정으로 작업 하려면 PolyBase 연결에 ID 이름이 **' 관리 서비스 ID '** 여야 합니다.

   1. `abfss://`PolyBase를 사용 하 여 범용 v2 저장소 계정에 연결 하는 체계를 사용 하 여 외부 데이터 원본을 만듭니다.

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - 범용 v1 또는 Blob Storage 계정과 연결 된 외부 테이블이 이미 있는 경우 해당 외부 테이블을 먼저 삭제 해야 합니다. 그런 다음 해당 하는 외부 데이터 원본을 삭제 합니다. 다음으로, `abfss://` 이전에 표시 된 것 처럼 범용 v2 저장소 계정에 연결 하는 체계를 사용 하 여 외부 데이터 원본을 만듭니다. 그런 다음이 새 외부 데이터 원본을 사용 하 여 모든 외부 테이블을 다시 만듭니다. [스크립트 생성 및 게시 마법사](/sql/ssms/scripting/generate-and-publish-scripts-wizard) 를 사용 하 여 모든 외부 테이블에 대 한 만들기 스크립트를 쉽게 생성할 수 있습니다.
       > - 구성표에 대 한 자세한 내용은 `abfss://` [Azure Data Lake Storage Gen2 URI 사용](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md)을 참조 하세요.
       > - 외부 데이터 원본 만들기에 대 한 자세한 내용은 [이 가이드](/sql/t-sql/statements/create-external-data-source-transact-sql)를 참조 하세요.

   1. [외부 테이블](/sql/t-sql/statements/create-external-table-transact-sql)을 사용 하 여 일반적인 방식으로 쿼리 합니다.

### <a name="sql-database-blob-auditing"></a>SQL Database blob 감사

Blob 감사는 사용자 고유의 스토리지 계정에 감사 로그를 푸시합니다. 이 저장소 계정에서 가상 네트워크 서비스 끝점 기능을 사용 하는 경우 SQL Database에서 저장소 계정으로의 연결이 중단 됩니다.

## <a name="add-a-virtual-network-firewall-rule-to-your-server"></a>서버에 가상 네트워크 방화벽 규칙 추가

긴 전,이 기능이 향상 되기 전에는 방화벽에서 라이브 가상 네트워크 규칙을 구현 하기 전에 가상 네트워크 서비스 끝점을 설정 해야 했습니다. SQL Database의 데이터베이스에 대해 지정 된 가상 네트워크 서브넷과 관련 된 끝점입니다. 1 월 2018을 기준으로 **IgnoreMissingVNetServiceEndpoint** 플래그를 설정 하 여이 요구 사항을 피할 수 있습니다. 이제 가상 네트워크 서비스 끝점을 설정 하지 않고 서버에 가상 네트워크 방화벽 규칙을 추가할 수 있습니다.

단순히 방화벽 규칙을 설정 하는 것은 서버를 보호 하는 데 도움이 되지 않습니다. 또한 보안을 적용 하려면 가상 네트워크 서비스 끝점을 설정 해야 합니다. 서비스 끝점을 켜면 가상 네트워크 서브넷에서 가동 중지 시간에서 켜기로 전환 될 때까지 가동 중지 시간이 발생 합니다. 이러한 가동 중지 시간은 특히 규모가 많은 가상 네트워크의 컨텍스트에서 특히 그렇습니다. **IgnoreMissingVNetServiceEndpoint** 플래그를 사용하여 전환 시 가동 중지 시간을 줄이거나 없앨 수 있습니다.

PowerShell을 사용하여 **IgnoreMissingVNetServiceEndpoint** 플래그를 설정할 수 있습니다. 자세한 내용은 [PowerShell을 통해 가상 네트워크 서비스 끝점을 만드는 PowerShell 및 SQL Database에 대 한 규칙][sql-db-vnet-service-endpoint-rule-powershell-md-52d]을 참조 하세요.

## <a name="errors-40914-and-40615"></a>오류 40914 및 40615

연결 오류 40914는 Azure Portal의 **방화벽** 창에 지정 된 대로 *가상 네트워크 규칙과* 관련이 있습니다. 오류 40615는 방화벽의 *IP 주소 규칙과* 관련이 있다는 점을 제외 하 고는 유사 합니다.

### <a name="error-40914"></a>오류 40914

**메시지 텍스트:** "로그인에서 요청한 서버 '*[서버 이름]*'을 (를) 열 수 없습니다. 클라이언트에서 서버에 액세스할 수 없습니다. "

**오류 설명:** 클라이언트가 가상 네트워크 서버 엔드포인트가 있는 서브넷에 있습니다. 그러나 서버에는 데이터베이스와 통신 하는 권한을 서브넷에 부여 하는 가상 네트워크 규칙이 없습니다.

**오류 해결:** Azure Portal의 **방화벽** 창에서 가상 네트워크 규칙 제어를 사용 하 여 서브넷에 대 한 [가상 네트워크 규칙을 추가](#anchor-how-to-by-using-firewall-portal-59j) 합니다.

### <a name="error-40615"></a>오류 40615

**메시지 텍스트:** " {0} 로그인에서 요청한 ' ' 서버를 열 수 없습니다. IP 주소가 ' ' 인 클라이언트에서 {1} 서버에 액세스할 수 없습니다. "

**오류 설명:** 클라이언트가 서버에 연결할 수 있는 권한이 없는 IP 주소에서 연결 하려고 합니다. 서버 방화벽에는 클라이언트가 지정 된 IP 주소에서 데이터베이스로 통신할 수 있도록 하는 IP 주소 규칙이 없습니다.

**오류 해결:** 클라이언트의 IP 주소를 IP 규칙으로 입력합니다. Azure Portal의 **방화벽** 창을 사용 하 여이 단계를 수행 합니다.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="use-the-portal-to-create-a-virtual-network-rule"></a>포털을 사용 하 여 가상 네트워크 규칙 만들기

이 섹션에서는 SQL Database에서 [Azure Portal][http-azure-portal-link-ref-477t] 를 사용 하 여 데이터베이스에 *가상 네트워크 규칙* 을 만드는 방법을 설명 합니다. 규칙은 *가상 네트워크 서비스 끝점* 으로 태그가 지정 된 특정 서브넷의 통신을 허용 하도록 데이터베이스에 지시 합니다.

> [!NOTE]
> 서버에 대 한 가상 네트워크 방화벽 규칙에 서비스 끝점을 추가 하려는 경우 먼저 서브넷에 대해 서비스 끝점이 설정 되어 있는지 확인 합니다.
>
> 서브넷에 대 한 서비스 끝점이 설정 되어 있지 않으면 포털에서 해당 끝점을 사용 하도록 설정 하 라는 메시지를 표시 합니다. 규칙을 추가 하는 동일한 창에서 **사용** 단추를 선택 합니다.

## <a name="powershell-alternative"></a>PowerShell 대체

또한 스크립트는 PowerShell cmdlet **AzSqlServerVirtualNetworkRule** 또는 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create)를 사용 하 여 가상 네트워크 규칙을 만들 수 있습니다. 관심이 있는 경우 [PowerShell을 참조 하 여 가상 네트워크 서비스 엔드포인트 및 SQL Database에 대 한 규칙을 만듭니다][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>REST API 대체

내부적으로 SQL 가상 네트워크 작업에 대 한 PowerShell cmdlet은 REST Api를 호출 합니다. REST API를 직접 호출할 수 있습니다.

- [가상 네트워크 규칙: 작업][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>필수 구성 요소

SQL Database와 관련 된 특정 가상 네트워크 서비스 끝점 *형식 이름* 으로 태그가 지정 된 서브넷이 이미 있어야 합니다.

- 관련 엔드포인트 형식 이름은 **Microsoft.Sql** 입니다.
- 서브넷이 형식 이름으로 태그가 지정될 수 없는 경우에는 [서브넷이 엔드포인트인지 확인][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]을 참조하세요.

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Azure Portal 단계

1. [Azure Portal][http-azure-portal-link-ref-477t]에 로그인합니다.

1. **SQL 서버** 를 검색 하 고 선택한 다음 서버를 선택 합니다. **보안** 에서 **방화벽 및 가상 네트워크** 를 선택 합니다.

1. **Azure 서비스에 대 한 액세스 허용** 을 **OFF** 로 설정 합니다.

    > [!IMPORTANT]
    > 제어를 설정 된 상태로 두면 서버는 Azure 경계 내의 모든 서브넷에서 **통신을 수락** 합니다. 이것은 Azure 데이터 센터에 대해 정의 된 범위 내에 있는 것으로 인식 되는 IP 주소 중 하나에서 발생 하는 통신입니다. 컨트롤 집합을 **ON** 으로 두면 보안 관점에서 과도 하 게 액세스할 수 있습니다. SQL Database의 가상 네트워크 규칙 기능을 함께 사용 하 여 Microsoft Azure Virtual Network 서비스 끝점 기능을 사용 하면 보안 노출 영역을 줄일 수 있습니다.

1. **가상 네트워크** 섹션에서 **+ 기존 항목 추가** 를 선택 합니다.

    ![+ 추가 기존 (서브넷 끝점)을 SQL 규칙으로 선택 하는 것을 보여 주는 스크린샷][image-portal-firewall-vnet-add-existing-10-png]

1. 새 **만들기/업데이트** 창에서 Azure 리소스의 이름으로 상자를 입력 합니다.

    > [!TIP]
    > 서브넷에 올바른 주소 접두사를 포함 해야 합니다. **주소 접두사** 값은 포털에서 찾을 수 있습니다. **모든 리소스** &gt; **모든 형식** &gt; **가상 네트워크** 로 이동 합니다. 필터에 가상 네트워크가 표시됩니다. 가상 네트워크를 선택한 다음 **서브넷** 을 선택 합니다. **주소 범위** 열에 필요한 주소 접두사가 있습니다.

    ![새 규칙에 대해 상자를 채우는 것을 보여 주는 스크린샷][image-portal-firewall-create-update-vnet-rule-20-png]

1. 창 아래쪽 근처의 **확인** 단추를 선택 합니다.

1. **방화벽** 창에서 결과 가상 네트워크 규칙을 참조 하세요.

    ![방화벽 창에서 새 규칙을 보여 주는 스크린샷][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> 다음 상태는 규칙에 적용됩니다.
>
> - **Ready**: 시작한 작업이 성공 했음을 나타냅니다.
> - **Failed**: 시작한 작업이 실패 했음을 나타냅니다.
> - **Deleted**: 삭제 작업에만 적용 되며 규칙이 삭제 되었으며 더 이상 적용 되지 않음을 나타냅니다.
> - **InProgress**: 작업이 진행 중임을 나타냅니다. 작업이 이 상태인 동안에는 이전 규칙이 적용됩니다.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>관련 문서

- [Azure 가상 네트워크 서비스 엔드포인트][vm-virtual-network-service-endpoints-overview-649d]
- [서버 수준 및 데이터베이스 수준 방화벽 규칙][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>다음 단계

- [PowerShell을 사용 하 여 가상 네트워크 서비스 끝점을 만든 다음 SQL Database에 대 한 가상 네트워크 규칙을 만듭니다.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [가상 네트워크 규칙:][rest-api-virtual-network-rules-operations-862r] REST api를 사용 하는 작업

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]:../index.yml
[rbac-what-is-813s]:../../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]:firewall-configure.md
[sql-db-vnet-service-endpoint-rule-powershell-md-52d]:scripts/vnet-service-endpoint-rule-powershell-create.md
[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]:scripts/vnet-service-endpoint-rule-powershell-create.md#a-verify-subnet-is-endpoint-ps-100
[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[vm-virtual-network-service-endpoints-overview-649d]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[vpn-gateway-indexmd-608y]: ../../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
[rest-api-virtual-network-rules-operations-862r]: /rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON
- Azure CLI
- ARM templates
-->
