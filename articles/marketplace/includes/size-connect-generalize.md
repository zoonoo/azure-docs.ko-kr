---
title: 포함 파일
description: 파일
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 03/25/2021
ms.openlocfilehash: 8898a762e8a1e7a2d5c104f99d12032c676a5ca4
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630055"
---
## <a name="generalize-the-image"></a>이미지 일반화

Azure Marketplace의 모든 이미지는 일반적으로 다시 사용할 수 있어야 합니다. 이를 달성하려면 운영 체제 VHD가 일반화되어야 합니다. 이 작업은 VM에서 인스턴스 관련 식별자와 소프트웨어 드라이버를 모두 제거합니다.

### <a name="for-windows"></a>Windows의 경우

Windows OS 디스크는 [sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) 도구를 사용 하 여 일반화 됩니다. 나중에 OS를 업데이트 하거나 다시 구성 하는 경우 sysprep을 다시 실행 해야 합니다.

> [!WARNING]
> Sysprep을 실행 한 후 업데이트가 자동으로 실행 될 수 있기 때문에 배포 될 때까지 VM을 해제 합니다. 이렇게 종료하면 이후 업데이트에서 운영 체제 또는 설치된 서비스에 대한 인스턴스 관련 변경이 수행되지 않습니다. sysprep 실행에 대한 자세한 내용은 [VHD를 일반화하는 단계](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)를 참조하세요.

### <a name="for-linux"></a>Linux의 경우

다음 프로세스에서 Linux VM을 일반화하여 별도의 VM으로 다시 배포합니다. 자세한 내용은 [가상 머신 또는 VHD의 이미지를 만드는 방법](../../virtual-machines/linux/capture-image.md)을 참조하세요. "캡처된 이미지에서 VM 만들기" 라는 섹션에 도달 하면를 중지할 수 있습니다.

1. Azure Linux 에이전트를 제거 합니다.
    1. SSH 클라이언트를 사용하여 Linux VM에 연결합니다.
    2. SSH 창에서 다음 명령을 입력 `sudo waagent –deprovision+user` 합니다.
    3. Y를 입력하여 계속합니다(확인 단계를 방지하려면 이전 명령에 -force 매개 변수를 추가할 수 있음).
    4. 명령이 완료 되 면 **Exit** 를 입력 하 여 SSH 클라이언트를 닫습니다.
2. 가상 컴퓨터를 중지 합니다.
    1. Azure Portal에서 RG(리소스 그룹)를 선택하고, VM을 할당 취소합니다.
    2. 이제 VM이 일반화 되어이 VM 디스크를 사용 하 여 새 VM을 만들 수 있습니다.

### <a name="capture-image"></a>이미지 캡처

VM이 준비 되 면 Azure 공유 이미지 갤러리에서 캡처할 수 있습니다. 캡처하려면 다음 단계를 따르세요.

1. [Azure Portal](https://ms.portal.azure.com/)에서 가상 컴퓨터의 페이지로 이동 합니다.
2. **캡처** 를 선택 합니다.
3. **공유 이미지에 이미지 공유 갤러리** 에서 **예, 이미지 버전으로 갤러리에 공유** 를 선택 합니다.
4. **운영 체제 상태** 에서 일반화를 선택 합니다.
5. 대상 이미지 갤러리를 선택 하거나 **새로 만듭니다**.
6. 대상 이미지 정의를 선택 하거나 **새로 만듭니다**.
7. 이미지에 대 한 **버전 번호** 를 제공 합니다.
8. **검토 + 만들기** 를 선택하여 선택 사항을 검토합니다.
9. 유효성 검사를 통과 한 후 **만들기** 를 선택 합니다.

SIG를 포함 하는 Azure 구독은 게시 하기 위해 게시자 계정과 동일한 테 넌 트 아래에 있어야 합니다. 또한 게시자 계정에는 SIG에 대 한 소유자 액세스 권한이 있어야 합니다. 

액세스 권한을 부여 하려면:

1. 공유 이미지 갤러리로 이동 합니다.
2. 왼쪽 패널에서 **Access control** (IAM)을 선택 합니다.
3. **추가** 를 선택한 다음 **역할 할당 추가** 를 선택 합니다.
4. **역할** 또는 **소유자** 를 선택 합니다.
5. **액세스 할당** 에서 **사용자, 그룹 또는 서비스 주체** 를 선택 합니다.
6. 이미지를 게시할 사용자의 Azure 전자 메일을 선택 합니다.
7. **저장** 을 선택합니다.

:::image type="content" source="../media/create-vm/add-role-assignment.png" alt-text="역할 할당 추가 창을 표시 합니다.":::

> [!NOTE]
> 이제 파트너 센터에서 SIG 이미지를 게시할 수 있으므로 SAS Uri를 생성할 필요가 없습니다. 그러나 SAS URI 생성 단계를 계속 참조 해야 하는 경우에는 [VM 이미지에 대 한 SAS uri를 생성 하는 방법](../azure-vm-get-sas-uri.md)을 참조 하세요.
