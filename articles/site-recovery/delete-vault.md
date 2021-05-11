---
title: Azure Site Recovery 자격 증명 모음 삭제
description: Azure Site Recovery에 대해 구성된 Recovery Services 자격 증명 모음을 삭제하는 방법에 대한 자세한 정보
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/05/2019
ms.author: sideeksh
ms.openlocfilehash: a33e04a24013d5450c98b91048fa418958d16886
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89426387"
---
# <a name="delete-a-site-recovery-services-vault"></a>Site Recovery Services 자격 증명 모음 삭제

이 문서에서는 Site Recovery용 Recovery Services 자격 증명 모음을 삭제하는 방법을 설명합니다. Azure Backup에서 사용되는 자격 증명 모음을 삭제하려면 [Azure에서 백업 자격 증명 모음 삭제](../backup/backup-azure-delete-vault.md)를 참조하세요.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="before-you-start"></a>시작하기 전에

자격 증명 모음을 삭제하려면 먼저 등록된 서버와 자격 증명 모음의 항목을 제거해야 합니다. 제거해야 하는 항목은 배포한 복제 시나리오에 따라 달라집니다. 


## <a name="delete-a-vault-azure-vm-to-azure"></a>자격 증명 모음 삭제 - Azure VM 및 Azure 간

1. [지침](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure)에 따라 보호된 VM을 모두 삭제합니다.
2. 그런 다음 자격 증명 모음을 삭제합니다.

## <a name="delete-a-vault-vmware-vm-to-azure"></a>자격 증명 모음 삭제 - VMware VM 및 Azure 간

1. [지침](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)에 따라 보호된 VM을 모두 삭제합니다.
2. [이 단계](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)를 수행하여 복제 정책을 모두 삭제합니다.
3. [이 단계](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)를 수행하여 vCenter에 대한 참조를 삭제합니다.
4. [지침](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)에 따라 구성 서버를 해제합니다.
5. 그런 다음 자격 증명 모음을 삭제합니다.


## <a name="delete-a-vault-hyper-v-vm-with-vmm-to-azure"></a>자격 증명 모음 삭제 - Hyper-V VM(VMM 포함) 및 Azure 간

1. [이 단계](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario)를 수행하여 System Center VMM에서 관리하는 Hyper-V VM을 삭제합니다.
2. 모든 복제본 정책을 분리하고 삭제합니다. 이 작업은 자격 증명 모음 > **Site Recovery 인프라** > **System Center VMM** > **복제 정책** 에서 수행합니다.
3. [이 단계](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server)를 수행하여 연결된 VMM 서버를 등록 취소합니다.
4. 그런 다음 자격 증명 모음을 삭제합니다.

## <a name="delete-a-vault-hyper-v-vm-to-azure"></a>자격 증명 모음 삭제 - Hyper-V VM 및 Azure 간

1. [이 단계](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure)를 수행하여 보호된 VM을 모두 삭제합니다.
2. 모든 복제본 정책을 분리하고 삭제합니다. 이 작업은 자격 증명 모음 > **Site Recovery 인프라** > **Hyper-V 사이트** > **복제 정책** 에서 수행합니다.
3. [지침](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site)에 따라 Hyper-V 호스트를 등록 취소합니다.
4. Hyper-V 사이트를 삭제합니다.
5. 그런 다음 자격 증명 모음을 삭제합니다.


## <a name="use-powershell-to-force-delete-the-vault"></a>PowerShell을 사용하여 자격 증명 모음 강제 삭제 

> [!Important]
> 제품을 테스트하고 데이터 손실을 우려하지 않는다면 강제 삭제 방법을 사용하여 자격 증명 모음 및 모든 해당 종속성을 빠르게 제거할 수 있습니다.
> PowerShell 명령은 자격 증명 모음의 모든 내용을 삭제하며 **되돌릴 수 없습니다**.

보호된 항목이 있더라도 Site Recovery 자격 증명 모음을 삭제하려면 다음 명령을 사용합니다.

```azurepowershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName "XXXXX"

$vault = Get-AzRecoveryServicesVault -Name "vaultname"

Remove-AzRecoveryServicesVault -Vault $vault
```

[Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault) 및 [Remove-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault)에 대해 자세히 알아보세요.
