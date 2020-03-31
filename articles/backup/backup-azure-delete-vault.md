---
title: 마이크로소프트 Azure 복구 서비스 자격 증명 모음 삭제
description: 이 문서에서는 종속성을 제거한 다음 Microsoft Azure 백업 복구 서비스(MARS) 자격 증명 모음을 삭제하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: f33f52048729b50015ba86db71118b9a21e1a2fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500397"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Azure 백업 복구 서비스 자격 증명 모음 삭제

이 문서에서는 Microsoft Azure [백업](backup-overview.md) 복구 서비스(MARS) 자격 증명 모음을 삭제하는 방법에 대해 설명합니다. 종속성을 제거한 다음 볼트를 삭제하는 방법에 대한 지침이 포함되어 있습니다.

## <a name="before-you-start"></a>시작하기 전에

보호된 서버 또는 백업 관리 서버와 같은 종속성이 있는 복구 서비스 자격 증명 모음은 삭제할 수 없습니다.

- 보호가 중지되었지만 백업 데이터를 유지한 경우에도 백업 데이터가 포함된 볼트는 삭제할 수 없습니다.

- 종속성이 포함된 볼트를 삭제하면 다음 메시지가 나타납니다.

  ![볼트 오류를 삭제합니다.](./media/backup-azure-delete-vault/error.png)

- 종속성이 포함된 포털에서 온-프레미스로 보호된 항목을 삭제하면 다음과 같은 경고 메시지가 나타납니다.

  ![보호된 서버 오류를 삭제합니다.](./media/backup-azure-delete-vault/error-message.jpg)

- 백업 항목이 경고 메시지 아래에 일시 삭제 된 상태로 있으면 영구적으로 삭제 될 때까지 기다려야합니다. 자세한 내용은 이 [문서를](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud)참조하십시오.

   ![볼트 오류를 삭제합니다.](./media/backup-azure-delete-vault/error-message-soft-delete.png)

