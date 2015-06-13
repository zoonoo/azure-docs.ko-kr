<properties
	pageTitle="Azure 백업 - 가상 컴퓨터 관리"
	description="Azure 가상 컴퓨터를 관리하는 방법을 알아봅니다."
	services="backup"
	documentationCenter=""
	authors="jimpark"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/28/2015"
	ms.author="jimpark"/>

# 가상 컴퓨터 관리


## 보호된 가상 컴퓨터 관리

1. 가상 컴퓨터에 대한 백업 설정을 보고 관리하려면 **보호된 항목** 탭을 클릭합니다.

  - **백업 세부 정보** 탭을 볼 보호된 항목의 이름을 클릭하면 마지막 백업에 대한 정보가 표시됩니다.

        ![Virtual machine backup](./media/backup-azure-manage-vms/backup-vmdetails.png)

2. 가상 컴퓨터에 대한 백업 정책 설정을 보고 관리하려면 **정책** 탭을 클릭합니다.

  - **백업 정책** 탭에는 기존 정책이 표시됩니다. 필요에 따라 수정할 수 있습니다. 새 정책 클릭을 만들어야 할 경우 **만들기** 에 **정책** 페이지입니다. 정책을 제거하려는 경우 해당 정책과 연결된 가상 컴퓨터가 없어야 합니다.

        ![Virtual machine policy](./media/backup-azure-manage-vms/backup-vmpolicy.png)

3. 가상 컴퓨터의 동작 또는 상태에 대한 자세한 정보는 **작업** 페이지에서 확인할 수 있습니다. 자세한 정보를 보려면 목록에서 작업을 클릭하거나 특정 가상 컴퓨터에 대한 작업을 필터링하세요.

    ![작업](./media/backup-azure-manage-vms/backup-job.png)

## 가상 컴퓨터의 요청 시 백업
보호를 위해 구성 되 면 주문형을 가상의 백업 취할 수 있습니다. 초기 백업을 보류 중인 경우 가상 컴퓨터에 대 한 주문형 백업 가상 컴퓨터의 전체 복사본에서에서 만들 Azure 백업 자격 증명 모음입니다. 첫번째 백업이 완료 되 면 요청 시 백업을 보내집니다 변경 내용을 이전 백업에서 Azure 백업 자격 증명 모음에 있습니다.

주문형 가상 컴퓨터의 백업을 수행 합니다.

1. 로 이동 **보호 되는 항목** 페이지를 선택 하 고 **Azure 가상 컴퓨터** 으로 **유형** (선택 되지 않은) 경우 클릭 하 고 **선택** 단추입니다.

    ![VM 유형](./media/backup-azure-manage-vms/vm-type.png)

2. 요청 시 백업을 수행 하 여 클릭 하려는 가상 컴퓨터를 선택 **지금 백업** 페이지의 아래쪽 단추입니다.

    ![지금 백업](./media/backup-azure-manage-vms/backup-now.png)

    선택한 가상 컴퓨터에 백업 작업을 만듭니다. 이 작업을 통해 만든 복구 지점의 보존 범위는 가상 컴퓨터와 연결 된 정책에 지정 된 것과 같은 됩니다.

    ![백업 작업 만들기](./media/backup-azure-manage-vms/creating-job.png)

    >[AZURE.NOTE]가상 컴퓨터와 연결 된 정책을 보려면 드릴 다운에 가상 컴퓨터는 **보호 되는 항목** 페이지 및 백업 정책 탭으로 이동 합니다.

3. 작업이 만들어지면 클릭할 수 있습니다 **작업 보기** 작업 페이지에서 해당 작업을 보려면 알림 표시줄에 단추입니다.

    ![백업 작업을 만들었습니다.](./media/backup-azure-manage-vms/created-job.png)

4. 작업의 성공적으로 완료 한 후 복구 지점 만들어질 수 있는 가상 컴퓨터를 복원 하는데 사용할 수 있습니다. 복구 지점 열 값에서 1 씩 또한 증가 됩니다이 **보호 되는 항목** 페이지입니다.

## 가상 컴퓨터 보호 중지
다음 옵션으로 가상 컴퓨터의 향후 백업을 중지 하도록 선택할 수 있습니다.

- Azure 백업 자격 증명 모음에 가상 컴퓨터와 연결 된 백업 데이터를 유지 합니다.
- 가상 컴퓨터와 연결 된 백업 데이터를 삭제 합니다.

