---
title: Azure 인스턴트 복원 기능
description: VM 백업 스택, Resource Manager 배포 모델에 대한 Azure 인스턴트 복원 기능 및 FAQ
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: sogup
ms.openlocfilehash: 1a25a9c3e0d099349286476f0ae3791efee1642f
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56452817"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Azure Backup 인스턴트 복원 기능을 사용하여 향상된 백업 및 복원 성능 얻기

> [!NOTE]
> 사용자의 피드백에 따라 Azure Stack 기능과의 혼동을 줄이기 위해 **VM 백업 스택 V2**의 이름을 **즉시 복원**으로 바꾸었습니다.

인스턴트 복원의 새 모델은 다음과 같은 기능 개선 사항을 제공합니다.

* 자격 증명 모음으로의 데이터 전송이 완료될 때까지 기다리지 않고 백업 작업의 일부로 만든 스냅숏을 복구에 사용할 수 있습니다. 이에 따라, 복원을 트리거하기 전에 스냅숏이 저장소에 복사되는 대기 시간이 줄었습니다.
* 기본적으로 스냅숏을 로컬에 2일 동안 보존하여 백업 및 복원 시간을 단축합니다. 이 기본 자격 증명 모음은 1~5일 사이의 임의 값으로 구성할 수 있습니다.
* 최대 4TB까지 디스크 크기를 지원합니다.
* 표준 SSD 디스크를 지원합니다.
*   복원할 때 비관리형 VM의 원래 스토리지 계정(디스크당)을 사용할 수 있습니다. 이 기능은 VM의 디스크가 저장소 계정에 분산된 경우에도 지원됩니다. 이 기능을 사용하면 다양한 VM 구성에서 복원 작업이 가속화됩니다.



## <a name="whats-new-in-this-feature"></a>이 기능의 새로운 사항

현재 백업 작업은 다음과 같은 두 단계로 구성됩니다.

1.  VM 스냅숏 만들기
2.  VM 스냅숏을 Azure Recovery Services 자격 증명 모음으로 전송

복구 지점은 1단계 및 2단계가 완료된 후에만 만들어지는 것으로 간주됩니다. 이 업그레이드의 일부로, 스냅숏이 완료되는 즉시 복구 지점이 생성되며 스냅숏 유형의 이 복구 지점을 사용하여 동일한 복원 흐름으로 복원을 수행할 수 있습니다. “스냅숏”을 복구 지점 유형으로 사용하여 Azure Portal에서 이 복구 지점을 식별할 수 있으며, 스냅숏이 자격 증명 모음으로 전송된 후 복구 지점 유형이 “스냅숏 및 자격 증명 모음”으로 바뀝니다.

![VM 백업 스택 Resource Manager 배포 모델의 백업 작업 - 저장소 및 자격 증명 모음](./media/backup-azure-vms/instant-rp-flow.png)

스냅숏은 7일 동안 보존됩니다. 이 기능을 사용하면 복원 시간을 줄여 이러한 스냅숏에서 복원 작업을 수행할 수 있습니다. 이 기능은 관리 디스크 사용자의 경우 백업 데이터에서 관리 디스크를 만드는 반면, 비관리 디스크 시나리오의 경우 자격 증명 모음에서 사용자 스토리지 계정으로 데이터를 다시 변환하고 복사하는 데 필요한 시간을 줄입니다.

## <a name="feature-considerations"></a>기능 고려 사항

