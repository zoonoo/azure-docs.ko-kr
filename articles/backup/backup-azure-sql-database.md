---
title: Azure에 SQL Server 데이터베이스 백업 | Microsoft Docs
description: 이 자습서에서는 Azure에 SQL Server를 백업하는 방법을 설명합니다. SQL Server 복구에 대해서도 설명합니다.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/19/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: 3d19b42e339e9776d0fdbbf7cfcfba07d69549ad
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39249083"
---
# <a name="back-up-sql-server-databases-to-azure"></a>Azure에 SQL Server 데이터베이스 백업

SQL Server 데이터베이스는 낮은 RPO(복구 지점 목표)와 장기 보존이 필요한 중요한 워크로드입니다. Azure Backup은 인프라가 필요 없는 SQL Server 백업 솔루션을 제공합니다. 복잡한 백업 서버, 관리 에이전트 또는 백업 저장소를 관리할 필요가 없습니다. Azure Backup은 SQL Server를 실행하는 모든 서버에 대한 백업은 물론 다른 워크로드에 대해서도 중앙 집중식 관리를 제공합니다.

이 문서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure에 SQL Server 인스턴스를 백업하기 위한 필수 구성 요소
> * Recovery Services 자격 증명 모음을 만들고 사용하는 방법
> * SQL Server 데이터베이스 백업을 구성하는 방법
> * 복구 지점에 대한 백업(또는 보존) 정책을 설정하는 방법
> * 데이터베이스를 복원하는 방법

이 문서의 절차를 시작하기 전에 Azure에서 실행 중인 SQL Server 인스턴스가 있어야 합니다. [SQL Marketplace 가상 머신을 사용하여 SQL Server 인스턴스를 신속하게 만들 수 있습니다](../sql-database/sql-database-get-started-portal.md).

## <a name="public-preview-limitations"></a>공개 미리 보기 제한 사항

다음 항목은 공개 미리 보기에 대해 알려진 제한 사항입니다.

