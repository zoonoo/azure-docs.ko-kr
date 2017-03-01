---
title: "Azure 가상 컴퓨터 백업 관리 및 모니터링 | Microsoft Docs"
description: "Azure 가상 컴퓨터 백업을 관리하고 모니터링하는 방법에 관해 알아봅니다."
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 4372944e-d33a-4f6a-bd5f-d04af2842713
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2016
ms.author: trinadhk;markgal;
translationtype: Human Translation
ms.sourcegitcommit: e12d533ac5befe020c0aad4aa64ca9ed50349c3d
ms.openlocfilehash: bea1012fcd63b15ebfb5137dcfbd7e868c4e060f
ms.lasthandoff: 02/17/2017


---
# <a name="manage-and-monitor-azure-virtual-machine-backups"></a>Azure 가상 컴퓨터 백업 관리 및 모니터링
> [!div class="op_single_selector"]
> * [Azure VM 백업 관리](backup-azure-manage-vms.md)
> * [클래식 VM 백업 관리](backup-azure-manage-vms-classic.md)
>
>

이 문서에서는 Azure에서 보호되는 클랙식 모델 가상 컴퓨터에 대한 일반적인 관리 및 모니터링 관련 정보를 제공합니다.  

> [!NOTE]
> Azure에는 리소스를 만들고 작업하기 위한 두 가지 배포 모델인 [리소스 관리자와 클래식](../azure-resource-manager/resource-manager-deployment-model.md)모델이 있습니다. 클래식 배포 모델 VM 작업에 대한 자세한 내용은 [Azure 가상 컴퓨터를 백업하기 위한 환경 준비](backup-azure-vms-prepare.md) 를 참조하세요.
>
>

## <a name="manage-protected-virtual-machines"></a>보호된 가상 컴퓨터 관리
보호된 가상 컴퓨터 관리하려면

1. 가상 컴퓨터에 대한 백업 설정을 보고 관리하려면 **보호된 항목** 탭을 클릭합니다.
2. **백업 세부 정보** 탭을 볼 보호된 항목의 이름을 클릭하면 마지막 백업에 대한 정보가 표시됩니다.

    ![가상 컴퓨터 백업](./media/backup-azure-manage-vms/backup-vmdetails.png)
3. 가상 컴퓨터에 대한 백업 정책 설정을 보고 관리하려면 **정책** 탭을 클릭합니다.

    ![가상 컴퓨터 정책](./media/backup-azure-manage-vms/manage-policy-settings.png)

    **백업 정책** 탭에는 기존 정책이 표시됩니다. 필요에 따라 수정할 수 있습니다. 새 정책을 만들어야 하는 경우 **정책** 페이지에서 **만들기**를 클릭합니다. 정책을 제거하려는 경우 해당 정책과 연결된 가상 컴퓨터가 없어야 합니다.

    ![가상 컴퓨터 정책](./media/backup-azure-manage-vms/backup-vmpolicy.png)
4. 가상 컴퓨터의 동작 또는 상태에 대한 자세한 정보는 **작업** 페이지에서 확인할 수 있습니다. 자세한 정보를 보려면 목록에서 작업을 클릭하거나 특정 가상 컴퓨터에 대한 작업을 필터링하세요.

    ![작업](./media/backup-azure-manage-vms/backup-job.png)

## <a name="on-demand-backup-of-a-virtual-machine"></a>가상 컴퓨터의 주문형 백업
보호를 위해 구성한 후에는 가상 컴퓨터의 주문형 백업을 수행할 수 있습니다. 가상 컴퓨터에 대한 초기 백업이 보류 중인 경우 주문형 백업은 Azure 백업 자격 증명 모음에 가상 컴퓨터의 전체 복사본을 만듭니다. 첫 번째 백업이 완료된 경우 주문형 백업은 이전 백업의 변경 내용(즉, 항상 증분)만 Azure 백업 자격 증명 모음으로 보냅니다.

> [!NOTE]
> 주문형 백업의 보존 범위는 VM에 해당하는 백업 정책의 일 단위 보존에 대해 지정된 보존 값으로 설정됩니다.  
>
>

가상 컴퓨터의 주문형 백업을 수행하려면

