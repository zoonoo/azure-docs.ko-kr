---
title: Azure Vm에서 SQL Server 데이터베이스 백업 | Microsoft Docs
description: Azure Vm에서 SQL Server 데이터베이스를 백업 하는 방법 알아보기
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: sachdevaswati
ms.openlocfilehash: 0307dc5c83782119f6c10279563b8b9f0a999d28
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236875"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Azure VM의 SQL Server 데이터베이스 백업

SQL Server 데이터베이스는 낮은 복구 지점 목표 (RPO) 및 장기 보존 요구 하는 중요 한 워크 로드. 사용 하 여 Azure virtual machines (Vm)에서 실행 되는 SQL Server 데이터베이스를 백업할 수 있습니다 [Azure Backup](backup-overview.md)합니다.

이 아티클에서 Azure Backup Recovery Services 자격 증명 모음에 Azure VM에서 실행 되는 SQL Server 데이터베이스를 백업 하는 방법에 설명 합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 자격 증명 모음을 만들고 구성합니다.
> * 데이터베이스를 검색 하 고 백업을 설정 합니다.
> * 데이터베이스에 대한 자동 보호를 설정합니다.


## <a name="prerequisites"></a>필수 조건

SQL Server 데이터베이스를 백업 하기 전에 다음 조건을 확인 합니다.

