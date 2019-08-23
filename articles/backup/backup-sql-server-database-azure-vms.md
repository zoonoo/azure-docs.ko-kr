---
title: Azure Vm에서 SQL Server 데이터베이스 백업 | Microsoft Docs
description: Azure Vm에서 SQL Server 데이터베이스를 백업 하는 방법 알아보기
ms.reviewer: vijayts
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: dacurwin
ms.openlocfilehash: 3c16d8b5f1611c6c05e60d65551f73eb2d395668
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69872901"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Azure VM의 SQL Server 데이터베이스 백업

SQL Server 데이터베이스는 낮은 RPO (복구 지점 목표) 및 장기 보존이 필요한 중요 한 워크 로드입니다. [Azure Backup](backup-overview.md)를 사용 하 여 Azure vm (가상 머신)에서 실행 되는 SQL Server 데이터베이스를 백업할 수 있습니다.

이 문서에서는 Azure VM에서 실행 되는 SQL Server 데이터베이스를 Azure Backup Recovery Services 자격 증명 모음에 백업 하는 방법을 보여 줍니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 자격 증명 모음을 만들고 구성합니다.
> * 데이터베이스를 검색 하 고 백업을 설정 합니다.
> * 데이터베이스에 대한 자동 보호를 설정합니다.


## <a name="prerequisites"></a>전제 조건

SQL Server 데이터베이스를 백업 하기 전에 다음 조건을 확인 합니다.

