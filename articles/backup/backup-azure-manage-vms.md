
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

  - **백업 정책** 탭에는 기존 정책이 표시됩니다. 필요에 따라 수정할 수 있습니다. 새 정책을 만들어야 하는 경우 **정책** 페이지에서 **만들기**를 클릭합니다. 정책을 제거하려는 경우 해당 정책과 연결된 가상 컴퓨터가 없어야 합니다.

        ![Virtual machine policy](./media/backup-azure-manage-vms/backup-vmpolicy.png)

3. 가상 컴퓨터의 동작 또는 상태에 대한 자세한 정보는 **작업** 페이지에서 확인할 수 있습니다. 자세한 정보를 보려면 목록에서 작업을 클릭하거나 특정 가상 컴퓨터에 대한 작업을 필터링하세요.

    ![작업](./media/backup-azure-manage-vms/backup-job.png)

## 가상 컴퓨터의 주문형 백업
보호를 위해 구성한 후에는 가상 컴퓨터의 주문형 백업을 수행할 수 있습니다. 가상 컴퓨터에 대한 초기 백업이 보류 중인 경우 주문형 백업은 Azure 백업 자격 증명 모음에 가상 컴퓨터의 전체 복사본을 만듭니다. 첫 번째 백업이 완료된 경우 주문형 백업은 이전 백업의 변경 내용만 Azure 백업 자격 증명 모음으로 보냅니다.

가상 컴퓨터의 주문형 백업을 수행하려면

1. **보호되는 항목** 페이지로 이동하고 **Azure 가상 컴퓨터**를 **유형**으로 선택한 다음(선택되지 않은 경우) **선택** 단추를 클릭합니다.

    ![VM 유형](./media/backup-azure-manage-vms/vm-type.png)

2. 주문형 백업을 수행할 가상 컴퓨터를 선택하고 페이지 맨 아래에 있는 **지금 백업** 단추를 클릭합니다.

    ![지금 백업](./media/backup-azure-manage-vms/backup-now.png)

    선택한 가상 컴퓨터에 대한 백업 작업이 생성됩니다. 이 작업을 통해 만든 복구 지점의 보존 범위는 가상 컴퓨터와 연결된 정책에 지정된 범위와 같습니다.

    ![백업 작업 만들기](./media/backup-azure-manage-vms/creating-job.png)

    >[AZURE.NOTE]가상 컴퓨터와 연결된 정책을 보려면 **보호되는 항목** 페이지에서 가상 컴퓨터로 드릴다운하고 백업 정책 탭으로 이동합니다.

3. 작업이 생성된 후에는 알림 표시줄의 **작업 보기** 단추를 클릭하여 작업 페이지에서 해당 작업을 확인할 수 있습니다.

    ![백업 작업 생성됨](./media/backup-azure-manage-vms/created-job.png)

4. 작업이 성공적으로 완료되면 가상 컴퓨터를 복원하는 데 사용할 수 있는 복구 지점이 생성됩니다. 또한 **보호되는 항목** 페이지에서 복구 지점 열 값이 1씩 증가합니다.

## 가상 컴퓨터 보호 중지
다음 옵션으로 가상 컴퓨터의 향후 백업을 중지하도록 선택할 수 있습니다.

- Azure 백업 자격 증명 모음에 가상 컴퓨터와 연결된 백업 데이터 유지
- 가상 컴퓨터와 연결된 백업 데이터 삭제

