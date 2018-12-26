---
title: Azure Site Recovery 서비스에 대해 구성된 Recovery Services 자격 증명 모음 삭제
description: Azure Site Recovery에 대해 구성된 Recovery Services 자격 증명 모음을 삭제하는 방법에 대한 자세한 정보
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajani-janaki-ram
ms.openlocfilehash: 7dc1be1316d833d4a37a8b46bf0145089bb3ae22
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52839130"
---
# <a name="delete-a-site-recovery-services-vault"></a>Site Recovery Services 자격 증명 모음 삭제

종속성으로 인해 Azure Site Recovery 자격 증명 모음을 삭제하지 못할 수 있으며, 수행해야 하는 작업은 Site Recovery 시나리오에 따라 다릅니다. Azure Backup에서 사용되는 자격 증명 모음을 삭제하려면 [Azure에서 백업 자격 증명 모음 삭제](../backup/backup-azure-delete-vault.md)를 참조하세요.



## <a name="delete-a-site-recovery-vault"></a>Site Recovery 자격 증명 모음 삭제 
자격 증명 모음을 삭제하려면 시나리오에 맞는 권장 단계를 따릅니다.

### <a name="vmware-vms-to-azure"></a>VMware VM에서 Azure로

1. [VMware에 대한 보호 사용 안 함](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)의 단계에 따라 보호되는 VM을 모두 삭제합니다.

2. [복제 정책 삭제](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)의 단계에 따라 모든 복제 정책을 삭제합니다.

3. [vCenter Server 삭제](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)의 단계에 따라 vCenter에 대한 참조를 삭제합니다.

4. [구성 서버 서비스 해제](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)의 단계에 따라 구성 서버를 삭제합니다.

5. 자격 증명 모음을 삭제합니다.


### <a name="hyper-v-vms-with-vmm-to-azure"></a>Hyper-V VM(VMM 있음)에서 Azure로
1. [Hyper-V VM(VMM 있음)에 대한 보호 사용 안 함](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario)의 단계에 따라 보호되는 모든 VM을 삭제합니다.

2. 자격 증명 모음 -> **Site Recovery 인프라** -> **System Center VMM** -> **복제 정책**으로 이동하여 모든 복제 정책을 분리 및 삭제합니다.

3.  [연결된 VMM 서버 등록 취소](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server)의 단계에 따라 VMM 서버에 대한 참조를 삭제합니다.

4.  자격 증명 모음을 삭제합니다.

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>Hyper-V VM(Virtual Machine Manager 없음)에서 Azure로
1. [Hyper-V 가상 컴퓨터에 대한 보호 사용 안 함(Hyper-V에서 Azure로)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure)의 단계에 따라 보호되는 VM을 모두 삭제합니다.

2. 자격 증명 모음 -> **Site Recovery 인프라** -> **Hyper-V 사이트** -> **복제 정책**으로 이동하여 모든 복제 정책을 분리 및 삭제합니다.

3. [Hyper-V 호스트 등록 취소](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site)의 단계에 따라 Hyper-V 서버에 대한 참조를 삭제합니다.

4. Hyper-V 사이트를 삭제합니다.

5. 자격 증명 모음을 삭제합니다.


## <a name="use-powershell-to-force-delete-the-vault"></a>PowerShell을 사용하여 자격 증명 모음 강제 삭제 

> [!Important]
> 제품을 테스트하고 데이터 손실을 우려하지 않는다면 강제 삭제 방법을 사용하여 자격 증명 모음 및 모든 해당 종속성을 빠르게 제거할 수 있습니다.
> PowerShell 명령은 자격 증명 모음의 모든 내용을 삭제하며 **되돌릴 수 없습니다**.

보호된 항목이 있더라도 Site Recovery 자격 증명 모음을 삭제하려면 다음 명령을 사용합니다.

    Connect-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmRecoveryServicesVault -Name "vaultname"

    Remove-AzureRmRecoveryServicesVault -Vault $vault

[Get-AzureRMRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault?view=azurermps-6.0.0) 및 [Remove-AzureRMRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/remove-azurermrecoveryservicesvault?view=azurermps-6.0.0)에 대해 자세히 알아봅니다.
