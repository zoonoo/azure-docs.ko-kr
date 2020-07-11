---
title: Azure Portal-SSE 관리 디스크를 사용 하 여 고객 관리 키를 사용 하도록 설정
description: Azure Portal를 통해 관리 디스크에서 고객이 관리 하는 키를 사용 하도록 설정 합니다.
author: roygara
ms.date: 07/10/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 0128d2b88d6eb2464ef95824330cfab07ee3703d
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86236147"
---
# <a name="enable-customer-managed-keys-with-server-side-encryption---managed-disks---portal"></a>서버 쪽 암호화 관리 디스크를 사용 하 여 고객이 관리 하는 키 사용-포털

Azure 디스크 저장소를 사용 하면 관리 디스크에 대해 SSE (서버 쪽 암호화)를 사용 하는 경우 (선택 하는 경우) 사용자 고유의 키를 관리할 수 있습니다. 고객 관리 키 및 기타 관리 되는 디스크 암호화 유형에 대 한 SSE 개념 정보는 디스크 암호화 문서의 [고객 관리 키](disk-encryption.md#customer-managed-keys) 섹션을 참조 하세요.

## <a name="restrictions"></a>제한

현재, 고객 관리형 키에는 다음과 같은 제한 사항이 있습니다.

- 디스크에 대해 이 기능을 사용하는 경우에는 사용하지 않도록 설정할 수 없습니다.
    이 문제를 해결해야 하는 경우 고객 관리형 키를 사용하지 않는 완전히 다른 관리 디스크로 [모든 데이터를 복사](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)해야 합니다.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> 고객 관리형 키는 Azure AD(Azure Active Directory)의 기능 중 하나인 Azure 리소스에 대한 관리 ID를 사용합니다. 고객 관리형 키를 구성하는 경우 관리 ID가 내부적으로 리소스에 자동으로 할당됩니다. 이후에 구독, 리소스 그룹 또는 관리 디스크를 Azure AD 디렉터리 간에 이동 하는 경우 관리 디스크와 연결 된 관리 되는 id가 새 테 넌 트로 전송 되지 않으므로 고객 관리 키가 더 이상 작동 하지 않을 수 있습니다. 자세한 정보는 [Azure AD 디렉터리 간에 구독 전송](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [고객 관리 키를 사용 하 여 암호화 된 디스크를 만들기 위한 Azure Resource Manager 템플릿 살펴보기](https://github.com/ramankumarlive/manageddiskscmkpreview))
- [고객 관리형 키를 사용하도록 설정된 디스크를 사용하여 컴퓨터 복제](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [PowerShell을 사용하여 Azure로 VMware VM의 재해 복구 설정](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [PowerShell과 Azure Resource Manager를 사용하여 Hyper-V VM용 Azure에 대한 재해 복구 설정](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