- 저장소 계정을 등록한 볼트는 삭제할 수 없습니다. 계정 등록을 취소하는 방법을 알아보려면 [저장소 계정 등록 취소를](manage-afs-backup.md#unregister-a-storage-account)참조하십시오.
  
볼트를 삭제하려면 설정과 일치하는 시나리오를 선택하고 권장 단계를 따릅니다.

시나리오 | 자격 증명 모음을 삭제하는 종속성을 제거하는 단계 |
-- | --
Azure Backup 에이전트를 사용하여 Azure백업을 사용하여 보호되는 온-프레미스 파일 및 폴더가 있습니다. | MARS 관리 [콘솔에서 백업 항목 삭제 단계 수행](#delete-backup-items-from-the-mars-management-console)
MABS(Microsoft Azure 백업 서버) 또는 DPM(시스템 센터 데이터 보호 관리자)을 사용하여 Azure에 보호되는 온-프레미스 컴퓨터가 있습니다. | [MABS 관리 콘솔에서 백업 항목 삭제 단계 수행](#delete-backup-items-from-the-mabs-management-console)
클라우드에서 보호된 항목(예: laaS 가상 시스템 또는 Azure Files 공유)이 있습니다.  | [클라우드에서 보호된 항목 삭제 단계 수행](#delete-protected-items-in-the-cloud)
온프레미스와 클라우드모두에서 항목을 보호했습니다. | 다음 섹션의 모든 단계를 다음 순서로 수행합니다. <br> 1. [클라우드에서 보호된 항목 삭제](#delete-protected-items-in-the-cloud)<br> 2. [MARS 관리 콘솔에서 백업 항목 삭제](#delete-backup-items-from-the-mars-management-console) <br> 3. [MABS 관리 콘솔에서 백업 항목 삭제](#delete-backup-items-from-the-mabs-management-console)
온-프레미스 또는 클라우드에서 보호된 항목이 없습니다. 그러나 Vault 삭제 오류가 계속 발생합니다. | Azure 리소스 [관리자를 사용하여 복구 서비스 자격 증명 모음 삭제 단계 수행](#delete-the-recovery-services-vault-by-using-azure-resource-manager) <br><br> 자격 증명 모음에 등록된 저장소 계정이 없는지 확인합니다. 계정 등록을 취소하는 방법을 알아보려면 [저장소 계정 등록 취소를](manage-afs-backup.md#unregister-a-storage-account)참조하십시오.

## <a name="delete-protected-items-in-the-cloud"></a>클라우드에서 보호된 항목 삭제

먼저 시작 **[전](#before-you-start)** 섹션을 읽고 종속성 및 볼트 삭제 프로세스를 이해합니다.

보호를 중지하고 백업 데이터를 삭제하려면 다음 단계를 수행하십시오.

1. 포털에서 **복구 서비스 자격 증명 모음으로**이동한 다음 백업 **항목으로**이동합니다. 그런 다음 클라우드에서 보호된 항목(예: Azure 가상 컴퓨터, Azure 저장소 [Azure Files 서비스]) 또는 Azure 가상 컴퓨터의 SQL 서버를 선택합니다.

    ![백업 유형을 선택합니다.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. 마우스 오른쪽 단추를 클릭하여 백업 항목을 선택합니다. 백업 항목이 보호되는지 여부에 따라 메뉴에 **백업 중지** 창 또는 백업 **데이터 삭제** 창이 표시됩니다.

    - 백업 **중지** 창이 나타나면 드롭다운 메뉴에서 **백업 데이터 삭제를** 선택합니다. 백업 항목의 이름을 입력한 다음(이 필드는 대/소문자를 구분함) 드롭다운 메뉴에서 이유를 선택합니다. 당신이 있는 경우, 귀하의 의견을 입력합니다. 그런 다음 **백업 중지를**선택합니다.

        ![백업 중지 창입니다.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - 백업 **데이터 삭제** 창이 나타나면 백업 항목의 이름을 입력한 다음(이 필드는 대/소문자를 구분함) 드롭다운 메뉴에서 이유를 선택합니다. 당신이 있는 경우, 귀하의 의견을 입력합니다. 그런 다음 **을 삭제를 선택합니다.**

         ![백업 데이터 삭제 창입니다.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

3. **알림** 아이콘: ![알림 아이콘을 확인합니다.](./media/backup-azure-delete-vault/messages.png) 프로세스가 완료되면 서비스에는 "백업*항목"* 에 *대한 백업 중지 및 백업 데이터 삭제라는*메시지가 표시됩니다. *작업을 성공적으로 완료했습니다.*
4. **백업 항목** 메뉴에서 **새로 고침을** 선택하여 백업 항목이 삭제되었는지 확인합니다.

      ![백업 항목 삭제 페이지입니다.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>온프레미스에서 보호된 항목 삭제

먼저 시작 **[전](#before-you-start)** 섹션을 읽고 종속성 및 볼트 삭제 프로세스를 이해합니다.

1. 볼트 대시보드 메뉴에서 **백업 인프라를**선택합니다.
2. 온-프레미스 시나리오에 따라 다음 옵션 중 하나를 선택합니다.

      - MARS의 경우 보호된 서버를 선택한 다음 **Azure 백업 에이전트를** **선택합니다.** 그런 다음 삭제할 서버를 선택합니다.

        ![MARS의 경우 볼트를 선택하여 대시보드를 엽니다.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - MABS 또는 DPM의 경우 **백업 관리 서버를 선택합니다.** 그런 다음 삭제할 서버를 선택합니다.

          ![MABS의 경우 볼트를 선택하여 대시보드를 엽니다.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. **삭제** 창이 경고 메시지와 함께 나타납니다.

     ![삭제 창입니다.](./media/backup-azure-delete-vault/delete-protected-server.png)

     동의 확인란의 경고 메시지와 지침을 검토합니다.
    > [!NOTE]
    >- 보호된 서버가 Azure 서비스와 동기화되고 백업 항목이 있는 경우 동의 확인란에 종속 백업 항목 수와 백업 항목을 볼 수 있는 링크가 표시됩니다.
    >- 보호된 서버가 Azure 서비스와 동기화되지 않고 백업 항목이 있는 경우 동의 확인란에 백업 항목 수만 표시됩니다.
    >- 백업 항목이 없는 경우 동의 확인란에서 삭제를 요청합니다.

4. 동의 확인란을 선택한 다음 **삭제를**선택합니다.

5. **알림** 아이콘이 ![백업](./media/backup-azure-delete-vault/messages.png)데이터를 삭제하는지 확인합니다. 작업이 완료되면 서비스에 *"백업 항목"에 대한 백업 중지 및 백업 데이터 삭제라는* 메시지가 표시됩니다. *작업을 성공적으로 완료했습니다.*
6. **백업 항목** 이 삭제되었는지 확인하려면 백업 항목 메뉴에서 새로 **고침을** 선택합니다.

이 프로세스가 완료되면 관리 콘솔에서 백업 항목을 삭제할 수 있습니다.

- [MARS 관리 콘솔에서 백업 항목 삭제](#delete-backup-items-from-the-mars-management-console)
- [MABS 관리 콘솔에서 백업 항목 삭제](#delete-backup-items-from-the-mabs-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>MARS 관리 콘솔에서 백업 항목 삭제

1. MARS 관리 콘솔을 열고 **작업** 창으로 이동한 다음 **백업 예약을**선택합니다.
2. **예약된 백업 수정 또는 중지** 페이지에서 이 백업 일정 사용 중지를 선택하고 저장된 모든 **백업을 삭제합니다.** 그런 다음 **을 선택합니다.**

    ![예약된 백업을 수정하거나 중지합니다.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. 예약된 백업 중지 페이지에서 **완료를** **선택합니다.**

    ![예약된 백업을 중지합니다.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. 수동으로 생성해야 하는 보안 PIN(개인 식별 번호)을 입력하라는 메시지가 표시됩니다. 이렇게 하려면 먼저 Azure 포털에 로그인합니다.
5. 복구 **서비스 볼트** > **설정** > **속성으로**이동합니다.
6. **보안 PIN에서** **생성을**선택합니다. 이 PIN을 복사합니다. PIN은 5분 동안만 유효합니다.
7. 관리 콘솔에서 PIN을 붙여넣은 다음 **확인을**선택합니다.

    ![보안 PIN을 생성합니다.](./media/backup-azure-delete-vault/security-pin.png)

8. 백업 **진행률 수정** 페이지에는 *삭제된 백업 데이터가 14일 동안 보존된다는 메시지가 나타납니다. 이 시간이 지나면 백업 데이터가 영구적으로 삭제됩니다.*  

    ![백업 인프라를 삭제합니다.](./media/backup-azure-delete-vault/deleted-backup-data.png)

온-프레미스 백업 항목을 삭제한 후 포털의 다음 단계를 따릅니다.

### <a name="delete-backup-items-from-the-mabs-management-console"></a>MABS 관리 콘솔에서 백업 항목 삭제

MABS 관리 콘솔에서 백업 항목을 삭제하는 데 사용할 수 있는 방법에는 두 가지가 있습니다.

#### <a name="method-1"></a>방법 1

보호를 중지하고 백업 데이터를 삭제하려면 다음 단계를 수행합니다.

1. DPM 관리자 콘솔을 연 다음 탐색 모음에서 **보호를** 선택합니다.
2. 디스플레이 창에서 제거할 보호 그룹 구성원을 선택합니다. 마우스 오른쪽 단추를 클릭하여 **그룹 구성원 보호 중지** 옵션을 선택합니다.
3. 보호 **중지** 대화 상자에서 **보호된 데이터 삭제를**선택한 다음 **온라인 저장소 삭제** 확인란을 선택합니다. 그런 다음 **보호 중지를**선택합니다.

    ![보호 중지 창에서 보호된 데이터 삭제를 선택합니다.](./media/backup-azure-delete-vault/delete-storage-online.png)

    보호된 멤버 상태가 *사용 가능한 비활성 복제본으로 변경됩니다.*

4. 비활성 보호 그룹을 마우스 오른쪽 단추로 클릭하고 **비활성 보호 제거를**선택합니다.

    ![비활성 보호를 제거합니다.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. **비활성 보호 삭제** 창에서 **온라인 저장소 삭제** 확인란을 선택한 다음 **확인을**선택합니다.

    ![온라인 저장소를 삭제합니다.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>방법 2

MABS 관리 콘솔을 **엽니다.** **데이터 보호 방법 선택에서**온라인 보호 확인란을 **선택취소합니다.**

  ![데이터 보호 방법을 선택합니다.](./media/backup-azure-delete-vault/data-protection-method.png)

온-프레미스 백업 항목을 삭제한 후 포털의 다음 단계를 따릅니다.

## <a name="delete-the-recovery-services-vault"></a>Recovery Services 자격 증명 모음 삭제

1. 모든 종속성이 제거되면 볼트 메뉴의 Essentials 창으로 **스크롤합니다.**
2. 백업 항목, 백업 관리 서버 또는 복제된 항목이 나열되어 있지 않은지 확인합니다. 항목이 여전히 볼트에 나타나는 경우 [시작하기 전](#before-you-start) 섹션을 참조하십시오.

3. 볼트에 항목이 더 이상 없는 경우 볼트 대시보드에서 **삭제를** 선택합니다.

    ![볼트 대시보드에서 삭제를 선택합니다.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. 자격 증명 모음을 삭제하려는지 확인하려면 **예를** 선택합니다. 볼트가 삭제됩니다. 포털이 **새** 서비스 메뉴로 돌아갑니다.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>PowerShell을 사용하여 복구 서비스 자격 증명 모음 삭제

먼저 시작 **[전](#before-you-start)** 섹션을 읽고 종속성 및 볼트 삭제 프로세스를 이해합니다.

보호를 중지하고 백업 데이터를 삭제하려면 다음을 수행하십시오.

- Azure VM 백업에서 SQL을 사용하고 SQL 인스턴스에 대해 자동 보호를 사용하도록 설정한 경우 먼저 자동 보호를 사용하지 않도록 설정합니다.

    ```PowerShell
        Disable-AzRecoveryServicesBackupAutoProtection
           [-InputItem] <ProtectableItemBase>
           [-BackupManagementType] <BackupManagementType>
           [-WorkloadType] <WorkloadType>
           [-PassThru]
           [-VaultId <String>]
           [-DefaultProfile <IAzureContextContainer>]
           [-WhatIf]
           [-Confirm]
           [<CommonParameters>]
    ```

  Azure 백업 보호 항목에 대한 보호를 사용하지 않도록 설정하는 방법에 대해 [자세히 알아봅니다.](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection?view=azps-2.6.0)

- 보호를 중지하고 클라우드의 모든 백업 보호 항목에 대한 데이터를 삭제합니다(예: laaS VM, Azure 파일 공유 등:

    ```PowerShell
       Disable-AzRecoveryServicesBackupProtection
       [-Item] <ItemBase>
       [-RemoveRecoveryPoints]
       [-Force]
       [-VaultId <String>]
       [-DefaultProfile <IAzureContextContainer>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
    ```

    [Learn more](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-2.6.0&viewFallbackFrom=azps-2.5.0) 백업 보호 항목에 대한 사용 안 함 보호에 대해 자세히 알아보세요.

- Azure 백업 에이전트(MARS)를 사용하여 보호되는 온-프레미스 파일 및 폴더의 경우 다음 PowerShell 명령을 사용하여 각 MARS PowerShell 모듈에서 백업된 데이터를 삭제합니다.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    다음 프롬프트가 표시되는 곳에 게시합니다.

    *Microsoft Azure 백업 이 백업 정책을 제거하시겠습니까? 삭제된 백업 데이터는 14일 동안 유지됩니다. 이 시간이 지나면 백업 데이터가 영구적으로 삭제됩니다. <br/> [Y] 예 [A] 예 모든 [N] 아니오 [L] 아니오 모든 [S] 일시 중단 [?] 도움말(기본값은 "Y"):*

- MABS(Microsoft Azure 백업 서버) 또는 DPM에서 Azure(시스템 센터 데이터 보호 관리자)로 보호되는 온-프레미스 시스템의 경우 다음 명령을 사용하여 Azure에서 백업된 데이터를 삭제합니다.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    다음 프롬프트가 표시되는 곳에 게시합니다.

   *마이크로소프트 Azure 백업* 이 백업 정책을 제거하시겠습니까? 삭제된 백업 데이터는 14일 동안 유지됩니다. 이 시간이 지나면 백업 데이터가 영구적으로 삭제됩니다. <br/>
   [Y] 예 [A] 예 모든 [N] 아니오 [L] 아니오 모든 [S] 일시 중단 [?] 도움말(기본값은 "Y"):*

백업된 데이터를 삭제한 후 온-프레미스 컨테이너 및 관리 서버의 등록을 취소합니다.

- Azure에 백업하는 MARS(Azure 백업 에이전트)를 사용하여 보호된 온-프레미스 파일 및 폴더의 경우:

    ```PowerShell
    Unregister-AzRecoveryServicesBackupContainer
              [-Container] <ContainerBase>
              [-PassThru]
              [-VaultId <String>]
              [-DefaultProfile <IAzureContextContainer>]
              [-WhatIf]
              [-Confirm]
              [<CommonParameters>]
    ```

    자격 증명 모음에서 Windows Server 또는 기타 컨테이너 등록 취소에 대해 [자세히 알아봅니다.](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0)

- MABS(Microsoft Azure 백업 서버) 또는 DPM에서 Azure로 보호되는 온-프레미스 시스템의 경우(시스템 센터 데이터 보호 관리:

    ```PowerShell
        Unregister-AzRecoveryServicesBackupManagementServer
          [-AzureRmBackupManagementServer] <BackupEngineBase>
          [-PassThru]
          [-VaultId <String>]
          [-DefaultProfile <IAzureContextContainer>]
          [-WhatIf]
          [-Confirm]
          [<CommonParameters>]
    ```

    볼트에서 백업 관리 컨테이너 등록 취소에 대해 [자세히 알아보세요.](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0)

백업된 데이터를 영구적으로 삭제하고 모든 컨테이너를 등록 취소한 후 자격 증명 모음을 삭제합니다.

Recovery Services 자격 증명 모음 삭제하려면

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

복구 서비스 자격 증명 모음 삭제에 대해 [자세히 알아보세요.](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault)

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>CLI를 사용하여 복구 서비스 자격 증명 모음 삭제

먼저 시작 **[전](#before-you-start)** 섹션을 읽고 종속성 및 볼트 삭제 프로세스를 이해합니다.

> [!NOTE]
> 현재 Azure Backup CLI는 Azure VM 백업만 관리를 지원하므로 볼트를 삭제하는 다음 명령은 볼트에 Azure VM 백업이 포함된 경우에만 작동합니다. 볼트에 Azure VM 이외의 유형의 백업 항목이 포함되어 있는 경우 Azure 백업 CLI를 사용하여 볼트를 삭제할 수 없습니다.

기존 복구 서비스 자격 증명 모음을 삭제하려면 다음을 수행하십시오.

- 보호를 중지하고 백업 데이터를 삭제하려면

    ```azurecli
    az backup protection disable --container-name
                             --item-name
                             [--delete-backup-data {false, true}]
                             [--ids]
                             [--resource-group]
                             [--subscription]
                             [--vault-name]
                             [--yes]
    ```

    자세한 내용은 이 [문서를](/cli/azure/backup/protection#az-backup-protection-disable)참조하십시오.

- 기존 복구 서비스 자격 증명 모음 삭제:

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    자세한 내용은 이 [문서를](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest) 참조하세요.

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Azure 리소스 관리자를 사용하여 복구 서비스 자격 증명 모음 삭제

복구 서비스 자격 증명 모음을 삭제하는 이 옵션은 모든 종속성이 제거되고 *Vault 삭제 오류가*계속 발생하더라도 권장됩니다. 다음 팁 중 일부를 시도하십시오.

- 볼트 메뉴의 **Essentials** 창에서 백업 항목, 백업 관리 서버 또는 복제된 항목이 나열되어 있지 않은지 확인합니다. 백업 항목이 있는 경우 [시작하기 전](#before-you-start) 섹션을 참조하십시오.
- [포털에서 볼트를](#delete-the-recovery-services-vault) 다시 삭제해 봅을 다시 시도합니다.
- 모든 종속성이 제거되고 *여전히 Vault 삭제 오류가*발생하면 ARMClient 도구를 사용하여 다음 단계(메모 후)를 수행합니다.

1. [chocolatey.org](https://chocolatey.org/) 가서 초콜릿을 다운로드하고 설치하십시오. 그런 다음 다음 명령을 실행하여 ARMClient를 설치합니다.

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Azure 계정에 로그인한 다음 다음 명령을 실행합니다.

    `ARMClient.exe login [environment name]`

3. Azure 포털에서 삭제하려는 자격 증명 모음에 대한 구독 ID 및 리소스 그룹 이름을 수집합니다.

ARMClient 명령에 대한 자세한 내용은 [ARMClient README](https://github.com/projectkudu/ARMClient/blob/master/README.md)을 참조하십시오.

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Azure 리소스 관리자 클라이언트를 사용하여 복구 서비스 자격 증명 모음을 삭제합니다.

1. 구독 ID, 리소스 그룹 이름 및 자격 증명 모음 이름을 사용하여 다음 명령을 실행합니다. 종속성이 없는 경우 다음 명령을 실행하면 볼트가 삭제됩니다.

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```

2. 볼트가 비어 있지 않으면 이 자격 증명 모음 내에 기존 리소스가 있으므로 *볼트를 삭제할 수 없다는* 오류 메시지가 표시됩니다. 볼트 내에서 보호된 항목 또는 컨테이너를 제거하려면 다음 명령을 실행합니다.

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Azure 포털에서 볼트가 삭제되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

[복구 서비스 자격 증명 모음에 대해 자세히 알아보기](backup-azure-recovery-services-vault-overview.md)<br/>
[복구 서비스 볼트 모니터링 및 관리에 대해 자세히 알아보기](backup-azure-manage-windows-server.md)