* 스냅숏이 디스크와 함께 저장되어 복구 지점 생성을 강화하고 복원 작업을 가속화합니다. 따라서 이 기간에 만든 스냅숏에 해당하는 스토리지 비용이 발생합니다.
* 증분 스냅숏은 페이지 Blob으로 저장됩니다. 비관리 디스크를 사용하는 모든 사용자의 경우 로컬 스토리지 계정에 저장된 스냅숏에 대한 요금이 청구됩니다. 관리형 VM 백업에서 사용하는 복원 지점 컬렉션이 기본 스토리지 수준에서 Blob 스냅숏을 사용하므로 관리 디스크의 경우 Blob 스냅숏 가격 책정에 해당하는 비용이 표시되고 증분됩니다.
* Premium Storage 계정의 경우, 빠른 복구 지점에 대해 만든 스냅숏이 10TB의 할당된 공간 한도에 포함됩니다.
* 복원 요구 사항을 기준으로 스냅숏 보존을 구성할 수 있습니다. 요구 사항에 따라, 아래에 설명된 대로 백업 정책 블레이드에서 스냅숏 보존을 최소 1일로 설정할 수 있습니다. 그러면 복원을 자주 수행하지 않는 경우 스냅숏 보존 비용을 절감하는 데 도움이 됩니다.


>[!NOTE]
>이 즉시 복원 업그레이드를 사용하면 모든 고객(**새 고객과 기존 고객 모두 포함**)의 스냅숏 보존 기간이 기본값인 2일로 설정됩니다. 그러나 요구 사항에 따라 기간을 1~5일 사이의 임의 값으로 설정할 수 있습니다.


## <a name="cost-impact"></a>비용 영향

인스턴트 복구에 사용되는 증분 스냅숏은 VM의 스토리지 계정에 저장됩니다. 증분 스냅숏은 스냅숏이 사용하는 공간이 스냅숏이 생성된 후 작성된 페이지에서 사용하는 공간과 같음을 의미합니다. 청구는 계속해서 스냅숏이 사용하는 공간 GB당 부과되며, GB당 가격은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/managed-disks/)에 언급된 내용과 동일합니다.


## <a name="upgrading-to-instant-restore"></a>인스턴트 복원으로 업그레이드

Azure Portal을 사용하는 경우 자격 증명 모음 대시보드에 알림이 표시됩니다. 이 알림은 대량 디스크 지원과 백업 및 복원 속도 개선과 관련됩니다.
인스턴트 복원으로 업그레이드하는 화면을 열려면 배너를 선택합니다.

![VM 백업 스택 Resource Manager 배포 모델의 백업 작업 - 지원 알림](./media/backup-azure-vms/instant-rp-banner.png)

아래 스크린샷과 같이 **업그레이드**를 클릭합니다.

![VM 백업 스택 Resource Manager 배포 모델의 백업 작업 - 업그레이드](./media/backup-azure-vms/instant-rp.png)

또는 자격 증명 모음의 **속성** 페이지로 이동하여 **VM 백업 스택** 아래의 **업그레이드** 옵션을 사용할 수 있습니다.

![VM 백업 스택의 백업 작업 - 속성 페이지](./media/backup-azure-vms/instant-restore-capability-properties.png)


## <a name="configure-snapshot-retention-using-azure-portal"></a>Azure Portal을 사용하여 스냅숏 보존 구성
이 옵션은 현재 미국 중서부, 인도 남부 및 오스트레일리아 동부에서 사용할 수 있습니다.

업그레이드된 사용자의 경우 Azure Portal의 **VM 백업 정책** 블레이드에서, **즉시 복원** 섹션 아래에 필드가 추가된 것을 확인할 수 있습니다. 특정 백업 정책과 연결된 모든 VM에 대해 **VM 백업 정책** 블레이드의 스냅숏 보존 기간을 변경할 수 있습니다.

![즉시 복원 기능](./media/backup-azure-vms/instant-restore-capability.png)

## <a name="upgrade-to-instant-restore-using-powershell"></a>PowerShell을 사용하여 인스턴트 복원으로 업그레이드

인스턴트 복원으로 셀프 서비스 및 업그레이드를 하려는 경우 관리자 권한 PowerShell 터미널에서 다음 cmdlet을 실행합니다.

1.  Azure 계정 로그인:

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  등록하려는 구독을 선택합니다.

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  이 구독을 등록합니다.

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="upgrade-to-instant-restore-using-cli"></a>CLI를 사용하여 인스턴트 복원으로 업그레이드

