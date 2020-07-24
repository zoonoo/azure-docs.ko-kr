---
title: 파일 포함
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f96bc753d14d53584aec6d9661e52adcd21ab764
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136170"
---
## <a name="supported-regions"></a>지원되는 Azure 지역

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="getting-started"></a>시작

1. [Azure Portal](https://aka.ms/diskencryptionupdates)에 로그인합니다.

    > [!IMPORTANT]
    > [제공 된 링크](https://aka.ms/diskencryptionupdates) 를 사용 하 여 Azure Portal에 액세스 해야 합니다. 휴지 상태의 이중 암호화는 현재 링크를 사용 하지 않고 공용 Azure Portal에 표시 되지 않습니다.

1. **디스크 암호화 집합**을 검색 하 고 선택 합니다.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="예제 텍스트":::

1. **+추가**를 선택합니다.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="예제 텍스트":::

1. 지원되는 영역 중 하나를 선택합니다.
1. **암호화 유형**에서 **플랫폼 관리 및 고객 관리 키를 사용 하 여 이중 암호화**를 선택 합니다.

    > [!NOTE]
    > 특정 암호화 유형을 사용 하 여 디스크 암호화 세트를 만든 후에는 변경할 수 없습니다. 다른 암호화 유형을 사용 하려면 새 디스크 암호화 집합을 만들어야 합니다.

1. 나머지 정보를 입력 합니다.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-create-disk-encryption-set-blade.png" alt-text="예제 텍스트":::

1. Azure Key Vault 및 키를 선택 하거나 필요한 경우 새 키를 만듭니다.

    > [!NOTE]
    > Key Vault 인스턴스를 만드는 경우 일시 삭제 및 보호 제거를 사용 하도록 설정 해야 합니다. 이러한 설정은 관리 디스크를 암호화 하는 데 Key Vault를 사용 하는 경우 필수 이며 실수로 인 한 삭제로 인해 데이터가 손실 되지 않도록 보호 합니다.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-select-key-vault.png" alt-text="예제 텍스트":::

1. **만들기**를 선택합니다.
1. 만든 디스크 암호화 집합으로 이동 하 여 표시 되는 오류를 선택 합니다. 이렇게 하면 디스크 암호화가 작동 하도록 설정 됩니다.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-set-error.png" alt-text="예제 텍스트":::

    알림이 표시 되 고 성공 해야 합니다. 이렇게 하면 키 자격 증명 모음에 설정 된 디스크 암호화를 사용할 수 있습니다.
    
    ![주요 자격 증명 모음에 대 한 성공적인 사용 권한 및 역할 할당의 스크린샷](media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)

1. 디스크로 이동 합니다.
1. **암호화**를 선택 합니다.
1. **암호화 유형**에서 **플랫폼 관리 및 고객 관리 키를 사용 하 여 이중 암호화**를 선택 합니다.
1. 디스크 암호화 집합을 선택 합니다.
1. **저장**을 선택합니다.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="예제 텍스트":::

이제 관리 디스크에서 미사용 암호화를 사용할 수 있습니다.
