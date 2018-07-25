---
title: Azure VM 백업 스택 V2로 업그레이드
description: VM 백업 스택, Resource Manager 배포 모델에 대한 업그레이드 프로세스 및 FAQ
services: backup
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 7/18/2018
ms.author: trinadhk
ms.openlocfilehash: c9dff77f6b9fffc02ec94caa3454500772651195
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136903"
---
# <a name="upgrade-to-azure-vm-backup-stack-v2"></a>Azure VM 백업 스택 V2로 업그레이드

VM(가상 머신) 백업 스택 업그레이드를 위한 Resource Manager 배포 모델에서는 다음과 같이 기능이 개선되었습니다.

* 백업 작업의 일부로 만든 스냅숏을 데이터 전송이 완료될 때까지 기다리지 않고 복구에 사용할 수 있습니다. 이에 따라, 복원을 트리거하기 전에 스냅숏이 저장소에 복사되는 대기 시간이 줄었습니다. 또한 첫 번째 백업을 제외한 프리미엄 VM을 백업하기 위한 추가 저장소 요구 사항이 없어졌습니다.  

* 스냅숏을 로컬에 7일 동안 유지하여 백업 및 복원 시간을 단축합니다.

* 최대 4TB의 디스크를 지원합니다.

* 복원할 때 관리되지 않는 VM의 원래 저장소 계정을 사용할 수 있습니다. 이 기능은 VM의 디스크가 저장소 계정에 분산된 경우에도 지원됩니다. 이는 다양한 VM 구성에 대한 복원 작업을 가속화합니다.
    > [!NOTE]
    > 이 기능은 원래 VM을 재정의하는 것과는 다릅니다. 
    >

## <a name="whats-changing-in-the-new-stack"></a>새 스택의 변경 사항
현재 백업 작업은 다음과 같은 두 단계로 구성됩니다.
1.  VM 스냅숏 만들기 
2.  VM 스냅숏을 Azure Backup 자격 증명 모음으로 전송 

복구 지점은 1단계 및 2단계가 완료된 후에만 만들어지는 것으로 간주됩니다. 새 스택의 일환으로, 스냅숏이 완료되는 즉시 복구 지점이 생성됩니다. 동일한 복원 흐름을 사용하여 이 복구 지점에서 복원할 수도 있습니다. Azure Portal에서 “스냅숏”을 복구 지점 형식으로 사용하여 이 복구 지점을 식별할 수 있습니다. 스냅숏이 자격 증명 모음에 전송되면 복구 지점 형식이 “스냅숏 및 자격 증명 모음”으로 변경됩니다. 

![VM 백업 스택 Resource Manager 배포 모델의 백업 작업 - 저장소 및 자격 증명 모음](./media/backup-azure-vms/instant-rp-flow.jpg) 

기본적으로 스냅숏은 7일 동안 보존됩니다. 이 기능을 사용하면 이러한 스냅숏에서 복원이 더 빨리 완료될 수 있습니다. 자격 증명 모음에서 고객의 저장소 계정으로 데이터를 다시 복사하는 데 필요한 시간이 줄어듭니다. 

## <a name="considerations-before-upgrade"></a>업그레이드 전 고려 사항

* VM 백업 스택의 업그레이드는 단방향이므로, 모든 백업이 이 흐름을 따릅니다. 구독 수준에서 변경이 수행되기 때문에 모든 VM이 이 흐름을 따릅니다. 추가된 모든 새 기능은 동일한 스택을 기반으로 합니다. 현재, 정책 수준에서 스택을 제어할 수 없습니다.

* 스냅숏은 복구 지점 생성을 강화하고 복원 작업을 가속화하기 위해 로컬에 저장됩니다. 따라서 7일 동안 만든 스냅숏에 해당하는 저장소 비용이 발생합니다.

* 증분 스냅숏은 페이지 Blob으로 저장됩니다. 관리되지 않는 디스크를 사용하는 모든 고객의 경우 고객의 로컬 저장소 계정에 저장된 7일 동안의 스냅숏에 대해 요금이 청구됩니다. 현재 가격 책정 모델에서는 관리 디스크에 대해 고객에게 부과되는 비용이 없습니다.

* 스냅숏 복구 지점에서 프리미엄 VM을 복원하는 경우, VM을 만드는 동안 임시 저장소 위치가 사용됩니다.

* 프리미엄 저장소 계정의 경우, 빠른 복구 지점에 대해 만든 스냅숏이 10TB의 할당된 공간 한도에 포함됩니다.