셸에서 다음 명령을 실행합니다.

1.  Azure 계정 로그인:

    ```
    az login
    ```

2.  등록하려는 구독을 선택합니다.

    ```
    az account set --subscription "Subscription Name"
    ```

3.  이 구독을 등록합니다.

    ```
    az feature register --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
    ```

## <a name="verify-that-the-upgrade-is-successful"></a>업그레이드가 성공했는지 확인

### <a name="powershell"></a>PowerShell
관리자 권한 PowerShell 터미널에서 다음 cmdlet을 실행합니다.

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" -ProviderNamespace Microsoft.RecoveryServices
```

### <a name="cli"></a>CLI
셸에서 다음 명령을 실행합니다.

```
az feature show --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
```

“등록됨”으로 표시되면 구독이 인스턴트 복원으로 업그레이드됩니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="what-are-the-cost-implications-of-instant-restore"></a>인스턴트 복원은 비용에 어떤 영향을 주나요?
스냅숏이 디스크와 함께 저장되어 복구 지점 생성 및 복원 작업을 가속화합니다. 따라서 VM 백업 정책의 일부로 선택된 스냅숏 보존에 해당하는 스토리지 비용이 표시됩니다.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Premium Storage 계정에서 인스턴트 복구 지점에 대해 만든 스냅숏이 10TB 스냅숏 한도에 포함되나요?
예, Premium Storage 계정의 경우, 인스턴트 복구 지점에 대해 만든 스냅숏이 10TB의 할당된 스냅숏 공간에 포함됩니다.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>스냅숏 보존은 5일 동안 어떻게 작동하나요?
매일 새 스냅숏이 생성되며, 5개의 개별 증분 스냅숏이 있습니다. 스냅숏 크기는 데이터 변동에 따라 달라지며, 대부분의 경우 약 2%-7%입니다.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>인스턴트 복원 스냅숏은 증분 스냅숏인가요, 아니면 전체 스냅숏인가요?
인스턴트 복원 기능의 일부로 생성되는 스냅숏은 증분 스냅숏입니다.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>인스턴트 복원 기능으로 인한 대략적인 비용 증가는 어떻게 계산할 수 있나요?
VM 변동에 따라 다릅니다. 안정된 상태에서는 비용 증가를 스냅숏 보존 기간 * VM당 일일 변동 * GB당 스토리지 비용으로 가정할 수 있습니다.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>복원 지점의 복구 유형이 “스냅숏 및 자격 증명 모음”이고 복원 작업을 수행하는 경우 어떤 복구 유형이 사용되나요?
복구 유형이 “스냅숏 및 자격 증명 모음”인 경우 로컬 스냅숏에서 자동으로 복원이 수행되며, 자격 증명 모음에서 수행되는 복원에 비해 훨씬 더 빠릅니다.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>스냅숏(계층 1) 보존 기간보다 짧은 복원 지점(계층 2)의 보존 기간을 선택하면 어떻게 되나요?
새 모델에서는 스냅숏(계층 1)을 삭제하지 않은 경우 복원 지점(계층 2) 삭제를 허용하지 않습니다. 복원 지점(계층 2) 보존 기간을 스냅숏 보존 기간 이상으로 예약하는 것이 좋습니다.

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>백업 정책에 설정된 보존 기간 이후에도 스냅숏이 있는 이유는 무엇인가요?
복구 지점에 스냅숏이 있고 사용 가능한 최신 RP인 경우 다음에 성공한 백업이 있을 때까지 보존됩니다. 이는 VM의 문제로 인해 모든 백업이 실패하는 경우에도 항상 하나 이상의 최신 RP가 있도록 요구하는 현재 설계된 GC 정책을 따른 것입니다. 정상적인 시나리오에서는 만료 후 최대 24시간 이내에 RP가 정리됩니다.
