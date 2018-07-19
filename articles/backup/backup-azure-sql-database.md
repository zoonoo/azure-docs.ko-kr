---
title: Azure에 SQL Server 데이터베이스 백업 | Microsoft Docs
description: 이 자습서에서는 Azure에 SQL Server 백업을 설명합니다. SQL Server 복구에 대해서도 설명합니다.
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
ms.date: 7/6/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: 32f45b66c4b1d22da3ffc4310a8a47c17319301f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38302826"
---
# <a name="back-up-sql-server-database-in-azure"></a>Azure에 SQL Server 데이터베이스 백업

SQL Server 데이터베이스는 낮은 RPO(복구 지점 목표)와 장기 보존이 필요한 중요한 워크로드입니다. Azure Backup은 인프라가 필요 없는 SQL Server 백업 솔루션을 제공합니다. 따라서 복잡한 백업 서버, 관리 에이전트 또는 백업 저장소를 관리할 필요가 없습니다. Azure Backup은 모든 SQL Server에 대한 백업은 물론 다른 워크로드에 대해서도 중앙 집중식 관리를 제공합니다.

 이 문서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure에 SQL Server를 백업하기 위한 필수 구성 요소
> * Recovery Services 자격 증명 모음 만들기 및 사용
> * SQL Server 데이터베이스 백업 구성
> * 복구 지점에 대한 백업(또는 보존) 정책 설정
> * 데이터베이스를 복원하는 방법

이 문서의 절차를 시작하기 전에 Azure에서 SQL Server를 실행해야 합니다. [SQL 마켓플레이스 가상 머신을 사용하여 SQL Server를 신속하게 만들 수 있습니다](../sql-database/sql-database-get-started-portal.md).

## <a name="public-preview-limitations"></a>공개 미리 보기 제한 사항

다음 항목은 공개 미리 보기에 대해 알려진 제한 사항입니다.