- Azure 공용 IP 주소에 액세스하려면 SQL 가상 머신(VM)에 인터넷 연결이 필요합니다. 자세한 내용은 [네트워크 연결 설정](backup-azure-sql-database.md#establish-network-connectivity)을 참조하세요.
- Recovery Services 자격 증명 모음 하나에서 최대 2,000개의 SQL 데이터베이스를 보호합니다. 추가 SQL 데이터베이스는 별도의 Recovery Services 자격 증명 모음에 저장해야 합니다.
- [분산형 가용성 그룹의 백업](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017)에는 제한 사항이 있습니다.
- SQL Server Always On FCI(장애 조치(failover) 클러스터 인스턴스)가 지원되지 않습니다.
- Azure Portal을 사용하여 SQL Server 데이터베이스를 보호하도록 Azure Backup을 구성합니다. Azure PowerShell, Azure CLI 및 REST API는 현재 지원되지 않습니다.

## <a name="support-for-azure-geos"></a>Azure 지역에 대한 지원

Azure Backup은 다음과 같은 지역에서 지원됩니다.

- 오스트레일리아 동남부(ASE) 
- 브라질 남부(BRS)
- 캐나다 중부(CNC)
- 캐나다 동부(CE)
- 미국 중부(CUS)
- 동아시아(EA)
- 오스트레일리아 동부(AE) 
- 미국 동부(EUS)
- 미국 동부 2(EUS2)
- 인도 중부(INC) 
- 인도 남부(INS)
- 일본 동부(JPE)
- 일본 서부(JPW)
- 한국 중부(KRC)
- 대한민국(KRS)
- 미국 중북부(NCUS) 
- 북유럽(NE) 
- 미국 중남부(SCUS) 
- 동남 아시아(SEA)
- 영국 남부(UKS) 
- 영국 서부(UKW) 
- 미국 중서부(WCUS)
- 유럽 서부(WE) 
- 미국 서부(WUS)
- 미국 서부 2(WUS 2) 

## <a name="support-for-operating-systems-and-sql-server-versions"></a>운영 체제 및 SQL Server 버전 지원

이 섹션에서는 SQL Server 버전 및 운영 체제에 대한 Azure Backup 지원에 대해 설명합니다. SQL Marketplace Azure 가상 머신 및 Marketplace 가상 머신이 아닌 가상 머신(SQL Server가 수동으로 설치됨)이 지원됩니다.

### <a name="supported-operating-systems"></a>지원되는 운영 체제

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

Linux는 현재 지원되지 않습니다.

### <a name="supported-sql-server-versions-and-editions"></a>지원되는 SQL Server 버전

- SQL Server 2012 Enterprise, Standard, Web, Developer, Express
- SQL Server 2014 Enterprise, Standard, Web, Developer, Express
- SQL Server 2016 Enterprise, Standard, Web, Developer, Express
- SQL Server 2017 Enterprise, Standard, Web, Developer, Express

## <a name="prerequisites"></a>필수 조건

SQL Server 데이터베이스를 백업하기 전에 다음 조건을 확인하십시오.

- SQL Server를 호스팅하는 가상 머신과 동일한 지역이나 로캘에서 [Recovery Services 자격 증명 모음을 만들거나](backup-azure-sql-database.md#create-a-recovery-services-vault) 식별합니다.
- SQL 데이터베이스를 백업하는 데 필요한 [가상 머신의 사용 권한을 확인](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms)합니다.
- [SQL 가상 머신에 네트워크 연결이 있는지](backup-azure-sql-database.md#establish-network-connectivity) 확인합니다.

> [!NOTE]
> SQL Server 데이터베이스를 백업하려면 한 번에 하나의 백업 솔루션만 사용할 수 있습니다. 이 기능을 사용하기 전에 다른 모든 SQL 백업을 사용하지 않도록 설정하십시오. 그렇지 않으면 백업이 간섭하여 오류가 발생합니다. IaaS VM의 Azure Backup을 SQL 백업과 함께 충돌 없이 사용할 수 있습니다.
>

이러한 조건이 환경에 있는 경우 [SQL Server 데이터베이스에 대한 백업 구성](backup-azure-sql-database.md#configure-backup-for-sql-server-databases)을 계속 진행합니다. 필수 구성 요소 중 없는 항목이 있을 경우 계속 읽어보세요.


## <a name="establish-network-connectivity"></a>네트워크 연결 설정

모든 작업을 위해 SQL 가상 머신에 Azure 공용 IP 주소에 대한 연결이 필요합니다. 공용 IP 주소에 연결되지 않으면 SQL 가상 머신 작업(예: 데이터베이스 검색, 백업 구성, 백업 예약, 복구 지점 복원 등)이 실패합니다. 다음 옵션 중 하나를 사용하여 백업 트래픽에 명확한 경로를 제공합니다.

- Azure 데이터 센터 IP 범위 허용 목록 만들기: Azure 데이터 센터 IP 범위 허용 목록을 만들려면 [다운로드센터 페이지에서 IP 범위에 대한 자세한 내용과 지침](https://www.microsoft.com/download/details.aspx?id=41653)을 사용합니다. 
- 트래픽 라우팅을 위한 HTTP 프록시 서버 배포 - VM에서 SQL 데이터베이스를 백업하는 경우 VM의 백업 확장은 HTTPS API를 사용하여 Azure Backup에 관리 명령을 보내고 Azure Storage에 데이터를 보냅니다. 백업 확장은 인증에 Azure AD(Azure Active Directory)도 사용합니다. HTTP 프록시를 통해 이 세 가지 서비스에 대한 백업 확장 트래픽을 라우팅합니다. 확장의 공용 인터넷에 액세스하도록 구성된 유일한 구성 요소입니다.

이러한 옵션의 장단점은 관리 효율성, 세부적인 제어 및 비용입니다.

> [!NOTE]
> Azure Backup의 서비스 태그는 일반 공급으로 제공해야 합니다.
>

| 옵션 | 장점 | 단점 |
| ------ | ---------- | ------------- |
| 허용 목록 IP 범위 | 추가 비용 없음 <br/> NSG에서 열기 위해 액세스하려면 **Set-AzureNetworkSecurityRule** cmdlet을 사용합니다. | 시간이 지남에 따라 영향을 받는 IP 범위가 변경되기 때문에 관리하기가 복잡합니다. <br/>Azure Storage 뿐만 아니라 Azure 전체에 대한 액세스를 제공합니다.|
| HTTP 프록시 사용   | 저장소 URL에 대한 프록시의 세부적인 제어가 허용됩니다. <br/>VM에 대한 인터넷 액세스의 단일 지점 <br/> Azure IP 주소 변경이 적용되지 않음 | 프록시 소프트웨어로 VM을 실행하기 위한 추가 비용입니다. |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>Marketplace SQL VM이 아닌 VM에 대한 사용 권한 설정

가상 머신을 백업하려면 Azure Backup에 **AzureBackupWindowsWorkload** 확장이 설치되어 있어야 합니다. Azure Marketplace 가상 머신을 사용하는 경우 [SQL Server 데이터베이스 검색](backup-azure-sql-database.md#discover-sql-server-databases)으로 건너뜁니다. SQL 데이터베이스를 호스팅하는 가상 머신이 Azure Marketplace에서 만들어지지 않은 경우에는 다음 절차를 완료하여 확장을 설치하고 적절한 권한을 설정합니다. **AzureBackupWindowsWorkload** 확장 외에 SQL 데이터베이스를 보호하려면 Azure Backup에 sysadmin 권한이 필요합니다. 가상 머신에서 데이터베이스를 검색하기 위해 Azure Backup은 **NT Service\AzureWLBackupPluginSvc** 계정을 만듭니다. Azure Backup에서 SQL 데이터베이스를 검색하려면 **NT Service\AzureWLBackupPluginSvc** 계정에 SQL 및 SQL sysadmin 권한이 있어야 합니다. 다음 절차에서는 이러한 권한을 제공하는 방법을 설명합니다.

권한을 구성하려면:

1. [Azure Portal](https://portal.azure.com)에서 SQL 데이터베이스를 보호하는 데 사용하는 Recovery Services 자격 증명 모음을 엽니다.

2. **Recovery Services 자격 증명 모음** 대시보드에서 **Backup**을 선택합니다. **백업 목표** 메뉴가 열립니다.

   ![Backup을 선택하여 백업 목표 메뉴 열기](./media/backup-azure-sql-database/open-backup-menu.png)

3. **백업 목표** 메뉴에서 **작업이 실행되는 위치**를 기본: **Azure**로 설정합니다.

4. **백업할 항목** 드롭다운 메뉴를 확장하고 **Azure VM의 SQL Server**를 선택합니다.

    ![백업을 위해 Azure VM에서 SQL Server 선택](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    **백업 목표** 메뉴에 **Discover DBs in VMs**(VM의 DB 검색)과 **백업 구성**이라는 두 가지 단계가 표시됩니다. **Discover DBs in VMs**(VM의 DB 검색) 단계에서 Azure 가상 머신 검색이 시작됩니다.

    ![두 가지 백업 목표 단계 검토](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. **Discover DBs in VMs**(VM의 DB 검색)에서 **검색 시작**을 선택하여 구독에서 보호되지 않은 가상 머신을 검색합니다. 모든 가상 머신을 검색하려면 시간이 좀 걸릴 수 있습니다. 검색 시간은 구독에 있는 보호되지 않은 가상 머신의 수에 따라 달라집니다.

    ![VM의 DB 검색 중에는 백업이 보류됩니다.](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    보호되지 않은 가상 머신이 검색되면 목록에 나타납니다. 보호되지 않은 가상 머신은 가상 머신 이름 및 리소스 그룹별로 나열됩니다. 여러 가상 머신의 이름이 동일할 수 있습니다. 하지만 이름이 동일한 가상 머신은 다른 리소스 그룹에 속합니다. 예상되는 가상 머신이 나열되지 않는 경우 가상 머신이 자격 증명 모음에 이미 보호되어 있는지 확인합니다.

6. 가상 머신 목록에서 백업할 SQL 데이터베이스가 있는 VM을 선택한 다음, **Discover DBs**(DB 검색)를 클릭합니다. 

    검색 프로세스가 가상 머신에 **AzureBackupWindowsWorkload** 확장을 설치합니다. 이 확장을 통해 Azure Backup 서비스가 가상 머신과 통신할 수 있기 때문에 SQL 데이터베이스를 백업할 수 있습니다. 확장이 설치된 후 Azure Backup에서 가상 머신에 Windows 가상 서비스 계정인 **NT Service\AzureWLBackupPluginSvc**이 만들어집니다. 가상 서비스 계정에는 SQL sysadmin 권한이 필요합니다. 가상 서비스 계정 설치 프로세스 중 `UserErrorSQLNoSysadminMembership` 오류가 발생하면 [SQL sysadmin 권한 수정](backup-azure-sql-database.md#fix-sql-sysadmin-permissions)을 참조하세요.

    **알림** 영역에 데이터베이스 검색 진행률이 표시됩니다. 작업이 완료되는 데 어느 정도 시간이 걸릴 수 있습니다. 작업 시간은 가상 머신에 있는 데이터베이스의 수에 따라 달라집니다. 선택한 데이터베이스가 검색되면 성공 메시지가 표시됩니다.

    ![배포 성공 메시지](./media/backup-azure-sql-database/notifications-db-discovered.png)

데이터베이스를 Recovery Services 자격 증명 모음과 연결한 후 다음 단계는 [백업 작업을 구성](backup-azure-sql-database.md#configure-backup-for-sql-server-databases)하는 것입니다.

### <a name="fix-sql-sysadmin-permissions"></a>SQL sysadmin 권한 수정

설치를 진행하는 동안 `UserErrorSQLNoSysadminMembership` 오류가 표시되면 SQL sysadmin 권한이 있는 계정을 사용하여 SSMS(SQL Server Management Studio)에 로그인합니다. 특별한 사용 권한이 필요하지 않으면 Windows 인증이 작동해야 합니다.

1. SQL Server에서 Security/Logins 폴더를 엽니다.

    ![Security/Logins 폴더를 열어서 계정 보기](./media/backup-azure-sql-database/security-login-list.png)

2. 로그인 폴더를 마우스 오른쪽 단추로 클릭하고 **새 로그인**을 선택합니다. **로그인 - 신규** 대화 상자에서 **검색**을 선택합니다.

    ![로그인 - 신규 대화 상자에서 검색 선택](./media/backup-azure-sql-database/new-login-search.png)

3. Windows 가상 서비스 계정 **NT Service\AzureWLBackupPluginSvc**는 가상 머신 등록 및 SQL 검색 단계 중에 만들어졌습니다. **선택할 개체 이름 입력** 상자에 표시된 대로 계정 이름을 입력합니다. **이름 확인**을 선택하여 이름을 확인합니다. 

    ![이름 확인을 클릭하여 알 수 없는 서비스 이름 확인](./media/backup-azure-sql-database/check-name.png)

4. **확인**을 선택하여 대화 상자를 닫습니다.

5. **서버 역할** 상자에서 **sysadmin** 역할이 선택되어 있는지 확인합니다. **확인**을 선택하여 대화 상자를 닫습니다.

    ![sysadmin 서버 역할이 선택되어 있는지 확인](./media/backup-azure-sql-database/sysadmin-server-role.png)

    이제 필요한 권한이 있어야 합니다.

6. 권한 오류를 수정했지만 데이터베이스를 Recovery Services 자격 증명 모음과 연결해야 합니다. Azure Portal의 **보호된 서버** 목록에서 오류 상태에 있는 서버를 마우스 오른쪽 단추로 클릭하고 **DB 다시 검색**을 선택합니다.

    ![서버서 적절한 권한이 있는지 확인](./media/backup-azure-sql-database/check-erroneous-server.png)

    **알림** 영역에 데이터베이스 검색 진행률이 표시됩니다. 작업이 완료되는 데 어느 정도 시간이 걸릴 수 있습니다. 작업 시간은 가상 머신에 있는 데이터베이스의 수에 따라 달라집니다. 선택한 데이터베이스가 발견되면 성공 메시지가 표시됩니다.

    ![배포 성공 메시지](./media/backup-azure-sql-database/notifications-db-discovered.png)

데이터베이스를 Recovery Services 자격 증명 모음과 연결한 후 다음 단계는 [백업 작업을 구성](backup-azure-sql-database.md#configure-backup-for-sql-server-databases)하는 것입니다.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server 데이터베이스 검색

Azure Backup은 SQL Server 인스턴스에서 모든 데이터베이스를 검색합니다. 백업 요구 사항에 따라 데이터베이스를 보호할 수 있습니다. 다음 절차에 따라 SQL 데이터베이스를 호스트하는 가상 머신을 식별합니다. 가상 머신을 식별한 후, Azure Backup은 SQL Server 데이터베이스를 검색하는 간단한 확장을 설치합니다.

1. [Azure Portal](https://portal.azure.com/)에서 구독에 로그인합니다.

2. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다.

    ![모든 서비스 선택](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. **모든 서비스** 대화 상자에서 **Recovery Services**를 입력합니다. 입력할 때 사용자의 입력에 따라 리소스 목록이 필터링됩니다. 목록에서 **Recovery Services 자격 증명 모음**을 선택합니다.

    ![Recovery Services 자격 증명 모음 입력 및 선택](./media/backup-azure-sql-database/all-services.png) <br/>

    구독의 Recovery Services 자격 증명 모음 목록이 표시됩니다. 

4. Recovery Services 자격 증명 모음 목록에서 SQL 데이터베이스를 보호하는 데 사용할 자격 증명 모음을 선택합니다.

5. **Recovery Services 자격 증명 모음** 대시보드에서 **Backup**을 선택합니다. **백업 목표** 메뉴가 열립니다.

   ![Backup을 선택하여 백업 목표 메뉴 열기](./media/backup-azure-sql-database/open-backup-menu.png)

6. **백업 목표** 메뉴에서 **작업이 실행되는 위치**를 기본: **Azure**로 설정합니다.

7. **백업할 항목** 드롭다운 메뉴를 펼쳐서 **Azure VM의 SQL Server**를 선택합니다.

    ![백업을 위해 Azure VM에서 SQL Server 선택](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    **백업 목표** 메뉴에 **Discover DBs in VMs**(VM의 DB 검색)과 **백업 구성**이라는 두 가지 단계가 표시됩니다.
    
    ![두 가지 백업 목표 단계 검토](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. **Discover DBs in VMs**(VM의 DB 검색)에서 **검색 시작**을 선택하여 구독에서 보호되지 않은 가상 머신을 검색합니다. 모든 가상 머신을 검색하려면 시간이 좀 걸릴 수 있습니다. 검색 시간은 구독에 있는 보호되지 않은 가상 머신의 수에 따라 달라집니다.

    ![VM의 DB 검색 중에는 백업이 보류됩니다.](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    보호되지 않은 가상 머신이 검색되면 목록에 나타납니다. 여러 가상 머신의 이름이 동일할 수 있습니다. 하지만 이름이 동일한 가상 머신은 다른 리소스 그룹에 속합니다. 보호되지 않은 가상 머신은 가상 머신 이름 및 리소스 그룹별로 나열됩니다. 예상되는 가상 머신이 나열되지 않는 경우 해당 가상 머신이 자격 증명 모음에 이미 보호되어 있는지 확인합니다.

9. 가상 머신 목록에서 백업할 SQL 데이터베이스가 있는 VM을 선택한 다음, **Discover DBs**(DB 검색)를 클릭합니다.

    Azure Backup은 가상 머신의 모든 SQL 데이터베이스를 검색합니다. 데이터베이스 검색 단계 중 어떤 일이 발생하는지에 대한 정보는 [백그라운드 작업](backup-azure-sql-database.md#background-operations)을 참조하세요. SQL 데이터베이스가 검색되면 [백업 작업을 구성](backup-azure-sql-database.md#configure-backup-for-sql-server-databases)할 준비가 됩니다.

### <a name="background-operations"></a>백그라운드 작업

**DB 검색** 도구를 사용하는 경우 Azure Backup은 백그라운드에서 다음과 같은 작업을 실행합니다.

- 워크로드 백업을 위해 Recovery Services 자격 증명 모음에 가상 머신을 등록합니다. 등록된 가상 머신의 모든 데이터베이스는 이 Recovery Services 자격 증명 모음에만 백업할 수 있습니다. 

- 가상 머신에 **AzureBackupWindowsWorkload** 확장을 설치합니다. SQL 데이터베이스 백업은 에이전트 없는 솔루션입니다. 확장은 가상 머신에 설치되고 SQL 데이터베이스에는 에이전트가 설치되지 않습니다.

- 가상 머신에 서비스 계정 **NT Service\AzureWLBackupPluginSvc**를 만듭니다. 모든 백업 및 복원 작업에는 서비스 계정이 사용됩니다. **NT Service\AzureWLBackupPluginSvc**에는 SQL sysadmin 권한이 필요합니다. 모든 SQL Marketplace 가상 머신에는 **SqlIaaSExtension**이 설치되어 제공됩니다. **AzureBackupWindowsWorkload** 확장은 **SQLIaaSExtension**을 사용하여 필요한 권한을 자동으로 확보합니다. 가상 머신에 **SqlIaaSExtension**이 설치되어 있지 않으면 DB 검색 작업이 실패하고 `UserErrorSQLNoSysAdminMembership` 오류 메시지가 나타납니다. 백업을 위해 sysadmin 권한을 추가하려면 [Marketplace SQL VM이 아닌 VM에 대한 Azure Backup 권한 설정](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms)의 지침을 따르십시오.

    ![VM 및 데이터베이스 선택](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-databases"></a>SQL Server 데이터베이스에 대한 백업 구성 

Azure Backup은 SQL Server 데이터베이스를 보호하고 백업 작업을 관리하는 관리 서비스를 제공합니다. 관리 및 모니터링 기능은 Recovery Services 자격 증명 모음에 따라 달라집니다. 

> [!NOTE]
> SQL Server 데이터베이스를 백업하려면 한 번에 하나의 백업 솔루션만 사용할 수 있습니다. 이 기능을 사용하기 전에 다른 모든 SQL 백업을 사용하지 않도록 설정하십시오. 그렇지 않으면 백업이 간섭하여 오류가 발생합니다. IaaS VM의 Azure Backup을 SQL 백업과 함께 충돌 없이 사용할 수 있습니다.
>

SQL 데이터베이스에 대한 보호를 구성하려면:

1. SQL 가상 머신에 등록된 Recovery Services 자격 증명 모음을 엽니다.

2. **Recovery Services 자격 증명 모음** 대시보드에서 **Backup**을 선택합니다. **백업 목표** 메뉴가 열립니다.

   ![Backup을 선택하여 백업 목표 메뉴 열기](./media/backup-azure-sql-database/open-backup-menu.png)

3. **백업 목표** 메뉴에서 **작업이 실행되는 위치**를 기본: **Azure**로 설정합니다.

4. **백업할 항목** 드롭다운 메뉴를 펼쳐서 **Azure VM의 SQL Server**를 선택합니다.

    ![백업을 위해 Azure VM에서 SQL Server 선택](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    **백업 목표** 메뉴에 **Discover DBs in VMs**(VM의 DB 검색)과 **백업 구성**이라는 두 가지 단계가 표시됩니다.
    
    이 문서의 단계를 순서대로 완료하면 보호되지 않은 가상 머신이 검색되었고 이 자격 증명 모음은 가상 머신에 등록되어 있습니다. 이제 SQL 데이터베이스에 대한 보호를 구성할 준비가 되었습니다.
    
5. **백업 목표** 메뉴에서 **백업 구성**을 선택합니다.

    ![백업 구성 선택](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    Azure Backup 서비스는 독립 실행형 데이터베이스 및 SQL Server Always On 가용성 그룹이 있는 모든 SQL 서버 인스턴스를 표시합니다. SQL 인스턴스에서 독립 실행형 데이터베이스를 보려면 인스턴스 이름 옆의 펼침 단추를 선택합니다. 다음 이미지는 독립 실행형 인스턴스 및 Always On 가용성 그룹의 예를 보여줍니다.

    > [!NOTE]
    > SQL Always On 가용성 그룹의 경우에 SQL 백업 기본 설정이 적용됩니다. 하지만 SQL 플랫폼 제한 사항이 있기 때문에 전체 및 차등 백업은 주 노드에서 발생해야 합니다. 로그 백업은 백업 기본 설정에 따라 발생합니다. 이러한 제한 사항이 있기 때문에 주 노드는 항상 가용성 그룹에 등록해야 합니다.
    >

    ![SQL 인스턴스에 있는 데이터베이스 목록](./media/backup-azure-sql-database/discovered-databases.png)

    AlwaysOn 가용성 그룹 왼쪽에 있는 펼침 단추를 선택하여 데이터베이스 목록을 표시합니다.

    ![AlwaysOn 가용성 그룹의 데이터베이스 목록](./media/backup-azure-sql-database/discovered-database-availability-group.png)

6. 데이터베이스 목록에서 보호할 데이터베이스를 모두 선택한 다음 **확인**을 선택합니다.

    ![보호할 여러 데이터베이스 선택](./media/backup-azure-sql-database/select-multiple-database-protection.png)

    한 번에 최대 50개의 데이터베이스를 선택합니다. 50개가 넘는 데이터베이스를 보호하려면 여러 번 전달합니다. 처음 데이터베이스 50개를 보호한 후 이 단계를 반복하여 다음 데이터베이스 집합을 보호합니다.

    > [!Note] 
    > 백업 로드를 최적화하기 위해 Azure Backup은 큰 백업 작업을 여러 일괄 처리로 나눕니다. 백업 작업 하나에 포함되는 최대 데이터베이스 수는 50개입니다.
    >
    >

7. 백업 정책을 만들거나 선택하려면 **백업** 메뉴에서 **백업 정책**을 선택합니다. **백업 정책** 메뉴가 열립니다.

    ![백업 정책 선택](./media/backup-azure-sql-database/select-backup-policy.png)

8. **백업 정책 선택** 드롭다운 목록 상자에서 백업 정책을 선택하고 **확인**을 선택합니다. 백업 정책을 만드는 방법에 대한 자세한 내용은 [백업 정책 정의](backup-azure-sql-database.md#define-a-backup-policy)를 참조하세요.

    ![목록에서 백업 정책 선택](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    **백업 정책** 메뉴의 **백업 정책 선택** 드롭다운 목록 상자에서 다음을 수행할 수 있습니다. 
    - 기본 정책 **HourlyLogBackup**을 선택합니다.
    - 이전에 SQL용으로 만든 기존 백업 정책을 선택합니다.
    - RPO(복구 지점 목표) 및 보존 범위를 기반으로 [새 정책을 정의](backup-azure-sql-database.md#define-a-backup-policy)합니다. 

    > [!Note]
    > Azure Backup은 조부-아버지-아들 백업 체계를 기반으로 하는 장기 보존을 지원합니다. 이 체계는 백 엔드 저장소 소비를 최적화하면서 준수 요구를 충족시킵니다.
    >

9. 백업 정책을 선택한 후, **백업** 메뉴에서 **백업 사용**을 선택합니다.

    ![선택한 백업 정책 사용](./media/backup-azure-sql-database/enable-backup-button.png)

    포털의 **알림** 영역에서 구성 진행률을 추적합니다.

    ![알림 영역](./media/backup-azure-sql-database/notifications-area.png)


### <a name="define-a-backup-policy"></a>백업 정책 정의

백업 정책은 백업이 수행되는 시기와 유지되는 기간에 대한 매트릭스를 정의합니다. Azure Backup을 사용하면 SQL 데이터베이스에 대한 세 가지 유형의 백업을 예약할 수 있습니다.

* 전체 백업: 전체 데이터베이스 백업은 전체 데이터베이스를 백업합니다. 전체 백업은 특정 데이터베이스 또는 파일 그룹 또는 파일 집합의 모든 데이터와 이런 데이터를 복구하기에 충분한 로그를 포함합니다. 많으면, 하루에 하나의 전체 백업을 트리거할 수 있습니다. 매일 또는 매주 간격으로 전체 백업을 수행하도록 선택할 수 있습니다. 
* 차등 백업: 차등 백업은 가장 최근에 수행한 이전 전체 데이터 백업을 기반으로 합니다. 차등 백업은 전체 백업 이후 변경된 데이터만 캡처합니다. 많으면, 하루에 하나의 차등 백업을 트리거할 수 있습니다. 전체 백업과 차등 백업을 같은 날에 구성할 수 없습니다.
* 트랜잭션 로그 백업: 로그 백업을 사용하면 특정 시간(초 단위)까지의 특정 시점 복원이 가능합니다. 많으면, 15분마다 트랜잭션 로그 백업을 구성할 수 있습니다.

정책은 Recovery Services 자격 증명 모음 수준에서 만들어집니다. 다수의 자격 증명 모음은 자격 증명 모음은 동일한 백업 정책을 사용할 수 있지만 자격 증명 모음마다 백업 정책을 적용해야 합니다. 백업 정책을 만드는 경우, 매일 전체 백업이 기본값입니다. 차등 백업을 추가할 수 있지만 전체 백업이 매주 발생하도록 구성하는 경우에만 가능합니다. 다음 절차에서는 Azure 가상 머신에서 SQL 서버 인스턴스에 대한 백업 정책을 만드는 방법에 대해 설명합니다.

백업 정책을 만들려면:

1. **백업 정책** 메뉴의 **백업 정책 선택** 드롭다운 목록 상자에서 **새로 만들기**를 선택합니다.

   ![새 백업 정책 만들기](./media/backup-azure-sql-database/create-new-backup-policy.png)

    **백업 정책** 메뉴에 새 SQL Server 백업 정책에 필요한 필드가 표시됩니다.

   ![새 백업 정책 필드](./media/backup-azure-sql-database/blank-new-policy.png)

2. **정책 이름** 상자에 이름을 입력합니다.

3. 전체 백업은 필수입니다. 전체 백업의 기본값을 그대로 사용하거나 **전체 백업**을 선택하여 정책을 편집합니다.

    ![새 백업 정책 필드](./media/backup-azure-sql-database/full-backup-policy.png)

    **전체 백업 정책** 메뉴에서 **백업 빈도**에 대해 **매일** 또는 **매주**를 선택합니다. **매일**의 경우 백업 작업이 시작될 때 시간과 표준 시간대를 선택합니다. 매일 전체 백업에 대해서는 차등 백업을 만들 수 없습니다.

   ![매일 간격 설정](./media/backup-azure-sql-database/daily-interval.png)

    **매주**의 경우 백업 작업이 시작되는 요일, 시간 및 표준 시간대를 선택합니다.

   ![매주 간격 설정](./media/backup-azure-sql-database/weekly-interval.png)

4. 기본적으로 모든 **보존 범위** 옵션(매일, 매주, 매월 및 매년)이 선택됩니다. 원치 않는 보존 범위 제한이 있으면 선택을 취소합니다. 사용할 간격을 설정합니다. **전체 백업 정책** 메뉴에서 **확인**을 클릭하여 설정을 적용합니다.

   ![보존 범위 간격 설정](./media/backup-azure-sql-database/retention-range-interval.png)

    복구 지점은 보존 범위를 기반으로 보존 태그가 지정됩니다. 예를 들어, 매일, 전체 백업을 선택하면 매일 하나의 전체 백업만 트리거됩니다. 주별 보존 범위 및 매주 보존 설정을 기반으로 특정 날짜에 대한 백업에 태그가 지정되어 유지됩니다. 매월 및 매년 보존 범위도 유사한 방식으로 작동합니다.

5. 차등 백업 정책을 추가하려면 **차등 백업**을 선택합니다. **차등 백업 정책** 메뉴가 열립니다. 

   ![차등 백업 정책 메뉴 열기](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    **차등 백업 정책** 메뉴에서 **사용**을 선택하여 빈도 및 보존 컨트롤을 엽니다. 많으면, 하루에 하나의 차등 백업을 트리거할 수 있습니다.
    
    > [!Important] 
    > 차등 백업은 최대 180일 동안 보존될 수 있습니다. 더 오래 보존해야 하는 경우에는 전체 백업을 사용해야 합니다.
    >

   ![차등 백업 정책 편집](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    **확인**을 선택하여 정책을 저장하고 주 **백업 정책** 메뉴로 돌아갑니다.

6. 트랜잭션 로그 백업 정책을 추가하려면 **로그 백업**을 선택합니다. **로그 백업** 메뉴가 열립니다.

    **로그 백업** 메뉴에서 **사용**을 선택한 다음, 빈도 및 보존 컨트롤을 설정합니다. 로그 백업은 최소 15분 간격으로 수행할 수 있으며 최대 35일 동안 보존할 수 있습니다. **확인**을 선택하여 정책을 저장하고 주 **백업 정책** 메뉴로 돌아갑니다.

   ![로그 백업 정책 편집](./media/backup-azure-sql-database/log-backup-policy-editor.png)

7. **백업 정책** 메뉴에서 **SQL 백업 압축**을 사용할지 여부를 선택합니다. 압축은 기본적으로 사용하지 않도록 설정되어 있습니다.

    백 엔드에서, Azure Backup은 SQL 네이티브 백업 압축을 사용합니다.

8. 백업 정책 편집을 완료 한 후, **확인**을 선택합니다. 

   ![새 백업 정책 적용](./media/backup-azure-sql-database/backup-policy-click-ok.png)

## <a name="restore-a-sql-database"></a>SQL 데이터베이스 복원

Azure Backup은 트랜잭션 로그 백업을 사용하여 개별 데이터베이스를 특정 날짜나 시간(초 단위까지)으로 복원하는 기능을 제공합니다. Azure Backup은 복원 시간을 기준으로 데이터를 복원하는 데 필요한 전체, 차등 및 로그 백업 체인을 자동으로 결정합니다.

특정한 전체 또는 차등 백업을 선택하여 특정 시간보다는 특정 복구 지점으로 복원할 수 있습니다.
 > [!Note]
 > “마스터” 데이터베이스의 복원을 트리거하기 전에 시작 옵션 `-m AzureWorkloadBackup`을 사용하여 단일 사용자 모드로 SQL Server 인스턴스를 시작합니다. `-m` 옵션의 인수는 클라이언트의 이름입니다. 이 클라이언트만 연결을 열 수 있습니다. 모든 시스템 데이터베이스(모델, 마스터, msdb)에 대해 복원을 트리거하기 전에 SQL 에이전트 서비스를 중지합니다. 이러한 데이터베이스에 대한 연결을 도용하려고 하는 응용 프로그램을 닫습니다.
>

데이터베이스를 복원하려면

1. SQL 가상 머신에 등록된 Recovery Services 자격 증명 모음을 엽니다.

2. **Recovery Services 자격 증명 모음** 대시보드의 **사용량**에서 **백업 항목**을 선택하여 **백업 항목** 메뉴를 엽니다.

    ![백업 항목 메뉴 열기](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png)에서도 확인할 수 있습니다.

3. **백업 항목** 메뉴의 **백업 관리 유형**에서 **SQL in Azure VM**(Azure VM의 SQL)을 선택합니다. 

    ![Azure VM의 SQL 선택](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    **백업 항목** 메뉴에 SQL 데이터베이스 목록이 표시됩니다. 

4. SQL 데이터베이스 목록에서 복원할 데이터베이스를 선택합니다.

    ![복원할 데이터베이스를 선택합니다.](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    데이터베이스를 선택하면 해당 메뉴가 열립니다. 이 메뉴는 다음을 비롯한 데이터베이스에 대한 백업 세부 사항을 제공합니다.

    * 가장 오래된 복원 지점 및 최신 복원 지점
    * 지난 24시간 동안의 백업 상태(트랜잭션 로그 백업으로 구성된 경우 전체 및 대량 로그된 복구 모델의 데이터베이스에 대한 상태)

5. 선택한 데이터베이스에 대한 메뉴에서 **DB 복원**을 선택합니다. **복원** 메뉴가 열립니다.

    ![복원 DB 선택](./media/backup-azure-sql-database/restore-db-button.png)

    **복원** 메뉴가 열리면 **복원 구성** 메뉴도 열립니다. **복원 구성** 메뉴는 복원을 구성하는 첫 번째 단계입니다. 이 메뉴를 사용하여 데이터를 복원할 위치를 선택합니다. 옵션은 다음과 같습니다.
    - **대체 위치**: 대체 위치에 데이터베이스를 복원하고 원래 원본 데이터베이스를 유지합니다.
    - **DB 덮어쓰기**: 원래 원본과 동일한 SQL Server 인스턴스에 데이터를 복원합니다. 이 옵션의 효과는 원래 데이터베이스를 덮어쓰는 것입니다.

    > [!Important]
    > 선택한 데이터베이스가 Always On 가용성 그룹에 속하면 SQL Server에서 데이터베이스를 덮어쓸 수 없습니다. 이런 경우 **대체 위치** 옵션만 사용됩니다.
    >

    ![복원 구성 메뉴](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>대체 위치에 복원

이 절차는 대체 위치에 데이터를 복원하는 과정을 안내합니다. 복원 중 데이터베이스를 덮어쓰려면 [데이터베이스 복원 및 덮어쓰기](backup-azure-sql-database.md#restore-and-overwrite-the-database)를 계속 진행합니다. 이 단계에서는, Recovery Services 자격 증명 모음이 열리고 **복원 구성** 메뉴가 보입니다. 이 단계가 아닌 경우 [SQL 데이터베이스 복원](backup-azure-sql-database.md#restore-a-sql-database)부터 시작합니다.

> [!NOTE]
> 동일한 Azure 지역의 SQL Server 인스턴스에 데이터베이스를 복원할 수 있습니다. 대상 서버를 Recovery Services 자격 증명 모음에 등록해야 합니다. 
>

**복원 구성** 메뉴의 **서버** 드롭다운 목록 상자에는 Recovery Services 자격 증명 모음에 등록된 SQL Server 인스턴스만 표시됩니다. 원하는 서버가 목록에 없는 경우에는 [SQL Server 데이터베이스 검색](backup-azure-sql-database.md#discover-sql-server-databases)을 참조하여 서버를 찾으십시오. 검색 프로세스 중에 새로운 서버가 Recovery Services 자격 증명 모음에 등록됩니다.

1. **복원 구성** 메뉴에서:

    * **Where to Restore**(복원할 위치)에서 **대체 위치**를 선택합니다.
    * **서버** 드롭다운 목록 상자를 열고 데이터베이스를 복원할 SQL Server 인스턴스를 선택합니다.
    * **인스턴스** 드롭다운 목록 상자를 열고 SQL Server 인스턴스를 선택합니다.
    * **복원된 DB 이름** 상자에 대상 데이터베이스의 이름을 입력합니다.
    * 해당되는 경우 **Overwrite if the DB with the same name already exists on selected SQL instance**(선택한 SQL 인스턴스에 이름이 같은 DB가 있으면 덮어쓰기)를 선택합니다.
    * **확인**을 선택하여 대상의 구성을 완료하고 복원 지점을 계속 선택합니다.

    ![복원 구성 메뉴에 대한 값 제공](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. **복원 지점 선택** 메뉴에서 **Logs (Point in Time)**(로그(특정 시점)) 또는 **Full & Differential**(전체 및 차등)을 복원 지점으로 선택합니다. 특정 시점 로그로 복원하려면 이 단계를 계속 진행합니다. 전체 및 차등 복원 지점으로 복원하려면 3단계를 진행합니다.

    ![복원 지점 선택 메뉴](./media/backup-azure-sql-database/recovery-point-menu.png)

    특정 시점 복원은 전체 및 대량 로그된 복구 모델을 사용하는 데이터베이스의 로그 백업에만 사용할 수 있습니다. 특정 시점으로 복원하려면:

    1. **Logs (Point in Time)**(로그(특정 시점))를 복원 유형으로 선택합니다.

        ![복원 지점 유형 선택](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. **복원 날짜/시간**에서 미니 캘린더를 선택하여 **달력**을 엽니다. **달력**에서 굵게 표시된 날짜에 복구 지점이 포함되고 현재 날짜는 강조 표시됩니다. 복구 지점이 있는 날짜를 선택합니다. 복구 지점이 없는 날짜는 선택할 수 없습니다.

        ![달력 열기](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        날짜를 선택한 후에는 타임라인 그래프에 사용 가능한 복구 지점이 연속적인 범위로 표시됩니다.

    3. 타임라인 그래프 또는 **시간** 대화 상자를 사용하여 복구 지점의 특정 시간을 지정합니다. **확인**을 선택하여 복원 지점 단계를 완료합니다.
    
       ![달력 열기](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        **복원 지점 선택** 메뉴가 닫히고 **고급 구성** 메뉴가 열립니다.

       ![고급 구성 메뉴](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. **고급 구성** 메뉴에서:

        * 복원 후 데이터베이스를 작동하지 않는 상태로 유지하려면, **Restore with NORECOVERY**(NORECOVERY를 사용하여 복원)를 **사용**으로 설정합니다.
        * 대상 서버에서 복원 위치를 변경하려면 **대상** 열에 새 경로를 입력합니다.
        * **확인**을 선택하여 설정을 승인합니다. **고급 구성** 메뉴를 닫습니다.

    5. **복원** 메뉴에서 **복원**을 선택하여 복원 작업을 시작합니다. **알림** 영역에서 복원 진행률을 추적하거나 데이터베이스 메뉴에서 **복원 작업**을 선택합니다.

       ![복원 작업 진행률](./media/backup-azure-sql-database/restore-job-notification.png)

3. **복원 지점 선택** 메뉴에서 **Logs (Point in Time)**(로그(특정 시점)) 또는 **Full & Differential**(전체 및 차등)을 복원 지점으로 선택합니다. 특정 시점 로그를 복원하려면 2단계로 돌아갑니다. 이 단계는 특정한 전체 또는 차등 복원 지점을 복원합니다. 지난 30일 동안의 전체 및 차등 복구 지점을 볼 수 있습니다. 30일 보다 오래된 복구 지점을 보려면 **필터**를 선택하여 **필터 복원 지점** 메뉴를 엽니다. 차등 복구 지점의 경우 Azure Backup에서 먼저 적절한 전체 복구 지점이 복원된 다음, 선택한 차등 복구 지점이 적용됩니다.

    ![복원 지점 선택 메뉴](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. **복원 지점 선택** 메뉴에서 **Full & Differential**(전체 및 차등)을 선택합니다.

       ![전체 및 차등 선택](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        메뉴에 사용 가능한 복구 지점 목록이 표시됩니다.

    2. 목록에서 복구 지점을 선택하고 **확인**을 선택하여 복원 지점 절차를 완료합니다. 

        ![전체 복구 지점 선택](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        **복원 지점** 메뉴가 닫히고 **고급 구성** 메뉴가 열립니다.

        ![고급 구성 메뉴](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. **고급 구성** 메뉴에서:

        * 복원 후 데이터베이스를 작동하지 않는 상태로 유지하려면, **Restore with NORECOVERY**(NORECOVERY를 사용하여 복원)를 **사용**으로 설정합니다. **Restore with NORECOVERY**(NORECOVERY를 사용하여 복원)는 기본적으로 사용하지 않도록 설정되어 있습니다.
        * 대상 서버에서 복원 위치를 변경하려면 **대상** 열에 새 경로를 입력합니다.
        * **확인**을 선택하여 설정을 승인합니다. **고급 구성** 메뉴를 닫습니다.

    4. **복원** 메뉴에서 **복원**을 선택하여 복원 작업을 시작합니다. **알림** 영역에서 복원 진행률을 추적하거나 데이터베이스 메뉴에서 **복원 작업**을 선택합니다.

       ![복원 작업 진행률](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>데이터베이스 복원 및 덮어쓰기

이 절차에서는 데이터를 복원하고 데이터베이스를 덮어쓰는 과정을 안내합니다. 대체 위치에 복원하려면 [대체 위치에 복원](backup-azure-sql-database.md#restore-to-an-alternate-location)을 계속 진행합니다. 이 단계에서는, Recovery Services 자격 증명 모음이 열리고 **복원 구성** 메뉴가 보입니다(다음 이미지 참조). 이 단계가 아닌 경우 [SQL 데이터베이스 복원](backup-azure-sql-database.md#restore-a-sql-database)부터 시작합니다.

![복원 구성 메뉴](./media/backup-azure-sql-database/restore-overwrite-db.png)

**복원 구성** 메뉴의 **서버** 드롭다운 목록 상자에는 Recovery Services 자격 증명 모음에 등록된 SQL Server 인스턴스만 표시됩니다. 원하는 서버가 목록에 없는 경우에는 [SQL Server 데이터베이스 검색](backup-azure-sql-database.md#discover-sql-server-databases)을 참조하여 서버를 찾으십시오. 검색 프로세스 중에 새로운 서버가 Recovery Services 자격 증명 모음에 등록됩니다.

1. **복원 구성** 메뉴에서 **DB 덮어쓰기**를 선택한 다음, **확인**을 클릭하여 대상에 대한 구성을 완료합니다. 

   ![DB 덮어쓰기 선택](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    **서버**, **인스턴스** 및 **복원된 DB 이름** 설정은 필요하지 않습니다.

2. **복원 지점 선택** 메뉴에서 **Logs (Point in Time)**(로그(특정 시점)) 또는 **Full & Differential**(전체 및 차등)을 복원 지점으로 선택합니다. 특정 시점 로그로 복원하려면 이 단계를 계속 진행합니다. 전체 및 차등 복원 지점으로 복원하려면 3단계를 진행합니다.

    ![복원 지점 선택 메뉴](./media/backup-azure-sql-database/recovery-point-menu.png)

    특정 시점 복원은 전체 및 대량 로그된 복구 모델을 사용하는 데이터베이스의 로그 백업에만 사용할 수 있습니다. 특정 시간(초 단위)으로 복원하려면:

    1. **Logs (Point in Time)**(로그(특정 시점))를 복원 지점으로 선택합니다.

        ![복원 지점 유형 선택](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. **복원 날짜/시간**에서 미니 캘린더를 선택하여 **달력**을 엽니다. **달력**에서 굵게 표시된 날짜에 복구 지점이 포함되고 현재 날짜는 강조 표시됩니다. 복구 지점이 있는 날짜를 선택합니다. 복구 지점이 없는 날짜는 선택할 수 없습니다.

        ![달력 열기](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        날짜를 선택한 후에는, 타임라인 그래프에 사용 가능한 복구 지점이 표시됩니다.

    3. 타임라인 그래프 또는 **시간** 대화 상자를 사용하여 복구 지점의 특정 시간을 지정합니다. **확인**을 선택하여 복원 지점 단계를 완료합니다.
    
       ![달력 열기](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        **복원 지점 선택** 메뉴가 닫히고 **고급 구성** 메뉴가 열립니다.

       ![고급 구성 메뉴](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. **고급 구성** 메뉴에서:

        * 복원 후 데이터베이스를 작동하지 않는 상태로 유지하려면, **Restore with NORECOVERY**(NORECOVERY를 사용하여 복원)를 **사용**으로 설정합니다.
        * 대상 서버에서 복원 위치를 변경하려면 **대상** 열에 새 경로를 입력합니다.
        * **확인**을 선택하여 설정을 승인합니다. **고급 구성** 메뉴를 닫습니다.

    5. **복원** 메뉴에서 **복원**을 선택하여 복원 작업을 시작합니다. **알림** 영역에서 복원 진행률을 추적하거나 데이터베이스 메뉴에서 **복원 작업**을 선택합니다.

       ![복원 작업 진행률](./media/backup-azure-sql-database/restore-job-notification.png)

3. **복원 지점 선택** 메뉴에서 **Logs (Point in Time)**(로그(특정 시점)) 또는 **Full & Differential**(전체 및 차등)을 복원 지점으로 선택합니다. 특정 시점 로그를 복원하려면 2단계로 돌아갑니다. 이 단계는 특정한 전체 또는 차등 복원 지점을 복원합니다. 지난 30일 동안의 전체 및 차등 복구 지점을 볼 수 있습니다. 30일 보다 오래된 복구 지점을 보려면 **필터**를 선택하여 **필터 복원 지점** 메뉴를 엽니다. 차등 복구 지점의 경우 Azure Backup에서 먼저 적절한 전체 복구 지점이 복원된 다음, 선택한 차등 복구 지점이 적용됩니다.

    ![복원 지점 선택 메뉴](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. **복원 지점 선택** 메뉴에서 **Full & Differential**(전체 및 차등)을 선택합니다.

       ![전체 및 차등 선택](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        메뉴에 사용 가능한 복구 지점 목록이 표시됩니다.

    2. 목록에서 복구 지점을 선택하고 **확인**을 선택하여 복원 지점 절차를 완료합니다. 

        ![전체 복구 지점 선택](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        **복원 지점** 메뉴가 닫히고 **고급 구성** 메뉴가 열립니다.

        ![고급 구성 메뉴](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. **고급 구성** 메뉴에서:

        * 복원 후 데이터베이스를 작동하지 않는 상태로 유지하려면, **Restore with NORECOVERY**(NORECOVERY를 사용하여 복원)를 **사용**으로 설정합니다. **Restore with NORECOVERY**(NORECOVERY를 사용하여 복원)는 기본적으로 사용하지 않도록 설정되어 있습니다.
        * 대상 서버에서 복원 위치를 변경하려면 **대상** 열에 새 경로를 입력합니다.
        * **확인**을 선택하여 설정을 승인합니다. **고급 구성** 메뉴를 닫습니다.

    4. **복원** 메뉴에서 **복원**을 선택하여 복원 작업을 시작합니다. **알림** 영역에서 복원 진행률을 추적하거나 데이터베이스 메뉴에서 **복원 작업**을 선택합니다.

       ![복원 작업 진행률](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>Azure VM의 SQL에 대한 Azure Backup 작업 관리

이 섹션에서는 Azure 가상 머신의 SQL에 사용할 수 있는 다양한 Azure Backup 관리 작업에 대한 정보를 제공합니다. 다음과 같은 상위 수준 작업이 있습니다.

* 작업 모니터링
* Backup 경고
* SQL 데이터베이스에 대한 보호 중지
* SQL 데이터베이스에 대한 보호 재개
* 임시 백업 작업 트리거
* SQL Server를 실행하는 서버 등록 취소

### <a name="monitor-backup-jobs"></a>백업 작업 모니터링
Azure Backup은 고급 백업 경고 및 오류에 대한 알림을 제공하는 엔터프라이즈 클래스 솔루션입니다. (아래에서 [백업 경고 보기](backup-azure-sql-database.md#view-backup-alerts) 섹션을 참조하세요.) 특정 작업을 모니터링하려면 요구 사항에 따라 다음 옵션 중 하나를 사용합니다.

#### <a name="use-the-azure-portal-for-adhoc-operations"></a>임시 작업에 Azure Portal 사용
Azure Backup은 수동으로 트리거된 작업이나 임시 작업을 **백업 작업** 포털에 모두 표시합니다. **백업 작업** 포털에 표시되는 작업은 다음과 같습니다.
- 모든 백업 구성 작업
- 수동으로 트리거된 백업 작업
- 복원 작업
- 등록 및 데이터베이스 검색 작업
- 백업 중지 작업 

![백업 작업 포털](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> 전체, 차등 및 로그 백업을 포함하는 예약된 백업 작업은 **백업 작업** 포털에 모두 표시되지 않습니다. SQL Server Management Studio를 사용하면 다음 섹션에서 설명하는 대로 예약된 백업 작업을 모니터링할 수 있습니다.
>

#### <a name="use-sql-server-management-studio-for-backup-jobs"></a>백업 작업에 SQL Server Management Studio 사용
Azure Backup은 모든 백업 작업에 대해 SQL 네이티브 API를 사용합니다. 네이티브 API를 사용하여 msdb 데이터베이스의 [SQL backupset 테이블](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017)에서 모든 작업 정보를 페치합니다.

다음 예제는 **DB1**이라는 데이터베이스에 대한 모든 백업 작업을 페치하는 쿼리입니다. 고급 모니터링에 대한 쿼리를 사용자 지정합니다.

```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  
 
```

### <a name="view-backup-alerts"></a>백업 경고 보기

로그 백업은 15분마다 발생하기 때문에, 경우에 따라, 백업 작업 모니터링이 번거로울 수 있습니다. 이런 상황에서 Azure Backup이 도움이 됩니다. 모든 백업 실패에 대해 이메일 경고가 트리거됩니다. 경고는 오류 코드별로 데이터베이스 수준에서 통합됩니다. 이메일 경고는 데이터베이스에 대한 첫 번째 백업 실패에 대해서만 전송됩니다. Azure Portal에 로그인하면 데이터베이스에 대한 모든 실패를 모니터링할 수 있습니다. 

백업 경고를 모니터링하려면:

1. [Azure Portal](https://portal.azure.com)에서 Azure 구독에 로그인합니다.

2. SQL 가상 머신에 등록된 Recovery Services 자격 증명 모음을 엽니다.

3. **Recovery Services 자격 증명 모음** 대시보드에서 **경고 및 이벤트**를 선택합니다. 

   ![경고 및 이벤트 선택](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. **경고 및 이벤트** 메뉴에서 **백업 경고**를 선택하여 경로 목록을 확인합니다.

   ![백업 경고 선택](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-for-a-sql-server-database"></a>SQL Server 데이터베이스에 대한 보호 중지

SQL Server 데이터베이스에 대한 보호를 중지하면 Azure Backup에 복구 지점을 유지할지 여부를 묻는 메시지가 표시됩니다. SQL 데이터베이스에 대한 보호를 중지하는 방법은 두 가지입니다.

* 미래의 모든 백업 작업을 중지하고 모든 복구 지점을 삭제
* 미래의 모든 백업 작업을 중지하지만 복구 지점은 유지

복구 지점을 유지하면 비용이 발생합니다. SQL에 대한 복구 지점에는 SQL 보호된 인스턴스 비용 및 소비된 저장소에 대한 비용이 발생하기 때문입니다. SQL의 Azure Backup 가격 책정에 대한 자세한 내용은 [Azure Backup 가격 책정 페이지](https://azure.microsoft.com/pricing/details/backup/)를 참조하세요. 

데이터베이스에 대한 보호를 중지하려면:

1. SQL 가상 머신에 등록된 Recovery Services 자격 증명 모음을 엽니다.

2. **Recovery Services 자격 증명 모음** 대시보드의 **사용량**에서 **백업 항목**을 선택하여 **백업 항목** 메뉴를 엽니다.

    ![백업 항목 메뉴 열기](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png)에서도 확인할 수 있습니다.

3. **백업 항목** 메뉴의 **백업 관리 유형**에서 **SQL in Azure VM**(Azure VM의 SQL)을 선택합니다. 

    ![Azure VM의 SQL 선택](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    **백업 항목** 메뉴에 SQL 데이터베이스 목록이 표시됩니다. 

4. SQL 데이터베이스 목록에서 보호를 중지할 데이터베이스를 선택합니다.

    ![보호를 중지할 데이터베이스 선택](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    데이터베이스를 선택하면 해당 메뉴가 열립니다.

5. 선택한 데이터베이스에 대한 메뉴에서 **백업 중지**를 선택합니다. 

    ![백업 중지 선택](./media/backup-azure-sql-database/stop-db-button.png)

    **백업 중지** 메뉴가 열립니다.

6. **백업 중지** 메뉴에서 **백업 데이터 보존** 또는 **백업 데이터 삭제**를 선택합니다. 선택적으로, 보호를 중지하는 이유와 메모를 제공할 수 있습니다.

    ![백업 중지 메뉴](./media/backup-azure-sql-database/stop-backup-button.png)

7. **백업 중지**를 선택하여 데이터베이스에 대한 보호를 중지합니다. 

### <a name="resume-protection-for-a-sql-database"></a>SQL 데이터베이스에 대한 보호 재개

SQL 데이터베이스에 대한 보호가 중지된 경우 **백업 데이터 보존** 옵션을 선택하면 보호를 다시 시작할 수 있습니다. 백업 데이터가 보존되지 않은 경우에는 보호를 다시 시작할 수 없습니다. 

1. SQL 데이터베이스에 대한 보호를 다시 시작하려면 백업 항목을 열고 **백업 다시 시작**을 선택합니다.

    ![백업 다시 시작을 선택하여 데이터베이스 보호 다시 시작](./media/backup-azure-sql-database/resume-backup-button.png)

   **백업 정책** 메뉴가 열립니다.

2. **백업 정책** 메뉴에서 정책을 선택한 다음, **저장**을 클릭합니다.

### <a name="trigger-an-adhoc-backup"></a>임시 백업 트리거

필요에 따라 임시 백업을 트리거합니다. 임시 백업에는 네 가지 유형이 있습니다.

* 전체 백업
* 복사 전용 전체 백업
* 차등 백업
* 로그 백업

각 유형에 대한 자세한 내용은 [SQL 백업 유형](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups)을 참조하세요.

### <a name="unregister-a-sql-server-instance"></a>SQL Server 인스턴스 등록 취소

보호를 제거한 후 자격 증명 모음을 삭제하기 전에 SQL Server 인스턴스 등록을 취소하려면:

1. SQL 가상 머신에 등록된 Recovery Services 자격 증명 모음을 엽니다.

2. **Recovery Services 자격 증명 모음** 대시보드의 **관리**에서 **백업 인프라**를 선택합니다.  

   ![백업 인프라 선택](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. **관리 서버**에서 **보호된 서버**를 선택합니다.

   ![보호된 서버 선택](./media/backup-azure-sql-database/protected-servers.png)

    **보호된 서버** 메뉴가 열립니다. 

4. **보호된 서버** 메뉴에서 등록을 취소할 서버를 선택합니다. 자격 증명 모음을 삭제하려면 모든 서버의 등록을 취소해야 합니다.

5. **보호된 서버** 메뉴에서 보호된 서버를 마우스 오른쪽 단추로 클릭하고 **삭제**를 선택합니다. 

   ![삭제 선택](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="faq"></a>FAQ

다음 섹션에서는 SQL 데이터베이스 백업에 대한 추가 정보를 제공합니다.

### <a name="can-i-throttle-the-speed-of-the-sql-server-backup-policy"></a>SQL Server 백업 정책의 속도를 제한할 수 있나요?

예. SQL Server 인스턴스에 대한 영향을 최소화하도록 백업 정책이 실행되는 속도를 제한할 수 있습니다.

설정을 변경하려면:

1. SQL Server 인스턴스의 C:\Program Files\Azure Workload Backup\bin 폴더에서 **TaskThrottlerSettings.json** 파일을 엽니다.

2. TaskThrottlerSettings.json 파일에서 **DefaultBackupTasksThreshold** 설정을 더 낮은 값(예: 5)으로 변경합니다.

3. 변경 내용을 저장합니다. 파일을 닫습니다.

4. SQL Server 인스턴스에서 **작업 관리자**를 엽니다. **Azure Backup 워크로드 코디네이터 서비스**를 다시 시작합니다.

### <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>보조 복제본에서 전체 백업을 실행할 수 있나요?

아니요. 이 기능은 지원되지 않습니다.

### <a name="do-successful-backup-jobs-create-alerts"></a>성공한 백업 작업에 경고가 만들어지나요?

아니요. 성공한 백업 작업에서는 경고가 생성되지 않습니다. 경고는 실패한 백업 작업에 대해서만 전송됩니다.

### <a name="can-i-see-details-for-scheduled-backup-jobs-in-the-jobs-menu"></a>작업 메뉴에서 예약된 백업 작업에 대한 세부 정보를 볼 수 있나요?

아니요. **백업 작업** 메뉴에는 임시 작업 세부 정보가 표시되지만 예약된 백업 작업은 표시되지 않습니다. 예약된 백업 작업이 실패하면 실패한 작업 경고에서 세부 정보가 제공됩니다. 예약된 작업과 임시 백업 작업을 모두 모니터링하려는 경우 [SQL Server Management Studio](backup-azure-sql-database.md#use-sql-server-management-studio-for-backup-jobs)를 사용하세요.

### <a name="when-i-select-a-sql-server-instance-are-future-databases-automatically-added"></a>SQL Server 인스턴스를 선택하면 향후 데이터베이스가 자동으로 추가되나요?

아니요. SQL Server 인스턴스에 대한 보호를 구성할 때 서버 수준 옵션을 선택하면 모든 데이터베이스가 추가됩니다. 보호를 구성한 후 SQL Server 인스턴스에 데이터베이스를 추가하는 경우에는 보호할 새 데이터베이스를 수동으로 추가해야 합니다. 데이터베이스는 구성된 보호에 자동으로 포함되지 않습니다.

### <a name="if-i-change-the-recovery-model-how-do-i-restart-protection"></a>복구 모델을 변경하는 경우 보호를 어떻게 다시 시작해야 하나요?

전체 백업을 트리거하세요. 로그 백업이 예상대로 시작됩니다.

### <a name="can-i-protect-sql-always-on-availability-groups-where-the-primary-replica-is-on-premises"></a>주 복제본이 온-프레미스에 있는 경우 SQL AlwaysOn 가용성 그룹을 보호할 수 있나요?

아니요. Azure Backup은 Azure에서 실행하는 SQL Server만 보호합니다. AG(가용성 그룹)이 Azure와 온-프레미스 머신 간에 분산되어 있는 경우 주 복제본을 Azure에서 실행하는 경우에만 가용성 그룹이 보호될 수 있습니다. 또한 Azure Backup은 Recovery Services 자격 증명 모음과 동일한 Azure 지역에서 실행 중인 노드만 보호합니다.

## <a name="next-steps"></a>다음 단계

Azure Backup에 대한 자세한 내용은 암호화된 가상 머신을 백업하는 Azure PowerShell 샘플을 참조하세요.

> [!div class="nextstepaction"]
> [암호화된 VM 백업](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