첫번째 옵션을 선택한 경우에 가상 컴퓨터를 복원 하려면 백업 데이터를 사용할 수 있습니다. 이러한 가상 컴퓨터에 대 한 세부 정보, 가격에 대 한 클릭 [여기](http://azure.microsoft.com/pricing/details/backup/).

에 가상 컴퓨터에 대 한 보호를 중지 합니다.

1. 이동 **보호 되는 항목** 페이지를 선택 하 고 **Azure 가상 컴퓨터** (선택) 되지 않은 경우 필터 유형을 클릭으로 **선택** 단추입니다.

    ![VM 유형](./media/backup-azure-manage-vms/vm-type.png)

2. 가상 컴퓨터를 선택 하 고 클릭할 **보호 중지** 페이지의 맨 아래에 있습니다.

    ![보호 중지](./media/backup-azure-manage-vms/stop-protection.png)

3. 기본적으로 Azure 백업 삭제 되지는 않습니다 가상 컴퓨터에 연결 된 백업 데이터.

    ![보호 중지를 확인 합니다.](./media/backup-azure-manage-vms/confirm-stop-protection.png)

    백업 데이터를 삭제 하려는 경우 확인란을 선택 합니다.

    ![확인란](./media/backup-azure-manage-vms/checkbox.png)

    백업을 중지에 대 한 이유를 선택 하십시오. 선택 사항 이지만, Azure 백업 피드백에 작동 하 고 고객 시나리오의 우선순위를 지정 하는 이유를 입력에 도움이 됩니다.

4. 클릭할 **전송** 제출 단추는 **보호를 중지** 작업 합니다. 클릭할 **작업 보기** 해당 작업에서 참조를 **작업** 페이지입니다.

    ![보호 중지](./media/backup-azure-manage-vms/stop-protect-success.png)

    선택 하지 않은 경우 **관련 된 백업 데이터를 삭제** 하는 동안 옵션 **보호 중지** post 작업 완료, 보호 상태 변경 내용 그런 다음 마법사를 **보호가 중지**.

    ![보호 중지](./media/backup-azure-manage-vms/protection-stopped-status.png)

    선택한 경우에 **관련 된 백업 데이터를 삭제** 옵션을 가상 컴퓨터의 일부를 사용할 수 없습니다 **보호 되는 항목** 페이지입니다.

## 가상 컴퓨터를 다시 보호
선택 하지 않은 경우는 **백업 데이터를 연결 하는 삭제** 옵션을 **보호 중지**, 등록 된 가상 컴퓨터를 백업 하는 것과 유사한 단계에 따라 가상 컴퓨터를 다시 보호할 수 있습니다. 보호 하 고,이 가상 컴퓨터 백업 데이터 보호를 중지 하기 전에 유지 되 고 됩니다 후 복구 지점이 생성 되 면 다시 보호 합니다.

다시 보호 한 후 가상 컴퓨터의 보호 상태를 변경 됩니다 **보호 된** 경우 이전에 복구 지점이 **보호 중지**.

  ![Reprotected VM](./media/backup-azure-manage-vms/reprotected-status.png)

>[AZURE.NOTE]가상 컴퓨터를 다시 보호 하는 경우 가상 컴퓨터가 처음에 보호 된 정책 보다 다른 정책이 선택할 수 있습니다.

## 가상 컴퓨터 등록 취소

백업 자격 증명 모음에서 가상 컴퓨터를 제거하려면 다음을 수행합니다.

1. 페이지 아래쪽에서 **등록 취소** 단추를 클릭합니다.

    ![보호 사용 안 함](./media/backup-azure-manage-vms/unregister-button.png)

    화면 아래쪽에 확인을 요청하는 토스트 알림이 표시됩니다. **예**를 클릭하여 계속합니다.

    ![보호 사용 안 함](./media/backup-azure-manage-vms/confirm-unregister.png)

## 백업 데이터를 삭제 합니다.
하거나 가상 컴퓨터와 연결 된 백업 데이터를 삭제할 수 있습니다.

- 보호 중지 작업 하는 동안
- 가상 컴퓨터에서 작업이 완료 되는 보호를 중지 한 후

상태는 "보호 중지"에 있는 가상 컴퓨터에서 백업 데이터를 삭제 하려면 성공적으로 완료를 게시할 **백업 중지** 작업:

1. 로 이동 **보호 되는 항목** 페이지를 선택 하 고 **Azure 가상 컴퓨터** 로 입력 하 고 클릭할 **선택** 단추입니다.

    ![VM 유형](./media/backup-azure-manage-vms/vm-type.png)

2. 가상 컴퓨터를 선택 합니다. 가상 컴퓨터에 있게 됩니다 **보호가 중지** 상태입니다.

    ![보호 중지](./media/backup-azure-manage-vms/protection-stopped-b.png)

3. 클릭할는 **삭제** 페이지의 아래쪽 단추입니다.

    ![백업 삭제](./media/backup-azure-manage-vms/delete-backup.png)

4. 에 **백업 데이터를 삭제할** 마법사 (권장) 하는 백업 데이터를 삭제 하기 위한 이유를 선택 하 고 클릭할 **제출**.

    ![백업 데이터를 삭제 합니다.](./media/backup-azure-manage-vms/delete-backup-data.png)

5. 선택한 가상 컴퓨터의 백업 데이터를 삭제할 작업을 만듭니다. 클릭할 **작업 보기** 작업 페이지에서 해당 작업을 볼 수 있습니다.

    ![데이터를 삭제 했습니다](./media/backup-azure-manage-vms/delete-data-success.png)

    가상 컴퓨터에 해당 항목에서 제거 작업이 완료 되 면 **보호 되는 항목** 페이지입니다.


###대시보드

에 **대시보드** 페이지 Azure 가상 컴퓨터, 저장소 및 최근 24 시간에 관련 된 작업에 대 한 정보를 검토할 수 있습니다. 백업 상태 및 연결된 백업 오류를 볼 수 있습니다.

  ![대시보드](./media/backup-azure-manage-vms/dashboard-protectedvms.png)

<!---HONumber=GIT-SubDir--> 