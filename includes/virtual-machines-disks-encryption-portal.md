---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3ba5d74aa245fbcd9d43f2b4398387d7f59e202c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299516"
---
### <a name="portal"></a>포털

디스크에 대해 고객 관리 키를 설정하려면 리소스를 처음 수행하는 경우 특정 순서로 리소스를 만들어야 합니다. 먼저 Azure 키 자격 증명 모음을 만들고 설정해야 합니다.

#### <a name="setting-up-your-azure-key-vault"></a>Azure 키 볼트 설정

1. [Azure 포털에](https://portal.azure.com/) 로그인하고 키 자격 증명 모음을 검색합니다.
1. 키 볼트를 검색하고 **선택합니다.**

    [![sse 키 - 볼트 - 포털 - 검색.png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Azure 키 자격 증명 모음, 디스크 암호화 집합, VM, 디스크 및 스냅숏은 모두 동일한 리전및 서브스크립션에 있어야 배포가 성공해야 합니다.

1. 새 키 자격 증명 모음을 만들려면 **+Add를** 선택합니다.
1. 새 리소스 그룹 만들기
1. 키 자격 증명 모음 이름을 입력하고 지역을 선택하고 가격 책정 계층을 선택합니다.
1. **검토 + 만들기를**선택하고 선택 사항을 확인한 다음 을 **선택합니다.**

    ![Azure 키 볼트 생성 환경의 스크린샷입니다. 만든 특정 값 표시](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. 키 자격 증명 모음 배포가 완료되면 키 볼트를 선택합니다.
1. **설정**에서 **키를** 선택합니다.
1. **생성/가져오기** 선택

    ![키 볼트 리소스 설정 창의 스크린샷입니다. 설정 내부의 생성/가져오기 단추를 표시합니다.](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. 키 **유형을** **RSA및** **RSA 키 크기로** 모두 **2080으로**설정합니다.
1. 원하는 대로 나머지 선택 항목을 입력한 다음 **만들기를**선택합니다.

    ![생성/가져오기 버튼을 선택하면 나타나는 키 블레이드 만들기의 스크린샷](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>디스크 암호화 세트 설정

디스크 암호화 세트를 만들고 구성하려면 다음 링크를 https://aka.ms/diskencryptionsets사용해야 합니다. Microsoft Azure 정부 지역에 있는 경우 이 링크를 대신 [https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)사용해야 합니다. 디스크 암호화 집합 만들기는 아직 전역 Azure Portal에서 사용할 수 없습니다.

1. 해당 지역에 적합한 디스크 암호화 세트 링크를 엽니다.

    공공 지역:[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Azure 정부 지역:[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)
    
1. **+추가**를 선택합니다.

    ![디스크 암호화 포털 메인 화면의 스크린샷입니다. 추가 단추 강조 표시](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. 리소스 그룹을 선택하고 암호화 집합의 이름을 지정하고 키 자격 증명 모음과 동일한 지역을 선택합니다.
1. **키 자격 증명 모음 및 키를**선택합니다.
1. 이전에 만든 키 자격 증명 모음 및 키와 버전을 선택합니다.
1. **선택**을 누릅니다.
1. **검토 + 만들기를** 선택한 다음 **을 만듭니다.**

    ![디스크 암호화 생성 블레이드의 스크린샷입니다. 구독, 리소스 그룹, 디스크 암호화 세트 이름, 지역 및 키 자격 증명 모음 + 키 선택기 표시](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. 디스크 암호화 집합생성이 완료되면 열고 팝업 경고를 선택합니다.

    ![경고 팝업 스크린샷: '디스크, 이미지 또는 스냅샷을 디스크 암호화 세트와 연결하려면 키 자격 증명 모음에 권한을 부여해야 합니다.' 계속하려면 이 경고를 선택합니다.](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

두 개의 알림이 나타나고 성공해야 합니다. 이렇게 하면 키 자격 증명 모음과 함께 설정된 디스크 암호화를 사용할 수 있습니다.

![키 자격 증명 모음에 대한 성공적인 권한 및 역할 할당 스크린샷입니다.](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>VM 배포

키 자격 증명 모음및 디스크 암호화 세트를 만들고 설정한 이제 암호화를 사용하여 VM을 배포할 수 있습니다.
VM 배포 프로세스는 표준 배포 프로세스와 유사하며, 유일한 차이점은 다른 리소스와 동일한 지역에 VM을 배포해야 하고 고객 관리 키를 사용하도록 선택하는 것입니다.

1. 해당 지역에 적합한 디스크 암호화 세트 링크를 엽니다.

    공공 지역:[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Azure 정부 지역:[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)

1. 가상 **컴퓨터를** 검색하고 **+ 추가를** 선택하여 VM을 만듭니다.
1. **기본** 탭에서 디스크 암호화 세트 및 Azure 키 자격 증명 모음과 동일한 지역을 선택합니다.
1. **기본** 탭의 다른 값을 원하는 대로 채웁니다.

    ![지역 값이 강조 표시된 VM 만들기 환경의 스크린샷입니다.](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. **디스크** 탭에서 고객이 **관리하는 키를 사용 하 고 미사용 암호화를**선택합니다.
1. 디스크 암호화 세트 드롭다운에서 **디스크 암호화 세트를** 선택합니다.
1. 원하는 대로 나머지 항목을 선택합니다.

    ![VM 생성 경험의 스크린 샷, 디스크 블레이드. 디스크 암호화 세트 드롭다운이 강조 표시됩니다.](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)

#### <a name="enable-on-an-existing-disk"></a>기존 디스크에서 사용

기존 디스크에서 디스크 암호화를 관리하고 구성하려면 다음 링크를 https://aka.ms/diskencryptionsets사용해야 합니다. 글로벌 Azure 포털에서는 기존 디스크에서 고객 관리 키를 사용하도록 설정하지 않습니다.

> [!CAUTION]
> VM에 연결된 모든 디스크에서 디스크 암호화를 사용하도록 설정하려면 VM을 중지해야 합니다.

1. 해당 지역에 적합한 디스크 암호화 세트 링크를 엽니다.

    공공 지역:[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Azure 정부 지역:[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)
    
1. 디스크 암호화 집합 중 하나와 동일한 지역에 있는 VM으로 이동합니다.
1. VM을 열고 **중지를 선택합니다.**

    ![예제 VM의 기본 오버레이 스크린샷입니다. 정지 버튼을 강조 표시](media/virtual-machines-disk-encryption-portal/sse-stop-VM-to-encrypt-disk.png)

1. VM이 중지를 완료한 후 디스크를 선택한 다음 암호화할 디스크를 **선택합니다.**

    ![디스크 블레이드가 열려 있는 예제 VM의 스크린샷입니다. OS 디스크를 선택할 예제 디스크로 강조 표시됩니다.](media/virtual-machines-disk-encryption-portal/sse-existing-disk-select.png)

1. **암호화를** 선택하고 **고객이 관리하는 키를 사용** 하 고 드롭다운 목록에서 디스크 암호화 집합을 선택 합니다.
1. **저장**을 선택합니다.

    ![예제 OS 디스크의 스크린샷입니다. 암호화 블레이드가 열려 있으며, 고객 관리 키가 있는 미사용 암호화가 선택되고 예제 Azure Key Vault가 선택됩니다. 이러한 선택을 하면 저장 단추가 선택됩니다.](media/virtual-machines-disk-encryption-portal/sse-encrypt-existing-disk-customer-managed-key.png)

1. 암호화하려는 VM에 연결된 다른 디스크에 대해 이 프로세스를 반복합니다.
1. 디스크가 고객 관리 키로 전환이 끝나면 암호화하려는 다른 연결된 디스크가 없는 경우 VM을 시작할 수 있습니다.