---
title: 파일 포함
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a8702f14bfb9aed5a3595203dde1d86ea56a151b
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133877"
---
[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>VM 배포

이제 키 자격 증명 모음 및 디스크 암호화 집합을 만들고 설정 했으므로 암호화를 사용 하 여 VM을 배포할 수 있습니다.
VM 배포 프로세스는 표준 배포 프로세스와 유사 합니다. 유일한 차이점은 다른 리소스와 동일한 지역에 VM을 배포 하 고 고객 관리 키를 사용 하도록 선택 하는 것입니다.

1. **Virtual Machines** 를 검색 하 고 **+ 추가** 를 선택 하 여 VM을 만듭니다.
1. **기본** 블레이드에서 디스크 암호화 집합과 동일한 지역을 선택 하 고 Azure Key Vault 합니다.
1. **기본** 블레이드에서 원하는 다른 값을 입력 합니다.

    ![지역 값이 강조 표시 된 VM 만들기 환경의 스크린샷](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. **디스크** 블레이드에서 **고객이 관리 하는 키를 사용 하 여 미사용 암호화**를 선택 합니다.
1. **디스크 암호화 집합** 드롭다운에서 설정 된 디스크 암호화를 선택 합니다.
1. 원하는 대로 나머지 항목을 선택 합니다.

    ![VM 만들기 환경의 스크린샷, 디스크 블레이드. 디스크 암호화 설정 드롭다운이 강조 표시 됩니다.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>기존 디스크에서 사용

> [!CAUTION]
> VM에 연결 된 모든 디스크에 대해 디스크 암호화를 사용 하도록 설정 하려면 VM을 중지 해야 합니다.
    
1. 디스크 암호화 집합 중 하 나와 동일한 지역에 있는 VM으로 이동 합니다.
1. VM을 열고 **중지**를 선택 합니다.

    ![예제 VM에 대 한 기본 오버레이 스크린샷 중지 단추가 강조 표시 된](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. VM이 중지 되 면 **디스크** 를 선택 하 고 암호화 하려는 디스크를 선택 합니다.

    ![디스크 블레이드가 열려 있는 예제 VM의 스크린샷 OS 디스크는 선택할 수 있는 예제 디스크로 강조 표시 됩니다.](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. **암호화** 를 선택 하 고 **고객이 관리 하는 키를 사용 하 여 미사용 암호화** 를 선택한 후 드롭다운 목록에서 설정 된 디스크 암호화를 선택 합니다.
1. **저장**을 선택합니다.

    ![예제 OS 디스크의 스크린샷 암호화 블레이드가 열리고, 고객이 관리 하는 키를 사용 하 여 미사용 암호화를 선택 하 고 Azure Key Vault 예제를 선택 합니다. 이러한 항목을 선택 하면 저장 단추가 선택 됩니다.](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. 암호화 하려는 VM에 연결 된 다른 모든 디스크에 대해이 프로세스를 반복 합니다.
1. 디스크가 고객이 관리 하는 키로 전환 되 면 암호화 하려는 다른 연결 된 디스크가 없는 경우 VM을 시작할 수 있습니다.
