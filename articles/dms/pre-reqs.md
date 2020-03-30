---
title: Azure 데이터베이스 마이그레이션 서비스에 대한 필수 구성 조건
description: Azure Database Migration Service를 사용하여 데이터베이스 마이그레이션을 수행하기 위한 필수 구성 요소의 개요를 알아봅니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 89cb63630e3dbe953ed3f4fd8796d01ba0d36067
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651494"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Azure Database Migration Service 사용을 위한 필수 구성 요소 개요

데이터베이스 마이그레이션을 수행할 때 Azure 데이터베이스 마이그레이션 서비스가 원활하게 실행되도록 하는 데 필요한 몇 가지 필수 구성 조건이 있습니다. 일부 필수 구성 요소는 서비스가 지원하는 모든 시나리오(원본-대상 쌍)에 적용되는 반면에 특정 시나리오에만 적용되는 필수 구성 요소도 있습니다.

Azure Database Migration Service 사용과 관련된 필구 구성 요소는 다음 섹션에 나열되어 있습니다.

## <a name="prerequisites-common-across-migration-scenarios"></a>마이그레이션 시나리오의 공통적인 필수 구성 요소

지원되는 모든 마이그레이션 시나리오에 공통적인 Azure Database Migration Service 필구 구성 요소는 다음을 수행해야 합니다.

