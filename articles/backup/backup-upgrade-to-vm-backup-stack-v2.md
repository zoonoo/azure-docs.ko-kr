---
title: Azure VM 백업 스택 V2로 업그레이드 | Microsoft Docs
description: VM 백업 스택 V2에 대한 업그레이드 프로세스 및 FAQ
services: backup, virtual-machines
documentationcenter: ''
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: ''
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/08/2018
ms.author: trinadhk, sogup
ms.openlocfilehash: 7e092dc1448a45277e01b1a8c6d2bc0e2a8a22a3
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="upgrade-to-vm-backup-stack-v2"></a>VM 백업 스택 V2로 업그레이드
VM(가상 머신) 백업 스택 V2 업그레이드는 다음과 같은 향상된 기능을 제공합니다.
* 백업 작업의 일부로 만든 스냅숏을 데이터 전송이 완료될 때까지 기다리지 않고 복구에 사용하는 기능.
스냅숏이 자격 증명 모음에 복사된 후 복구가 트리거될 때까지 기다리는 시간이 줄어듭니다. 또한 첫 번째 백업을 제외한 프리미엄 VM을 백업하기 위한 추가 저장소 요구 사항이 사라집니다.  

* 스냅숏을 로컬로 7일 동안 유지하여 백업 및 복원 시간을 단축합니다. 

* 최대 4TB의 디스크를 지원합니다.  

* 관리되지 않는 VM 복원을 수행할 때 원래 저장소 계정을 사용하는 기능(저장소 계정에 배포되는 디스크가 VM에 있더라도). 다양한 구성의 VM을 복원하는 시간이 더 빨라집니다. 
    > [!NOTE] 
    > 원래 VM을 재정의하는 것과는 다릅니다. 
    > 
    >

## <a name="what-is-changing-in-the-new-stack"></a>새 스택에서 변경되는 내용은 무엇입니까?
현재 백업 작업은 다음과 같은 두 단계로 구성됩니다.
1.  VM 스냅숏 만들기. 
2.  VM 스냅숏을 Azure Backup 자격 증명 모음으로 전송. 

복구 지점은 1단계 및 2단계가 완료된 후에만 만들어지는 것으로 간주됩니다. 새 스택의 일환으로, 스냅숏이 완료되는 즉시 복구 지점이 생성됩니다. 동일한 복원 흐름을 사용하여 이 복구 지점에서 복원할 수도 있습니다. Azure Portal에서 "스냅숏"을 복구 지점 형식으로 사용하여 이 복구 지점을 식별할 수 있습니다. 스냅숏이 자격 증명 모음에 전송되면 복구 지점 형식이 “스냅숏 및 자격 증명 모음”으로 변경됩니다. 

![VM 백업 스택 V2의 백업 작업](./media/backup-azure-vms/instant-rp-flow.jpg) 

기본적으로 스냅숏은 7일 동안 유지됩니다. 이러한 스냅숏을 사용하면 자격 증명 모음에서 고객 저장소 계정으로 데이터를 복사하는 데 필요한 시간이 단축되므로 복원이 더 빨리 완료됩니다. 

## <a name="considerations-before-upgrade"></a>업그레이드 전 고려 사항
* 이 업그레이드는 VM 백업 스택의 단방향 업그레이드입니다. 따라서 이후의 모든 백업은 이 흐름으로 이동합니다. **구독 수준에서 설정되므로 모든 VM이 이 흐름으로 이동합니다**. 모든 새 기능 추가는 동일한 스택을 기반으로 합니다. 정책 수준에서 이를 제어하는 기능은 향후 릴리스에서 제공될 예정입니다. 
* 프리미엄 디스크를 사용하는 VM의 경우 첫 번째 백업이 완료될 때까지 저장소 계정에서 VM 크기와 동일한 저장소 공간을 사용할 수 있어야 합니다. 
* 복구 지점 만들기를 향상하고 복원 속도를 높이기 위해 스냅숏이 로컬에 저장되므로 7일 동안 스냅숏에 해당하는 저장소 비용이 표시됩니다.
* 증분 스냅숏은 페이지 Blob으로 저장됩니다. 관리되지 않는 디스크를 사용하는 모든 고객은 고객의 로컬 저장소 계정에 저장된 7일 동안의 스냅숏에 대해 요금이 청구됩니다. 현재 가격 모델에 따라 관리 디스크에는 고객에 대한 비용이 없습니다.
* 프리미엄 VM의 스냅숏 복구 지점에서 복원을 수행하는 경우 복원 작업의 일환으로 VM을 만드는 동안 임시 저장소 위치가 사용되는 것을 볼 수 있습니다. 
* 프리미엄 저장소 계정의 경우 인스턴트 복구를 위해 생성된 스냅숏은 프리미엄 저장소 계정에 할당된 10TB의 공간을 차지합니다.

## <a name="how-to-upgrade"></a>업그레이드 방법
### <a name="the-azure-portal"></a>Azure 포털
Azure Portal을 사용하는 경우 자격 증명 모음 대시보드에 대형 디스크 지원과 백업 및 복원 속도 향상에 관련된 알림이 표시됩니다.

![VM 백업 스택 V2의 백업 작업](./media/backup-azure-vms/instant-rp-banner.png) 

새 스택으로 업그레이드하는 화면을 열려면 배너를 선택합니다. 

![VM 백업 스택 V2의 백업 작업](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
관리자 권한 PowerShell 터미널에서 다음 cmdlet을 실행합니다.
1.  Azure 계정에 로그인합니다. 

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

## <a name="verify-whether-the-upgrade-is-complete"></a>업그레이드가 완료되었는지 확인합니다.
관리자 권한 PowerShell 터미널에서 다음 cmdlet을 실행합니다.

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
```

등록되었다고 표시되면 구독이 VM 백업 스택 V2로 업그레이드됩니다. 



