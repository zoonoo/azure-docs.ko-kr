---
title: Azure에서 Recovery Services 자격 증명 모음 삭제
description: Recovery Services 자격 증명 모음을 삭제 하는 방법을 설명 합니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: raynew
ms.openlocfilehash: e195d9a4b9d2bbe21848e083dbccf864188e0790
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508503"
---
# <a name="delete-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 삭제

이 문서에서는 삭제 하는 방법에 설명 합니다는 [Azure Backup](backup-overview.md) Recovery Services 자격 증명 모음입니다. 종속성을 제거 자격 증명 모음을 삭제 하 고 강제로 자격 증명 모음을 삭제 하기 위한 지침을 포함 합니다.


## <a name="before-you-start"></a>시작하기 전에

시작 하기 전에 서버가 있는 Recovery Services 자격 증명 모음을 삭제할 수 없습니다는 이해, 등록 또는 백업 데이터를 보유 하는 중요 한 것입니다.

- 자격 증명 모음을 정상적으로 삭제 하려면 포함 된 서버의 등록을 취소 자격 증명 모음 데이터를 제거한 다음 자격 증명 모음을 삭제 합니다.
- 자격 증명 모음을 삭제 하려고 하면는 아직 종속성, 오류 메시지가 발생 합니다 및 포함 하 여 자격 증명 모음 종속성을 수동으로 제거 해야 합니다.
    - 백업 된 항목
    - 보호 된 서버
    - Backup 관리 서버 (Azure Backup Server, DPM) ![해당 대시보드를 열려면 자격 증명 모음 선택](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)
- Recovery Services 자격 증명 모음에 있는 모든 데이터를 유지 하 고 자격 증명 모음을 삭제 하려면에 않으려면 강제로 자격 증명 모음을 삭제할 수 있습니다.
- 자격 증명 모음을 삭제하려고 해도 할 수 없는 경우 자격 증명 모음이 여전히 백업 데이터를 받도록 구성되어 있습니다.


## <a name="delete-a-vault-from-the-azure-portal"></a>Azure portal에서 자격 증명 모음 삭제

