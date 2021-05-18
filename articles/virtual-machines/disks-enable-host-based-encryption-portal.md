---
title: 호스트 - Azure Portal - 관리 디스크에서 암호화를 사용하여 엔드투엔드 암호화 사용
description: 호스트에서 암호화를 사용하여 Azure 관리 디스크 - Azure Portal에서 엔드투엔드 암호화를 사용하도록 설정합니다.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: cdb22805e2e68893d3883272b66c2cfac13c807e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104721871"
---
# <a name="use-the-azure-portal-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Azure Portal을 사용하여 호스트에서 암호화를 사용하는 엔드투엔드 암호화를 사용하도록 설정

호스트에서 암호화를 사용하도록 설정하면 VM 호스트에 저장된 데이터는 미사용 및 스토리지 서비스로 암호화된 흐름으로 암호화됩니다. 호스트에서 암호화 및 기타 관리 디스크 암호화 유형에 대한 개념 정보는 다음을 참조하세요.

* Linux: [호스트에서 암호화 - VM 데이터의 엔드투엔드 암호화](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

* Windows: [호스트에서 암호화 - VM 데이터의 엔드투엔드 암호화](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>제한

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]


### <a name="supported-vm-sizes"></a>지원되는 VM 크기

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>사전 요구 사항

VM/VMSS에 대한 EncryptionAtHost 속성을 사용하기 전에 구독에 이 기능을 사용하도록 설정해야 합니다. 구독 기능을 사용하도록 설정하려면 아래 단계를 따르세요.

1. **Azure Portal**: [Azure Portal](https://portal.azure.com)에서 Cloud Shell 아이콘을 선택합니다.

    ![Azure Portal에서 Cloud Shell을 시작하는 아이콘](../Cloud-Shell/media/overview/portal-launch-icon.png)
    
2.  다음 명령을 실행하여 구독 기능을 등록합니다.

    ```powershell
     Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute" 
    ```

3.  기능을 사용해 보기 전에 아래 명령을 사용하여 등록 상태가 등록됨(몇 분 소요)인지 확인합니다.

    ```powershell
     Get-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"  
    ```


[제공된 링크](https://aka.ms/diskencryptionupdates)를 사용하여 Azure Portal에 로그인합니다.

> [!IMPORTANT]
> [제공된 링크](https://aka.ms/diskencryptionupdates)를 사용하여 Azure Portal에 액세스해야 합니다. 현재 호스트에서 암호화는 링크를 사용하지 않고 공용 Azure Portal에 표시되지 않습니다.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Azure Key Vault 및 디스크 암호화 집합 만들기

기능을 사용하도록 설정한 후에는 Azure Key Vault 및 디스크 암호화 집합을 아직 설정하지 않은 경우 설정해야 합니다.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>VM 배포

호스트에서 암호화를 사용하도록 설정하려면 새 VM을 배포해야 하며, 기존 VM에서는 사용하도록 설정할 수 없습니다.

1. **가상 머신** 을 검색하고 **+ 추가** 를 선택하여 VM을 만듭니다.
1. 새 가상 머신을 만들고 적절한 지역과 지원되는 VM 크기를 선택합니다.
1. 원하는 대로 **기본** 블레이드에 다른 값을 입력한 다음 **디스크** 블레이드로 진행합니다.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="가상 머신 만들기 기본 블레이드, 지역 및 VM 크기가 강조 표시된 스크린샷.":::

1. **디스크** 블레이드에서 **호스트에서 암호화** 에 대해 **예** 를 선택합니다.
1. 원하는 대로 나머지 항목을 선택합니다.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="가상 머신 만들기 디스크 블레이드, 호스트에서 암호화가 강조 표시된 스크린샷.":::

1. VM 배포 프로세스를 마치고 환경에 맞는 선택을 합니다.

이제 암호화를 사용하여 VM을 배포했으며, 호스트에서 암호화를 사용하여 연결된 모든 디스크가 암호화됩니다.

## <a name="next-steps"></a>다음 단계

[Azure Resource Manager 템플릿 샘플](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