첫 번째 옵션을 선택한 경우 백업 데이터를 사용하여 가상 컴퓨터를 복원할 수 있습니다. 이러한 가상 컴퓨터의 가격 정보를 보려면 [여기](http://azure.microsoft.com/pricing/details/backup/)를 클릭하세요.

가상 컴퓨터에 대한 보호를 중지하려면

1. **보호되는 항목** 페이지로 이동하고 **Azure 가상 컴퓨터**를 필터 유형으로 선택한 다음(선택되지 않은 경우) **선택** 단추를 클릭합니다.

    ![VM 유형](./media/backup-azure-manage-vms/vm-type.png)

2. 가상 컴퓨터를 선택하고 페이지 맨 아래에 있는 **보호 중지**를 클릭합니다.

    ![보호 중지](./media/backup-azure-manage-vms/stop-protection.png)

3. 기본적으로 Azure 백업은 가상 컴퓨터와 연결된 백업 데이터를 삭제하지 않습니다.

    ![보호 중지 확인](./media/backup-azure-manage-vms/confirm-stop-protection.png)

    백업 데이터를 삭제하려면 확인란을 선택합니다.

    ![확인란](./media/backup-azure-manage-vms/checkbox.png)

    백업을 중지하는 이유를 선택합니다. 선택 사항이지만, 이유를 제공하면 Azure 백업이 피드백에 따라 작동하고 고객 시나리오의 우선 순위를 지정하는 데 도움이 됩니다.

4. **제출** 단추를 클릭하여 **보호 중지** 작업을 제출합니다. **작업 보기**를 클릭하여 **작업** 페이지에서 해당 작업을 확인합니다.

    ![보호 중지](./media/backup-azure-manage-vms/stop-protect-success.png)

    **보호 중지** 마법사에서 **연결된 백업 데이터 삭제** 옵션을 선택하지 않은 경우 작업 완료 후 보호 상태가 **보호 중지됨**으로 바뀝니다. 명시적으로 삭제될 때까지 Azure 백업을 사용하여 데이터를 유지합니다. **보호되는 항목** 페이지에서 가상 컴퓨터를 선택하고 **삭제**를 클릭하여 데이터를 항상 삭제할 수 있습니다.

    ![보호 중지됨](./media/backup-azure-manage-vms/protection-stopped-status.png)

    **연결된 백업 데이터 삭제** 옵션을 선택한 경우 가상 컴퓨터가 **보호되는 항목** 페이지에 포함되지 않습니다.

## 가상 컴퓨터 다시 보호
**보호 중지**에서 **연결된 백업 데이터 삭제** 옵션을 선택하지 않은 경우 등록된 가상 컴퓨터 백업과 유사한 단계에 따라 가상 컴퓨터를 다시 보호할 수 있습니다. 보호하고 나면 이 가상 컴퓨터는 보호를 중지하기 전에 유지된 백업 데이터와 다시 보호한 후 생성된 복구 지점을 갖게 됩니다.

다시 보호한 후 **보호 중지** 전에 복구 지점이 있는 경우 가상 컴퓨터의 보호 상태가 **보호됨**으로 바뀝니다.

  ![다시 보호된 VM](./media/backup-azure-manage-vms/reprotected-status.png)

>[AZURE.NOTE]가상 컴퓨터를 다시 보호하는 경우 가상 컴퓨터가 처음에 보호된 정책과 다른 정책을 선택할 수 있습니다.

## 가상 컴퓨터 등록 취소

백업 자격 증명 모음에서 가상 컴퓨터를 제거하려면 다음을 수행합니다.

1. 페이지 아래쪽에서 **등록 취소** 단추를 클릭합니다.

    ![보호 사용 안 함](./media/backup-azure-manage-vms/unregister-button.png)

    화면 아래쪽에 확인을 요청하는 토스트 알림이 표시됩니다. **예**를 클릭하여 계속합니다.

    ![보호 사용 안 함](./media/backup-azure-manage-vms/confirm-unregister.png)

## 백업 데이터 삭제
다음 중 하나에 가상 컴퓨터와 연결된 백업 데이터를 삭제할 수 있습니다.

- 보호 중지 작업 중
- 가상 컴퓨터에서 보호 중지 작업이 완료된 후

**백업 중지** 작업 작업이 성공적으로 완료된 후 "보호 중지됨" 상태인 백업 데이터를 가상 컴퓨터에서 삭제하려면

1. **보호되는 항목** 페이지로 이동하고 **Azure 가상 컴퓨터**를 유형으로 선택한 다음 **선택** 단추를 클릭합니다.

    ![VM 유형](./media/backup-azure-manage-vms/vm-type.png)

2. 가상 컴퓨터를 선택합니다. 가상 컴퓨터가 **보호 중지됨** 상태로 전환됩니다.

    ![보호 중지됨](./media/backup-azure-manage-vms/protection-stopped-b.png)

3. 페이지 맨 아래에 있는 **삭제** 단추를 클릭합니다.

    ![백업 삭제](./media/backup-azure-manage-vms/delete-backup.png)

4. **백업 데이터 삭제** 마법사에서 백업 데이터를 삭제하는 이유를 선택하고(권장) **제출**을 클릭합니다.

    ![백업 데이터 삭제](./media/backup-azure-manage-vms/delete-backup-data.png)

5. 선택한 가상 컴퓨터의 백업 데이터를 삭제하는 작업이 생성됩니다. **작업 보기**를 클릭하여 작업 페이지에서 해당 작업을 확인합니다.

    ![데이터 삭제 성공](./media/backup-azure-manage-vms/delete-data-success.png)

    작업이 완료되면 가상 컴퓨터에 해당하는 항목이 **보호되는 항목** 페이지에서 제거됩니다.


###대시보드

**대시보드** 페이지에서 Azure 가상 컴퓨터, 저장소 및 최근 24시간 동안의 관련 작업에 대한 정보를 검토할 수 있습니다. 백업 상태 및 연결된 백업 오류를 볼 수 있습니다.

  ![대시보드](./media/backup-azure-manage-vms/dashboard-protectedvms.png)
 

<!---HONumber=July15_HO3-->