1. 자격 증명 모음 대시보드를 엽니다.  
2. 대시보드를 클릭 **삭제**합니다. 삭제할 것임을 확인 합니다.

    ![대시보드를 열려면 자격 증명 모음 선택](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

오류가 발생 하는 경우 제거 [backup 항목](#remove-backup-items)를 [인프라 서버](#remove-azure-backup-management-servers), 및 [복구 지점을](#remove-azure-backup-agent-recovery-points), 한 다음 자격 증명 모음을 삭제 합니다.

![자격 증명 모음 오류를 삭제 합니다.](./media/backup-azure-delete-vault/error.png)


## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Azure Resource Manager 클라이언트를 사용 하 여 Recovery Services 자격 증명 모음 삭제

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. 설치에서 chocolatey [여기](https://chocolatey.org/) 실행 ARMClient를 설치 하 여 아래 명령을:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Azure 계정에 로그인 하 고이 명령을 실행 합니다.

    `ARMClient.exe login [environment name]`

3. Azure portal에서 삭제 하려는 자격 증명 모음에 대 한 구독 ID 및 리소스 그룹 이름을 수집 합니다.

이 참조 ARMClient 명령에 대 한 자세한 내용은 [문서](https://github.com/projectkudu/ARMClient/blob/master/README.md)합니다.

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Azure Resource Manager 클라이언트를 사용 하 여 Recovery Services 자격 증명 모음을 삭제 하려면

1. 사용자 구독 ID, 리소스 그룹 이름 및 자격 증명 모음 이름을 사용 하 여 다음 명령을 실행 합니다. 명령을 실행 하면 모든 종속성이 없는 경우 자격 증명 모음을 삭제 합니다.

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. 자격 증명 모음의 비어 있지, "자격 증명 모음 수 없습니다.이 자격 증명이 모음 내에서 기존 리소스는 삭제 됨" 오류가 나타납니다. 보호 된 항목을 제거 하려면 다음을 수행 하는 자격 증명 모음에 내에서 컨테이너 /:

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Azure portal에서 데이터를 자격 증명 모음 삭제를 확인 합니다.


## <a name="remove-vault-items-and-delete-the-vault"></a>자격 증명 모음 항목을 제거 하 고 자격 증명 모음 삭제

Recovery Services 자격 증명 모음을 삭제 하기 전에 모든 종속성을 제거 합니다.

### <a name="remove-backup-items"></a>백업 항목 제거

이 절차는 Azure files에서 백업 데이터를 제거 하는 방법을 보여 주는 예제를 제공 합니다.

1. 클릭 **Backup 항목** > **Azure Storage (Azure Files)**

    ![백업 유형 선택](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

2. 각 Azure Files 항목을 제거 하 고 클릭 마우스 오른쪽 단추로 클릭 **백업 중지**합니다.

    ![백업 유형 선택](./media/backup-azure-delete-vault/stop-backup-item.png)


3. **Backup 중지** > **옵션을 선택**선택 **Backup 데이터 삭제**합니다.
4. 항목의 이름을 입력 하 고 클릭 **백업 중지**합니다.
   - 이 항목을 삭제할 것인지 확인 합니다.
   - 합니다 **백업 중지** 확인 한 후 단추를 활성화 합니다.
   - 유지 하 고 데이터를 삭제 하지 마십시오 자격 증명 모음을 삭제할 수 없습니다.

     ![백업 데이터 삭제](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. 필요에 따라 데이터를 삭제 하는 이유는 이유를 제공 하 고 주석을 추가 합니다.
6. 삭제 작업이 완료 되었는지 확인 하려면 Azure 메시지 확인 ![백업 데이터 삭제](./media/backup-azure-delete-vault/messages.png).
7. 서비스 작업이 완료 되 면 메시지를 보냅니다: **백업 프로세스가 중지 되 고 백업 데이터가 삭제**합니다.
8. 목록에서 항목을 삭제한 후 **Backup 항목** 메뉴에서 **새로 고침**을 클릭하면 자격 증명 모음의 항목이 표시됩니다.

      ![백업 데이터 삭제](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="deleting-backup-items-from-management-console"></a>관리 콘솔에서 백업 항목 삭제

백업 인프라에서 백업 항목을 삭제 하려면 관리 콘솔 (MARS, Azure Backup Server 또는 SC DPM 백 항목 보호 되는 위치에 따라) 온-프레미스 서버로 이동 합니다.

### <a name="for-mars-agent"></a>MARS 에이전트에 대 한

- MARS 관리 콘솔을 시작로 이동 합니다 **작업** 창 선택 **백업 일정**합니다.
- **수정 또는 예약 된 Backup 중지** 마법사 옵션을 선택 **이 백업 일정을 사용 하 여를 중지 하 고 저장 된 모든 백업이 삭제** 누릅니다 **다음**합니다.

    ![예약된 백업 수정 또는 중지](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- **예약 된 백업 중지** 마법사를 클릭 **마침**합니다.

    ![예약된 된 백업 중지](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- 보안 Pin을 입력 하 라는 메시지가 표시 됩니다. PIN을 생성 하기 위해 수행 된 다음 단계:
  - Azure 포털에 로그인합니다.
  - **Recovery Services 자격 증명 모음** > **설정** > **속성**을 찾습니다.
  - **보안 PIN** 아래에서 **생성**을 클릭합니다. 이 PIN을 복사 합니다. (이 핀의 유효 기간 5 분 동안만)
- 관리 콘솔 (클라이언트 앱)에서 PIN을 붙여넣고 클릭 **확인**합니다.

  ![보안 Pin](./media/backup-azure-delete-vault/security-pin.png)

- 에 **백업 진행 중 수정** 나타납니다 마법사 *삭제 된 백업 데이터는 14 일간 유지 됩니다. 해당 시간 이후에 백업 데이터가 영구적으로 삭제 됩니다.*  

    ![백업 인프라를 삭제 합니다.](./media/backup-azure-delete-vault/deleted-backup-data.png)

완료 하는 온-프레미스에서 백업 항목을 삭제, 이제는 포털에서 다음 단계:
- MARS의 단계에 대 한 [제거 Azure Backup 에이전트 복구 지점](#remove-azure-backup-agent-recovery-points)

### <a name="for-mabs-agent"></a>MABS 에이전트에 대 한

온라인 보호를 중지/삭제, 중 하나를 수행 하는 방법은 여러 가지가 있습니다를 메서드 아래:

**방법 1**

시작 합니다 **MABS 관리** 콘솔. 에 **데이터 보호 방법 선택** 섹션을 선택 취소 **온라인 보호**합니다.

  ![데이터 보호 방법 선택](./media/backup-azure-delete-vault/data-protection-method.png)

**방법 2**

보호 그룹을 삭제 하려면 먼저 그룹의 보호를 중지 해야 합니다. 보호를 중지 하 고 보호 그룹의 삭제를 사용 하도록 설정 하려면 다음 절차를 따르십시오.

1.  DPM 관리자 콘솔에서 클릭 **Protection** 탐색 모음에서.
2.  디스플레이 창에서 제거할 보호 그룹 구성원을 선택 합니다. 마우스 오른쪽 단추로 클릭 하려는 **그룹 구성원 보호 중지** 옵션입니다.
3.  **보호 중지** 대화 상자에서 **보호 된 데이터 삭제** > **온라인 저장소 삭제** 확인란을 클릭 하 고 **중지 보호**합니다.

    ![온라인 저장소 삭제](./media/backup-azure-delete-vault/delete-storage-online.png)

보호 된 멤버 상태를 이제 변경 되었습니다 **비활성 복제본 사용 가능**합니다.

5. 선택한 비활성 보호 그룹을 마우스 오른쪽 단추로 클릭 **비활성 보호 제거**합니다.

    ![비활성 보호 제거](./media/backup-azure-delete-vault/remove-inactive-protection.png)

6. **비활성 보호 삭제** 창에서 **온라인 저장소 삭제** 클릭 **확인**합니다.

    ![디스크 및 온라인 복제본 제거](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

완료 하는 온-프레미스에서 백업 항목을 삭제, 이제는 포털에서 다음 단계:
- 단계에 따라 DPM 및 MABS에 대 한 [제거 하는 Azure Backup 관리 서버가](#remove-azure-backup-management-servers)합니다.


### <a name="remove-azure-backup-management-servers"></a>Azure Backup 관리 서버를 제거 합니다.

Azure backup 관리 서버를 제거 하기 전에 나열 된 단계를 수행할 수 있는지 확인 [관리 콘솔에서 백업 항목을 삭제](#deleting-backup-items-from-management-console)합니다.

1. 자격 증명 모음 대시보드 메뉴에서 클릭 **Backup 인프라**합니다.
2. 클릭 **Backup 관리 서버** 서버를 표시 합니다.

    ![해당 대시보드를 열려면 자격 증명 모음 선택](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. 항목을 마우스 오른쪽 단추로 클릭 > **삭제**합니다.
4. 에 **삭제할** 메뉴에서 서버 이름을 입력 하 고 클릭 **삭제**합니다.

     ![백업 데이터 삭제](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)
5.  필요에 따라 데이터를 삭제 하는 이유는 이유를 제공 하 고 주석을 추가 합니다.

> [!NOTE]
> 아래 오류를 다음에 나열 된 단계를 수행 하는 첫 번째 표시 되는 경우 [관리 콘솔에서 백업 항목을 삭제](#deleting-backup-items-from-management-console)합니다.
>
>![삭제 하지 못했습니다.](./media/backup-azure-delete-vault/deletion-failed.png)
>
> 관리 콘솔에서 백업을 삭제 하는 단계를 수행할 수 없는 경우 예를 들어 관리 콘솔을 사용 하 여 서버에 사용할 수 없어서 Microsoft 지원에 문의 합니다.

6. 삭제 작업이 완료 되었는지 확인 하려면 Azure 메시지 확인 ![백업 데이터 삭제](./media/backup-azure-delete-vault/messages.png).
7. 서비스 작업이 완료 되 면 메시지를 보냅니다: **백업 프로세스가 중지 되 고 백업 데이터가 삭제**합니다.
8. 목록의 항목을 삭제 한 후를 **Backup 인프라** 메뉴에서 클릭 **새로 고침** 자격 증명 모음에서 항목을 표시 합니다.


### <a name="remove-azure-backup-agent-recovery-points"></a>Azure Backup 에이전트 복구 지점을 제거합니다

Azure 백업 복구 지점은 제거 하기 전에 나열 된 단계를 수행할 수 있는지 확인 [관리 콘솔에서 백업 항목을 삭제](#deleting-backup-items-from-management-console)합니다.

1. 자격 증명 모음 대시보드 메뉴에서 클릭 **Backup 인프라**합니다.
2. 클릭 **보호 된 서버** 인프라 서버를 표시 합니다.

    ![대시보드를 열려면 자격 증명 모음 선택](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. **보호되는 서버** 목록에서 Azure Backup 에이전트를 클릭합니다.

    ![백업 유형 선택](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. Azure Backup 에이전트를 사용 하 여 보호 된 서버의 목록에서 서버를 클릭 합니다.

    ![특정 보호된 서버 선택](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. 선택한 서버 대시보드에서 **삭제**합니다.

    ![선택한 대시보드 삭제](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. 에 **삭제할** 메뉴에서 서버 이름을 입력 하 고 클릭 **삭제**합니다.

     ![백업 데이터 삭제](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. 필요에 따라 데이터를 삭제 하는 이유는 이유를 제공 하 고 주석을 추가 합니다.

> [!NOTE]
> 아래 오류를 다음에 나열 된 단계를 수행 하는 첫 번째 표시 되는 경우 [관리 콘솔에서 백업 항목을 삭제](#deleting-backup-items-from-management-console)합니다.
>
>![삭제 하지 못했습니다.](./media/backup-azure-delete-vault/deletion-failed.png)
>
> 관리 콘솔에서 백업을 삭제 하는 단계를 수행할 수 없는 경우 예를 들어 관리 콘솔을 사용 하 여 서버에 사용할 수 없어서 Microsoft 지원에 문의 합니다. 

8. 삭제 작업이 완료 되었는지 확인 하려면 Azure 메시지 확인 ![백업 데이터 삭제](./media/backup-azure-delete-vault/messages.png).
9. 목록의 항목을 삭제 한 후를 **Backup 인프라** 메뉴에서 클릭 **새로 고침** 자격 증명 모음에서 항목을 표시 합니다.


### <a name="delete-the-vault-after-removing-dependencies"></a>종속성을 제거한 후 자격 증명 모음 삭제

1. 모든 종속성을 제거한 경우 스크롤하여 합니다 **Essentials** 자격 증명 모음 메뉴의 창.
2. 확인 된 **Backup 항목**를 **Backup 관리 서버**, 또는 **복제 된 항목** 나열 합니다. 자격 증명 모음에 항목이 아직 나타날를 제거 합니다.

3. 자격 증명 모음에 더 이상 항목이 없을 경우 자격 증명 모음 대시보드에서 **삭제**를 클릭합니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. 자격 증명 모음을 삭제할 것인지 확인하려면 **예**를 클릭합니다. 자격 증명 모음이 삭제되고 포털이 **새로 만들기** 서비스 메뉴로 돌아갑니다.

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>백업 프로세스를 중지하지만 데이터가 남아 있는 경우?

백업 프로세스를 중지 해도 실수로 데이터를 유지 하는 경우에 이전 섹션에 설명 된 대로 백업 데이터를 삭제 해야 합니다.

## <a name="next-steps"></a>다음 단계

[에 대 한 자세한](backup-azure-recovery-services-vault-overview.md) Recovery Services 자격 증명 모음입니다.
