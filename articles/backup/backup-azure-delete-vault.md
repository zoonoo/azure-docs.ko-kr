---
title: Azure에서 Recovery Services 자격 증명 모음 삭제
description: Recovery Services 자격 증명 모음을 삭제 하는 방법을 설명 합니다.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: raynew
ms.openlocfilehash: f98b9a02d12cc53ba23857b203ee3eaed9dd7cfa
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466648"
---
# <a name="delete-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 삭제

이 문서에서는 [Azure Backup](backup-overview.md) Recovery Services 자격 증명 모음을 삭제 하는 방법을 설명 합니다. 종속성을 제거한 후 자격 증명 모음을 삭제 하는 방법에 대 한 지침이 포함 되어 있습니다.


## <a name="before-you-start"></a>시작하기 전에

보호 된 서버 또는 자격 증명 모음과 연결 된 백업 관리 서버와 같이 종속성이 있는 Recovery Services 자격 증명 모음을 삭제할 수 없습니다.<br/>
백업 데이터를 포함 하는 자격 증명 모음은 삭제할 수 없습니다 (즉, 보호를 중지 했지만 백업 데이터를 유지 한 경우에도).

종속성을 포함 하는 자격 증명 모음을 삭제 하면 다음과 같은 오류가 표시 됩니다.

![자격 증명 모음 삭제 오류](./media/backup-azure-delete-vault/error.png)

자격 증명 모음을 정상적으로 삭제 하려면 설치와 일치 하는 시나리오를 선택 하 고 권장 단계를 수행 합니다.

