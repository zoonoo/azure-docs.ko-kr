---
title: Azure Site Recovery 자격 증명 모음 삭제
description: Azure Site Recovery에 대해 구성된 Recovery Services 자격 증명 모음을 삭제하는 방법에 대한 자세한 정보
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/05/2019
ms.author: rajanaki
ms.openlocfilehash: 0e409ffdedbac822aedf48833f2dd85f8e04afa2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75894974"
---
# <a name="delete-a-site-recovery-services-vault"></a>Site Recovery Services 자격 증명 모음 삭제

이 문서에서는 Site Recovery에 대 한 Recovery Services 자격 증명 모음을 삭제 하는 방법을 설명 합니다. Azure Backup에서 사용되는 자격 증명 모음을 삭제하려면 [Azure에서 백업 자격 증명 모음 삭제](../backup/backup-azure-delete-vault.md)를 참조하세요.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="before-you-start"></a>시작하기 전에

자격 증명 모음을 삭제 하려면 먼저 등록 된 서버 및 자격 증명 모음에 있는 항목을 제거 해야 합니다. 제거 해야 하는 항목은 배포한 복제 시나리오에 따라 달라 집니다. 


## <a name="delete-a-vault-azure-vm-to-azure"></a>자격 증명 모음 삭제-azure VM

1. [다음 지침](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure) 에 따라 보호 된 vm을 모두 삭제 합니다.
2. 그런 다음 자격 증명 모음을 삭제 합니다.

## <a name="delete-a-vault-vmware-vm-to-azure"></a>자격 증명 모음 삭제-VMware VM에서 Azure로

1. [다음 지침](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) 에 따라 보호 된 vm을 모두 삭제 합니다.
2. 모든 복제 정책을 삭제 하려면 [다음 단계](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) 를 수행 합니다.
3. [다음 단계](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)를 사용 하 여 vCenter에 대 한 참조를 삭제 합니다.
4. [다음 지침](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server) 에 따라 구성 서버를 서비스 해제 합니다.
5. 그런 다음 자격 증명 모음을 삭제 합니다.


## <a name="delete-a-vault-hyper-v-vm-with-vmm-to-azure"></a>Azure에 대 한 자격 증명 모음 삭제-Hyper-v VM (VMM 포함)

1. System Center VMM에서 관리 하는 Hyper-v Vm을 삭제 하려면 [다음 단계](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario) 를 수행 합니다.
2. 모든 복제 정책을 분리 하 고 삭제 합니다. **System Center VMM** > **복제 정책**에 대 한 **Site Recovery 인프라** > > 자격 증명 모음에서이 작업을 수행 합니다.
3. 연결 된 VMM 서버를 등록 취소 하려면 [다음 단계](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) 를 수행 합니다.
4. 그런 다음 자격 증명 모음을 삭제 합니다.

## <a name="delete-a-vault-hyper-v-vm-to-azure"></a>자격 증명 모음 삭제-Hyper-v VM에서 Azure로

1. 모든 보호 된 Vm을 삭제 하려면 [다음 단계](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure) 를 수행 합니다.
2. 모든 복제 정책을 분리 하 고 삭제 합니다. 자격 증명 모음에서**hyper-v 사이트** > **복제 정책**에 대 한 **Site Recovery 인프라** > >이 작업을 수행 합니다.
3. Hyper-v 호스트의 등록을 취소 하려면 [다음 지침](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site) 을 따르세요.
4. Hyper-V 사이트를 삭제합니다.
5. 그런 다음 자격 증명 모음을 삭제 합니다.


## <a name="use-powershell-to-force-delete-the-vault"></a>PowerShell을 사용하여 자격 증명 모음 강제 삭제 

> [!Important]
> 제품을 테스트하고 데이터 손실을 우려하지 않는다면 강제 삭제 방법을 사용하여 자격 증명 모음 및 모든 해당 종속성을 빠르게 제거할 수 있습니다.
> PowerShell 명령은 자격 증명 모음의 모든 내용을 삭제 하며 **되돌릴 수 없습니다**.

보호된 항목이 있더라도 Site Recovery 자격 증명 모음을 삭제하려면 다음 명령을 사용합니다.

    Connect-AzAccount

    Select-AzSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzRecoveryServicesVault -Name "vaultname"

    Remove-AzRecoveryServicesVault -Vault $vault

[AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)및 [AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault)에 대해 자세히 알아보세요.