- Azure 공용 IP 주소에 액세스하려면 SQL 가상 머신에 인터넷 연결이 필요합니다. 자세한 내용은 [네트워크 연결 설정](backup-azure-sql-database.md#establish-network-connectivity) 섹션을 참조하세요.
- Recovery Services 자격 증명 모음 하나에서 최대 2000개의 SQL 데이터베이스를 보호할 수 있습니다. 추가 SQL 데이터베이스는 별도의 Recovery Services 자격 증명 모음에 저장해야 합니다.
- [분산형 가용성 그룹 백업에는 제한 사항이 있습니다](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017).
- SQL FCI(장애 조치 클러스터 인스턴스)는 지원되지 않습니다.
- Azure Portal을 사용하여 SQL Server 데이터베이스를 보호하도록 Azure Backup을 구성합니다. Azure PowerShell, CLI 및 REST API에 대한 지원은 현재 제공되지 않습니다.

## <a name="supported-azure-geos"></a>지원되는 Azure GEO

- 오스트레일리아 동남부(ASE) 
- 브라질 남부(BRS)
- 캐나다 중부(CNC)
- 캐나다 동부(CE)
- 미국 중부(CUS)
- 동아시아(EA)
- 오스트레일리아 동부(AE) 
- 미국 동부(EUS)
- 미국 동부 2(EUS2)
- 일본 동부(JPE)
- 일본 서부(JPW)
- 인도 중부(INC) 
- 인도 남부(INS)
- 한국 중부(KRC)
- 대한민국(KRS)
- 미국 중북부(NCUS) 
- 북유럽(NE) 
- 미국 중남부(SCUS) 
- 동남 아시아(SEA)
- 영국 남부(UKS) 
- 영국 서부(UKW) 
- 유럽 서부(WE) 
- 미국 서부(WUS)
- 미국 중서부(WCUS)
- 미국 서부 2(WUS 2) 

## <a name="supported-operating-systems-and-versions-of-sql-server"></a>지원되는 운영 체제 및 SQL Server 버전

다음 운영 체제가 지원됩니다. SQL 마켓플레이스 Azure 가상 머신 및 마켓플레이스가 아닌 가상 머신(SQL Server가 수동으로 설치됨)이 지원됩니다.

### <a name="supported-operating-systems"></a>지원되는 운영 체제

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

Linux는 현재 지원되지 않습니다.

### <a name="supported-versionseditions-of-sql-server"></a>지원되는 SQL Server 버전

- SQL 2012 Enterprise, Standard, Web, Developer, Express
- SQL 2014 Enterprise, Standard, Web, Developer, Express
- SQL 2016 Enterprise, Standard, Web, Developer, Express
- SQL 2017 Enterprise, Standard, Web, Developer, Express


## <a name="prerequisites-for-using-azure-backup-to-protect-sql-server"></a>Azure Backup을 사용하여 SQL Server를 보호하기 위한 필수 구성 요소 

SQL Server 데이터베이스를 백업하기 전에 다음 조건을 확인하십시오. :

- SQL Server를 호스팅하는 가상 머신과 동일한 지역이나 로캘에서 [Recovery Services 자격 증명 모음을 만들거나](backup-azure-sql-database.md#create-a-recovery-services-vault) 식별합니다.
- SQL 데이터베이스를 백업하는 데 필요한 [가상 머신의 사용 권한을 확인](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms)합니다.
- [SQL 가상 머신에 네트워크 연결이 있습니다](backup-azure-sql-database.md#establish-network-connectivity).

> [!NOTE]
> SQL Server 데이터베이스를 백업하려면 한 번에 하나의 백업 솔루션만 사용할 수 있습니다. 이 기능을 사용하기 전에 다른 SQL 백업을 비활성화하십시오. 그렇지 않으면 백업에 방해가 되어 실패합니다. IaaS VM의 Azure Backup을 SQL 백업과 함께 충돌 없이 사용할 수 있습니다. 
>

환경에 이러한 조건이 존재하는 경우 [SQL 데이터베이스를 보호하도록 자격 증명 모음 구성](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database) 섹션을 참조하세요. 필수 구성 요소 중 없는 항목이 있으면 이 섹션을 계속 참조하세요.


## <a name="establish-network-connectivity"></a>네트워크 연결 설정

모든 작업을 위해 SQL 가상 머신에 Azure 공용 IP 주소에 대한 연결이 필요합니다. 공용 IP 주소에 연결되지 않으면 SQL 가상 머신 작업(예: 데이터베이스 검색, 백업 구성, 예약된 백업, 복구 지점 복원 등)이 실패합니다. 다음 옵션 중 하나를 사용하여 백업 트래픽에 명확한 경로를 제공합니다.

- Azure 데이터 센터 IP 범위 허용 목록 만들기 - Azure 데이터 센터 IP 범위 허용 목록을 만들려면 [다운로드센터 페이지에서 IP 범위에 대한 자세한 내용과 지침](https://www.microsoft.com/download/details.aspx?id=41653)을 사용합니다. 
- 라우팅 트래픽을 위한 HTTP 프록시 서버 배포 - VM에서 SQL 데이터베이스를 백업하는 경우 VM의 백업 확장은 HTTPS API를 사용하여 Azure Backup에 관리 명령을 보내고 Azure Storage에 데이터를 보냅니다. 백업 확장은 인증에 AAD(Azure Active Directory)를 사용합니다. 이 세 가지 서비스에 대한 백업 확장 트래픽은 HTTP 프록시를 통해 라우팅합니다. 이것이 공용 인터넷에 액세스하도록 구성된 유일한 구성 요소이기 때문입니다.

이러한 옵션의 장단점은 관리 효율성, 세분적인 제어 및 비용입니다.

> [!NOTE]
>Azure Backup의 서비스 태그는 일반 공급으로 제공해야 합니다.
>

| 옵션 | 장점 | 단점 |
| ------ | ---------- | ------------- |
| 허용 목록 IP 범위 | 추가 비용 없음 <br/> NSG에서 액세스를 여는 경우 **Set-AzureNetworkSecurityRule** cmdlet을 사용합니다. | 시간이 지남에 따라 영향을 받는 IP 범위가 변경되기 때문에 관리하기가 복잡합니다. <br/>저장소뿐만 아니라 Azure 전체에 대한 액세스를 제공합니다.|
| HTTP 프록시 사용   | 저장소 URL에 대한 프록시의 세부적인 제어가 허용됩니다. <br/>VM에 대한 인터넷 액세스의 단일 지점 <br/> Azure IP 주소 변경이 적용되지 않음 | 프록시 소프트웨어를 사용하여 VM을 실행하기 위한 추가 비용입니다. |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>마켓플레이스 SQL VM이 아닌 VM에 대한 사용 권한 설정

가상 머신을 백업하려면 Azure Backup에 **AzureBackupWindowsWorkload** 확장이 설치되어 있어야 합니다. Azure Marketplace 가상 머신을 사용하는 경우 [SQL Server 데이터베이스 검색](backup-azure-sql-database.md#discover-sql-server-databases)으로 건너뜁니다. SQL 데이터베이스를 호스팅하는 가상 머신을 Azure Marketplace에서 만들지 않은 경우에는 다음 섹션을 완료하여 확장을 설치하고 적절한 권한을 설정합니다. **AzureBackupWindowsWorkload** 확장 외에 SQL 데이터베이스를 보호하려면 Azure Backup에 sysadmin 권한이 필요합니다. 가상 머신에서 데이터베이스를 검색하는 동안 Azure Backup은 NT Service\AzureWLBackupPluginSvc라는 계정을 만듭니다. Azure Backup에서 SQL 데이터베이스를 검색하려면 NT Service\AzureWLBackupPluginSvc 계정에 SQL 및 SQL sysadmin 권한이 있어야 합니다. 다음 절차에서는 이러한 권한을 제공하는 방법을 설명합니다.

권한을 구성하려면:

1. [Azure Portal](https://portal.azure.com)에서 SQL 데이터베이스를 보호하는 데 사용하는 Recovery Services 자격 증명 모음을 엽니다.
2. 자격 증명 모음 대시보드 메뉴에서 **+ 백업**을 클릭하여 **백업 목표** 메뉴를 엽니다.

   ![+ 백업을 클릭하여 백업 목표 메뉴를 엽니다.](./media/backup-azure-sql-database/open-backup-menu.png)

3. **백업 목표** 메뉴의 **작업이 실행되는 위치** 메뉴에서 **Azure**를 기본으로 유지합니다.

4. **백업할 항목** 메뉴에서 드롭다운 메뉴를 확장하고 **Azure VM의 SQL Server**를 선택합니다.

    ![+ 백업을 클릭하여 백업 목표 메뉴를 엽니다.](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    **백업 목표** 메뉴에 **Discover DBs in VMs**(VM의 DB 검색)과 **백업 구성**이라는 두 가지 새로운 단계가 표시됩니다. **Discover DBs in VMs**(VM의 DB 검색)에서 Azure 가상 머신 검색이 시작됩니다.

    ![새 백업 목표 단계 표시](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. **검색 시작**을 클릭하여 구독에서 보호되지 않은 가상 머신을 검색합니다. 구독에 있는 보호되지 않은 가상 머신의 수에 따라 모든 가상 머신을 검색하는 데 약간의 시간이 걸릴 수 있습니다.

    ![보류 중인 Backup](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    보호되지 않은 가상 머신이 검색되면 목록에 나타납니다. 보호되지 않은 가상 머신은 가상 머신 이름 및 리소스 그룹별로 나열됩니다. 여러 가상 머신의 이름이 동일할 수 있습니다. 하지만 이름이 동일한 가상 머신은 다른 리소스 그룹에 속합니다. 예상되는 가상 머신이 목록에 나타나지 않는 경우 가상 머신이 자격 증명 모음에 이미 보호되어 있는지 확인합니다.

6. 가상 머신 목록에서 백업할 SQL 데이터베이스를 포함하는 VM을 선택하고 **Discover DBs**(DB 검색)을 클릭합니다. 

    검색 프로세스가 가상 머신에 **AzureBackupWindowsWorkload** 확장을 설치합니다. 이 확장을 통해 Azure Backup 서비스가 가상 머신과 통신할 수 있기 때문에 SQL 데이터베이스를 백업할 수 있습니다. 확장이 설치되면 Azure Backup에서 가상 머신에 Windows 가상 서비스 계정인 **NT Service\AzureWLBackupPluginSvc**이 만들어집니다. 가상 서비스 계정에는 SQL sysadmin 권한이 필요합니다. 가상 서비스 계정을 설치하는 동안 **UserErrorSQLNoSysadminMembership** 오류가 표시되는 경우 [SQL sysadmin 권한 수정](backup-azure-sql-database.md#fixing-sql-sysadmin-permissions) 섹션을 참조하세요.

    알림 영역에 데이터베이스 검색 진행률이 표시됩니다. 가상 머신에 있는 데이터베이스의 수에 따라 작업을 완료하는 데 약간의 시간이 걸릴 수 있습니다. 선택한 데이터베이스가 검색되면 성공 메시지가 표시됩니다.

    ![배포 성공 알림 메시지](./media/backup-azure-sql-database/notifications-db-discovered.png)

데이터베이스를 Recovery Services 자격 증명 모음과 연결한 후 다음 단계는 [백업 구성](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database)입니다.

### <a name="fixing-sql-sysadmin-permissions"></a>SQL sysadmin 권한 수정

설치를 진행하는 동안 **UserErrorSQLNoSysadminMembership** 오류가 표시되면 SQL sysadmin 권한이 있는 계정을 사용하여 SSMS(SQL Server Management Studio)에 로그인합니다. 특별한 사용 권한이 필요하지 않으면 Windows 인증이 작동해야 합니다.

1. SQL Server에서 **Security/Logins** 폴더를 엽니다.

    ![SQL Server를 열고 security 및 login 폴더를 열어서 계정을 확인합니다.](./media/backup-azure-sql-database/security-login-list.png)

2. Logins 폴더를 마우스 오른쪽 단추로 클릭하여 **새 로그인**을 선택하고 로그인 - 새로 만들기 대화 상자에서 **검색**을 클릭합니다.

    ![[로그인 - 신규] 대화 상자에서 검색 열기](./media/backup-azure-sql-database/new-login-search.png)

3. 가상 머신 등록 및 SQL 검색 단계에서 Windows 가상 서비스 계정인 **NT Service\AzureWLBackupPluginSvc**가 이미 만들어졌기 때문에 계정 이름을 **선택할 개체 이름 입력** 대화 상자에 나타나는 대로 입력합니다. **이름 확인**을 클릭하여 이름을 확인할 수 있습니다. 

    ![이름 확인 단추를 클릭하여 알 수 없는 서비스 이름을 확인합니다.](./media/backup-azure-sql-database/check-name.png)

4. **확인**을 클릭하여 [사용자 또는 그룹 선택] 대화 상자를 닫습니다.

5. **서버 역할** 대화 상자에서 **sysadmin** 역할이 선택되어 있는지 확인합니다. 그런 다음, **확인**을 클릭하여 **로그인 - 신규**를 닫습니다.

    ![sysadmin 서버 역할이 선택되어 있는지 확인](./media/backup-azure-sql-database/sysadmin-server-role.png)

    이제 필요한 권한이 있어야 합니다.

6. 권한 오류를 수정했지만 아직 데이터베이스를 Recovery Services 자격 증명 모음과 연결해야 합니다. Azure Portal **보호된 서버** 목록에서 오류가 있는 서버를 마우스 오른쪽 단추로 클릭하고 **DB 다시 검색**을 선택합니다.

    ![서버서 적절한 권한이 있는지 확인](./media/backup-azure-sql-database/check-erroneous-server.png)

    알림 영역에 데이터베이스 검색 진행률이 표시됩니다. 가상 머신에 있는 데이터베이스의 수에 따라 작업을 완료하는 데 약간의 시간이 걸릴 수 있습니다. 선택한 데이터베이스가 검색되면 알림 영역에 성공 메시지가 표시됩니다.

    ![배포 성공 알림 메시지](./media/backup-azure-sql-database/notifications-db-discovered.png)

데이터베이스를 Recovery Services 자격 증명 모음과 연결한 후 다음 단계는 [백업 구성](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database)입니다.

[!INCLUDE [Section explaining how to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server 데이터베이스 검색

Azure Backup은 SQL Server 인스턴스의 모든 데이터베이스를 검색하여 백업 요구 사항에 따라 보호할 수 있습니다. 다음 절차에 따라 SQL 데이터베이스를 호스팅하는 가상 머신을 식별합니다. 가상 머신이 식별되면 Azure Backup은 SQL Server 데이터베이스를 검색하는 간단한 확장을 설치합니다.

1. [Azure Portal](https://portal.azure.com/)에서 구독에 로그인합니다.
2. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다.

    ![주 메뉴에서 모든 서비스 옵션 선택](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. 모든 서비스 대화 상자에서 *Recovery Services*를 입력합니다. 입력하기 시작하면 입력은 리소스 목록을 필터링합니다. **Recovery Services 자격 증명 모음**이 보이면 선택합니다.

    ![모든 서비스 대화 상자에서 Recovery Services 입력](./media/backup-azure-sql-database/all-services.png) <br/>

    구독의 Recovery Services 자격 증명 모음 목록이 표시됩니다. 

4. Recovery Services 자격 증명 모음 목록에서 SQL 데이터베이스를 보호하는 데 사용할 자격 증명 모음을 선택합니다.

5. 자격 증명 모음 대시보드 메뉴에서 **+ 백업**을 클릭하여 **백업 목표** 메뉴를 엽니다.

   ![+ 백업을 클릭하여 백업 목표 메뉴를 엽니다.](./media/backup-azure-sql-database/open-backup-menu.png)

6. **백업 목표** 메뉴의 **작업이 실행되는 위치** 메뉴에서 **Azure**를 기본으로 유지합니다.

7. **백업할 항목** 메뉴에서 드롭다운 메뉴를 확장하고 **Azure VM의 SQL Server**를 선택합니다.

    ![+ 백업을 클릭하여 백업 목표 메뉴를 엽니다.](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    선택하면 **백업 목표** 메뉴에 Discover DBs in VMs(VM의 DB 검색)과 백업 구성이라는 두 가지 새로운 단계가 표시됩니다. 

    ![새 백업 목표 단계 표시](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. **검색 시작**을 클릭하여 구독에서 보호되지 않은 가상 머신을 검색합니다. 구독에 있는 보호되지 않은 가상 머신의 수에 따라 모든 가상 머신을 검색하는 데 약간의 시간이 걸릴 수 있습니다.

    ![보류 중인 Backup](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    보호되지 않은 가상 머신이 검색되면 목록에 나타납니다. 여러 가상 머신의 이름이 동일할 수 있습니다. 하지만 이름이 동일한 여러 가상 머신은 다른 리소스 그룹에 속합니다. 보호되지 않은 가상 머신은 가상 머신 이름 및 리소스 그룹별로 나열됩니다. 예상되는 가상 머신이 나열되지 않는 경우 가상 머신이 자격 증명 모음에 이미 보호되어 있는지 확인합니다.

9. 가상 머신 목록에서 보호할 SQL 데이터베이스가 포함된 가상 머신의 확인란을 선택하고 **DB 검색**을 클릭합니다.

    Azure Backup은 가상 머신의 모든 SQL 데이터베이스를 검색합니다. 데이터베이스 검색 단계에서 수행되는 작업에 대한 정보는 [SQL 데이터베이스 검색 시 백 엔드 작업](backup-azure-sql-database.md#backend-operations-when-discovering-sql-databases) 섹션을 참조하세요. SQL 데이터베이스가 검색되면 [백업 작업을 구성](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database)할 준비가 됩니다.

### <a name="backend-operations-when-discovering-sql-databases"></a>SQL 데이터베이스 검색 시 백 엔드 작업

**DB 검색** 도구를 사용하는 경우 Azure Backup은 백그라운드에서 다음과 같은 작업을 실행합니다.

- 워크로드 백업을 위해 Recovery Services 자격 증명 모음에 가상 머신을 등록합니다. 등록된 가상 머신의 모든 데이터베이스는 이 Recovery Services 자격 증명 모음에만 백업할 수 있습니다. 

- 가상 머신에 **AzureBackupWindowsWorkload** 확장을 설치합니다. SQL 데이터베이스 백업은 에이전트가 없는 솔루션입니다. 즉 가상 머신에 설치된 확장을 사용하기 때문에 SQL 데이터베이스에 에이전트가 설치되지 않습니다.

- 가상 머신에 서비스 계정인 **NT Service\AzureWLBackupPluginSvc**를 만듭니다. 모든 백업 및 복원 작업에는 서비스 계정이 사용됩니다. **NT Service\AzureWLBackupPluginSvc**에는 SQL sysadmin 권한이 필요합니다. 모든 SQL Marketplace 가상 머신에는 SqlIaaSExtension이 설치되어 있으며 AzureBackupWindowsWorkload는 SQLIaaSExtension을 사용하여 필요한 권한을 자동으로 받습니다. 가상 머신에 SqlIaaSExtension이 설치되어 있지 않으면 DB 검색 작업이 실패하고 **UserErrorSQLNoSysAdminMembership**이라는 오류 메시지가 나타납니다. 백업을 위해 sysadmin 권한을 추가하려면 [마켓플레이스 SQL VM이 아닌 VM에 대한 Azure Backup 권한 설정](backup-azure-sql-database.md#set-permissions-for-non--marketplace-sql-vms)의 지침을 따르십시오.

    ![VM 및 데이터베이스 선택](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-database"></a>SQL Server 데이터베이스에 대한 백업 구성

Azure Backup은 SQL Server 데이터베이스를 보호하고 백업 작업을 관리하는 관리 서비스를 제공합니다. 관리 및 모니터링 기능은 Recovery Services 자격 증명 모음에 따라 달라집니다. 

> [!NOTE]
> SQL Server 데이터베이스를 백업하려면 한 번에 하나의 백업 솔루션만 사용할 수 있습니다. 이 기능을 사용하기 전에 다른 SQL 백업을 비활성화하십시오. 그렇지 않으면 백업에 방해가 되어 실패합니다. IaaS VM의 Azure Backup을 SQL 백업과 함께 충돌 없이 사용할 수 있습니다. 
>

SQL 데이터베이스에 대한 보호를 구성하려면:

1. SQL 가상 머신에 등록된 Recovery Services 자격 증명 모음을 엽니다.

2. 자격 증명 모음 대시보드 메뉴에서 **+ 백업**을 클릭하여 **백업 목표** 메뉴를 엽니다.

    ![+ 백업을 클릭하여 백업 목표 메뉴를 엽니다.](./media/backup-azure-sql-database/open-backup-menu.png)

3. **백업 목표** 메뉴의 **작업이 실행되는 위치** 메뉴에서 **Azure**를 기본으로 유지합니다.

4. **백업할 항목** 메뉴에서 드롭다운 메뉴를 확장하고 **Azure VM의 SQL Server**를 선택합니다.

    ![+ 백업을 클릭하여 백업 목표 메뉴를 엽니다.](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    선택하면 **백업 목표** 메뉴에 Discover DBs in VMs(VM의 DB 검색)과 백업 구성이라는 두 가지 새로운 단계가 표시됩니다. 이 문서를 순서대로 읽은 경우에는 보호되지 않은 가상 머신이 이미 검색되었고 이 자격 증명 모음은 가상 머신에 등록되어 있습니다. 이제 SQL 데이터베이스에 대한 보호를 구성할 준비가 되었습니다.

5. 백업 목표 메뉴에서 **백업 구성**을 클릭합니다.

    ![새 백업 목표 단계 표시](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    Azure Backup 서비스는 독립 실행형 데이터베이스는 물론 SQL AlwaysOn 가용성 그룹이 있는 모든 SQL 인스턴스를 표시합니다. SQL 인스턴스에서 독립 실행형 데이터베이스를 보려면 인스턴스 이름 옆의 펼침 단추를 클릭하여 데이터베이스를 봅니다. 다음 이미지는 독립 실행형 인스턴스 및 Always On 가용성 그룹의 예를 보여줍니다.

    > [!NOTE]
    > SQL Always On 가용성 그룹의 경우에 SQL 백업 기본 설정을 사용합니다. 하지만 SQL 플랫폼 제한 사항이 있기 때문에 전체 및 차등 백업은 주 노드에서 발생해야 합니다. 로그 백업은 백업 기본 설정을 기반으로 발생할 수 있습니다. 이러한 제한 사항이 있기 때문에 주 노드를 가용성 그룹에 등록해야 합니다.
    >

    ![SQL 인스턴스에 있는 데이터베이스 목록](./media/backup-azure-sql-database/discovered-databases.png)

    데이터베이스 목록을 보려면 AlwaysOn 가용성 그룹 옆의 펼침 단추를 클릭합니다.

    ![AlwaysOn 가용성 그룹의 데이터베이스 목록](./media/backup-azure-sql-database/discovered-database-availability-group.png)

6. 데이터베이스 목록에서 보호할 항목을 모두 선택하고 **확인**을 클릭합니다.

    ![보호할 여러 데이터베이스 선택](./media/backup-azure-sql-database/select-multiple-database-protection.png)

    한 번에 최대 50개의 데이터베이스를 선택할 수 있습니다. 50개가 넘는 데이터베이스를 보호하려면 여러 번 전달합니다. 처음 데이터베이스 50개를 보호한 후 이 단계를 반복하여 다음 데이터베이스 집합을 보호합니다.
    > [!Note] 
    > 백업 로드를 최적화하기 위해 Azure Backup은 큰 백업 작업을 여러 일괄 처리로 나눕니다. 백업 작업 하나에 포함되는 최대 데이터베이스 수는 50개입니다.
    >
    >

7. 백업 정책을 만들거나 선택하려면 **백업** 메뉴에서 **백업 정책**을 선택하여 메뉴를 엽니다.

    ![백업 정책 선택 옵션](./media/backup-azure-sql-database/select-backup-policy.png)

8. **백업 정책 선택** 드롭다운 메뉴에서 백업 정책을 선택하고 **확인**을 클릭합니다. 자체 백업 정책 만들기에 대한 내용은 [백업 정책 정의](backup-azure-sql-database.md#define-a-backup-policy) 섹션을 참조하세요.

    ![드롭다운 메뉴에서 백업 정책 선택](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    백업 정책 메뉴의 **백업 정책 선택** 드롭다운 메뉴에서: 
    - 기본 HourlyLogBackup 정책, 
    - 이전에 SQL용으로 만든 기존 백업 정책을 선택하여,
    - RPO(복구 지점 목표) 및 보존 범위를 기반으로 [새 정책을 정의](backup-azure-sql-database.md#define-a-backup-policy)할 수 있습니다. 

    > [!Note]
    > Azure Backup은 정책 준수 요구 사항을 충족하면서 백 엔드 저장소 소비를 최적화하기 위해 GFS(Grandfather-Father-Son) 체계를 기반으로 장기 보존을 지원합니다.
    >

9. 백업 정책을 선택했으면 **백업 메뉴**에서 **백업 사용**을 클릭합니다.

    ![선택한 백업 정책 사용](./media/backup-azure-sql-database/enable-backup-button.png)

    포털의 알림 영역에서 구성 진행률을 추적할 수 있습니다.

    ![알림 영역 보기](./media/backup-azure-sql-database/notifications-area.png)


### <a name="define-a-backup-policy"></a>백업 정책 정의

백업 정책은 백업이 수행되는 시기와 백업이 유지되는 기간에 대한 매트릭스를 정의합니다. Azure Backup을 사용하여 SQL 데이터베이스에 대한 세 가지 유형의 백업을 예약할 수 있습니다.

* 전체 백업 - 전체 데이터베이스 백업은 전체 데이터베이스를 백업합니다. 전체 백업은 특정 데이터베이스 또는 파일 그룹 또는 파일 집합의 모든 데이터와 이런 데이터를 복구하기에 충분한 로그를 포함합니다. 많으면, 하루에 하나의 전체 백업을 트리거할 수 있습니다. 매일 또는 매주 간격으로 전체 백업을 수행하도록 선택할 수 있습니다. 
* 차등 백업 - 차등 백업은 가장 최근에 수행한 이전 전체 데이터 백업을 기반으로 합니다. 차등 백업은 전체 백업 이후 변경된 데이터만 캡처합니다. 많으면, 하루에 하나의 차등 백업을 트리거할 수 있습니다. 전체 백업과 차등 백업을 같은 날에 구성할 수 없습니다.
* 트랜잭션 로그 백업 - 로그 백업을 사용하면 특정 시간(초 단위)까지의 특정 시점 복원이 가능합니다. 많으면, 15분마다 트랜잭션 로그 백업을 구성할 수 있습니다.

정책은 Recovery Services 자격 증명 모음 수준에서 만들어집니다. 자격 증명 모음이 여러 개 있는 경우 자격 증명 모음은 동일한 백업 정책을 사용할 수 있지만 자격 증명 모음마다 백업 정책을 적용해야 합니다. 백업 정책을 만드는 경우 매일, 전체 백업이 기본값입니다. 차등 백업을 추가할 수 있지만 전체 백업이 주 단위로 수행되도록 전환하는 경우에만 가능합니다. 다음 절차에서는 Azure 가상 머신에서 SQL 서버에 대한 백업 정책을 만드는 방법에 대해 설명합니다.

백업 정책을 만들려면

1. 백업 정책 메뉴의 **백업 정책 선택** 드롭다운 메뉴에서 **새로 만들기**를 선택합니다.

   ![새 백업 정책 만들기](./media/backup-azure-sql-database/create-new-backup-policy.png)

    백업 정책 메뉴가 새 SQL Server 백업 정책에 필요한 필드를 제공하도록 전환됩니다.

   ![새 백업 정책 필드](./media/backup-azure-sql-database/blank-new-policy.png)

2. **정책 이름**에 이름을 제공합니다. 

3. 전체 백업은 필수입니다. 전체 백업의 기본값을 적용하거나 **전체 백업**을 클릭하여 정책을 편집할 수 있습니다.

    ![새 백업 정책 필드](./media/backup-azure-sql-database/full-backup-policy.png)

    전체 백업 정책에서 빈도에 대해 매일 또는 매주를 선택합니다. 매일을 선택한 경우 백업 작업이 시작되는 시간과 표준 시간대를 선택합니다. 매일 전체 백업을 선택하면 차등 백업을 만들 수 없습니다.

   ![매일에 대한 간격 설정](./media/backup-azure-sql-database/daily-interval.png)

    매주를 선택한 경우 백업 작업이 시작되는 요일, 시간 및 표준 시간대를 선택합니다.

   ![매주에 대한 간격 설정](./media/backup-azure-sql-database/weekly-interval.png)

4. 기본적으로 모든 보유 기간 옵션(매일, 매주, 매월 및 매년)이 선택됩니다. 원하지 않는 보존 범위 제한을 선택 취소하고 사용할 간격을 설정합니다. 전체 백업 정책 메뉴에서 **확인**을 클릭하여 설정을 적용합니다.

   ![보존 범위 간격 설정](./media/backup-azure-sql-database/retention-range-interval.png)

    복구 지점은 보존 범위에 따라 보존 태그가 지정됩니다. 예를 들어, 매일, 전체 백업을 선택하면 매일 하나의 전체 백업만 트리거됩니다. 매주 보존에 따라, 매주 보존 범위를 기반으로 특정 요일의 백업에 태그가 지정되고 보존됩니다. 매월 및 매년 보존 범위도 유사하게 작동합니다.

5. 차등 백업 정책을 추가하려면 **차등 백업**을 클릭하여 해당 메뉴를 엽니다. 

   ![차등 정책 열기](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    차등 백업 정책 메뉴에서 **사용**을 선택하여 빈도 및 보존 컨트롤을 엽니다. 많으면, 하루에 하나의 차등 백업을 트리거할 수 있습니다.
    > [!Important] 
    > 차등 백업은 최대 180일 동안 보존될 수 있습니다. 더 길게 보존해야 하는 경우 전체 백업을 사용해야 하며 차등 백업은 사용할 수 없습니다.
    >

   ![차등 정책 편집](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    **확인**을 클릭하여 정책을 저장하고 주 백업 정책 메뉴로 돌아갑니다.

6. 트랜잭션 로그 백업 정책을 추가하려면 **로그 백업**을 클릭하여 해당 메뉴를 엽니다. 로그 백업 메뉴에서 **사용**을 선택하고 빈도 및 보존 컨트롤을 설정합니다. 로그 백업은 최소 15분 간격으로 수행할 수 있으며 최대 35일 동안 보존할 수 있습니다. **확인**을 클릭하여 정책을 저장하고 주 백업 정책 메뉴로 돌아갑니다.

   ![로그 백업 정책 편집](./media/backup-azure-sql-database/log-backup-policy-editor.png)

7. SQL 백업 압축을 사용할지 여부를 선택합니다. 압축은 기본적으로 사용하지 않도록 설정되어 있습니다.

    백 엔드에서, Azure Backup은 SQL 네이티브 백업 압축을 사용합니다.

8. 백업 정책을 모두 편집했으면 **확인**을 클릭합니다. 

   ![새 정책 적용](./media/backup-azure-sql-database/backup-policy-click-ok.png)

## <a name="restore-a-sql-database"></a>SQL 데이터베이스 복원

Azure Backup은 트랜잭션 로그 백업을 사용하여 특정 날짜나 시간, 특정 시간의 초까지 개별 데이터베이스를 복원하는 기능을 제공합니다. 제공하는 복원 시간에 따라 Azure Backup은 데이터를 복원하는 데 필요한 전체, 차등 및 로그 백업 체인을 자동으로 결정합니다.

또는 구체적인 전체 또는 차등 백업을 선택하여 특정 시간보다는 특정 복구 지점으로 복원할 수 있습니다.
 > [!Note]
 > “마스터” 테이터베이스 복원을 트리거하기 전에 시작 옵션 “-m AzureWorkloadBackup”을 사용하여 단일 사용자 모드로 SQL Server를 시작하세요. -m에 대한 인수는 클라이언트 이름입니다. 이 클라이언트는 연결을 열기 위해 허용됩니다. 모든 시스템 데이터베이스(모델, 마스터, msdb)의 경우 복원을 트리거하기 전에 SQL 에이전트 서비스를 중지하세요. 이러한 DB에 대한 연결을 도용하려고 할 수 있는 응용 프로그램을 닫습니다.
>

데이터베이스를 복원하려면

1. SQL 가상 머신에 등록된 Recovery Services 자격 증명 모음을 엽니다.

2. 자격 증명 모음 대시보드에서 **사용량** 백업 항목을 선택하여 백업 항목 메뉴를 엽니다.

    ![+ 백업을 클릭하여 백업 목표 메뉴를 엽니다.](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png)에서도 확인할 수 있습니다.

3. **백업 항목** 메뉴에서 백업 관리 유형인 **SQL in Azure VM**(Azure VM의 SQL)을 선택합니다. 

    ![+ 백업을 클릭하여 백업 목표 메뉴를 엽니다.](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    백업 항목 목록이 SQL 데이터베이스 목록을 표시하도록 조정됩니다. 

4. SQL 데이터베이스 목록에서 복원할 데이터베이스를 선택합니다.

    ![목록에서 Azure VM의 SQL 선택](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    데이터베이스를 선택하면 해당 메뉴가 열립니다. 이 메뉴는 다음을 비롯한 데이터베이스에 대한 백업 세부 사항을 제공합니다.

    * 가장 오래된 복원 지점 및 최신 지점 복원,
    * 지난 24 시간 동안의 백업 상태(트랜잭션 로그 백업으로 구성된 경우 전체 및 대량 로그 복구 모델의 데이터베이스에 대한 상태)

5. 선택한 데이터베이스 메뉴에서 **DB 복원**을 클릭하여 복원 메뉴를 엽니다.

    ![데이터베이스 복원 선택](./media/backup-azure-sql-database/restore-db-button.png)

    **복원** 메뉴가 열리고 **복원 구성** 메뉴도 열립니다. **복원 구성** 메뉴는 복원을 구성하는 첫 번째 단계입니다. 이 메뉴에서 데이터를 복원할 위치를 선택합니다. 옵션은 다음과 같습니다.
    * 대체 위치 - 원래 원본 데이터베이스를 유지하면서 대체 위치에 데이터베이스를 복원하려면 이 옵션을 사용합니다.
    * DB 덮어쓰기 - 원래 원본과 동일한 SQL Server 인스턴스에 데이터를 복원합니다. 이 옵션의 결과는 원래 데이터베이스를 덮어쓰는 것입니다.

    > [!Important]
    > 선택한 데이터베이스가 Always On 가용성 그룹에 속하면 SQL에서 데이터베이스를 겹쳐쓸 수 없습니다. 이런 경우 **대체 위치** 옵션만 사용됩니다.
    >

    ![구성을 클릭하여 복원 구성 메뉴 열기](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>대체 위치에 복원

이 절차는 대체 위치에 데이터를 복원하는 과정을 안내합니다. 복원할 때 데이터베이스를 덮어쓰려면 [데이터베이스 복원 및 덮어쓰기](backup-azure-sql-database.md#restore-and-overwrite-the-database) 섹션으로 이동하세요. 이 절차에서는 Recovery Services 자격 증명 모음이 열려있고 [복원 구성] 메뉴에 있다고 가정합니다. 그렇지 않은 경우 [SQL 데이터베이스 복원](backup-azure-sql-database.md#restore-a-sql-database) 섹션부터 시작하세요.

> [!NOTE]
> 동일한 Azure 지역에서 SQL Server에 데이터베이스를 복원할 수 있습니다. 대상 서버는 Recovery Services 자격 증명 모음에 등록되어야 합니다. 
>

**서버** 드롭다운 메뉴에는 Recovery Services 자격 증명 모음에 등록된 SQL 서버만 표시됩니다. 원하는 서버가 **서버** 목록에 없는 경우에는 [SQL Server 데이터베이스 검색](backup-azure-sql-database.md#discover-sql-server-databases)을 참조하여 서버를 찾으십시오. 데이터베이스를 검색하는 동안 새로운 서버를 찾으면 Recovery Services 자격 증명 모음에 등록됩니다.

1. **복원 구성** 메뉴에서:

    * **대체 위치**를 선택하고,
    * **서버**에 대해 데이터베이스를 복원할 SQL 서버를 선택합니다.
    * **인스턴스** 드롭다운 메뉴에서 SQL 인스턴스를 선택합니다.
    * **복원된 DB 이름** 대화 상자에 대상 데이터베이스의 이름을 제공합니다.
    * 해당되는 경우 **Overwrite if the DB with the same name already exists on selected SQL instance**(선택한 SQL 인스턴스에 이름이 같은 DB가 있으면 덮어쓰기)를 선택합니다.
    * **확인**을 클릭하여 대상 구성을 완료하고 복원 지점 선택으로 이동합니다.

    ![복원 구성 메뉴에서 대체 위치, 인스턴스 및 이름 선택](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. **복원 지점 선택** 메뉴에서 Logs (Point in Time)(로그(특정 시점)) 또는 Full & Differential(전체 및 차등) 복원 지점을 선택할 수 있습니다. 특정 시점 로그로 복원하려면 이 단계를 계속 진행합니다. 전체 또는 차등 복원 지점을 복원하려면 3단계로 건너뜁니다.

    ![복원 지점 선택 메뉴](./media/backup-azure-sql-database/recovery-point-menu.png)

    특정 시점 복원은 전체 및 대량 로그된 복구 모델을 사용하는 데이터베이스의 로그 백업에만 사용할 수 있습니다. 특정 시점으로 복원하려면:

    1. **Logs (Point in Time)**(로그(특정 시점))를 복원 옵션으로 선택합니다.

        ![복원 지점 유형 선택](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. **복원 날짜/시간**에서 달력 아이콘을 클릭하여 달력을 엽니다. 굵게 표시된 날짜에 복구 지점이 포함되고 현재 날짜는 강조 표시됩니다. 달력에서 복구 지점이 있는 날짜를 선택합니다. 복구 지점이 없는 날짜는 선택할 수 없습니다.

        ![달력 열기](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        날짜를 선택하면 타임라인 그래프에 사용 가능한 복구 지점이 연속적인 범위로 표시됩니다.

    3. 타임라인 그래프나 시간 대화 상자를 사용하여 복구 지점에 대한 특정 시간을 지정하고 **확인**을 클릭하여 복원 지점 단계를 완료합니다.
    
       ![달력 열기](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        **복원 지점 선택** 메뉴가 닫히고 **고급 구성** 메뉴가 열립니다.

       ![고급 구성 메뉴](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. **고급 구성** 메뉴에서:

        * 복원 후 데이터베이스를 작동하지 않는 상태로 유지하려면, **Restore with NORECOVERY**(NORECOVERY를 사용하여 복원) 메뉴에서 **사용**을 선택합니다.
        * 대상 서버에서 복원 위치를 변경하려면 **대상** 열에 새 경로를 제공합니다.
        * **확인**을 클릭하여 설정을 승인하고 **고급 구성**을 닫습니다.

    5. **복원** 메뉴에서 **복원**을 클릭하여 복원 작업을 시작합니다. 알림 영역에서 진행률을 추적할 수 있습니다. 데이터베이스 복원 작업에서 진행률을 추적할 수도 있습니다.

       ![고급 구성 메뉴](./media/backup-azure-sql-database/restore-job-notification.png)

3. **복원 지점 선택** 메뉴에서 복원 지점을 선택합니다. Logs (Point in Time)(로그(특정 시점)) 또는 Full & Differential(전체 및 차등)을 선택할 수 있습니다. 특정 시점 로그를 복원하려면 2단계로 돌아갑니다. 이 단계는 특정한 전체 또는 차등 복원 지점을 복원합니다. 이 옵션을 사용하면 지난 30일 동안의 전체 및 차등 복구 지점을 볼 수 있습니다. 30일 보다 오래된 복구 지점을 보려면 **필터**를 클릭하여 **필터 복원 지점** 메뉴를 엽니다. 차등 복구 지점을 선택한 경우 Azure Backup에서 먼저 적절한 전체 복구 지점이 복원된 다음, 선택한 차등 복구 지점이 적용됩니다.

    ![복원 지점 선택 메뉴](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. **복원 지점 선택** 메뉴에서 **Full & Differential**(전체 및 차등)을 선택합니다.

       ![복원 지점 선택 메뉴](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        사용 가능한 복구 지점 목록이 표시됩니다.

    2. 복구 지점 목록에서 복구 지점을 선택하고 **확인**을 클릭하여 복원 지점 절차를 완료합니다. 

        ![전체 복구 지점 선택](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        **복원 지점** 메뉴가 닫히고 **고급 구성** 메뉴가 열립니다.

        ![고급 구성 메뉴](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. **고급 구성** 메뉴에서:

        * 복원 후 데이터베이스를 작동하지 않는 상태로 유지하려면, **Restore with NORECOVERY**(NORECOVERY를 사용하여 복원) 메뉴에서 **사용**을 선택합니다. **Restore with NORECOVERY**(NORECOVERY를 사용하여 복원)는 기본적으로 사용하지 않도록 설정되어 있습니다.
        * 대상 서버에서 복원 위치를 변경하려면 **대상** 열에 새 경로를 제공합니다.
        * **확인**을 클릭하여 설정을 승인하고 **고급 구성**을 닫습니다.

    4. **복원** 메뉴에서 **복원**을 클릭하여 복원 작업을 시작합니다. 알림 영역에서 진행률을 추적할 수 있습니다. 데이터베이스 복원 작업에서 진행률을 추적할 수도 있습니다.

       ![고급 구성 메뉴](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>데이터베이스 복원 및 덮어쓰기

이 절차에서는 데이터를 복원하고 데이터베이스를 덮어쓰는 과정을 안내합니다. 대체 위치에 복원하려면 [대체 위치에 복원](backup-azure-sql-database.md#restore-to-an-alternate-location) 섹션으로 이동합니다. 이 절차에서는 Recovery Services 자격 증명 모음이 열려있고 **복원 구성** 메뉴에 있다고 가정합니다(아래 이미지 참조). 그렇지 않은 경우 [SQL 데이터베이스 복원](backup-azure-sql-database.md#restore-a-sql-database) 섹션부터 시작하세요.

![구성을 클릭하여 복원 구성 메뉴 열기](./media/backup-azure-sql-database/restore-overwrite-db.png)

**서버** 드롭다운 메뉴에는 Recovery Services 자격 증명 모음에 등록된 SQL 서버만 표시됩니다. 원하는 서버가 **서버** 목록에 없는 경우에는 [SQL Server 데이터베이스 검색](backup-azure-sql-database.md#discover-sql-server-databases)을 참조하여 서버를 찾으십시오. 데이터베이스를 검색하는 동안 새로운 서버를 찾으면 Recovery Services 자격 증명 모음에 등록됩니다.

1. **복원 구성** 메뉴에서 **DB 덮어쓰기**를 선택하고 **확인**을 클릭하여 대상 구성을 완료합니다. 

   ![DB 덮어쓰기 클릭](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    **서버**, **인스턴스** 및 **복원된 DB 이름** 대화 상자는 필요하지 않습니다.

2. **복원 지점 선택** 메뉴에서 Logs (Point in Time)(로그(특정 시점)) 또는 Full & Differential(전체 및 차등) 복원 지점을 선택할 수 있습니다. 특정 시점 로그를 복원하려면 이 단계를 계속 진행합니다. 전체 및 차등 복원 지점을 복원하려면 3단계로 건너뜁니다.

    ![복원 지점 선택 메뉴](./media/backup-azure-sql-database/recovery-point-menu.png)

    특정 시점 복원은 전체 및 대량 로그된 복구 모델을 사용하는 데이터베이스의 로그 백업에만 사용할 수 있습니다. 특정 시점 복원을 특정 시간(초 단위)으로 선택하려면:

    1. **Logs (Point in Time)**(로그(특정 시점))를 복원 옵션으로 선택합니다.

        ![복원 지점 유형 선택](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. **복원 날짜/시간**에서 달력 아이콘을 클릭하여 달력을 엽니다. 굵게 표시된 날짜에 복구 지점이 포함되고 현재 날짜는 강조 표시됩니다. 달력에서 복구 지점이 있는 날짜를 선택합니다. 복구 지점이 없는 날짜는 선택할 수 없습니다.

        ![달력 열기](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        날짜를 선택하면 타임라인 그래프에 사용 가능한 복구 지점이 표시됩니다.

    3. 타임라인 그래프나 시간 대화 상자를 사용하여 복구 지점에 대한 특정 시간을 지정하고 **확인**을 클릭하여 복원 지점 단계를 완료합니다.
    
       ![달력 열기](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        **복원 지점 선택** 메뉴가 닫히고 **고급 구성** 메뉴가 열립니다.

       ![고급 구성 메뉴](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. **고급 구성** 메뉴에서:

        * 복원 후 데이터베이스를 작동하지 않는 상태로 유지하려면, **Restore with NORECOVERY**(NORECOVERY를 사용하여 복원) 메뉴에서 **사용**을 선택합니다.
        * 대상 서버에서 복원 위치를 변경하려면 **대상** 열에 새 경로를 제공합니다.
        * **확인**을 클릭하여 설정을 승인하고 **고급 구성**을 닫습니다.

    5. **복원** 메뉴에서 **복원**을 클릭하여 복원 작업을 시작합니다. 알림 영역에서 진행률을 추적할 수 있습니다. 데이터베이스 복원 작업에서 진행률을 추적할 수도 있습니다.

       ![고급 구성 메뉴](./media/backup-azure-sql-database/restore-job-notification.png)

3. **복원 지점 선택** 메뉴에서 복원 지점을 선택합니다. Logs (Point in Time)(로그(특정 시점)) 또는 Full & Differential(전체 및 차등)을 선택할 수 있습니다. 특정 시점 로그를 복원하려면 2단계로 돌아갑니다. 이 단계는 특정한 전체 또는 차등 복원 지점을 복원합니다. 이 옵션을 사용하면 지난 30일 동안의 전체 및 차등 복구 지점을 볼 수 있습니다. 30일 보다 오래된 복구 지점을 보려면 **필터**를 클릭하여 **필터 복원 지점** 메뉴를 엽니다. 차등 복구 지점을 선택한 경우 Azure Backup에서 먼저 적절한 전체 복구 지점이 복원된 다음, 선택한 차등 복구 지점이 적용됩니다.

    ![복원 지점 선택 메뉴](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. **복원 지점 선택** 메뉴에서 **Full & Differential**(전체 및 차등)을 선택합니다.

       ![복원 지점 선택 메뉴](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        사용 가능한 복구 지점 목록이 표시됩니다.

    2. 복구 지점 목록에서 복구 지점을 선택하고 **확인**을 클릭하여 복원 지점 절차를 완료합니다. 

        ![전체 복구 지점 선택](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        **복원 지점** 메뉴가 닫히고 **고급 구성** 메뉴가 열립니다.

        ![고급 구성 메뉴](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. **고급 구성** 메뉴에서:

        * 복원 후 데이터베이스를 작동하지 않는 상태로 유지하려면, **Restore with NORECOVERY**(NORECOVERY를 사용하여 복원) 메뉴에서 **사용**을 선택합니다. **Restore with NORECOVERY**(NORECOVERY를 사용하여 복원)는 기본적으로 사용하지 않도록 설정되어 있습니다.
        * 대상 서버에서 복원 위치를 변경하려면 **대상** 열에 새 경로를 제공합니다.
        * **확인**을 클릭하여 설정을 승인하고 **고급 구성**을 닫습니다.

    4. **복원** 메뉴에서 **복원**을 클릭하여 복원 작업을 시작합니다. 알림 영역에서 진행률을 추적할 수 있습니다. 데이터베이스 복원 작업에서 진행률을 추적할 수도 있습니다.

       ![고급 구성 메뉴](./media/backup-azure-sql-database/restore-job-notification.png)


## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>Azure VM의 SQL에 대한 Azure Backup 작업 관리

이 섹션에서는 Azure 가상 머신의 SQL에 사용할 수 있는 다양한 Azure Backup 관리 작업에 대한 정보를 제공합니다. 다음과 같은 상위 수준 작업이 있습니다.

* 작업 모니터링
* 백업 경고
* SQL 데이터베이스에 대한 보호 중지
* SQL 데이터베이스에 대한 보호 재개
* 임시 백업 작업 트리거
* SQL Server 등록 취소

### <a name="monitor-jobs"></a>작업 모니터링
엔터프라이즈 클래스 솔루션인 Azure Backup은 고급 Backup 경고 및 오류에 대한 알림을 제공합니다(아래의 백업 경고 섹션 참조). 특별한 작업을 모니터링하려는 경우 요구 사항에 따라 다음 옵션 중 하나를 사용할 수 있습니다.

#### <a name="use-azure-portal-for-all-adhoc-operations"></a>모든 임시 작업에 Azure Portal 사용
Azure Backup은 백업 작업 포털에서 수동으로 트리거된 작업이나 임시 작업을 모두 표시합니다. 포털에서 사용할 수 있는 작업에는 모든 백업 구성 작업, 수동으로 트리거된 백업 작업, 복원 작업, 데이터베이스 등록 및 검색 작업, 백업 중지 작업 등이 있습니다. 
![고급 구성 메뉴](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> 전체, 차등 및 로그 백업을 비롯한 예약된 모든 백업 작업은 포털에 표시되지 않으며 아래에 설명된 대로 SQL Server Management Studio를 사용하여 모니터링될 수 있습니다.
>

#### <a name="use-sql-server-management-studio-for-backup-jobs"></a>백업 작업에 SQL Server Management Studio 사용
Azure Backup은 모든 백업 작업에 대해 SQL 네이티브 API를 사용합니다. 네이티브 API를 사용하면 msdb 데이터베이스의 [SQL backupset 테이블](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017)에서 모든 작업 정보를 가져올 수 있습니다.

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

### <a name="backup-alerts"></a>백업 경고

로그 백업이 15분마다 발생하면 백업 작업을 모니터링하는 일이 가끔은 번거로울 수 있습니다. Azure Backup은 백업 실패가 발생하면 이메일 경고가 트리거되는 기능을 제공하여 번거로울 수 있는 상황에 대비하였습니다. 경고는 오류 코드별로 데이터베이스 수준에서 통합됩니다. 예를 들어, 데이터베이스에 다수의 백업 실패가 있는 경우, 실패할 때마다 경고를 받는 대신 첫 번째 실패에 대해서만 이메일을 수신합니다. 그러면 Azure Portal에 로그인하여 해당 데이터베이스에 대한 후속 실패를 모니터링할 수 있습니다. 

백업 경고를 모니터링하려면:

1. [Azure Portal](https://portal.azure.com)에서 Azure 구독에 로그인합니다.

2. SQL 가상 머신에 등록된 Recovery Services 자격 증명 모음을 엽니다.

3. Recovery Services 자격 증명 모음 메뉴에서 **경고 및 이벤트**를 선택합니다. 

   ![고급 구성 메뉴](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. **경고 및 이벤트** 메뉴에서 **백업 경고**를 선택하여 경로 목록을 확인합니다.

   ![고급 구성 메뉴](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-on-a-sql-server-database"></a>SQL Server 데이터베이스에 대한 보호 중지

SQL Server 데이터베이스에 대한 보호를 중지하면 Azure Backup에 복구 지점을 유지할지 묻는 메시지가 표시됩니다. SQL 데이터베이스 보호를 중지하는 방법은 두 가지입니다.

* 미래의 모든 백업 작업을 중지하고 모든 복구 지점을 삭제,
* 미래의 모든 백업 작업을 중지하지만 복구 지점은 유지 

복구 지점을 유지하면 비용이 발생합니다. SQL에 대한 복구 지점에는 SQL 보호된 인스턴스 비용 및 소비된 저장소에 대한 비용이 발생하기 때문입니다. SQL의 Azure Backup 가격 책정에 대한 자세한 내용은 [Azure Backup 가격 책정 페이지](https://azure.microsoft.com/pricing/details/backup/)를 참조하세요. 데이터베이스에 대한 보호를 중지하려면:

1. SQL 가상 머신에 등록된 Recovery Services 자격 증명 모음을 엽니다.

2. 자격 증명 모음 대시보드에서 **사용량** 백업 항목을 선택하여 백업 항목 메뉴를 엽니다.

    ![+ 백업을 클릭하여 백업 목표 메뉴를 엽니다.](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png)에서도 확인할 수 있습니다.

3. **백업 항목** 메뉴에서 백업 관리 유형인 **SQL in Azure VM**(Azure VM의 SQL)을 선택합니다. 

    ![+ 백업을 클릭하여 백업 목표 메뉴를 엽니다.](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    백업 항목 목록이 SQL 데이터베이스 목록을 표시하도록 조정됩니다. 

4. SQL 데이터베이스 목록에서 보호를 중지할 데이터베이스를 선택합니다.

    ![목록에서 Azure VM의 SQL 선택](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    데이터베이스를 선택하면 해당 메뉴가 열립니다. 

5. 선택한 데이터베이스 메뉴에서 **백업 중지**를 클릭하여 데이터베이스 보호를 중지합니다.

    ![데이터베이스 복원 선택](./media/backup-azure-sql-database/stop-db-button.png)

    **백업 중지** 메뉴가 열립니다.

6. **백업 중지** 메뉴에서 백업 데이터 보존 또는 백업 데이터 삭제를 선택합니다. 필요에 따라 보호를 중지하는 이유와 메모를 제공할 수 있습니다.

    ![데이터베이스 복원 선택](./media/backup-azure-sql-database/stop-backup-button.png)

7. **백업 중지**를 클릭하여 데이터베이스에 대한 보호를 중지합니다. 

### <a name="resume-protection-for-a-sql-database"></a>SQL 데이터베이스에 대한 보호 재개

SQL 데이터베이스에 대한 보호를 중지할 때 **백업 데이터 보존** 옵션을 선택한 경우 보호를 다시 시작할 수 있습니다. 백업 데이터가 보존되지 않은 경우에는 보호를 다시 시작할 수 없습니다. 

1. SQL 데이터베이스에 대한 보호를 다시 시작하려면 백업 항목을 열고 **백업 다시 시작**을 클릭합니다.

    ![데이터베이스 보호 다시 시작](./media/backup-azure-sql-database/resume-backup-button.png)

   백업 정책 메뉴가 열립니다.

2. **백업 정책** 메뉴에서 정책을 선택하고 **저장**을 클릭합니다.

### <a name="trigger-an-adhoc-backup"></a>임시 백업 트리거

임시 백업은 언제든지 트리거할 수 있습니다. 임시 백업에는 네 가지 유형이 있습니다. 각 유형에 대한 자세한 내용은 [SQL 백업 유형](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups) 문서를 참조하세요.

* 전체 백업
* 복사 전용 전체 백업
* 차등 백업
* 로그 백업

### <a name="unregister-a-sql-server"></a>SQL Server 등록 취소

보호를 제거한 후 자격 증명 모음을 삭제하기 전에 SQL Server 등록을 해제하려면

1. SQL 가상 머신에 등록된 Recovery Services 자격 증명 모음을 엽니다.

2. 자격 증명 모음 메뉴의 **관리** 섹션에서 **백업 인프라**를 클릭합니다.  

   ![데이터베이스 보호 다시 시작](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. **관리 서버** 섹션에서 **보호된 서버**를 클릭합니다.

   ![데이터베이스 보호 다시 시작](./media/backup-azure-sql-database/protected-servers.png)

    보호된 서버 메뉴가 열립니다. 

4. **보호된 서버** 메뉴에서 등록을 취소할 서버를 선택합니다. 자격 증명 모음을 삭제하려면 모든 서버의 등록을 취소해야 합니다.

5. 보호된 서버 메뉴에서 보호된 서버를 마우스 오른쪽 단추로 클릭하고 **삭제**를 선택합니다. 

   ![데이터베이스 보호 다시 시작](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="sql-database-backup-faq"></a>SQL 데이터베이스 백업 FAQ

다음 섹션에서는 SQL 데이터베이스 백업에 대한 추가 정보를 제공합니다.

### <a name="can-i-throttle-the-speed-of-the-sql-backup-policy-so-it-minimizes-impact-on-the-sql-server"></a>SQL Server에 미치는 영향을 최소화하도록 SQL 백업 정책의 속도를 제한할 수 있나요?

예, 백업 정책을 실행하는 속도를 제한할 수 있습니다. 설정을 변경하려면:

1. SQL Server의 `C:\Program Files\Azure Workload Backup\bin` 폴더에서 **TaskThrottlerSettings.json**을 엽니다.

2. **TaskThrottlerSettings.json** 파일에서 **DefaultBackupTasksThreshold**를 더 낮은 값(예: 5)으로 변경합니다.

3. 변경 내용을 저장하고 파일을 닫습니다.

4. SQL Server에서 작업 관리자를 열고 **Azure Backup 워크로드 코디네이터 서비스**를 다시 시작합니다.

### <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>보조 복제본에서 전체 백업을 실행할 수 있나요?

아니요, 이 기능은 지원되지 않습니다.

### <a name="do-successful-backup-jobs-create-alerts"></a>성공한 백업 작업이 경고를 만드나요?

아니요. 성공한 백업 작업에서는 경고를 생성하지 않습니다. 경고는 실패한 백업 작업에 대해서만 전송됩니다.

### <a name="are-scheduled-backup-job-details-shown-in-the-jobs-menu"></a>예약된 백업 작업 세부 정보가 작업 메뉴에서 표시되나요?

아니요. 작업 메뉴에서는 임시 작업 세부 정보를 보여주지만 예약된 백업 작업을 표시하지 않습니다. 예약된 백업 작업에 실패한 경우 실패한 작업 경고에서 모든 세부 정보를 찾을 수 있습니다. 예약된 모든 임시 백업 작업을 모니터링하려는 경우 [SQL Server Management Studio를 사용합니다](backup-azure-sql-database.md#use-sql-server-management-studio-for-backup-jobs).

### <a name="if-i-select-a-sql-server-will-future-databases-automatically-be-added"></a>SQL Server를 선택하면 향후 데이터베이스가 자동으로 추가되나요?

아니요. SQL Server에 대한 보호를 구성하면 서버 수준에서 확인란을 선택하는 경우 모든 데이터베이스가 추가됩니다. 그러나 보호를 구성한 후에 SQL Server에 데이터베이스를 추가하는 경우 수동으로 새 데이터베이스를 추가하여 보호해야 합니다. 데이터베이스는 구성된 보호에 자동으로 포함되지 않습니다.

### <a name="if-i-change-the-recovery-model-how-do-i-restart-protection"></a>복구 모델을 변경하는 경우 보호를 다시 시작해야 하나요?

복구 모델을 변경한 경우 전체 백업을 트리거하면 로그 백업이 예상 대로 시작됩니다.

## <a name="next-steps"></a>다음 단계

Azure Backup에 대한 자세한 내용은 암호화된 가상 머신 백업에 대한 PowerShell 샘플을 참조하세요.

> [!div class="nextstepaction"]
> [암호화된 VM 백업](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