1. SQL Server 인스턴스를 호스트 하는 VM과 동일한 지역 또는 로캘로 [Recovery Services 자격 증명 모음](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) 을 식별 하거나 만듭니다.
2. VM이 [네트워크에 연결](backup-sql-server-database-azure-vms.md#establish-network-connectivity)되어 있는지 확인 합니다.
3. SQL Server 데이터베이스가 [Azure Backup에 대 한 데이터베이스 명명 지침](#database-naming-guidelines-for-azure-backup)을 따르는지 확인 합니다.
4. 특히 SQL 2008 및 2008 r 2의 경우 서버 등록을 사용 하도록 [레지스트리 키를 추가](#add-registry-key-to-enable-registration) 합니다. 기능이 일반 공급 되는 경우에는이 단계가 필요 하지 않습니다.
5. 데이터베이스에 대해 다른 백업 솔루션을 사용 하도록 설정 하지 않았는지 확인 합니다. 데이터베이스를 백업 하기 전에 다른 모든 SQL Server 백업을 사용 하지 않도록 설정 합니다.

> [!NOTE]
> Azure VM에 대해 Azure Backup를 사용 하도록 설정 하 고 충돌 없이 VM에서 실행 되는 SQL Server 데이터베이스에도 사용 하도록 설정할 수 있습니다.


### <a name="establish-network-connectivity"></a>네트워크 연결 설정

모든 작업에 대해 SQL Server VM는 Azure 공용 IP 주소에 대 한 연결이 필요 합니다. Azure 공용 IP 주소에 연결 하지 않고 VM 작업 (데이터베이스 검색, 백업 구성, 백업 예약, 복구 지점의 복원 등)이 실패 합니다.

다음 옵션 중 하나를 사용 하 여 연결을 설정 합니다.

- **Azure 데이터 센터 IP 범위를 허용**합니다. 이 옵션을 선택 하면 다운로드의 [IP 범위가](https://www.microsoft.com/download/details.aspx?id=41653) 허용 됩니다. NSG (네트워크 보안 그룹)에 액세스 하려면 AzureNetworkSecurityRule cmdlet을 사용 합니다. 안전 받는 사람이 지역 특정 Ip만을 나열 하는 경우 인증을 사용 하도록 설정 하려면 수신 받는 사람 목록에 Azure Active Directory (Azure AD) 서비스 태그를 업데이트 해야 합니다.

- **NSG 태그를 사용 하 여 액세스를 허용**합니다.  NSG를 사용 하 여 연결을 제한 하는 경우 AzureBackup 서비스 태그를 사용 하 여 Azure Backup에 대 한 아웃 바운드 액세스를 허용 해야 합니다. 또한 Azure AD 및 Azure Storage에 대 한 [규칙](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) 을 사용 하 여 인증 및 데이터 전송에 대 한 연결을 허용 해야 합니다. 포털 또는 PowerShell에서이 작업을 수행할 수 있습니다.

    포털을 사용 하 여 규칙을 만들려면:
    
    - **모든 서비스**에서 **네트워크 보안** 그룹으로 이동 하 여 네트워크 보안 그룹을 선택 합니다.
    - **설정**에서 **아웃 바운드 보안 규칙** 을 선택 합니다.
    - **추가**를 선택합니다. [보안 규칙 설정](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings)에 설명 된 대로 새 규칙을 만드는 데 필요한 모든 세부 정보를 입력 합니다. **대상** 옵션을 **서비스 태그로** 설정 하 고 **대상 서비스 태그** 를 **azurebackup**으로 설정 했는지 확인 합니다.
    - **추가**를 클릭 하 여 새로 만든 아웃 바운드 보안 규칙을 저장 합니다.
    
   Powershell을 사용 하 여 규칙을 만들려면

   - Azure 계정 자격 증명을 추가 하 고 국가를 업데이트 합니다.<br/>
    ``Add-AzureRmAccount``
  - NSG 구독 선택<br/>
    ``Select-AzureRmSubscription "<Subscription Id>"``
  - NSG 선택<br/>
    ```$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"```
  - Azure Backup 서비스 태그에 아웃 바운드 규칙 허용 추가<br/>
   ```Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"```
  - NSG 저장<br/>
    ```Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg```

   
- **Azure 방화벽 태그를 사용 하 여 액세스를 허용**합니다. Azure 방화벽을 사용 하는 경우 AzureBackup [FQDN 태그](https://docs.microsoft.com/azure/firewall/fqdn-tags)를 사용 하 여 응용 프로그램 규칙을 만듭니다. 이렇게 하면 Azure Backup에 대 한 아웃 바운드 액세스가 가능 합니다.
- **트래픽을 라우팅하는 HTTP 프록시 서버를 배포**합니다. Azure VM에서 SQL Server 데이터베이스를 백업 하면 VM의 백업 확장이 HTTPS Api를 사용 하 여 Azure Backup 및 데이터에 대 한 관리 명령을 Azure Storage 보냅니다. 또한 백업 확장은 인증에 Azure AD를 사용 합니다. HTTP 프록시를 통해 이 세 가지 서비스에 대한 백업 확장 트래픽을 라우팅합니다. 확장의 공용 인터넷에 액세스하도록 구성된 유일한 구성 요소입니다.

연결 옵션에는 다음과 같은 장점과 단점이 있습니다.

**옵션** | **장점** | **단점**
--- | --- | ---
IP 범위 허용 | 추가 비용 없음 | 시간이 지남에 따라 IP 주소 범위가 변경 되기 때문에 관리가 복잡 합니다. <br/><br/> 뿐만 아니라 Azure 전체에 대 한 액세스를 제공 Azure Storage
NSG 서비스 태그 사용 | 범위 변경이 자동으로 병합 됨에 따라 보다 쉽게 관리할 수 있습니다. <br/><br/> 추가 비용 없음 <br/><br/> | NSGs에만 사용할 수 있습니다. <br/><br/> 전체 서비스에 대 한 액세스를 제공 합니다.
Azure 방화벽 FQDN 태그 사용 | 필요한 Fqdn이 자동으로 관리 되기 때문에 관리 하기가 더 쉬워졌습니다. | Azure 방화벽과 함께 사용할 수 있습니다.
HTTP 프록시 사용 | 저장소 Url에 대 한 프록시를 세부적으로 제어할 수 있습니다. <br/><br/> Vm에 대 한 인터넷 액세스의 단일 지점 <br/><br/> Azure IP 주소 변경이 적용 되지 않음 | 프록시 소프트웨어를 사용 하 여 VM을 실행 하기 위한 추가 비용

### <a name="database-naming-guidelines-for-azure-backup"></a>Azure Backup에 대 한 데이터베이스 명명 지침

데이터베이스 이름에는 다음 요소를 사용 하지 마십시오.

  * 후행 및 선행 공백
  * 후행 느낌표 (!)
  * 닫는 대괄호 (])
  * 세미콜론 '; '
  * 슬래시 '/'

별칭은 지원 되지 않는 문자에 사용할 수 있지만이를 방지 하는 것이 좋습니다. 자세한 내용은 [테이블 서비스 데이터 모델 이해](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN)를 참조하세요.

### <a name="add-registry-key-to-enable-registration"></a>등록을 사용 하도록 설정 하는 레지스트리 키 추가

1. Regedit 열기
2. 레지스트리 디렉터리 경로를 만듭니다. HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WorkloadBackup\TestHook (WorkloadBackup 아래에 ' 키 ' TestHook를 만들어야 하며,이는 Microsoft에서 생성 해야 합니다.)
3. 레지스트리 디렉터리 경로 아래에서 문자열 이름 **AzureBackupEnableWin2K8R2SP1** 및 값을 사용 하 여 새 ' 문자열 값 '을 만듭니다. **True**

    ![등록을 사용 하도록 설정 하는 RegEdit](media/backup-azure-sql-database/reg-edit-sqleos-bkp.png)

또는 다음 명령을 사용 하 여 .reg 파일을 실행 하 여이 단계를 자동화할 수 있습니다.

```csharp
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WorkloadBackup\TestHook]
"AzureBackupEnableWin2K8R2SP1"="True"
```

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server 데이터베이스 검색

VM에서 실행 되는 데이터베이스를 검색 하는 방법:

1. [Azure Portal](https://portal.azure.com)에서 데이터베이스를 백업하는 데 사용하는 Recovery Services 자격 증명 모음을 엽니다.

2. **Recovery Services 자격 증명 모음** 대시보드에서 **백업**을 선택 합니다.

   ![Backup을 선택하여 백업 목표 메뉴 열기](./media/backup-azure-sql-database/open-backup-menu.png)

3. **백업 목표**에서 **워크 로드가 실행 되는 위치** 를 **Azure**로 설정 합니다.

4. **백업할 항목**에서 **Azure VM의 SQL Server**를 선택합니다.

    ![백업을 위해 Azure VM에서 SQL Server 선택](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. **백업 목표** > **VM에서 DB 검색** 에서 **검색 시작**을 선택하여 구독에서 보호되지 않은 가상 머신을 검색합니다. 이 검색은 구독에서 보호 되지 않는 Vm 수에 따라 시간이 오래 걸릴 수 있습니다.

   - 검색 후에는 보호되지 않은 VM이 이름 및 리소스 그룹별로 나열된 목록에 표시됩니다.
   - VM이 원하는 대로 나열 되지 않은 경우 자격 증명 모음에 이미 백업 되어 있는지 여부를 확인 합니다.
   - 여러 Vm은 동일한 이름을 가질 수 있지만 다른 리소스 그룹에 속합니다.

     ![VM의 DB 검색 중에는 백업이 보류됩니다.](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. VM 목록에서 SQL Server 데이터베이스가 실행되는 VM, **DB 검색**을 차례로 선택합니다.

7. **알림에서**데이터베이스 검색을 추적 합니다. 이 작업에 필요한 시간은 VM 데이터베이스의 수에 따라 달라 집니다. 선택한 데이터베이스가 검색되면 성공 메시지가 표시됩니다.

    ![배포 성공 메시지](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup은 VM의 모든 SQL Server 데이터베이스를 검색합니다. 검색 하는 동안 백그라운드에서 다음 요소가 발생 합니다.

    - Azure Backup는 워크 로드 백업용 자격 증명 모음에 VM을 등록 합니다. 등록 된 VM의 모든 데이터베이스는이 자격 증명 모음에만 백업할 수 있습니다.
    - Azure Backup VM에 AzureBackupWindowsWorkload 로드 확장을 설치 합니다. SQL 데이터베이스에 에이전트가 설치 되어 있지 않습니다.
    - Azure Backup VM에 대 한 서비스 계정 NT Service\AzureWLBackupPluginSvc를 만듭니다.
      - 모든 백업 및 복원 작업에는 서비스 계정이 사용됩니다.
      - NT Service\AzureWLBackupPluginSvc에는 SQL sysadmin 권한이 필요 합니다. Marketplace에서 만든 모든 SQL Server Vm은 SqlIaaSExtension가 설치 된 상태로 제공 됩니다. AzureBackupWindowsWorkload 로드 확장은 SQLIaaSExtension를 사용 하 여 필요한 권한을 자동으로 가져옵니다.
    - Marketplace에서 VM을 만들지 않았거나 SQL 2008 및 2008 r 2를 사용 하는 경우 VM에 SqlIaaSExtension이 설치 되어 있지 않을 수 있으며, 오류 메시지 UserErrorSQLNoSysAdminMembership과 함께 검색 작업이 실패 합니다. 이 문제를 해결 하려면 [VM 권한 설정](backup-azure-sql-database.md#set-vm-permissions)의 지침을 따르세요.

        ![VM 및 데이터베이스 선택](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>백업 구성  

1. **백업 목표** > 에서2단계: **백업**구성을 선택 하 고 **백업 구성**을 선택 합니다.

   ![백업 구성 선택](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. **백업할 항목 선택**에 등록 된 모든 가용성 그룹 및 독립 실행형 SQL Server 인스턴스가 표시 됩니다. 행 왼쪽에 있는 화살표를 선택 하 여 해당 인스턴스 또는 Always On 가용성 그룹에 있는 보호 되지 않는 모든 데이터베이스의 목록을 확장 합니다.  

    ![독립 실행형 데이터베이스를 사용하는 모든 SQL Server 인스턴스 표시](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. 보호할 데이터베이스를 모두 선택한 다음 **확인**을 선택 합니다.

   ![데이터베이스 보호](./media/backup-azure-sql-database/select-database-to-protect.png)

   백업 로드를 최적화하기 위해 Azure Backup은 한 백업 작업의 최대 데이터베이스 수를 50개로 설정합니다.

     * 50개가 넘는 데이터베이스를 보호하려면 여러 백업을 구성합니다.
     * 전체 인스턴스 또는 Always On 가용성 그룹을 [사용 하도록 설정](#enable-auto-protection) 하려면 **autoprotect** 드롭다운 목록에서 **켜기**를 선택 하 고 **확인**을 선택 합니다.

    > [!NOTE]
    > [자동 보호](#enable-auto-protection) 기능은 모든 기존 데이터베이스를 한 번에 보호할 뿐만 아니라 해당 인스턴스 또는 가용성 그룹에 추가 된 새 데이터베이스를 자동으로 보호 합니다.  

4. **확인** 을 선택 하 여 **백업 정책을**엽니다.

    ![Always On 가용성 그룹에 대 한 자동 보호 사용](./media/backup-azure-sql-database/enable-auto-protection.png)

5. **백업 정책**에서 정책을 선택 하 고 **확인**을 선택 합니다.

   - 기본 정책을 HourlyLogBackup로 선택 합니다.
   - 이전에 SQL용으로 만든 기존 백업 정책을 선택합니다.
   - RPO(복구 지점 목표) 및 보존 범위를 기반으로 새 정책을 정의합니다.

     ![백업 정책 선택](./media/backup-azure-sql-database/select-backup-policy.png)

6. **백업**에서 **백업 사용**을 선택합니다.

    ![선택한 백업 정책 사용](./media/backup-azure-sql-database/enable-backup-button.png)

7. 포털의 **알림** 영역에서 구성 진행률을 추적합니다.

    ![알림 영역](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>백업 정책 만들기

백업 정책은 백업이 수행되는 시기와 유지되는 기간을 정의합니다.

- 정책은 자격 증명 모음 수준에서 만들어집니다.
- 다수의 자격 증명 모음은 자격 증명 모음은 동일한 백업 정책을 사용할 수 있지만 자격 증명 모음마다 백업 정책을 적용해야 합니다.
- 백업 정책을 만드는 경우 매일 전체 백업이 기본값입니다.
- 차등 백업을 추가할 수 있지만 전체 백업이 매주 발생하도록 구성하는 경우에만 가능합니다.
- [여러 유형의 백업 정책](backup-architecture.md#sql-server-backup-types)에 대해 알아봅니다.

백업 정책을 만들려면:

1. 자격 증명 모음에서 **백업 정책** > **추가**를 선택 합니다.
2. **추가**에서 **Azure VM의 SQL Server** 를 선택 하 여 정책 유형을 정의 합니다.

   ![새 백업 정책에 대한 정책 유형 선택](./media/backup-azure-sql-database/policy-type-details.png)

3. **정책 이름**에 새 정책의 이름을 입력합니다.
4. **전체 백업 정책**에서 **백업 빈도**를 선택 합니다. **매일** 또는 **매주**를 선택 합니다.

   - **매일**의 경우 백업 작업이 시작될 때 시간과 표준 시간대를 선택합니다.
   - **매주**의 경우 백업 작업이 시작되는 요일, 시간 및 표준 시간대를 선택합니다.
   - 전체 백업 옵션을 해제할 수 없기 때문에 전체 백업을 실행 합니다.
   - 정책을 보려면 **전체 백업** 을 선택 합니다.
   - 매일 전체 백업에 대해서는 차등 백업을 만들 수 없습니다.

     ![새 백업 정책 필드](./media/backup-azure-sql-database/full-backup-policy.png)  

5. **보존 범위**에서 모든 옵션이 기본적으로 선택 됩니다. 원하지 않는 보존 범위 제한을 모두 지우고 사용할 간격을 설정 합니다.

    - 모든 백업 유형에 대 한 최소 보존 기간 (전체, 차등 및 로그)은 7 일입니다.
    - 복구 지점은 보존 범위를 기반으로 보존 태그가 지정됩니다. 예를 들어, 매일, 전체 백업을 선택하면 매일 하나의 전체 백업만 트리거됩니다.
    - 주간 보존 범위와 주간 보존 설정에 따라 특정 날짜에 대 한 백업 태그가 지정 되 고 보존 됩니다.
    - 매월 및 매년 보존 범위는 비슷한 방식으로 작동 합니다.

       ![보존 범위 간격 설정](./media/backup-azure-sql-database/retention-range-interval.png)

6. **전체 백업 정책** 메뉴에서 **확인**을 클릭하여 설정을 적용합니다.
7. 차등 백업 정책을 추가하려면 **차등 백업**을 선택합니다.

   ![보존 범위 간격 설정](./media/backup-azure-sql-database/retention-range-interval.png)
   ![차등 백업 정책 메뉴 열기](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. **차등 백업 정책**에서 **사용**을 선택하여 빈도 및 보존 컨트롤을 엽니다.

    - 하루에 하나의 차등 백업만 트리거할 수 있습니다.
    - 차등 백업은 최대 180일 동안 보존될 수 있습니다. 더 긴 보존을 위해 전체 백업을 사용 합니다.

9. **확인**을 선택하여 정책을 저장하고 주 **백업 정책** 메뉴로 돌아갑니다.

10. 트랜잭션 로그 백업 정책을 추가하려면 **로그 백업**을 선택합니다.
11. **로그 백업**에서 **사용**을 선택한 다음, 빈도 및 보존 컨트롤을 설정합니다. 로그 백업은 15 분 마다 자주 발생 하며 최대 35 일 동안 보존 될 수 있습니다.
12. **확인**을 선택하여 정책을 저장하고 주 **백업 정책** 메뉴로 돌아갑니다.

    ![로그 백업 정책 편집](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. **백업 정책** 메뉴에서 **SQL 백업 압축**을 사용할지 여부를 선택합니다.
    - 압축은 기본적으로 사용하지 않도록 설정되어 있습니다.
    - 백 엔드에서, Azure Backup은 SQL 네이티브 백업 압축을 사용합니다.

14. 백업 정책 편집을 완료 한 후, **확인**을 선택합니다.


### <a name="modify-policy"></a>정책 수정
정책을 수정 하 여 백업 빈도 또는 보존 범위를 변경 합니다.

> [!NOTE]
> 보존 기간을 변경 하면 새 복구 지점이 아닌 모든 이전 복구 소급 적용 됩니다.

자격 증명 모음 대시보드에서**백업 정책** **관리** > 로 이동 하 여 편집 하려는 정책을 선택 합니다.

  ![백업 정책 관리](./media/backup-azure-sql-database/modify-backup-policy.png)


## <a name="enable-auto-protection"></a>자동 보호 사용  

자동 보호를 사용 하도록 설정 하 여 기존 및 미래의 모든 데이터베이스를 독립 실행형 SQL Server 인스턴스 또는 Always On 가용성 그룹에 자동으로 백업할 수 있습니다.

- 한 번에 자동 보호를 위해 선택할 수 있는 데이터베이스 수에는 제한이 없습니다.
- 자동 보호를 사용 하도록 설정 하는 경우 인스턴스에서 보호 된 데이터베이스를 선택적으로 보호 하거나 제외할 수 없습니다.
- 인스턴스에 이미 보호 된 데이터베이스가 포함 되어 있는 경우 자동 보호를 설정한 후에도 해당 정책에 따라 보호 된 상태로 유지 됩니다. 나중에 추가 된 모든 보호 되지 않은 데이터베이스에는 자동 보호를 사용 하도록 설정할 때 정의한 **백업 구성**에 나열 된 단일 정책만 있습니다. 그러나 나중에 자동으로 보호 되는 데이터베이스와 연결 된 정책을 변경할 수 있습니다.  

자동 보호를 사용 하도록 설정 하려면:

  1. **백업할 항목**에서 자동 보호를 사용하도록 설정하려는 인스턴스를 선택합니다.
  2. **Autoprotect**에서 드롭다운 목록을 선택 하 고 **설정**을 선택한 다음 **확인**을 선택 합니다.

      ![가용성 그룹에서 자동 보호 사용](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. 백업은 모든 데이터베이스에 대해 함께 구성되며 **백업 작업**에서 추적할 수 있습니다.

자동 보호를 사용 하지 않도록 설정 해야 하는 경우 **백업 구성**에서 인스턴스 이름을 선택 하 고 인스턴스에 대해 **Autoprotect 사용 안 함** 을 선택 합니다. 모든 데이터베이스는 계속 백업 되지만 이후 데이터베이스는 자동으로 보호 되지 않습니다.

![해당 인스턴스에서 자동 보호 사용 안 함](./media/backup-azure-sql-database/disable-auto-protection.png)

 
## <a name="next-steps"></a>다음 단계

다음 작업을 수행하는 방법을 배워 보십시오.

- [백업 SQL Server 데이터베이스 복원](restore-sql-database-azure-vm.md)
- [백업 SQL Server 데이터베이스 관리](manage-monitor-sql-database-backup.md)