1. **보호 항목** 페이지로 이동하고 **Azure 가상 컴퓨터**를 **형식**으로 선택한(선택되지 않은 경우) 다음 **선택** 단추를 클릭합니다.

    ![VM 유형](./media/backup-azure-manage-vms/vm-type.png)
2. 주문형 백업을 수행할 가상 컴퓨터를 선택하고 페이지 맨 아래에 있는 **지금 백업** 단추를 클릭합니다.

    ![지금 백업](./media/backup-azure-manage-vms/backup-now.png)

    선택한 가상 컴퓨터에 대한 백업 작업이 생성됩니다. 이 작업을 통해 만든 복구 지점의 보존 범위는 가상 컴퓨터와 연결된 정책에 지정된 범위와 같습니다.

    ![백업 작업 만들기](./media/backup-azure-manage-vms/creating-job.png)

   > [!NOTE]
   > 가상 컴퓨터와 연결된 정책을 보려면 **보호되는 항목** 페이지에서 가상 컴퓨터로 드릴다운하고 백업 정책 탭으로 이동합니다.
   >
   >
3. 작업이 생성된 후에는 알림 표시줄의 **작업 보기** 단추를 클릭하여 작업 페이지에서 해당 작업을 확인할 수 있습니다.

    ![백업 작업 생성됨](./media/backup-azure-manage-vms/created-job.png)
4. 작업이 성공적으로 완료되면 가상 컴퓨터를 복원하는 데 사용할 수 있는 복구 지점이 생성됩니다. 또한 **보호되는 항목** 페이지에서 복구 지점 열 값이 1씩 증가합니다.

## <a name="stop-protecting-virtual-machines"></a>가상 컴퓨터 보호 중지
다음 옵션으로 가상 컴퓨터의 향후 백업을 중지하도록 선택할 수 있습니다.

* Azure 백업 자격 증명 모음에 가상 컴퓨터와 연결된 백업 데이터 유지
* 가상 컴퓨터와 연결된 백업 데이터 삭제

