---
title: 파일 포함
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f0a706cc51027bc503da9ee3bc637216e9e52853
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87171371"
---
## <a name="restrictions"></a>제한

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>지원되는 Azure 지역

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>지원되는 VM 크기

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>전제 조건

호스트에서 Vm 또는 가상 머신 확장 집합에 대 한 암호화를 사용할 수 있으려면 구독에서 기능을 사용 하도록 설정 해야 합니다. encryptionAtHost@microsoft에 전자 메일을 보냅니다. 구독에 사용할 수 있는 기능을 제공 하는 구독 Id를 사용 하는 com.

[제공 된 링크](https://aka.ms/diskencryptionupdates)를 사용 하 여 Azure Portal에 로그인 합니다.

> [!IMPORTANT]
> [제공 된 링크](https://aka.ms/diskencryptionupdates) 를 사용 하 여 Azure Portal에 액세스 해야 합니다. 호스트의 암호화는 현재 링크를 사용 하지 않고 공용 Azure Portal에 표시 되지 않습니다.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Azure Key Vault 및 디스크 암호화 집합 만들기

기능이 사용 하도록 설정 되 면 Azure Key Vault 및 디스크 암호화 집합이 아직 설정 되지 않은 경우 설정 해야 합니다.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>VM 배포

새 VM을 배포 하 여 호스트에서 암호화를 사용 하도록 설정 해야 하며, 기존 Vm에서는 사용할 수 없습니다.

1. **Virtual Machines** 를 검색 하 고 **+ 추가** 를 선택 하 여 VM을 만듭니다.
1. 새 가상 컴퓨터를 만들고 적절 한 지역 및 지원 되는 VM 크기를 선택 합니다.
1. **기본** 블레이드에서 원하는 다른 값을 입력 한 다음 **디스크** 블레이드로 이동 합니다.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="가상 머신 만들기 기본 사항 블레이드, 지역 및 V M 크기의 스크린샷 강조 표시 됩니다.":::

1. **디스크** 블레이드에서 **호스트의 암호화**에 대해 **예** 를 선택 합니다.
1. 원하는 대로 나머지 항목을 선택 합니다.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="가상 컴퓨터 만들기 디스크 블레이드의 스크린샷, 호스트의 암호화가 강조 표시 됩니다.":::

1. VM 배포 프로세스를 완료 하 고 사용자 환경에 적합 한 선택 항목을 만듭니다.

이제 암호화를 사용 하 여 VM을 배포 했으며, 호스트에서 암호화를 사용 하 여 연결 된 모든 디스크가 암호화 됩니다.