시나리오 | 자격 증명 모음을 삭제 하기 위한 종속성을 제거 하는 단계 |
-- | --
MARS (Azure Backup 에이전트)를 사용 하 여 보호 되는 온-프레미스 파일 및 폴더를 Azure로 백업 합니다. | 백업 데이터 및 백업 항목 삭제- [MARS 에이전트의](#for-mars-agent) 단계를 수행 합니다.
MABS (Microsoft Azure Backup 서버) 또는 DPM을 사용 하 여 보호 된 온-프레미스 컴퓨터 (System Center Data Protection Manager)가 있습니다. | 백업 데이터 및 백업 항목 삭제- [MABS 에이전트에 대 한](#for-mabs-agent) 단계를 수행 합니다.
클라우드에서 항목을 보호 합니다 (예: laaS VM, Azure 파일 공유 등)  | [클라우드에서 보호 되는 항목에 대 한](#for-protected-items-in-cloud) 백업 데이터 및 백업 항목 삭제의 단계를 수행 합니다.
온-프레미스와 클라우드 모두에 항목을 보호 했습니다. | 아래 시퀀스에서 백업 데이터 및 백업 항목 삭제의 단계를 수행 합니다. <br> - [클라우드에서 보호 되는 항목의 경우](#for-protected-items-in-cloud)<br> - [MABS 에이전트의 경우](#for-mars-agent) <br> - [MABS 에이전트의 경우](#for-mabs-agent)
온-프레미스 또는 클라우드에 보호 된 항목이 없습니다. 그러나 여전히 자격 증명 모음 삭제 오류가 발생 합니다. | [Azure Resource Manager 클라이언트를 사용 하 여 Recovery Services 자격 증명 모음 삭제](#delete-the-recovery-services-vault-using-azure-resource-manager-client) 의 단계를 수행 합니다.
원본 온-프레미스 서버를 더 이상 사용할 수 없으며 (분실/서비스 해제 됨) Recovery Services 자격 증명 모음을 삭제 하려고 합니다. | Microsoft 지원에 문의 하세요.

## <a name="delete-backup-data-and-backup-items"></a>백업 데이터 및 백업 항목 삭제

계속 하기 전에 **[이](#before-you-start)** 섹션을 읽고 종속성 및 자격 증명 모음 삭제 프로세스를 이해 합니다.

### <a name="for-protected-items-in-cloud"></a>클라우드에서 보호 되는 항목의 경우

보호를 중지 하 고 백업 데이터를 삭제 하려면 다음을 수행 합니다.

1. 포털 > Recovery Services 자격 증명 모음 > 백업 항목에서 클라우드에서 보호 된 항목을 선택 합니다.

    ![백업 유형 선택](./media/backup-azure-delete-vault/azure-storage-selected.jpg)

2. 각 항목에 대해 마우스 오른쪽 단추를 클릭 하 고 **백업 중지**를 선택 해야 합니다.

    ![백업 유형 선택](./media/backup-azure-delete-vault/stop-backup-item.png)

3. 백업 > 중지에서**옵션을 선택**하 고 **백업 데이터 삭제**를 선택 합니다.
4. 항목의 이름을 입력 하 고 **백업 중지**를 클릭 합니다.
   - 항목을 삭제할 것인지 확인 합니다.
   - 확인 후 **백업 중지** 단추가 활성화 됩니다.
   - 데이터를 유지 하 고 삭제 하지 않은 경우에는 자격 증명 모음을 삭제할 수 없습니다.

     ![백업 데이터 삭제](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. 알림![백업데이터](./media/backup-azure-delete-vault/messages.png)삭제를 확인 합니다. 완료 되 면 서비스는 다음과 같은 메시지를 표시 합니다. **"*백업 항목*"의 백업 및 백업 데이터 삭제를 중지 하는 중**입니다. **작업을 완료 했습니다**.
6. 백업 **항목 메뉴에서** **새로 고침** 을 클릭 하 여 백업 항목이 제거 되었는지 확인 합니다.

      ![백업 데이터 삭제](./media/backup-azure-delete-vault/empty-items-list.png)

### <a name="for-mars-agent"></a>MARS 에이전트의 경우

보호를 중지 하 고 백업 데이터를 삭제 하려면 아래 나열 된 순서 대로 단계를 수행 합니다.

- [1단계: MARS 관리 콘솔에서 백업 항목 삭제](#step-1-delete-backup-items-from-mars-management-console)
- [2단계: 포털에서 Azure Backup 에이전트 제거](#step-1-delete-backup-items-from-mars-management-console)


#### <a name="step-1-delete-backup-items-from-mars-management-console"></a>1단계: MARS 관리 콘솔에서 백업 항목 삭제

서버를 사용할 수 없기 때문에이 단계를 수행할 수 없는 경우 Microsoft 지원에 문의 하세요.

- MARS 관리 콘솔을 시작 하 고 **작업** 창으로 이동 하 여 **Backup 예약**을 선택 합니다.
- **예약 된 백업 수정 또는 중지** 마법사에서 **이 백업 일정 사용 중지 옵션을 선택 하 고 저장 된 모든 백업을 삭제** 한 후 **다음**을 클릭 합니다.

    ![예약된 백업 수정 또는 중지](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- **예약 된 백업 중지** 마법사에서 **마침**을 클릭 합니다.

    ![예약 된 백업 중지](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- 보안 Pin을 입력 하 라는 메시지가 표시 됩니다. PIN을 생성 하려면 아래 단계를 수행 합니다.
  - Azure 포털에 로그인합니다.
  - **Recovery Services 자격 증명 모음** > **설정** > **속성**을 찾습니다.
  - **보안 PIN** 아래에서 **생성**을 클릭합니다. 이 PIN을 복사 합니다. 이 PIN은 5 분 동안만 유효 합니다.
- 관리 콘솔 (클라이언트 앱)에서 PIN을 붙여 넣고 **확인을**클릭 합니다.

  ![보안 Pin](./media/backup-azure-delete-vault/security-pin.png)

- **백업 수정 진행률** 마법사 *에서 삭제 된 백업 데이터는 14 일 동안 보존 됩니다. 이 시간 후에는 백업 데이터가 영구적으로 삭제 됩니다.*  

    ![백업 인프라 삭제](./media/backup-azure-delete-vault/deleted-backup-data.png)

온-프레미스에서 백업 항목을 삭제 했으므로 포털에서 다음 단계를 완료 합니다.

#### <a name="step-2-from-portal-remove-azure-backup-agent"></a>2단계: 포털에서 Azure Backup 에이전트 제거

계속 하기 전에 [1 단계](#step-1-delete-backup-items-from-mars-management-console) 를 완료 했는지 확인 합니다.

1. 자격 증명 모음 대시보드 메뉴에서 **Backup 인프라**를 클릭 합니다.
2. **보호 된 서버** 를 클릭 하 여 인프라 서버를 봅니다.

    ![대시보드를 열려면 자격 증명 모음 선택](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. **보호되는 서버** 목록에서 Azure Backup 에이전트를 클릭합니다.

    ![백업 유형 선택](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. Azure Backup 에이전트를 사용 하 여 보호 되는 서버 목록에서 서버를 클릭 합니다.

    ![특정 보호된 서버 선택](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. 선택한 서버 대시보드에서 **삭제**를 클릭 합니다.

    ![선택한 대시보드 삭제](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. **삭제** 메뉴에서 서버 이름을 입력 하 고 **삭제**를 클릭 합니다.

     ![백업 데이터 삭제](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

> [!NOTE]
> 아래 오류가 표시 되는 경우 먼저 [관리 콘솔에서 백업 항목 삭제](#step-1-delete-backup-items-from-mars-management-console)에 나열 된 단계를 수행 합니다.
>
>![삭제 실패](./media/backup-azure-delete-vault/deletion-failed.png)
>
>관리 콘솔에서 서버를 사용할 수 없기 때문에 관리 콘솔에서 백업을 삭제 하는 단계를 수행할 수 없는 경우 Microsoft 지원에 문의 하세요.

7. 알림![백업데이터](./media/backup-azure-delete-vault/messages.png)삭제를 확인 합니다. 완료 되 면 서비스는 다음과 같은 메시지를 표시 합니다. **"*백업 항목*"의 백업 및 백업 데이터 삭제를 중지 하는 중**입니다. **작업을 완료 했습니다**.
8. 백업 **항목 메뉴에서** **새로 고침** 을 클릭 하 여 백업 항목이 제거 되었는지 확인 합니다.


### <a name="for-mabs-agent"></a>MABS 에이전트의 경우

보호를 중지 하 고 백업 데이터를 삭제 하려면 아래 나열 된 순서 대로 단계를 수행 합니다.

- [1단계: MABS 관리 콘솔에서 백업 항목 삭제](#step-1-delete-backup-items-from-mabs-management-console)
- [2단계: 포털에서 Azure Backup 관리 서버 제거](#step-2-from-portal-remove-azure-backup-agent)

#### <a name="step-1-delete-backup-items-from-mabs-management-console"></a>1단계: MABS 관리 콘솔에서 백업 항목 삭제

서버를 사용할 수 없기 때문에이 단계를 수행할 수 없는 경우 Microsoft 지원에 문의 하세요.

**방법 1** 보호를 중지 하 고 백업 데이터를 삭제 하려면 다음 단계를 수행 합니다.

1.  DPM 관리자 콘솔의 탐색 모음에서 **보호** 를 클릭 합니다.
2.  디스플레이 창에서 제거할 보호 그룹 구성원을 선택 합니다. **그룹 구성원의 보호 중지** 옵션을 마우스 오른쪽 단추로 클릭 하 여 선택 합니다.
3.  **보호 중지** 대화 상자에서 **보호 된 데이터** > 삭제**저장소 온라인 삭제** 확인란을 선택 하 고 **보호 중지**를 클릭 합니다.

    ![온라인으로 저장소 삭제](./media/backup-azure-delete-vault/delete-storage-online.png)

이제 보호 된 구성원 상태가 **사용 가능한 비활성 복제본**으로 변경 됩니다.

5. 비활성 보호 그룹을 마우스 오른쪽 단추로 클릭 하 고 **비활성 보호 제거**를 선택 합니다.

    ![비활성 보호 제거](./media/backup-azure-delete-vault/remove-inactive-protection.png)

6. **비활성 보호 삭제** 창에서 **온라인 저장소 삭제** 를 선택 하 고 **확인**을 클릭 합니다.

    ![디스크 및 온라인에서 복제본 제거](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

**방법 2** **Mabs 관리** 콘솔을 시작 합니다. **데이터 보호 방법 선택** 섹션에서 **온라인 보호**를 선택 취소 합니다.

  ![데이터 보호 방법 선택](./media/backup-azure-delete-vault/data-protection-method.png)

온-프레미스에서 백업 항목을 삭제 했으므로 포털에서 다음 단계를 완료 합니다.

#### <a name="step-2-from-portal-remove-azure-backup-management-servers"></a>2단계: 포털에서 Azure Backup 관리 서버 제거

계속 하기 전에 [1 단계](#step-1-delete-backup-items-from-mabs-management-console) 를 완료 했는지 확인 합니다.

1. 자격 증명 모음 대시보드 메뉴에서 **Backup 인프라**를 클릭 합니다.
2. 서버를 보려면 **백업 관리 서버** 를 클릭 합니다.

    ![자격 증명 모음을 선택 하 여 대시보드를 엽니다.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. 항목을 마우스 오른쪽 단추로 클릭 하 > **삭제**합니다.
4. **삭제** 메뉴에서 서버 이름을 입력 하 고 **삭제**를 클릭 합니다.

     ![백업 데이터 삭제](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

> [!NOTE]
> 아래 오류가 표시 되는 경우 먼저 [관리 콘솔에서 백업 항목 삭제](#step-2-from-portal-remove-azure-backup-management-servers)에 나열 된 단계를 수행 합니다.
>
>![삭제 실패](./media/backup-azure-delete-vault/deletion-failed.png)
>
> 관리 콘솔에서 서버를 사용할 수 없기 때문에 관리 콘솔에서 백업을 삭제 하는 단계를 수행할 수 없는 경우 Microsoft 지원에 문의 하세요.

5. 알림![백업데이터](./media/backup-azure-delete-vault/messages.png)삭제를 확인 합니다. 완료 되 면 서비스는 다음과 같은 메시지를 표시 합니다. **"*백업 항목*"의 백업 및 백업 데이터 삭제를 중지 하는 중**입니다. **작업을 완료 했습니다**.
6. 백업 **항목 메뉴에서** **새로 고침** 을 클릭 하 여 백업 항목이 제거 되었는지 확인 합니다.


## <a name="delete-the-recovery-services-vault"></a>Recovery Services 자격 증명 모음 삭제

1. 모든 종속성이 제거 되 면 자격 증명 모음 메뉴의 **Essentials** 창으로 스크롤합니다.
2. **백업 항목**, **백업 관리 서버**또는 **복제 된 항목이** 나열 되어 있지 않은지 확인 합니다. 자격 증명 모음에 항목이 계속 표시 되는 경우 [제거](#delete-backup-data-and-backup-items)합니다.

3. 자격 증명 모음에 더 이상 항목이 없을 경우 자격 증명 모음 대시보드에서 **삭제**를 클릭합니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. 자격 증명 모음을 삭제할 것인지 확인하려면 **예**를 클릭합니다. 자격 증명 모음이 삭제되고 포털이 **새로 만들기** 서비스 메뉴로 돌아갑니다.

## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Azure Resource Manager 클라이언트를 사용 하 여 Recovery Services 자격 증명 모음 삭제

Recovery Services 자격 증명 모음을 삭제 하는이 옵션은 모든 종속성이 제거 되 고 *자격 증명 모음 삭제 오류가 계속 발생*하는 경우에만 권장 됩니다.



- 자격 증명 모음 메뉴의 **Essentials** 창에서 **백업 항목**, **백업 관리 서버**또는 **복제 된 항목이** 나열 되지 않는지 확인 합니다. 백업 항목이 있는 경우 [백업 데이터 및 백업 항목 삭제](#delete-backup-data-and-backup-items)의 단계를 수행 합니다.
- [포털에서 자격 증명 모음 삭제](#delete-the-recovery-services-vault)를 다시 시도 합니다.
- 모든 종속성이 제거 되 고 *자격 증명 모음 삭제 오류가 계속 발생* 하는 경우 ARMClient 도구를 사용 하 여 아래 지정 된 단계를 수행 합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. [여기](https://chocolatey.org/) 에서 chocolatey를 설치 하 고 ARMClient을 설치 하려면 다음 명령을 실행 합니다.

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Azure 계정에 로그인 하 고 다음 명령을 실행 합니다.

    `ARMClient.exe login [environment name]`

3. Azure Portal에서 삭제 하려는 자격 증명 모음에 대 한 구독 ID 및 리소스 그룹 이름을 수집 합니다.

ARMClient 명령에 대 한 자세한 내용은이 [문서](https://github.com/projectkudu/ARMClient/blob/master/README.md)를 참조 하세요.

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Azure Resource Manager 클라이언트를 사용 하 여 Recovery Services 자격 증명 모음 삭제

1. 구독 ID, 리소스 그룹 이름 및 자격 증명 모음 이름을 사용 하 여 다음 명령을 실행 합니다. 명령을 실행할 때 종속성이 없는 경우 자격 증명 모음을 삭제 합니다.

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. 자격 증명 모음이 비어 있지 않으면 "이 자격 증명 모음 내에 기존 리소스가 있으므로 자격 증명 모음을 삭제할 수 없습니다." 라는 오류가 표시 됩니다. 자격 증명 모음 내에서 보호 된 항목/컨테이너를 제거 하려면 다음을 수행 합니다.

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Azure Portal에서 자격 증명 모음이 삭제 되었는지 확인 합니다.


## <a name="next-steps"></a>다음 단계

[자세한 정보](backup-azure-recovery-services-vault-overview.md) 자격 증명 모음을 Recovery Services 합니다.<br/>
Recovery Services 자격 증명 모음 모니터링 및 관리 [에 대해 알아봅니다](backup-azure-manage-windows-server.md) .
