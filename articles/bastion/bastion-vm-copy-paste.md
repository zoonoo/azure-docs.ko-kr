---
title: 복사 하는 가상 컴퓨터에서 붙여넣습니다. Azure 배스 천 | Microsoft Docs
description: 이 문서에서는 설명 복사 방법과 배스 천을 사용 하 여 Azure VM에서 붙여 넣습니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 9d69d1a9fae258c9546a8c794fc0b0c68b952049
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191812"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion-preview"></a>복사 하는 가상 컴퓨터에 붙여넣습니다. Azure는 배스 천 (미리 보기)

이 문서에서는 복사한 Azure 배스 천을 사용 하는 경우에 가상 컴퓨터에서 텍스트를 붙여 넣습니다. VM을 사용 하 여 작업 하기 전에 해야 하는 단계를 수행한 [요새 호스트를 만드는](bastion-create-host-portal.md)합니다. 그런 다음 중 하나를 사용 하 여 사용 하려는 VM에 연결할 [RDP](bastion-connect-vm-rdp.md) 하거나 [SSH](bastion-connect-vm-ssh.md)합니다.

> [!IMPORTANT]
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>

고급 클립보드 API 액세스를 지 원하는 브라우저에 대 한 복사 하 수 복사 하 여 로컬 장치에서 응용 프로그램 사이 동일한 방식으로 로컬 장치 및 원격 세션 간에 텍스트를 붙여 넣습니다. 다른 브라우저의 경우 배스 천 클립보드 액세스 도구 팔레트를 사용할 수 있습니다.

  ![클립보드를 허용 합니다.](./media/bastion-vm-manage/allow.png)

만 텍스트 복사/붙여넣기가 지원 됩니다. 직접 복사 및 붙여넣기에 대 한 브라우저가 있습니다 클립보드 액세스에 대 한 때 묻는 배스 천 세션을 초기화 하는 중입니다. **허용** 웹 페이지에서 클립보드에 액세스 합니다.

## <a name="to"></a>원격 세션에 복사 합니다.

사용 하 여 가상 컴퓨터에 연결한 후 합니다 [Azure portal](https://aka.ms/BastionHost) 배스 천 미리 보기:

1. 로컬 장치에서 로컬 클립보드에 텍스트/콘텐츠를 복사 합니다.
1. 원격 세션에서 두 개의 화살표를 선택 하 여 배스 천 클립보드 액세스 도구 팔레트를 시작 합니다. 화살표는 세션의 왼쪽된 가운데에 있습니다.

    ![도구 팔레트](./media/bastion-vm-manage/left.png)

    ![클립보드](./media/bastion-vm-manage/clipboard.png)

1. 일반적으로 복사 된 텍스트 배스 천 복사 붙여넣기 색상표에서 자동으로 표시 합니다. 텍스트가 없으면 색상표에서 텍스트 영역에 다음 텍스트를 붙여넣습니다.
1. 텍스트 영역의 텍스트를 원격 세션에 붙여 넣을 수 있습니다.

    ![붙여넣기](./media/bastion-vm-manage/local.png)

## <a name="from"></a>원격 세션에서 복사

사용 하 여 가상 컴퓨터에 연결한 후 합니다 [Azure portal](https://aka.ms/BastionHost) 배스 천 미리 보기:

1. 원격 세션에서 (Ctrl + C를 사용 하 여) 원격 클립보드에 텍스트/콘텐츠를 복사 합니다.

    ![도구 팔레트](./media/bastion-vm-manage/remote.png)

1. 원격 세션에서 두 개의 화살표를 선택 하 여 배스 천 클립보드 액세스 도구 팔레트를 시작 합니다. 화살표는 세션의 왼쪽된 가운데에 있습니다.

    ![클립보드](./media/bastion-vm-manage/clipboard2.png)

1. 일반적으로 복사 된 텍스트 배스 천 복사 붙여넣기 색상표에서 자동으로 표시 합니다. 텍스트가 없으면 색상표에서 텍스트 영역에 다음 텍스트를 붙여넣습니다.
1. 텍스트 영역의 텍스트를 로컬 장치에 붙여넣을 수 있습니다.

    ![붙여넣기](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>다음 단계

읽기를 [배스 천 FAQ](bastion-faq.md)합니다.