## <a name="upgrade"></a>업그레이드
### <a name="the-azure-portal"></a>Azure 포털
Azure Portal을 사용하는 경우 자격 증명 모음 대시보드에 알림이 표시됩니다. 이 알림은 대량 디스크 지원과 백업 및 복원 속도 개선과 관련됩니다.

![VM 백업 스택 Resource Manager 배포 모델의 백업 작업 - 지원 알림](./media/backup-azure-vms/instant-rp-banner.png) 

새 스택으로 업그레이드하는 화면을 열려면 배너를 선택합니다. 

![VM 백업 스택 Resource Manager 배포 모델의 백업 작업 - 업그레이드](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
관리자 권한 PowerShell 터미널에서 다음 cmdlet을 실행합니다.
1.  Azure 계정 로그인: 

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  미리 보기에 등록할 구독을 선택합니다.

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  이 구독을 비공개 미리 보기에 등록합니다.

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="verify-that-the-upgrade-is-finished"></a>업그레이드 완료 확인
관리자 권한 PowerShell 터미널에서 다음 cmdlet을 실행합니다.

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
```

“등록됨”이 표시되면 구독이 VM 백업 스택 Resource Manager 배포 모델로 업그레이드된 것입니다.

## <a name="frequently-asked-questions"></a>질문과 대답

다음 질문과 대답은 포럼 및 고객 질문에서 수집되었습니다.

### <a name="will-upgrading-to-v2-impact-current-backups"></a>V2로 업그레이드하면 현재 백업이 영향을 받나요?

V2로 업그레이드하는 경우, 현재 백업에는 영향을 주지 않으며, 환경을 다시 구성할 필요가 없습니다. 업그레이드해도 백업 환경은 계속해서 이전처럼 작동합니다.

### <a name="what-does-it-cost-to-upgrade-to-azure-backup-stack-v2"></a>Azure Backup 스택 v2로 업그레이드하는 비용은 얼마인가요?

Azure Backup 스택 v2로 업그레이드하는 데 필요한 비용은 없습니다. 스냅숏은 복구 지점 생성 및 복원 작업을 가속화하기 위해 로컬에 저장됩니다. 따라서 7일 동안 만든 스냅숏에 해당하는 저장소 비용이 발생합니다.

### <a name="does-upgrading-to-stack-v2-increase-the-premium-storage-account-snapshot-limit-by-10-tb"></a>스택 v2로 업그레이드하면 프리미엄 저장소 계정 스냅숏 한도가 10TB 증가하나요?

아니요.

### <a name="in-premium-storage-accounts-do-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Premium Storage 계정에서 빠른 복구 지점에 대해 만든 스냅숏이 10TB 스냅숏 한도에 포함되나요?

예, 프리미엄 저장소 계정의 경우, 빠른 복구 지점에 대해 만든 스냅숏이 10TB의 할당된 공간에 포함됩니다.

### <a name="how-does-the-snapshot-work-during-the-seven-day-period"></a>스냅숏은 7일 동안 어떻게 작동하나요? 

매일 새 스냅숏이 생성되므로, 7개의 개별 스냅숏이 있습니다. 첫째 날에는 서비스에서 복사를 수행하지 **않고**, 다음 6일 동안 변경 내용을 추가합니다.

### <a name="what-happens-if-the-default-resource-group-is-deleted-accidentally"></a>기본 리소스 그룹을 실수로 삭제하면 어떻게 되나요?

리소스 그룹을 삭제하면 해당 지역의 모든 보호된 VM에 대한 빠른 복구 지점이 손실됩니다. 다음 백업이 수행될 때 리소스 그룹이 다시 생성되며, 백업이 예상대로 계속됩니다. 이 기능은 빠른 복구 지점에만 국한되지 않습니다.

### <a name="can-i-delete-the-default-resource-group-created-for-instant-recovery-points"></a>빠른 복구 지점에 대해 생성된 기본 리소스 그룹을 삭제할 수 있나요?

Azure Backup 서비스는 관리되는 리소스 그룹을 만듭니다. 현재, 리소스 그룹을 변경하거나 수정할 수 없습니다. 또한 리소스 그룹을 잠그면 안 됩니다. 이 지침은 V2 스택에만 국한되지 않습니다.
 
### <a name="is-a-v2-snapshot-an-incremental-snapshot-or-full-snapshot"></a>v2 스냅숏은 증분 스냅숏인가요, 아니면 전체 스냅숏인가요?

증분 스냅숏은 비관리형 디스크에 사용됩니다. 관리 디스크에 대한 스냅숏은 전체 스냅숏입니다.