가상 컴퓨터와 연결된 백업 데이터를 유지하도록 선택한 경우 백업 데이터를 사용하여 가상 컴퓨터를 복원할 수 있습니다. 이러한 가상 컴퓨터의 가격 정보를 보려면 [여기](https://azure.microsoft.com/pricing/details/backup/)를 클릭하세요.

가상 컴퓨터에 대한 보호를 중지하려면

1. **보호 항목** 페이지로 이동하고 **Azure 가상 컴퓨터**를 필터 형식으로 선택한(선택되지 않은 경우) 다음 **선택** 단추를 클릭합니다.

    ![VM 유형](./media/backup-azure-manage-vms/vm-type.png)
2. 가상 컴퓨터를 선택하고 페이지 맨 아래에 있는 **보호 중지** 를 클릭합니다.

    ![보호 중지](./media/backup-azure-manage-vms/stop-protection.png)
3. 기본적으로 Azure 백업은 가상 컴퓨터와 연결된 백업 데이터를 삭제하지 않습니다.

    ![보호 중지 확인](./media/backup-azure-manage-vms/confirm-stop-protection.png)

    백업 데이터를 삭제하려면 확인란을 선택합니다.

    ![확인란](./media/backup-azure-manage-vms/checkbox.png)

    백업을 중지하는 이유를 선택합니다. 선택 사항이지만, 이유를 제공하면 Azure 백업이 피드백에 따라 작동하고 고객 시나리오의 우선 순위를 지정하는 데 도움이 됩니다.
4. **제출** 단추를 클릭하여 **보호 중지** 작업을 제출합니다. **작업 보기**를 클릭하여 **작업** 페이지에서 해당 작업을 확인합니다.

    ![보호 중지](./media/backup-azure-manage-vms/stop-protect-success.png)

    **보호 중지** 마법사에서 **연결된 백업 데이터 삭제** 옵션을 선택하지 않은 경우 작업 완료 후 보호 상태는 **보호 중지됨**으로 바뀝니다. 명시적으로 삭제될 때까지 Azure 백업을 사용하여 데이터를 유지합니다. **보호 항목** 페이지에서 가상 컴퓨터를 선택하고 **삭제**를 클릭하면 언제든지 데이터를 삭제할 수 있습니다.

    ![보호 중지됨](./media/backup-azure-manage-vms/protection-stopped-status.png)

    **연결된 백업 데이터 삭제** 옵션을 선택한 경우 가상 컴퓨터가 **보호 항목** 페이지에 포함되지 않습니다.

## <a name="re-protect-virtual-machine"></a>가상 컴퓨터 다시 보호
**보호 중지**에서 **연결된 백업 데이터 삭제** 옵션을 선택하지 않은 경우 등록된 가상 컴퓨터의 백업과 유사한 단계를 수행하면 해당 가상 컴퓨터를 다시 보호할 수 있습니다. 보호하고 나면 이 가상 컴퓨터는 보호를 중지하기 전에 유지된 백업 데이터와 다시 보호한 후 생성된 복구 지점을 갖게 됩니다.

다시 보호한 후에 **보호 중지** 이전의 복구 지점이 있는 경우 가상 컴퓨터의 보호 상태는 **보호됨**으로 바뀝니다.

  ![다시 보호된 VM](./media/backup-azure-manage-vms/reprotected-status.png)

> [!NOTE]
> 가상 컴퓨터를 다시 보호하는 경우 가상 컴퓨터가 처음에 보호된 정책과 다른 정책을 선택할 수 있습니다.
>
>

## <a name="unregister-virtual-machines"></a>가상 컴퓨터 등록 취소
백업 자격 증명 모음에서 가상 컴퓨터를 제거하려면 다음을 수행합니다.

1. 페이지 아래쪽에서 **등록 취소** 단추를 클릭합니다.

    ![보호 사용 안 함](./media/backup-azure-manage-vms/unregister-button.png)

    화면 아래쪽에 확인을 요청하는 토스트 알림이 표시됩니다. **예** 를 클릭하여 계속합니다.

    ![보호 사용 안 함](./media/backup-azure-manage-vms/confirm-unregister.png)

## <a name="delete-backup-data"></a>백업 데이터 삭제
다음 중 하나에 가상 컴퓨터와 연결된 백업 데이터를 삭제할 수 있습니다.

* 보호 중지 작업 중
* 가상 컴퓨터에서 보호 중지 작업이 완료된 후

*백업 중지* 작업이 성공적으로 완료된 후 **보호 중지됨** 상태인 백업 데이터를 가상 컴퓨터에서 삭제하려면

1. **보호 항목** 페이지로 이동하고 **Azure 가상 컴퓨터**를 *형식*으로 선택한 다음 **선택** 단추를 클릭합니다.

    ![VM 유형](./media/backup-azure-manage-vms/vm-type.png)
2. 가상 컴퓨터를 선택합니다. 가상 컴퓨터가 **보호 중지됨** 상태로 전환됩니다.

    ![보호 중지됨](./media/backup-azure-manage-vms/protection-stopped-b.png)
3. 페이지 맨 아래에 있는 **삭제** 단추를 클릭합니다.

    ![백업 삭제](./media/backup-azure-manage-vms/delete-backup.png)
4. **백업 데이터 삭제** 마법사에서 백업 데이터를 삭제하는 이유를 선택하고(권장) **제출**을 클릭합니다.

    ![백업 데이터 삭제](./media/backup-azure-manage-vms/delete-backup-data.png)
5. 선택한 가상 컴퓨터의 백업 데이터를 삭제하는 작업이 생성됩니다. **작업 보기** 를 클릭하여 작업 페이지에서 해당 작업을 확인합니다.

    ![데이터 삭제 성공](./media/backup-azure-manage-vms/delete-data-success.png)

    작업이 완료되면 가상 컴퓨터에 해당하는 항목이 **보호되는 항목** 페이지에서 제거됩니다.

## <a name="dashboard"></a>대시보드
**대시보드** 페이지에서 Azure 가상 컴퓨터, 저장소 및 최근 24시간 동안의 관련 작업에 대한 정보를 검토할 수 있습니다. 백업 상태 및 연결된 백업 오류를 볼 수 있습니다.

![대시보드](./media/backup-azure-manage-vms/dashboard-protectedvms.png)

> [!NOTE]
> 대시보드의 값은 24시간마다 한 번 새로 고쳐집니다.
>
>

## <a name="auditing-operations"></a>작업 감사
Azure 백업은  백업 자격 증명 모음에서 관리 작업이 무엇을 수행하는지 정확하게 볼 수 있도록고객에 의해 트리거된 백업 작업의 "작업 로그"를 검토합니다. 작업 로그를 사용하면 백업 작업에 대한 post-mortem 및 감사 지원이 가능합니다.

다음 작업은 작업 로그에 기록됩니다.

* 등록
* 등록 취소
* 보호 구성
* 백업(예약된 백업 및 BackupNow 통해 요청된 백업 모두)
* 복원
* 보호 중지
* 백업 데이터 삭제
* 정책 추가
* 정책 삭제
* 정책 업데이트
* 작업 취소

백업 자격 증명 모음에 해당하는 작업 로그를 보려면

1. Azure Portal에서 **관리 서비스**로 이동한 다음 **작업 로그** 탭을 클릭합니다.

    ![작업 로그](./media/backup-azure-manage-vms/ops-logs.png)
2. 필터에서 **백업**을 *형식*으로 선택하고 *서비스 이름*에서 백업 자격 증명 모음 이름을 지정한 다음 **제출**을 클릭합니다.

    ![작업 로그 필터](./media/backup-azure-manage-vms/ops-logs-filter.png)
3. 작업 로그에서 작업을 선택하고 **세부 정보**를 클릭하여 작업에 해당하는 세부 정보를 확인합니다.

    ![작업 로그 Fetching 세부 정보](./media/backup-azure-manage-vms/ops-logs-details.png)

    **세부 정보 마법사** 는 트리거된 작업, 작업 ID, 이 작업은 트리거되는 리소스 및 작업의 시작 시간에 대한 정보를 포함합니다.

    ![작업 세부 정보](./media/backup-azure-manage-vms/ops-logs-details-window.png)

## <a name="alert-notifications"></a>경고 알림
포털에서 작업에 대한 사용자 지정 경고 알림을 받을 수 있습니다. 작업 로그 이벤트에서 PowerShell 기반 경고 규칙을 정의하여 수행됩니다. *PowerShell 버전 1.3.0 이상*을 사용하는 것이 좋습니다.

사용자 지정 알림을 정의하여 백업 실패에 대해 경고하려면 예제 명령은 다음과 같습니다.

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.Backup/backupVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/microsoft.backupbvtd2/BackupVault/trinadhVault -Actions $actionEmail
```

**ResourceId**: 이 섹션에서 설명한 것처럼 작업 로그 팝업에서 가져올 수 있습니다. 작업의 세부 정보 팝업 창에서 ResourceUri는 이 cmdlet를 위해 제공된 ResourceId입니다.

**OperationName**: "Microsoft.Backup/backupvault/<EventName>" 형식이며, 여기서 EventName은 Register,Unregister,ConfigureProtection,Backup,Restore,StopProtection,DeleteBackupData,CreateProtectionPolicy,DeleteProtectionPolicy,UpdateProtectionPolicy 중하나입니다.

**상태**: 지원되는 값은 시작함, 성공함 및 실패함입니다.

**ResourceGroup**: 작업이 트리거되는 리소스의 ResourceGroup입니다. ResourceId 값에서 얻을 수 있습니다. ResourceId 값에서 */resourceGroups/* 및 */providers/* 필드 사이의 값은 ResourceGroup의 값입니다.

**이름**: 경고 규칙의 이름입니다.

**CustomEmail**: 경고 알림을 보낼 사용자 지정 메일 주소를 지정합니다.

**SendToServiceOwners**: 이 옵션은 구독의 모든 관리자 및 공동 관리자에게 경고 알림을 보냅니다. **New-AzureRmAlertRuleEmail** cmdlet에 사용될 수 있음

### <a name="limitations-on-alerts"></a>경고에 대한 제한
이벤트 기반 경고는 다음과 같은 제한 사항에 종속됩니다.

1. 백업 자격 증명 모음의 모든 가상 컴퓨터에서 경고를 유발합니다. 이에 사용자 지정으로 백업 자격 증명 모음에서 가상 컴퓨터의 특정 집합에 대한 경고를 가져올 수 없습니다.
2. 이 기능은 미리 보기 상태입니다. [자세히 알아보기](../monitoring-and-diagnostics/insights-powershell-samples.md#create-alert-rules)
3. "alerts-noreply@mail.windowsazure.com"에서 경고를 받게 됩니다. 현재는 전자 메일 보낸 사람을 수정할 수 없습니다.

## <a name="next-steps"></a>다음 단계
* [Azure VM 복원](backup-azure-restore-vms.md)

