---
title: Virtual Machine Scale Sets에 대 한 Azure Disk Encryption 사용
description: 이 문서에서는 Virtual Machine Scale Sets에 대해 Microsoft Azure 디스크 암호화를 사용 하도록 설정 하는 지침을 제공 합니다.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 6b9805d66149a18216a200bc89a79b3e06106c9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83195120"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets에 대 한 Azure Disk Encryption

Azure Disk Encryption는 가상 머신의 OS 및 데이터 디스크에 대 한 볼륨 암호화를 제공 하 여 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호 하 고 보호 합니다. 자세히 알아보려면 [Azure Disk Encryption: Linux vm](../virtual-machines/linux/disk-encryption-overview.md) 및 [Azure Disk Encryption: Windows vm](../virtual-machines/windows/disk-encryption-overview.md) 을 참조 하세요.  

Azure Disk Encryption는 다음과 같은 경우에 Windows 및 Linux 가상 머신 확장 집합에도 적용할 수 있습니다.
- 관리 디스크를 사용 하 여 만든 크기 집합 네이티브 또는 관리 되지 않는 디스크 크기 집합에는 Azure Disk encryption을 사용할 수 없습니다.
- Windows 확장 집합의 OS 및 데이터 볼륨.
- Linux 확장 집합의 데이터 볼륨. Linux 확장 집합에 현재 OS 디스크 암호화가 지원 되지 않습니다.

[Azure CLI를 사용 하 여 가상 머신 확장 집합 암호화](disk-encryption-cli.md) 또는 Azure PowerShell 자습서를 [사용 하 여 가상 머신 확장 집합 암호화](disk-encryption-powershell.md) 를 사용 하 여 몇 분만에 가상 머신 확장 집합에 대 한 Azure Disk Encryption의 기본 사항을 배울 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Resource Manager를 사용 하 여 가상 머신 확장 집합 암호화](disk-encryption-azure-resource-manager.md)
- [Azure Disk Encryption을 위한 Key Vault 만들기 및 구성](disk-encryption-key-vault.md)
- [가상 머신 확장 집합 확장 시퀀싱을 통한 Azure Disk Encryption 사용](disk-encryption-extension-sequencing.md)