* [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 또는 [VPN을](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)사용하여 온-프레미스 소스 서버에 대한 사이트 간 연결을 제공하는 Azure 리소스 관리자 배포 모델을 사용하여 Azure 데이터베이스 마이그레이션 서비스에 대한 Microsoft Azure 가상 네트워크를 만듭니다.
* 가상 네트워크 네트워크 보안 그룹(NSG) 규칙이 다음 통신 포트 443, 53, 9354, 445, 12000을 차단하지 않도록 합니다. 가상 네트워크 NSG 트래픽 필터링에 대한 자세한 내용은 [네트워크 보안 그룹과 네트워크 트래픽 필터링](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)문서를 참조하십시오.
* 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우 Azure Database Migration Service에서 원본 데이터베이스에 액세스하여 마이그레이션할 수 있도록 허용하는 방화벽 규칙을 추가해야 합니다.
* [데이터베이스 엔진 액세스를 위한 Windows 방화벽](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성합니다.
* 문서 [서버 네트워크 프로토콜 사용 또는 사용 안 함](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)의 지침을 수행하여 SQL Server Express를 설치하는 동안 기본적으로 사용 안 함으로 설정되어 있는 TCP/IP 프로토콜을 사용하도록 설정합니다.

    > [!IMPORTANT]
    > Azure 데이터베이스 마이그레이션 서비스의 인스턴스를 만들려면 일반적으로 동일한 리소스 그룹 내에 있지 않은 가상 네트워크 설정에 액세스해야 합니다. 따라서 DMS 인스턴스를 만드는 사용자에게는 구독 수준에서 권한이 필요합니다. 필요에 따라 할당할 수 있는 필수 역할을 만들려면 다음 스크립트를 실행하십시오.
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read", `
    > "Microsoft.DataMigration/*/read", `
    > "Microsoft.Resources/subscriptions/resourceGroups/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action", `
    > "Microsoft.Network/virtualNetworks/subnets/join/action", `
    > "Microsoft.Network/virtualNetworks/write", `
    > "Microsoft.Network/virtualNetworks/read", `
    > "Microsoft.Resources/deployments/validate/action", `
    > "Microsoft.Resources/deployments/*/read", `
    > "Microsoft.Resources/deployments/*/write"
    >
    > $writerActions += $readerActions
    >
    > # TODO: replace with actual subscription IDs
    > $subScopes = ,"/subscriptions/00000000-0000-0000-0000-000000000000/","/subscriptions/11111111-1111-1111-1111-111111111111/"
    >
    > function New-DmsReaderRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Reader"
    > $aRole.Description = "Lets you perform read only actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    >
    > $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    >
    > function New-DmsContributorRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Contributor"
    > $aRole.Description = "Lets you perform CRUD actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    >
    >   $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    > 
    > function Update-DmsReaderRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Reader"
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > function Update-DmsConributorRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Contributor"
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > # Invoke above functions
    > New-DmsReaderRole
    > New-DmsContributorRole
    > Update-DmsReaderRole
    > Update-DmsConributorRole
    > ```

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>SQL Server를 Azure SQL Database로 마이그레이션하기 위한 필수 구성 요소

모든 마이그레이션 시나리오에 공통적인 Azure Database Migration Service 필수 구성 요소 외에도 한 시나리오나 다른 시나리오에만 적용되는 필수 구성 요소가 있습니다.

Azure Database Migration Service를 사용하여 SQL Server에서 Azure SQL Database로 마이그레이션을 수행하는 경우, 모든 마이그레이션 시나리오에 공통적인 필수 구성 요소 외에도 다음과 같은 필구 구성 요소를 충족해야 합니다.

* Azure 포털에서 Azure SQL 데이터베이스 만들기 문서의 세부 정보를 따라 수행 되는 Azure SQL Database [인스턴스의 인스턴스를](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)만듭니다.
* [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 이상을 다운로드 및 설치합니다.
* Azure Database Migration Service가 기본적으로 TCP 포트 1433인 원본 SQL Server에 액세스하도록 허용하려면 Windows 방화벽을 엽니다.
* 동적 포트를 사용하여 명명된 여러 SQL Server 인스턴스를 실행하는 경우 SQL Browser 서비스를 사용하도록 설정하고 Azure Database Migration Service가 원본 서버에서 명명된 인스턴스에 연결할 수 있도록 방화벽을 통해 UDP 포트 1434에 액세스할 수 있습니다.
* 대상 데이터베이스에 대한 Azure Database Migration Service 액세스를 허용하도록 Azure SQL Database 서버에 서버 수준 [방화벽 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)을 만듭니다. Azure 데이터베이스 마이그레이션 서비스에 사용되는 가상 네트워크의 서브넷 범위를 제공합니다.
* 원본 SQL Server 인스턴스에 연결하는 데 사용되는 자격 증명에는 [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) 권한이 있어야 합니다.
* 대상 Azure SQL Database 인스턴스에 연결하는 데 사용되는 자격 증명에는 대상 Azure SQL 데이터베이스에 대한 CONTROL DATABASE 권한이 있어야 합니다.

   > [!NOTE]
   > Azure Database Migration Service를 사용하여 SQL Server에서 Azure SQL Database로 마이그레이션을 수행하기 위해 필요한 전체 필수 구성 요소 목록은 [SQL Server를 Azure SQL Database로 마이그레이션](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql) 자습서를 참조하세요.
   >

## <a name="prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance"></a>SQL Server를 Azure SQL Database 관리 인스턴스로 마이그레이션하기 위한 필수 구성 조건

* Azure 포털에서 Azure SQL Database 관리 인스턴스 만들기 문서의 세부 정보를 따라 [Azure SQL Database](https://aka.ms/sqldbmi)관리 인스턴스 만들기
* 방화벽을 열고 Azure Database Migration Service IP 주소 또는 서브넷 범위에 대한 포트 445에서 SMB 트래픽을 허용합니다.
* Azure Database Migration Service가 기본적으로 TCP 포트 1433인 원본 SQL Server에 액세스하도록 허용하려면 Windows 방화벽을 엽니다.
* 동적 포트를 사용하여 명명된 여러 SQL Server 인스턴스를 실행하는 경우 SQL Browser 서비스를 사용하도록 설정하고 Azure Database Migration Service가 원본 서버에서 명명된 인스턴스에 연결할 수 있도록 방화벽을 통해 UDP 포트 1434에 액세스할 수 있습니다.
* 원본 SQL Server와 대상 Managed Instance를 연결하는 데 사용되는 로그인이 sysadmin 서버 역할의 구성원인지 확인합니다.
* Azure Database Migration Service가 원본 데이터베이스를 백업하는 데 사용할 수 있는 네트워크 공유를 만듭니다.
* 원본 SQL Server 인스턴스를 실행 중인 서비스 계정에 본인이 만든 네트워크 공유에 대한 쓰기 권한이 있고, 원본 서버의 컴퓨터 계정에 동일한 공유에 대한 읽기/쓰기 액세스 권한이 있는지 확인합니다.
* 이전에 만든 네트워크 공유에 대한 전체 제어 권한을 갖고 있는 Windows 사용자(및 암호)를 메모해 둡니다. Azure 데이터베이스 마이그레이션 서비스는 복원 작업을 위해 백업 파일을 Azure Storage 컨테이너에 업로드하기 위해 사용자 자격 증명을 가장합니다.
* [Storage Explorer를 사용하여 Azure Blob Storage 리소스 관리](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) 문서의 단계에 따라 Blob 컨테이너를 만들고 해당 SAS URI를 검색합니다. SAS URI를 만드는 동안 정책 창에서 모든 권한(읽기, 쓰기, 삭제, 나열)을 선택해야 합니다.

   > [!NOTE]
   > Azure Database Migration Service를 사용하여 SQL Server에서 Azure SQL Database Managed Instance로 마이그레이션을 수행하기 위해 필요한 전체 필수 구성 요소 목록은 [SQL Server를 Azure SQL Database Managed Instance로 마이그레이션](https://aka.ms/migratetomiusingdms) 자습서를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Database Migration Service 및 국가별 가용성에 대한 개요는 [Azure Database Migration Service란?](dms-overview.md) 문서를 참조하세요.
