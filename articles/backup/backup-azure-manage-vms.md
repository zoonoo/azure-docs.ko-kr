
<properties
	pageTitle="Azure 백업 - 가상 컴퓨터 관리 | Microsoft Azure"
	description="Azure 가상 컴퓨터를 관리하는 방법을 알아봅니다."
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/09/2015" ms.author="aashishr"; "jimpark"/>

# 가상 컴퓨터 관리

## 보호된 가상 컴퓨터 관리

보호된 가상 컴퓨터 관리하려면

1. 가상 컴퓨터에 대한 백업 설정을 보고 관리하려면 **보호된 항목** 탭을 클릭합니다.

2. **백업 세부 정보** 탭을 볼 보호된 항목의 이름을 클릭하면 마지막 백업에 대한 정보가 표시됩니다.

    ![가상 컴퓨터 백업](./media/backup-azure-manage-vms/backup-vmdetails.png)

3. 가상 컴퓨터에 대한 백업 정책 설정을 보고 관리하려면 **정책** 탭을 클릭합니다.

    **백업 정책** 탭에는 기존 정책이 표시됩니다. 필요에 따라 수정할 수 있습니다. 새 정책을 만들어야 하는 경우 **정책** 페이지에서 **만들기**를 클릭합니다. 정책을 제거하려는 경우 해당 정책과 연결된 가상 컴퓨터가 없어야 합니다.

    ![가상 컴퓨터 정책](./media/backup-azure-manage-vms/backup-vmpolicy.png)

4. 가상 컴퓨터의 동작 또는 상태에 대한 자세한 정보는 **작업** 페이지에서 확인할 수 있습니다. 자세한 정보를 보려면 목록에서 작업을 클릭하거나 특정 가상 컴퓨터에 대한 작업을 필터링하세요.

    ![작업](./media/backup-azure-manage-vms/backup-job.png)

## 가상 컴퓨터의 주문형 백업
보호를 위해 구성한 후에는 가상 컴퓨터의 주문형 백업을 수행할 수 있습니다. 가상 컴퓨터에 대한 초기 백업이 보류 중인 경우 주문형 백업은 Azure 백업 자격 증명 모음에 가상 컴퓨터의 전체 복사본을 만듭니다. 첫 번째 백업이 완료된 경우 주문형 백업은 이전 백업의 변경 내용만 Azure 백업 자격 증명 모음으로 보냅니다.

가상 컴퓨터의 주문형 백업을 수행하려면

1. **보호되는 항목** 페이지로 이동하고 **Azure 가상 컴퓨터**를 **형식**으로 선택한 다음(선택되지 않은 경우) **선택** 단추를 클릭합니다.

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

