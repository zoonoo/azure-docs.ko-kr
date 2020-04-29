---
title: VNet 및 방화벽 뒤에 있는 저장소 계정에 감사
description: 가상 네트워크 및 방화벽 뒤의 저장소 계정에서 데이터베이스 이벤트를 쓰도록 감사 구성
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/19/2020
ms.custom: azure-synapse
ms.openlocfilehash: 6345d210e26747f921595039a2a3c8e11be11fda
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80387634"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>VNet 및 방화벽 뒤에 있는 저장소 계정에 감사를 작성 합니다.

[Azure SQL Database](sql-database-technical-overview.md) 및 [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 에 대 한 감사는 가상 네트워크 및 방화벽 뒤에 있는 [Azure Storage 계정](../storage/common/storage-account-overview.md) 에 데이터베이스 이벤트 쓰기를 지원 합니다. 

이 문서에서는이 옵션에 대해 Azure SQL Server 및 Azure storage 계정을 구성 하는 두 가지 방법을 설명 합니다. 첫 번째는 Azure Portal을 사용 하 고, 두 번째는 REST를 사용 합니다.

### <a name="background"></a>배경

[VNet (azure Virtual Network)](../virtual-network/virtual-networks-overview.md) 은 azure에서 개인 네트워크의 기본 구성 요소입니다. VNet을 사용하면 Azure VM(Virtual Machines)과 같은 다양한 형식의 Azure 리소스가 서로, 인터넷 및 특정 온-프레미스 네트워크와 안전하게 통신할 수 있습니다. VNet은 사용자 고유의 데이터 센터에 있는 기존 네트워크와 유사 하지만, 확장, 가용성 및 격리와 같은 Azure 인프라의 추가 이점을 제공 합니다.

VNet 개념, 모범 사례 등에 대해 자세히 알아보려면 [Azure Virtual Network 이란?](../virtual-network/virtual-networks-overview.md)을 참조 하세요.

가상 네트워크를 만드는 방법에 대해 자세히 알아보려면 [빠른 시작: Azure Portal 사용 하 여 가상 네트워크 만들기](../virtual-network/quick-create-portal.md)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

감사가 VNet 또는 방화벽 뒤에 있는 저장소 계정에 쓰도록 하려면 다음 필수 구성 요소가 필요 합니다.

> [!div class="checklist"]
> * 범용 v2 저장소 계정입니다. 범용 v1 또는 blob storage 계정이 있는 경우 범용 [v2 저장소 계정으로 업그레이드](../storage/common/storage-account-upgrade.md)합니다. 자세한 내용은 [저장소 계정 유형](../storage/common/storage-account-overview.md#types-of-storage-accounts)을 참조 하세요.
> * 저장소 계정은 Azure SQL Database 서버와 동일한 구독 및 동일한 위치에 있어야 합니다. 
> * Azure Storage 계정에는 `Allow trusted Microsoft services to access this storage account`가 필요 합니다. 저장소 계정 **방화벽 및 가상 네트워크**에서이를 설정 합니다.
> * 선택한 저장소 계정 `Microsoft.Authorization/roleAssignments/write` 에 대 한 권한이 있어야 합니다. 자세한 내용은 [Azure 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조 하세요.

## <a name="configure-in-azure-portal"></a>Azure Portal에서 구성

구독과 [Azure Portal](https://portal.azure.com) 에 연결 합니다. 리소스 그룹 및 Azure SQL database 서버로 이동 합니다.

1. 보안 제목에서 **감사** 를 클릭 합니다. **켜기를**선택 합니다.

2. **저장소**를 선택합니다. 로그를 저장할 저장소 계정을 선택 합니다. 저장소 계정은 [필수 조건](#prerequisites)에 나열 된 요구 사항을 준수 해야 합니다.

3. **저장소 세부 정보** 열기 

  > [!NOTE]
  > 선택한 저장소 계정이 VNet 뒤에 있으면 다음과 같은 메시지가 표시 됩니다.
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage: requires an Active Directory admin on the server; enables 'Allow trusted Microsoft services to access this storage account' on the storage account; and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >이 메시지가 표시 되지 않으면 저장소 계정이 VNet 뒤에 있지 않은 것입니다.

4. 보존 기간에 대 한 일 수를 선택 합니다. 그런 다음 **확인**을 클릭합니다. 보존 기간 보다 오래 된 로그는 삭제 됩니다.

5. 감사 설정에서 **저장** 을 선택 합니다.

VNet 또는 방화벽 뒤에 있는 저장소 계정에 쓰도록 감사를 구성 했습니다. 

## <a name="configure-with-rest-commands"></a>REST 명령을 사용 하 여 구성

Azure Portal를 사용 하는 대신 REST 명령을 사용 하 여 VNet 및 방화벽 뒤의 저장소 계정에서 데이터베이스 이벤트를 쓰도록 감사를 구성할 수 있습니다. 

이 섹션의 샘플 스크립트를 실행 하기 전에 스크립트를 업데이트 해야 합니다. 스크립트에서 다음 값을 바꿉니다.

|샘플 값|샘플 설명|
|:-----|:-----|
|`<subscriptionId>`| Azure 구독 ID|
|`<resource group>`| Resource group|
|`<sql database server>`| Azure SQL 데이터베이스 서버 이름|
|`<administrator login>`| SQL database 관리자 계정 |
|`<complex password>`| 관리자 계정에 대 한 복잡 한 암호|

VNet 또는 방화벽 뒤에 있는 저장소 계정에 이벤트를 쓰도록 SQL 감사를 구성 하려면:

1. Azure Active Directory (Azure AD)를 사용 하 여 Azure SQL Database 서버를 등록 합니다. PowerShell 또는 REST API 중 하나를 사용 합니다.

   **PowerShell**
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```
   
   [**REST API**](https://docs.microsoft.com/rest/api/sql/servers/createorupdate):

   샘플 요청

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2015-05-01-preview
   ```

   요청 본문

   ```json
   {
   "identity": {
              "type": "SystemAssigned",
              },
   "properties": {
     "fullyQualifiedDomainName": "<azure server name>.database.windows.net",
     "administratorLogin": "<administrator login>",
     "administratorLoginPassword": "<complex password>",
     "version": "12.0",
     "state": "Ready"
   }
   ```

2. [Azure Portal](https://portal.azure.com)을 엽니다. 스토리지 계정으로 이동합니다. **Access Control (IAM)** 을 찾고 **역할 할당 추가**를 클릭 합니다. 이전 단계에서와 같이 Azure Active Directory (Azure AD)에 등록 한 Azure SQL 데이터베이스를 호스팅하는 Azure SQL Server에 **저장소 Blob 데이터 참가자** RBAC 역할을 할당 합니다.

   > [!NOTE]
   > 소유자 권한이 있는 멤버만 이 단계를 수행할 수 있습니다. Azure 리소스에 대 한 다양 한 기본 제공 역할은 [azure 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조 하세요.

3. *Storageaccountaccesskey*를 지정 하지 않고 [Azure SQL server의 blob 감사 정책을](/rest/api/sql/server%20auditing%20settings/createorupdate)구성 합니다.

   샘플 요청

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2017-03-01-preview
   ```

   요청 본문

   ```json
   {
     "properties": {
      "state": "Enabled",
      "storageEndpoint": "https://<storage account>.blob.core.windows.net"
     }
   }
   ```

## <a name="next-steps"></a>다음 단계

- [PowerShell을 사용 하 여 가상 네트워크 서비스 끝점을 만든 다음 Azure SQL Database에 대 한 가상 네트워크 규칙을 만듭니다.](sql-database-vnet-service-endpoint-rule-powershell.md)
- [Virtual Network 규칙: REST Api를 사용 하는 작업](/rest/api/sql/virtualnetworkrules)
- [데이터베이스 서버에 대한 가상 네트워크 서비스 엔드포인트 및 규칙 사용](sql-database-vnet-service-endpoint-rule-overview.md)
