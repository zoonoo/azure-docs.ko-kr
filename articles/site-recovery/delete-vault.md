---
title: Azure 사이트 복구 자격 증명 모음 삭제
description: Azure Site Recovery에 대해 구성된 Recovery Services 자격 증명 모음을 삭제하는 방법에 대한 자세한 정보
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/05/2019
ms.author: rajanaki
ms.openlocfilehash: 0e409ffdedbac822aedf48833f2dd85f8e04afa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894974"
---
# <a name="delete-a-site-recovery-services-vault"></a>Site Recovery Services 자격 증명 모음 삭제

이 문서에서는 사이트 복구에 대한 복구 서비스 자격 증명 모음을 삭제하는 방법에 대해 설명합니다. Azure Backup에서 사용되는 자격 증명 모음을 삭제하려면 [Azure에서 백업 자격 증명 모음 삭제](../backup/backup-azure-delete-vault.md)를 참조하세요.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="before-you-start"></a>시작하기 전에

볼트를 삭제하려면 먼저 등록된 서버와 볼트의 항목을 제거해야 합니다. 제거해야 하는 것은 배포한 복제 시나리오에 따라 다릅니다. 


## <a name="delete-a-vault-azure-vm-to-azure"></a>Azure에 대한 볼트-Azure VM 삭제

1. [이 지침에](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure) 따라 보호된 모든 VM을 삭제합니다.
2. 그런 다음 볼트를 삭제합니다.

## <a name="delete-a-vault-vmware-vm-to-azure"></a>Azure에 볼트 VM웨어 VM 삭제

1. [이 지침에](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) 따라 보호된 모든 VM을 삭제합니다.
2. [다음 단계에](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) 따라 모든 복제 정책을 삭제합니다.
3. [다음 단계를](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)사용하여 vCenter에 대한 참조를 삭제합니다.
4. [다음 지침에](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server) 따라 구성 서버를 해제합니다.
5. 그런 다음 볼트를 삭제합니다.


## <a name="delete-a-vault-hyper-v-vm-with-vmm-to-azure"></a>볼트 하이퍼 VM(VMM 이 있음)을 Azure로 삭제

1. 다음 [단계에](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario) 따라 시스템 센터 VMM에서 관리하는 Hyper-V VM을 삭제합니다.
2. 모든 복제 정책의 연결을 해제하고 삭제합니다. **시스템 센터 VMM** > **복제 정책에**대한 사이트 복구 **인프라에** > > 볼트에서 이 작업을 수행합니다.
3. 다음 [단계에](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) 따라 연결된 VMM 서버의 등록을 취소합니다.
4. 그런 다음 볼트를 삭제합니다.

## <a name="delete-a-vault-hyper-v-vm-to-azure"></a>Azure에 볼트 하이퍼 VM 삭제

1. [다음 단계에](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure) 따라 보호된 모든 VM을 삭제합니다.
2. 모든 복제 정책의 연결을 해제하고 삭제합니다. **하이퍼 V 사이트** > **복제 정책에**대한 사이트 복구 **인프라에** > > 볼트에서 이 작업을 수행합니다.
3. 하이퍼-V 호스트등록을 취소하려면 [다음 지침을](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site) 따르십시오.
4. Hyper-V 사이트를 삭제합니다.
5. 그런 다음 볼트를 삭제합니다.


## <a name="use-powershell-to-force-delete-the-vault"></a>PowerShell을 사용하여 자격 증명 모음 강제 삭제 

> [!Important]
> 제품을 테스트하고 데이터 손실을 우려하지 않는다면 강제 삭제 방법을 사용하여 자격 증명 모음 및 모든 해당 종속성을 빠르게 제거할 수 있습니다.
> PowerShell 명령은 볼트의 모든 내용을 삭제하며 **되돌릴 수 없습니다.**

보호된 항목이 있더라도 Site Recovery 자격 증명 모음을 삭제하려면 다음 명령을 사용합니다.

    Connect-AzAccount

    Select-AzSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzRecoveryServicesVault -Name "vaultname"

    Remove-AzRecoveryServicesVault -Vault $vault

[Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)및 [제거-아즈리커버리서비스볼트에](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault)대해 자세히 알아보십시오.
