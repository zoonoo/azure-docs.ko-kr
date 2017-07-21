---
title: "Recovery Services 자격 증명 모음 삭제"
service: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/04/2017
ms.author: rajani-janaki-ram
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: 32fcab0c9e4665d07691dc3792bdee90fb01fe66
ms.contentlocale: ko-kr
ms.lasthandoff: 07/06/2017


---
# <a name="delete-recovery-services-vault"></a>Recovery Services 자격 증명 모음 삭제
종속성으로 인해 복구 서비스 자격 증명 모음은 삭제할 수 없으며, VMWare에서 Azure로, Hyper-V(VMM 있음 및 없음)에서 Azure로 및 Azure Backup과 같은 Azure Site Recovery 시나리오의 형식에 따라 수행해야 하는 작업이 달라집니다. Azure Backup에 사용되는 자격 증명 모음을 삭제하려면 [이](../backup/backup-azure-delete-vault.md) 링크를 확인합니다.

>[!Important]
>제품을 테스트하여 신속하게 자격 증명 모음을 삭제하고자 하며 데이터 손실을 우려하지 않는다면 강제 제거 방식을 사용하여 자격 증명 모음 및 모든 해당 종속성을 제거할 수 있습니다.

> PowerShell 명령은 자격 증명 모음의 모든 내용을 삭제하며 되돌릴 수 없는 단계임에 주의하세요.

## <a name="force-delete-vault-using-powershell"></a>Powershell을 사용하여 자격 증명 모음 강제 제거

보호된 항목이 있는 경우 Site Recovery 자격 증명 모음을 삭제하려면 다음 단계를 수행합니다.

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmSiteRecoveryVault -Name "vaultname"

    Remove-AzureRmSiteRecoveryVault -Vault $vault



시나리오에 맞는 권장되는 단계에 따라(순서대로) 자격 증명 모음을 삭제합니다.

## <a name="delete-vault-used-in-site-recovery-for-protecting-vmware-vms-to-azure"></a>Azure로 VMWare VM 보호를 위해 Site Recovery에 사용된 자격 증명 모음을 삭제합니다.
1. 모든 보호된 VM이 삭제되었는지 확인합니다. [방법](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server)을 참조하세요.
2.  모든 복제 정책이 삭제되었는지 확인합니다. [방법](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy)을 참조하세요.
3.  vCenter에 대한 참조가 삭제되었는지 확인합니다. [방법](site-recovery-vmware-to-azure-manage-vCenter.md##delete-a-vcenter-in-azure-site-recovery)을 참조하세요.
4. 구성 서버가 삭제되었는지 확인합니다. [방법](site-recovery-vmware-to-azure-manage-configuration-server.md##decommissioning-a-configuration-server)을 참조하세요.
5. 이제 자격 증명 모음 삭제를 시도해 보세요.


## <a name="delete-vault-used-in-site-recovery-for-protecting-hyper-v-vms-with-vmm-to-azure"></a>Azure로 Hyper-V VM(VMM 있음) 보호를 위해 Site Recovery에 사용된 자격 증명 모음을 삭제합니다.
1.  모든 보호된 VM이 삭제되었는지 확인합니다. [방법](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server)을 참조하세요.
- 모든 복제 정책이 삭제되었는지 확인합니다. [방법](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy)을 참조하세요.
-   VMM 서버에 대한 참조를 삭제합니다. [방법](site-recovery-manage-registration-and-protection.md##unregister-a-connected-vmm-server)을 참조하세요.
-   이제 자격 증명 모음 삭제를 시도해 보세요.

## <a name="delete-vault-used-in-site-recovery--for-protecting-hyper-v-vms-without-vmm-to-azure"></a>Azure로 Hyper-V VM(VMM 없음) 보호를 위해 Site Recovery에서 사용된 자격 증명 모음을 삭제합니다.
1. 모든 보호된 VM이 삭제되었는지 확인합니다. [방법](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server)을 참조하세요.
- 모든 복제 정책이 삭제되었는지 확인합니다. [방법](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy)을 참조하세요.
-   Hyper-V 서버에 대한 참조를 삭제합니다. [방법](/site-recovery-manage-registration-and-protection.md##unregister-a-hyper-v-host-in-a-hyper-v-site)을 참조하세요.
-   Hyper-V 사이트를 삭제합니다.
-   이제 자격 증명 모음 삭제를 시도해 보세요.