1. 식별 또는 만들기를 [Recovery Services 자격 증명 모음](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) 동일한 지역 또는 SQL Server 인스턴스를 호스팅하는 VM으로 로캘.
2. 확인 합니다 [필요한 VM 권한](backup-azure-sql-database.md#fix-sql-sysadmin-permissions) SQL 데이터베이스를 백업 하려면.
3. VM에 있는지 확인 하십시오 [네트워크 연결](backup-sql-server-database-azure-vms.md#establish-network-connectivity)합니다.
4. SQL Server 데이터베이스에 따라 있는지 확인 합니다 [Azure Backup에 대 한 명명 지침 데이터베이스](#database-naming-guidelines-for-azure-backup)합니다.
5. 데이터베이스에서 활성화 되어 다른 백업 솔루션이 없는지 확인 합니다. 데이터베이스를 백업 하기 전에 다른 모든 SQL Server 백업을 사용 하지 않도록 설정 합니다.

> [!NOTE]
> Azure VM에 대 한 및 충돌 없이 VM에서 실행 되는 SQL Server 데이터베이스에 대 한 Azure Backup을 사용할 수 있습니다.


### <a name="establish-network-connectivity"></a>네트워크 연결 설정

모든 작업에 대 한 SQL Server VM에 Azure 공용 IP 주소에 대 한 연결에 필요합니다. VM 작업 (데이터베이스 검색 백업을 구성, 백업 일정, 복구 지점 복원 및 등)는 Azure 공용 IP 주소에 연결할 수 없는 실패 합니다.

다음 옵션 중 하나를 사용 하 여 연결을 설정 합니다.

- **Azure 데이터 센터 IP 범위 허용**합니다. 이 옵션을 사용 하면 [IP 범위](https://www.microsoft.com/download/details.aspx?id=41653) 다운로드에서 합니다. 네트워크 보안 그룹 (NSG)에 액세스 하려면 Set-azurenetworksecurityrule cmdlet을 사용 합니다. 안전한 경우 받는 사람 목록 지역별 Ip만, 인증을 사용 하도록 설정 하려면 Azure Active Directory (Azure AD) 서비스 태그 안전 받는 사람 목록을 업데이트 해야 합니다.

- **NSG 태그를 사용 하 여 액세스를 허용**합니다. Nsg를 사용 하 여 연결을 제한 하는 경우이 옵션 AzureBackup 태그를 사용 하 여 Azure Backup에 대 한 아웃 바운드 액세스를 허용 하는 NSG 규칙을 추가 합니다. 이 태그 외에 해야 해당 [규칙](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) Azure ad 및 Azure Storage 인증 및 데이터 전송에 대 한 연결을 허용 하도록 합니다. AzureBackup 태그는 PowerShell에서 현재 사용할 수만 있습니다. AzureBackup 태그를 사용 하 여 규칙을 만들려면:

    - Azure 계정 자격 증명을 추가 하 고 국가별 클라우드를 업데이트 합니다.<br/>
    `Add-AzureRmAccount`

    - NSG 구독 선택<br/>
    `Select-AzureRmSubscription "<Subscription Id>"`

     - NSG를 선택 합니다.<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

    - 추가 Azure Backup 서비스 태그에 대 한 아웃 바운드 규칙을 허용 합니다.<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

  - NSG를 저장 합니다.<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`
- **Azure 방화벽 태그를 사용 하 여 액세스를 허용할**합니다. Azure 방화벽을 사용 하는 경우는 AzureBackup를 사용 하 여 응용 프로그램 규칙을 만듭니다 [FQDN 태그](https://docs.microsoft.com/azure/firewall/fqdn-tags)합니다. Azure Backup에 대 한 아웃 바운드 액세스 허용
- **트래픽을 라우트하도록 HTTP 프록시 서버를 배포**합니다. Azure VM에서 SQL Server 데이터베이스를 백업할 때 VM의 백업 확장이 HTTPS Api를 Azure Backup 및 Azure Storage에 데이터 관리 명령을 보내는 데 사용 합니다. 또한 예비 내선 번호는 인증에 대 한 Azure AD를 사용합니다. HTTP 프록시를 통해 이 세 가지 서비스에 대한 백업 확장 트래픽을 라우팅합니다. 확장의 공용 인터넷에 액세스하도록 구성된 유일한 구성 요소입니다.

연결 옵션에는 장점과 단점은 다음과 같습니다.

**옵션** | **장점** | **단점**
--- | --- | ---
IP 범위 허용 | 추가 비용 없이 | IP 주소 범위가 시간이 지나면 변경 때문에 관리 하는 복합 <br/><br/> Azure Storage 뿐 아니라 Azure 전체에 대 한 액세스를 제공합니다.
NSG 서비스 태그를 사용 합니다. | 범위 변경 내용이 자동으로 병합 하는 관리 하기가 <br/><br/> 추가 비용 없이 <br/><br/> | Nsg와만 사용할 수 있습니다. <br/><br/> 전체 서비스에 대 한 액세스를 제공합니다.
태그를 사용 하 여 Azure 방화벽 FQDN | 필요한 Fqdn은 자동으로 관리 되는 관리 하기가 쉬워집니다 | Azure 방화벽을 사용 하 여만 사용할 수 있습니다.
HTTP 프록시 사용 | 저장소는 프록시에 세부적으로 제어 Url 허용 <br/><br/> Vm에 대 한 단일 지점 인터넷 액세스 <br/><br/> Azure IP 주소 변경이 적용 되지 않는 | 프록시 소프트웨어를 사용 하 여 VM을 실행 하는 추가 비용이

### <a name="set-vm-permissions"></a>VM 권한 설정

SQL Server 데이터베이스에 대 한 백업을 구성한 경우 Azure Backup에서는 다음과 같은

- AzureBackupWindowsWorkload 확장을 추가합니다.
- 가상 머신에서 데이터베이스를 검색할 NT SERVICE\AzureWLBackupPluginSvc 계정을 만듭니다. 이 계정은 백업에 사용 됩니다, 복원 및 SQL sysadmin 권한이 필요 합니다.
- VM에서 실행 되는 데이터베이스를 검색 합니다. Azure Backup에는 NT AUTHORITY\SYSTEM 계정을 사용 합니다. 이 계정은 SQL에서 공용 로그인 이어야 합니다.

Azure Marketplace에서 SQL Server VM을 만들지 않은 경우 UserErrorSQLNoSysadminMembership 라는 오류가 표시 될 수 있습니다. 자세한 내용은 참조 기능 고려 사항 및 제한 사항 섹션에서 찾을 [Azure Vm에서 SQL Server 백업에 대 한](backup-azure-sql-database.md#fix-sql-sysadmin-permissions)합니다.

### <a name="database-naming-guidelines-for-azure-backup"></a>데이터베이스를 Azure Backup에 대 한 명명 지침

데이터베이스 이름에 다음 요소를 사용 하지 마십시오.

  * 선행 공백 및 후행
  * 후행 느낌표 (!)
  * 닫는 대괄호 (])
  * 세미콜론 ';'
  * 슬래시 '/'

별칭 지정은 지원 되지 않는 문자에 사용할 수 있지만 방지 하는 것이 좋습니다. 자세한 내용은 [테이블 서비스 데이터 모델 이해](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN)를 참조하세요.


[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server 데이터베이스 검색

VM에서 실행 되는 데이터베이스를 검색 하는 방법.

1. [Azure Portal](https://portal.azure.com)에서 데이터베이스를 백업하는 데 사용하는 Recovery Services 자격 증명 모음을 엽니다.

2. 에 **Recovery Services 자격 증명 모음** 대시보드에서 **백업**합니다.

   ![Backup을 선택하여 백업 목표 메뉴 열기](./media/backup-azure-sql-database/open-backup-menu.png)

3. **Backup 목표**설정 **워크 로드가 실행 되는?** 하 **Azure**합니다.

4. **백업할 항목**에서 **Azure VM의 SQL Server**를 선택합니다.

    ![백업을 위해 Azure VM에서 SQL Server 선택](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. **백업 목표** > **VM에서 DB 검색** 에서 **검색 시작**을 선택하여 구독에서 보호되지 않은 가상 머신을 검색합니다. 이 검색에는 구독에서 보호 되지 않는 Vm의 수에 따라 시간이 걸릴 수 있습니다.

   - 검색 후에는 보호되지 않은 VM이 이름 및 리소스 그룹별로 나열된 목록에 표시됩니다.
   - VM 예상한 대로 목록에 없으면 여부를 이미 백업 자격 증명 모음에서 참조 하세요.
   - 여러 Vm에 동일한 이름을 가질 수 있습니다 하지만 다른 리소스 그룹에 소속 됩니다.

     ![VM의 DB 검색 중에는 백업이 보류됩니다.](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. VM 목록에서 SQL Server 데이터베이스가 실행되는 VM, **DB 검색**을 차례로 선택합니다.

7. 추적 데이터베이스 검색 **알림을**합니다. 이 작업에 필요한 시간은 VM 데이터베이스 수에 따라 달라 집니다. 선택한 데이터베이스가 검색되면 성공 메시지가 표시됩니다.

    ![배포 성공 메시지](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup은 VM의 모든 SQL Server 데이터베이스를 검색합니다. 검색 중에 다음 요소를 백그라운드에서 발생합니다.

    - Azure Backup 워크 로드 백업에 대 한 자격 증명 모음을 사용 하 여 VM을 등록합니다. 이 자격 증명이 모음에만 등록 된 VM의 모든 데이터베이스를 백업할 수 있습니다.
    - Azure Backup VM에서 AzureBackupWindowsWorkload 확장을 설치합니다. 에이전트가 없습니다. SQL 데이터베이스에 설치 됩니다.
    - Azure Backup은 VM에서 서비스 계정을 NT Service\AzureWLBackupPluginSvc를 만듭니다.
      - 모든 백업 및 복원 작업에는 서비스 계정이 사용됩니다.
      - NT Service\AzureWLBackupPluginSvc SQL sysadmin 권한이 필요합니다. Marketplace에서 만든 모든 SQL Server Vm 설치 SqlIaaSExtension 함께 제공 됩니다. AzureBackupWindowsWorkload 확장 된 SQLIaaSExtension를 사용 하 여 자동으로 필요한 권한을 얻습니다.
    - Marketplace에서 VM을 만들지 않은 경우 VM에 설치 된 SqlIaaSExtension 없습니다 하 고 검색 작업 UserErrorSQLNoSysAdminMembership 오류 메시지와 함께 실패 합니다. 이 문제를 해결 하려면 다음을 수행 합니다 [지침](backup-azure-sql-database.md#fix-sql-sysadmin-permissions)합니다.

        ![VM 및 데이터베이스 선택](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>백업 구성  

1. **Backup 목표** > **2 단계: 백업 구성**을 선택 **백업 구성**합니다.

   ![백업 구성 선택](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. **백업할 항목 선택**를 모든 등록 된 가용성 그룹 및 독립 실행형 SQL Server 인스턴스를 표시 합니다. 해당 인스턴스 또는 Always On 가용성 그룹에서 보호 되지 않은 모든 데이터베이스의 목록을 확장 하는 행의 왼쪽에 있는 화살표를 선택 합니다.  

    ![독립 실행형 데이터베이스를 사용하는 모든 SQL Server 인스턴스 표시](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. 모든 데이터베이스를 보호 하 고 클릭 하려는 선택 **확인**합니다.

   ![데이터베이스 보호](./media/backup-azure-sql-database/select-database-to-protect.png)

   백업 로드를 최적화하기 위해 Azure Backup은 한 백업 작업의 최대 데이터베이스 수를 50개로 설정합니다.

     * 50개가 넘는 데이터베이스를 보호하려면 여러 백업을 구성합니다.
     * 사용할 수 있도록 [ ](#enable-auto-protection) 전체 인스턴스 또는 Always On 가용성 그룹입니다. 에 **AUTOPROTECT** 드롭 다운 목록에서 **ON**를 선택한 후 **확인**합니다.

    > [!NOTE]
    > 합니다 [자동 보호](#enable-auto-protection) 기능 뿐만 아니라 모든 기존 데이터베이스에 대 한 한 번에 보호를 사용 하도록 설정 되지만 자동으로 해당 인스턴스 또는 가용성 그룹에 추가 된 모든 새 데이터베이스를 보호 합니다.  

4. 선택 **확인** 열려는 **Backup 정책**합니다.

    ![Always On 가용성 그룹에 대 한 자동 보호를 사용 하도록 설정](./media/backup-azure-sql-database/enable-auto-protection.png)

5. **Backup 정책**정책을 선택한 다음 선택 **확인**합니다.

   - 기본 정책 HourlyLogBackup로 선택 합니다.
   - 이전에 SQL용으로 만든 기존 백업 정책을 선택합니다.
   - RPO(복구 지점 목표) 및 보존 범위를 기반으로 새 정책을 정의합니다.

     ![백업 정책 선택](./media/backup-azure-sql-database/select-backup-policy.png)

6.  **백업**를 선택 **백업 사용**합니다.

    ![선택한 백업 정책 사용](./media/backup-azure-sql-database/enable-backup-button.png)

7. 포털의  **알림**  영역에서 구성 진행률을 추적합니다.

    ![알림 영역](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>백업 정책 만들기

백업 정책은 백업이 수행되는 시기와 유지되는 기간을 정의합니다.

- 정책은 자격 증명 모음 수준에서 만들어집니다.
- 다수의 자격 증명 모음은 자격 증명 모음은 동일한 백업 정책을 사용할 수 있지만 자격 증명 모음마다 백업 정책을 적용해야 합니다.
- 백업 정책을 만드는 경우 매일 전체 백업이 기본값입니다.
- 차등 백업을 추가할 수 있지만 전체 백업이 매주 발생하도록 구성하는 경우에만 가능합니다.
- 에 대 한 자세한 [다양 한 유형의 백업 정책이](backup-architecture.md#sql-server-backup-types)합니다.

백업 정책을 만들려면:

1. 자격 증명 모음에서 선택 **Backup 정책** > **추가**합니다.
2. **추가**를 선택 **Azure VM에서 SQL Server** 정책 형식을 정의 합니다.

   ![새 백업 정책에 대한 정책 유형 선택](./media/backup-azure-sql-database/policy-type-details.png)

3. **정책 이름**에 새 정책의 이름을 입력합니다.
4. **전체 백업 정책**를 선택 된 **백업 빈도**. 선택할 **일일** 또는 **매주**합니다.

   - **매일**의 경우 백업 작업이 시작될 때 시간과 표준 시간대를 선택합니다.
   - **매주**의 경우 백업 작업이 시작되는 요일, 시간 및 표준 시간대를 선택합니다.
   - 해제할 수 없습니다 때문에 전체 백업을 실행 합니다 **전체 백업** 옵션입니다.
   - 선택 **전체 백업** 정책을 보려면.
   - 매일 전체 백업에 대해서는 차등 백업을 만들 수 없습니다.

     ![새 백업 정책 필드](./media/backup-azure-sql-database/full-backup-policy.png)  

5. **보존 범위**, 기본적으로 모든 옵션을 선택 합니다. 지우기를 및 다음 사용할 간격을 설정 하지 보존 범위를 제한 합니다.

    - 백업 (전체, 차등 및 로그)의 모든 형식에 대 한 최소 보존 기간은 7 일입니다.
    - 복구 지점은 보존 범위를 기반으로 보존 태그가 지정됩니다. 예를 들어, 매일, 전체 백업을 선택하면 매일 하나의 전체 백업만 트리거됩니다.
    - 백업에 대 한 특정 날짜는 태그가 지정 된 보존 매주 보존 범위 및 매주 보존 설정에 기반 합니다.
    - 매월 및 매년 보존 범위는 비슷한 방식으로 작동합니다.

       ![보존 범위 간격 설정](./media/backup-azure-sql-database/retention-range-interval.png)

6. **전체 백업 정책** 메뉴에서 **확인**을 클릭하여 설정을 적용합니다.
7. 차등 백업 정책을 추가하려면 **차등 백업**을 선택합니다.

   ![보존 범위 간격 설정](./media/backup-azure-sql-database/retention-range-interval.png)
   ![차등 백업 정책 메뉴 열기](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. **차등 백업 정책**에서 **사용**을 선택하여 빈도 및 보존 컨트롤을 엽니다.

    - 하나의 차등 백업은 하루를 트리거할 수 있습니다.
    - 차등 백업은 최대 180일 동안 보존될 수 있습니다. 장기 보존을 위해 전체 백업을 사용 합니다.

9. **확인**을 선택하여 정책을 저장하고 주 **백업 정책** 메뉴로 돌아갑니다.

10. 트랜잭션 로그 백업 정책을 추가하려면 **로그 백업**을 선택합니다.
11. **로그 백업**에서 **사용**을 선택한 다음, 빈도 및 보존 컨트롤을 설정합니다. 로그 백업을 15 분 간격으로 자주 발생할 수 있으며 최대 35 일 동안 보존할 수 있습니다.
12. **확인**을 선택하여 정책을 저장하고 주 **백업 정책** 메뉴로 돌아갑니다.

    ![로그 백업 정책 편집](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. **백업 정책** 메뉴에서 **SQL 백업 압축**을 사용할지 여부를 선택합니다.
    - 압축은 기본적으로 사용하지 않도록 설정되어 있습니다.
    - 백 엔드에서, Azure Backup은 SQL 네이티브 백업 압축을 사용합니다.

14. 백업 정책 편집을 완료 한 후, **확인**을 선택합니다.


### <a name="modify-policy"></a>정책 수정
백업 빈도 또는 보존 범위를 변경 하려면 정책을 수정 합니다.

> [!NOTE]
> 보존 기간에 변경 내용을 새로 외에도 모든 이전 복구 지점에 소급 적용 됩니다.

로 자격 증명 모음 대시보드에서 **관리** > **Backup 정책** 편집 하려는 정책을 선택 합니다.

  ![백업 정책 관리](./media/backup-azure-sql-database/modify-backup-policy.png)


## <a name="enable-auto-protection"></a>자동 보호 사용  

자동 보호를 독립 실행형 SQL Server 인스턴스 또는 Always On 가용성 그룹에 자동으로 모든 기존 및 차후의 데이터베이스를 백업할 수 있습니다.

- 데이터베이스 자동 보호를 위해 한 번에 선택할 수 있습니다 수에 제한은 없습니다.
- 선택적으로 보호 하거나 데이터베이스 자동 보호를 사용 하도록 설정 하면 시간에 인스턴스에 대 한 보호에서 제외할 수 없습니다.
- 인스턴스는 이미 보호 된 데이터베이스 일부에 포함 된 경우 자동 보호를 설정한 후에 해당 정책을 아래에 있는 보호 된 계속 됩니다. 나중에 추가 하는 모든 보호 되지 않은 데이터베이스에는 아래에 자동으로 보호를 사용 하도록 설정 시 정의 하는 하나의 정책만 생깁니다 **백업 구성**합니다. 그러나 나중에 자동으로 보호 되는 데이터베이스와 연결 된 정책을 변경할 수 있습니다.  

자동 보호 수 있도록 합니다.

  1. **백업할 항목**에서 자동 보호를 사용하도록 설정하려는 인스턴스를 선택합니다.
  2. 드롭다운 목록에서 선택 **AUTOPROTECT**, 선택 **ON**를 선택한 후 **확인**합니다.

      ![가용성 그룹에 자동 보호를 사용 하도록 설정](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. 백업은 모든 데이터베이스에 대해 함께 구성되며 **백업 작업**에서 추적할 수 있습니다.

자동 보호를 사용 하지 않도록 설정 해야 할 경우 아래에서 인스턴스 이름을 선택 **백업 구성**를 선택한 후 **사용 하지 않도록 설정 Autoprotect** 인스턴스에 대 한 합니다. 모든 데이터베이스는 계속 백업할 수 있지만 이후의 데이터베이스를 자동으로 보호 되지 않습니다.

![해당 인스턴스에서 자동 보호 사용 안 함](./media/backup-azure-sql-database/disable-auto-protection.png)

 
## <a name="next-steps"></a>다음 단계

다음 작업을 수행하는 방법을 배워 보십시오.

- [SQL Server 데이터베이스 백업 복원](restore-sql-database-azure-vm.md)
- [백업 SQL Server 데이터베이스를 관리 합니다.](manage-monitor-sql-database-backup.md)
