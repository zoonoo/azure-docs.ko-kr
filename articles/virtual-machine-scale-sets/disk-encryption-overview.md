---
title: 가상 시스템 규모 집합에 대한 Azure 디스크 암호화 사용
description: 이 문서에서는 가상 시스템 규모 집합에 대 한 Microsoft Azure 디스크 암호화 를 사용 하는 방법에 대 한 지침을 제공 합니다.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 36eb83d99af299260ebbbd74fe56d69921ba389b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278982"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>가상 시스템 규모 집합에 대한 Azure 디스크 암호화

Azure Disk 암호화는 가상 시스템의 OS 및 데이터 디스크에 대한 볼륨 암호화를 제공하여 조직 보안 및 규정 준수 약정을 충족하기 위해 데이터를 보호하고 보호하는 데 도움이 됩니다. 자세한 내용은 [Azure 디스크 암호화: Linux VM](../virtual-machines/linux/disk-encryption-overview.md) 및 [Azure 디스크 암호화: Windows VM을](../virtual-machines/windows/disk-encryption-overview.md) 참조하십시오.  

Azure 디스크 암호화는 다음과 같은 경우 Windows 및 Linux 가상 시스템 규모 집합에도 적용할 수 있습니다.
- 관리 디스크로 만든 집합을 확장합니다. Azure Disk 암호화는 네이티브(또는 관리되지 않는) 디스크 규모 집합에 대해 지원되지 않습니다.
- Windows 스케일 세트의 OS 및 데이터 볼륨입니다.
- Linux 규모의 데이터 볼륨 집합입니다. 현재 Linux 규모 집합에서는 OS 디스크 암호화가 지원되지 않습니다.

[Azure CLI를 사용하여 가상 시스템 스케일 집합을 암호화하거나 Azure](disk-encryption-cli.md) PowerShell 자습서를 사용하여 가상 시스템 규모 [집합을 암호화하여](disk-encryption-powershell.md) 몇 분 만에 가상 시스템 규모 집합에 대한 Azure 디스크 암호화의 기본 을 배울 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 리소스 관리자를 사용하여 가상 시스템 스케일 집합 암호화](disk-encryption-azure-resource-manager.md)
- [Azure Disk Encryption을 위한 Key Vault 만들기 및 구성](disk-encryption-key-vault.md)
- [가상 머신 확장 집합 확장 시퀀싱을 통한 Azure Disk Encryption 사용](disk-encryption-extension-sequencing.md)
