---
title: Azure에서 Recovery Services 자격 증명 모음 삭제
description: 이 문서는 Recovery Services 자격 증명 모음을 삭제하는 방법을 설명합니다. 문서에는 자격 증명 모음을 삭제하려고 하나 할 수 없는 경우의 문제 해결 단계가 포함되어 있습니다.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 7/6/2018
ms.author: markgal
ms.openlocfilehash: 5a3a67a41525d30b73bb203eeeacbdf49bb35193
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901853"
---
# <a name="delete-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 삭제

이 문서에서는 Recovery Services 자격 증명 모음에서 모든 항목을 제거한 다음, 삭제하는 방법을 설명합니다. 서버에 등록하고 백업 데이터를 보유하는 경우는 Recovery Services 자격 증명 모음을 삭제할 수 없습니다. 자격 증명 모음을 삭제하려고 해도 할 수 없는 경우 자격 증명 모음이 여전히 백업 데이터를 받도록 구성되어 있습니다.

자격 증명 모음을 삭제하는 방법을 알아보려면 [Azure Portal에서 자격 증명 모음 삭제](backup-azure-delete-vault.md#delete-a-vault-from-azure-portal) 섹션을 참조합니다. Recovery Services 자격 증명 모음에 있는 모든 데이터를 보존하지 않고 자격 증명 모음을 삭제하려는 경우 [강제로 자격 증명 모음 삭제](backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-force) 섹션을 참조합니다. 자격 증명 모음의 내용이 확실하지 않고 자격 증명 모음을 삭제할 수 있는지 확인해야 하는 경우 [자격 증명 모음 종속성 제거 및 자격 증명 모음 삭제](backup-azure-delete-vault.md#remove-vault-dependencies-and-delete-vault) 섹션을 참조합니다.

## <a name="delete-a-vault-from-azure-portal"></a>Azure Portal에서 자격 증명 모음 삭제

Recovery Services 자격 증명 모음이 이미 열려 있는 경우 두 번째 단계를 건너뜁니다.

1. Azure Portal을 열고 대시보드에서 삭제할 자격 증명 모음을 엽니다.

   허브 메뉴에서 대시보드에 고정된 Recovery Services 자격 증명 모음이 없는 경우 **모든 서비스**를 클릭하고 리소스 목록에 **Recovery Services**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. 구독에서 자격 증명 모음 목록을 보려면 **Recovery Services 자격 증명 모음**을 클릭합니다.

   ![Recovery Services 자격 증명 모음 만들기 1단계](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   Recovery Services 자격 증명 모음의 목록이 표시됩니다. 

   ![목록에서 자격 증명 모음 선택](./media/backup-azure-delete-vault/choose-vault-to-delete-.png)

2. 목록에서 삭제할 자격 증명 모음을 선택합니다. 자격 증명 모음을 선택하면 해당 자격 증명 모음 대시보드가 열립니다.

    ![대시보드를 열려면 자격 증명 모음 선택](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

3. 자격 증명 모음을 삭제하려면 자격 증명 모음 대시보드에서 **삭제**를 클릭합니다. 자격 증명 모음을 삭제할 것인지 확인하는 메시지가 나타납니다.

    ![대시보드를 열려면 자격 증명 모음 선택](./media/backup-azure-delete-vault/click-delete-button-to-delete-vault.png)

    **자격 증명 모음 삭제 오류**가 표시되는 경우 자격 증명 모음에서 종속성을 제거하거나 PowerShell을 사용하여 강제로 자격 증명 모음을 삭제할 수 있습니다. 다음 섹션에서는 이러한 작업을 수행하는 방법에 대해 설명합니다.

    ![자격 증명 모음 삭제 오류](./media/backup-azure-delete-vault/vault-delete-error.png)


## <a name="delete-the-recovery-services-vault-by-force"></a>Recovery Services 자격 증명 모음 강제 삭제

PowerShell을 사용하여 Recovery Services 자격 증명 모음을 강제로 삭제할 수 있습니다. 강제란 Recovery Services 자격 증명 모음 및 모든 관련 백업 데이터를 영구적으로 삭제한다는 의미입니다. 

> [!Warning]
> PowerShell을 사용하여 Recovery Services 자격 증명 모음을 삭제하는 경우 자격 증명 모음에서 모든 백업 데이터를 영구적으로 삭제하려는지 확인합니다.
>

Recovery Services 자격 증명 모음 삭제하려면

1. Azure 계정에 로그인합니다.

   `Connect-AzureRmAccount` 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

   ```powershell
    Connect-AzureRmAccount
   ```
   Azure Backup을 처음 사용할 때에는 [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider)를 사용하여 구독에서 Azure Recovery Services 공급자를 등록해야 합니다.

   ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
   ```

2. 관리자 권한으로 PowerShell 창을 엽니다.

3. `Set-ExecutionPolicy Unrestricted`를 사용하여 모든 제한을 제거합니다.

4. chocolately.org에서 Azure Resource Manager 클라이언트 패키지를 다운로드하려면 다음 명령을 실행합니다.

    `iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

5. Azure Resource Manager API 클라이언트를 설치하려면 다음 명령을 사용합니다.

   `choco.exe install armclient`

6. Azure Portal에서 삭제하려는 Recovery Services 자격 증명 모음에 대한 구독 ID 및 연결된 리소스 그룹 이름을 수집합니다.

7. PowerShell에서 구독 ID, 리소스 그룹 이름 및 Recovery Services 자격 증명 모음 이름을 사용하여 다음 명령을 실행합니다. 명령을 실행 하면 자격 증명 모음 및 모든 종속성을 삭제합니다.

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
8. Azure Portal에서 구독에 로그인하고 자격 증명 모음이 삭제됐는지 확인합니다.


## <a name="remove-vault-dependencies-and-delete-vault"></a>자격 증명 모음 종속성 제거 및 자격 증명 모음 삭제

자격 증명 모음 종속성을 수동으로 제거하려면 각 항목이나 서버 및 Recovery Services 자격 증명 모음 사이의 구성을 삭제합니다. 다음 절차를 진행할 때 다음에 대한 **Backup 항목** 메뉴(이미지 참조)를 사용합니다.

* Azure Storage(Azure 파일) 백업
* Azure VM 백업의 SQL Server
* Azure 가상 머신 백업
* Microsoft Azure Recovery Services 에이전트 백업

다음에 대한 **Backup 인프라** 메뉴(이미지 참조)를 사용합니다.

* Azure Backup Server 백업
* System Center DPM 백업

    ![대시보드를 열려면 자격 증명 모음 선택](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)

1. 자격 증명 모음 대시보드 메뉴에서 보호된 항목 섹션으로 스크롤하여 **Backup 항목**을 클릭합니다. 이 메뉴에서 Azure File Servers, Azure VM의 SQL Servers 및 Azure 가상 머신을 중지하고 삭제할 수 있습니다. 이 예제에서는 Azure File Server에서 백업 데이터를 제거하겠습니다.

    ![대시보드를 열려면 자격 증명 모음 선택](./media/backup-azure-delete-vault/selected-backup-items.png)

2. 해당 형식의 모든 항목을 보려면 백업 유형을 선택합니다.

    ![백업 유형 선택](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

3. 목록의 모든 항목의 경우 해당 항목을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **백업 중지**를 선택합니다.

    ![백업 유형 선택](./media/backup-azure-delete-vault/stop-backup-item.png) 

    백업 중지 메뉴가 열립니다.

4. **Backup 중지** 메뉴의 **옵션 선택** 메뉴에서 **Backup 데이터 삭제**를 선택하고 항목의 이름을 입력하고 **백업 중지**를 클릭합니다.

    항목 이름을 입력하여 항목을 삭제할지 확인합니다. 항목을 확인하면 **Backup 중지** 단추가 활성화됩니다. 데이터를 보존하는 경우 자격 증명 모음은 삭제할 수 없습니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    원하는 경우 데이터를 삭제하는 이유를 제공하고 메모를 추가합니다. 작업이 완료되었는지 확인하려면 Azure 메시지 ![delete backup data](./media/backup-azure-delete-vault/messages.png)를 입력합니다. <br/>
    작업이 완료되면 서비스에서는 *백업 프로세스가 중지되고 백업 데이터가 삭제되었다*는 메시지를 전송합니다.

5. 목록에서 항목을 삭제한 후 **Backup 항목** 메뉴에서 **새로 고침**을 클릭하면 자격 증명 모음의 항목이 표시됩니다.

      ![백업 데이터 삭제](./media/backup-azure-delete-vault/empty-items-list.png)

      목록에 항목이 없는 경우 Recovery Services 자격 증명 모음 메뉴에서 **필수** 창을 스크롤합니다. 어떤 **Backup 항목**, **Backup 관리 서버** 또는 **복제된 항목**도 나열되지 않아야 합니다. 자격 증명 모음에 항목이 아직 나타날 경우 3단계로 돌아가서 다른 항목 유형 목록을 선택합니다.  

6. 자격 증명 모음 도구 모음에 더 이상 항목이 없을 때 **삭제**를 클릭합니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

7. 자격 증명 모음을 삭제할 것인지 확인하려면 **예**를 클릭합니다.

    자격 증명 모음이 삭제되고 포털이 **새로 만들기** 서비스 메뉴로 돌아갑니다.

## <a name="removing-azure-backup-server-or-dpm"></a>Azure Backup Server 또는 DPM 제거

1. 자격 증명 모음 대시보드 메뉴에서 관리 섹션으로 스크롤하여 **Backup 인프라**를 클릭합니다. 

2. Azure Backup Server 및 System Center DPM 서버를 보려면 하위 메뉴에서 **Backup 관리 서버**를 클릭합니다. Azure File Servers, Azure VM의 SQL Servers 및 Azure 가상 머신을 중지하고 삭제할 수 있습니다. 

    ![대시보드를 열려면 자격 증명 모음 선택](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. 삭제하려는 항목을 마우스 오른쪽 단추로 클릭하고 하위 메뉴에서 **삭제**를 선택합니다.

    ![백업 유형 선택](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

    백업 중지 메뉴가 열립니다.

4. **Backup 중지** 메뉴의 **옵션 선택** 메뉴에서 **Backup 데이터 삭제**를 선택하고 항목의 이름을 입력하고 **백업 중지**를 클릭합니다.

    삭제하려는지 확인하려면 해당 이름을 입력합니다. 항목을 확인하면 **Backup 중지** 단추가 활성화됩니다. 데이터를 보존하는 경우 자격 증명 모음은 삭제할 수 없습니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    필요에 따라 데이터를 삭제하는 이유를 제공하고 메모를 추가할 수 있습니다. 작업이 완료되었는지 확인하려면 Azure 메시지 ![delete backup data](./media/backup-azure-delete-vault/messages.png)를 입력합니다. <br/>
    작업이 완료되면 서비스에서는 백업 프로세스가 중지되고 백업 데이터가 삭제되었다는 메시지를 전송합니다.

5. 목록에서 항목을 삭제한 후 **Backup 항목** 메뉴에서 **새로 고침**을 클릭하면 자격 증명 모음의 나머지 항목이 표시됩니다.

      ![백업 데이터 삭제](./media/backup-azure-delete-vault/empty-items-list.png)

      목록에 항목이 없는 경우 Recovery Services 자격 증명 모음 메뉴에서 **필수** 창을 스크롤합니다. 어떤 **Backup 항목**, **Backup 관리 서버** 또는 **복제된 항목**도 나열되지 않아야 합니다. 자격 증명 모음에 항목이 아직 나타날 경우 3단계로 돌아가서 다른 항목 유형 목록을 선택합니다.  
6. 자격 증명 모음에 더 이상 항목이 없을 경우 자격 증명 모음 대시보드에서 **삭제**를 클릭합니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

7. 자격 증명 모음을 삭제할 것인지 확인하려면 **예**를 클릭합니다.

    자격 증명 모음이 삭제되고 포털이 **새로 만들기** 서비스 메뉴로 돌아갑니다.


## <a name="removing-azure-backup-agent-recovery-points"></a>Azure Backup 에이전트 복구 지점 제거

1. 자격 증명 모음 대시보드 메뉴에서 관리 섹션으로 스크롤하여 **Backup 인프라**를 클릭합니다.

2. 하위 메뉴에서 **보호되는 서버**를 클릭하여 Azure Backup 에이전트를 포함한 보호되는 서버 유형 목록을 봅니다.

    ![대시보드를 열려면 자격 증명 모음 선택](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. **보호되는 서버** 목록에서 Azure Backup 에이전트를 클릭합니다.

    ![백업 유형 선택](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

    Azure Backup 에이전트를 사용하여 보호된 서버 목록을 엽니다.

    ![특정 보호된 서버 선택](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

4. 서버 목록에서 해당 메뉴를 열려면 한 서버를 클릭합니다.

    ![선택한 서버 대시보드 보기](./media/backup-azure-delete-vault/selected-protected-server.png)

5. 선택한 서버 대시보드 메뉴에서 **삭제**를 클릭합니다.

    ![선택한 대시보드 삭제](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. **삭제** 메뉴에서 항목의 이름을 입력하고 **삭제**를 클릭합니다.

    항목 이름을 입력하여 항목을 삭제할지 확인합니다. 항목을 확인하면 **삭제** 단추가 활성화됩니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

    필요에 따라 데이터를 삭제하는 이유를 제공하고 메모를 추가할 수 있습니다. 작업이 완료되었는지 확인하려면 Azure 메시지 ![delete backup data](./media/backup-azure-delete-vault/messages.png)를 입력합니다. <br/>
    작업이 완료되면 서비스에서는 백업 프로세스가 중지되고 백업 데이터가 삭제되었다는 메시지를 전송합니다.

7. 목록에서 항목을 삭제한 후 **Backup 항목** 메뉴에서 **새로 고침**을 클릭하면 자격 증명 모음의 나머지 항목이 표시됩니다.

      ![백업 데이터 삭제](./media/backup-azure-delete-vault/empty-items-list.png)

      목록에 항목이 없는 경우 Recovery Services 자격 증명 모음 메뉴에서 **필수** 창을 스크롤합니다. 어떤 **Backup 항목**, **Backup 관리 서버** 또는 **복제된 항목**도 나열되지 않아야 합니다. 자격 증명 모음에 항목이 아직 나타날 경우 3단계로 돌아가서 다른 항목 유형 목록을 선택합니다.  
8. 자격 증명 모음에 더 이상 항목이 없을 경우 자격 증명 모음 대시보드에서 **삭제**를 클릭합니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

9. 자격 증명 모음을 삭제할 것인지 확인하려면 **예**를 클릭합니다.

    자격 증명 모음이 삭제되고 포털이 **새로 만들기** 서비스 메뉴로 돌아갑니다.

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>백업 프로세스를 중지하지만 데이터가 남아 있는 경우?

백업 프로세스를 중지하지만 실수로 데이터가 *남아 있는* 경우 자격 증명 모음을 삭제하려면 먼저 백업 데이터를 삭제해야 합니다. 백업 데이터를 삭제하려면 다음을 수행합니다.

1. **백업 항목** 메뉴에서 항목을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **백업 데이터 삭제**를 클릭합니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    **백업 데이터 삭제** 메뉴가 열립니다.
2. **백업 데이터 삭제** 메뉴에서 항목의 이름을 입력하고 **삭제**를 클릭합니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/delete-retained-vault.png)

    데이터를 삭제한 후 4c단계로 돌아가서 프로세스를 계속합니다.
