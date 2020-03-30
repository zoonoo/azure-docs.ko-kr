---
title: VNet 및 방화벽 뒤의 저장소 계정에 대한 감사
description: 가상 네트워크 및 방화벽 뒤에 있는 저장소 계정에 데이터베이스 이벤트를 작성하도록 감사를 구성합니다.
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
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387634"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>VNet 및 방화벽 뒤에 있는 저장소 계정에 감사 작성

[Azure SQL 데이터베이스](sql-database-technical-overview.md) 및 Azure [Synapse Analytics에](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 대한 감사는 가상 네트워크 및 방화벽 뒤에 있는 [Azure Storage 계정에](../storage/common/storage-account-overview.md) 데이터베이스 이벤트를 작성하는 것을 지원합니다. 

이 문서에서는 이 옵션에 대해 Azure SQL Server 및 Azure 저장소 계정을 구성하는 두 가지 방법을 설명합니다. 첫 번째는 Azure 포털을 사용하고 두 번째는 REST를 사용합니다.

### <a name="background"></a>배경

[Azure 가상 네트워크(VNet)는](../virtual-network/virtual-networks-overview.md) Azure에서 개인 네트워크의 기본 구성 요소입니다. VNet을 사용하면 Azure VM(Virtual Machines)과 같은 다양한 형식의 Azure 리소스가 서로, 인터넷 및 특정 온-프레미스 네트워크와 안전하게 통신할 수 있습니다. VNet은 자체 데이터 센터의 기존 네트워크와 유사하지만 확장, 가용성 및 격리와 같은 Azure 인프라의 추가 이점을 제공합니다.

VNet 개념, 모범 사례 및 기타 여러 가지에 대해 자세히 알아보려면 [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)입니다.

가상 네트워크를 만드는 방법에 대한 자세한 내용은 [빠른 시작: Azure 포털을 사용하여 가상 네트워크 만들기](../virtual-network/quick-create-portal.md)를 참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

감사가 VNet 또는 방화벽 뒤에 있는 저장소 계정에 쓰려면 다음 필수 구성 조건이 필요합니다.

> [!div class="checklist"]
> * 범용 v2 저장소 계정입니다. 범용 v1 또는 Blob 저장소 계정이 있는 경우 [범용 v2 저장소 계정으로 업그레이드합니다.](../storage/common/storage-account-upgrade.md) 자세한 내용은 [저장소 계정 유형을](../storage/common/storage-account-overview.md#types-of-storage-accounts)참조하십시오.
> * 저장소 계정은 Azure SQL Database 서버와 동일한 구독 및 동일한 위치에 있어야 합니다. 
> * Azure 저장소 계정에는 이가 필요합니다. `Allow trusted Microsoft services to access this storage account` 저장소 계정 방화벽 **및 가상 네트워크에서**이 설정을 합니다.
> * 선택한 저장소 `Microsoft.Authorization/roleAssignments/write` 계정에 대한 권한이 있어야 합니다. 자세한 내용은 [Azure 기본 제공 역할을](../role-based-access-control/built-in-roles.md)참조하십시오.

## <a name="configure-in-azure-portal"></a>Azure Portal에서 구성

구독을 사용하여 [Azure 포털에](https://portal.azure.com) 연결합니다. 리소스 그룹 및 Azure SQL 데이터베이스 서버로 이동합니다.

1. 보안 제목 아래에서 **감사를 클릭합니다.** **을 선택합니다.**

2. **저장소**를 선택합니다. 로그가 저장될 저장소 계정을 선택합니다. 저장소 계정은 [필수 구성 조건에](#prerequisites)나열된 요구 사항을 준수해야 합니다.

3. **저장소 세부 정보** 열기 

  > [!NOTE]
  > 선택한 저장소 계정이 VNet 뒤에 있으면 다음 메시지가 표시됩니다.
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage: requires an Active Directory admin on the server; enables 'Allow trusted Microsoft services to access this storage account' on the storage account; and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >이 메시지가 표시되지 않으면 저장소 계정이 VNet 뒤에 있지 않습니다.

4. 보존 기간의 일 수를 선택합니다. 그런 다음 **확인**을 클릭합니다. 보존 기간보다 오래된 로그는 삭제됩니다.

5. 감사 설정에서 **저장을** 선택합니다.

감사를 VNet 또는 방화벽 뒤에 있는 저장소 계정에 기록하도록 성공적으로 구성했습니다. 

## <a name="configure-with-rest-commands"></a>REST 명령으로 구성

Azure 포털을 사용하는 대신 REST 명령을 사용하여 감사를 구성하여 VNet 및 방화벽 뒤에 있는 저장소 계정에 데이터베이스 이벤트를 작성하도록 구성할 수 있습니다. 

이 섹션의 샘플 스크립트에서는 스크립트를 실행하기 전에 스크립트를 업데이트해야 합니다. 스크립트에서 다음 값을 바꿉니다.

|샘플 값|샘플 설명|
|:-----|:-----|
|`<subscriptionId>`| Azure 구독 ID입니다.|
|`<resource group>`| Resource group|
|`<sql database server>`| Azure SQL 데이터베이스 서버 이름|
|`<administrator login>`| SQL 데이터베이스 관리자 계정 |
|`<complex password>`| 관리자 계정에 대한 복잡한 암호|

VNet 또는 방화벽 뒤에 있는 저장소 계정에 이벤트를 작성하도록 SQL Audit를 구성하려면 다음을 수행합니다.

1. Azure SQL Database 서버를 Azure Active 디렉터리(Azure AD)로 등록합니다. PowerShell 또는 REST API를 사용합니다.

   **Powershell**
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```
   
   [**나머지 API**](https://docs.microsoft.com/rest/api/sql/servers/createorupdate):

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

2. [Azure Portal](https://portal.azure.com)을 엽니다. 스토리지 계정으로 이동합니다. **IAM(액세스 제어)을** **찾아역할 할당 추가를**클릭합니다. 이전 단계에서와 같이 Azure Active Directory(Azure AD)에 등록한 Azure SQL 데이터베이스를 호스팅하는 Azure SQL Server에 **저장소 Blob 데이터 기여자** RBAC 역할을 할당합니다.

   > [!NOTE]
   > 소유자 권한이 있는 멤버만 이 단계를 수행할 수 있습니다. Azure 리소스에 대한 다양한 기본 제공 역할에 대한 경우 [Azure 기본 제공 역할을](../role-based-access-control/built-in-roles.md)참조하십시오.

3. *storageAccountAccessKey를*지정하지 않고 [Azure SQL 서버의 Blob 감사 정책을](/rest/api/sql/server%20auditing%20settings/createorupdate)구성합니다.

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

- [PowerShell을 사용하여 가상 네트워크 서비스 끝점을 만든 다음 Azure SQL Database에 대한 가상 네트워크 규칙을 만듭니다.](sql-database-vnet-service-endpoint-rule-powershell.md)
- [가상 네트워크 규칙: REST API가 있는 작업](/rest/api/sql/virtualnetworkrules)
- [데이터베이스 서버에 대한 가상 네트워크 서비스 엔드포인트 및 규칙 사용](sql-database-vnet-service-endpoint-rule-overview.md)