가상 컴퓨터와 연결된 백업 데이터를 유지하도록 선택한 경우 백업 데이터를 사용하여 가상 컴퓨터를 복원할 수 있습니다. 이러한 가상 컴퓨터의 가격 정보를 보려면 [여기](http://azure.microsoft.com/pricing/details/backup/)를 클릭하세요.

가상 컴퓨터에 대한 보호를 중지하려면

1. **보호되는 항목** 페이지로 이동하고 **Azure 가상 컴퓨터**를 필터 형식으로 선택한 다음(선택되지 않은 경우) **선택** 단추를 클릭합니다.

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

    ![Reprotected VM](./media/backup-azure-manage-vms/reprotected-status.png)

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

**백업 중지** 작업이 성공적으로 완료된 후 *보호 중지됨* 상태인 백업 데이터를 가상 컴퓨터에서 삭제하려면

1. **보호되는 항목** 페이지로 이동하고 **Azure 가상 컴퓨터**를 *형식*으로 선택한 다음 **선택** 단추를 클릭합니다.

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

## 대시보드
**대시보드** 페이지에서 Azure 가상 컴퓨터, 저장소 및 최근 24시간 동안의 관련 작업에 대한 정보를 검토할 수 있습니다. 백업 상태 및 연결된 백업 오류를 볼 수 있습니다.

    ![Dashboard](./media/backup-azure-manage-vms/dashboard-protectedvms.png)

## 작업 감사
Azure 백업은 백업 자격 증명 모음에서 관리 작업이 무엇을 수행하는지 정확하게 볼 수 있도록고객에 의해 트리거된 백업 작업의 "작업 로그"를 검토합니다. 작업 로그를 사용하면 백업 작업에 대한 post-mortem 및 감사 지원이 가능합니다.

다음 작업은 작업 로그에 기록됩니다.

- 등록
- 등록 취소
- 보호 구성
- 백업(예약된 백업 및 BackupNow 통해 요청된 백업 모두)
- 복원
- 보호 중지
- 백업 데이터 삭제
- 정책 추가
- 정책 삭제
- 정책 업데이트
- 작업 취소

백업 자격 증명 모음에 해당하는 작업 로그를 보려면

1. Azure 포털에서 **관리 서비스**로 이동한 다음 **작업 로그** 탭을 클릭합니다.

    ![작업 로그](./media/backup-azure-manage-vms/ops-logs.png)

2. 필터에서 **백업**을 *형식*으로 선택하고 *서비스 이름*에서 자격 증명 모음을 지정하고 **제출**을 클릭합니다.

    ![작업 로그 필터](./media/backup-azure-manage-vms/ops-logs-filter.png)

3. 작업 로그에서 작업을 선택하고 **세부 정보**를 클릭하여 작업에 해당하는 세부 정보를 확인합니다.

    ![작업 로그 Fetching 세부 정보](./media/backup-azure-manage-vms/ops-logs-details.png)

    **세부 정보 마법사**는 트리거된 작업, 작업 ID, 이 작업은 트리거되는 리소스 및 작업의 시작 시간에 대한 정보를 포함합니다.

    ![작업 세부 정보](./media/backup-azure-manage-vms/ops-logs-details-window.png)

## 경고 알림
포털에서 작업에 대한 사용자 지정 경고 알림을 받을 수 있습니다. 작업 로그 이벤트에서 PowerShell 기반 경고 규칙을 정의하여 수행됩니다.

Azure 리소스 모드에서 경고 작업에 기반한 이벤트입니다. 승격된 명령 모드에서 다음 cmdlet을 실행하여 Azure 리소스 모드로 전환합니다.

```
PS C:\> Switch-AzureMode AzureResourceManager
```

사용자 지정 알림을 정의하여 백업 실패에 대해 경고하려면 예제 명령은 다음과 같습니다.

```
PS C:\> Add-AlertRule -Operator GreaterThanOrEqual -Threshold 1 -ResourceId '/subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/microsoft.backupbvtd2/BackupVault/trinadhVault' -EventName Backup  -EventSource Administrative -Level Error -OperationName 'Microsoft.Backup/backupVault/Backup' -ResourceProvider Microsoft.Backup -Status Failed  -SubStatus Failed -RuleType Event -Location eastus -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -Name Backup-Failed -Description 'Backup failed for one of the VMs in vault trinadhkVault' -CustomEmails 'contoso@microsoft.com' -SendToServiceOwners
```

**ResourceId**: 이 섹션에서 설명한 것처럼 작업 로그 팝업에서 가져올 수 있습니다. 작업의 세부 정보 팝업 창에서 ResourceUri는 이 cmdlet를 위해 제공된 ResourceId입니다.

**EventName**: IaaS VM 백업에 대한 경고의 경우 지원되는 값은 다음과 같습니다. Register,Unregister,ConfigureProtection,Backup,Restore,StopProtection,DeleteBackupData,CreateProtectionPolicy,DeleteProtectionPolicy,UpdateProtectionPolicy

**수준**: 지원되는 값은 정보, 오류입니다. 실패한 작업에 대한 경고의 경우 오류를 사용하고 성공한 작업에 대한 경고의 경우 정보를 사용합니다.

**OperationName**: EventName이 위에서 설명한 것처럼 "Microsoft.Backup/backupvault/<EventName>" 형식입니다.

**상태**: 지원되는 값은 시작함, 성공함 및 실패함입니다. 정보를 Succeeded 상태에 대한 수준으로 유지하는 것이 좋습니다.

**하위 상태**: 백업 작업에 대한 상태와 동일합니다.

**RuleType**: 백업 경고가 이벤트를 기반으로 한 것처럼 *이벤트*로 유지합니다.

**ResourceGroup**: 작업이 트리거되는 리소스의 ResourceGroup입니다. ResourceId 값에서 얻을 수 있습니다. ResourceId 값에서 */resourceGroups/* 및 */providers/* 필드 간의 값은 ResourceGroup에 대한 값입니다.

**이름**: 경고 규칙의 이름입니다.

**설명**: 경고 규칙에 대한 설명입니다.

**CustomEmails**: 경고 알림을 보내려는 사용자 지정 전자 메일 주소를 지정합니다.

**SendToServiceOwners**: 이 옵션은 구독의 모든 관리자 및 공동 관리자에게 경고 알림을 보냅니다.

샘플 경고 메일은 다음과 유사합니다.

샘플 헤더:

![경고 헤더](./media/backup-azure-manage-vms/alert-header.png)

경고 메일의 샘플 본문:

![경고 본문](./media/backup-azure-manage-vms/alert-body.png)

### 경고에 대한 제한
이벤트 기반 경고는 다음과 같은 제한 사항에 종속됩니다.

1. 백업 자격 증명 모음의 모든 가상 컴퓨터에서 경고를 유발합니다. 이에 사용자 지정으로 백업 자격 증명 모음에서 가상 컴퓨터의 특정 집합에 대한 경고를 가져올 수 없습니다.
2. 다음 경고 기간에서 트리거된 경고와 일치하는 이벤트가 있는 경우 경고는 자동으로 해결됩니다. AlertRule cmdlet에서 *WindowSize* 매개 변수를 사용하여 경고 트리거 기간을 설정합니다.

## 다음 단계

- [Azure VM 복원](backup-azure-restore-vms.md)

<!---HONumber=Sept15_HO2-->