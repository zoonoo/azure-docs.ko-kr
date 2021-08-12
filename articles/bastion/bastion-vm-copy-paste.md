---
title: '가상 머신으로 혹은 가상 머신에서 복사하여 붙여넣기: Azure Bastion'
description: Bastion을 사용하여 Azure VM으로 혹은 Azure VM에서 복사하고 붙여넣는 방법에 대해 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cherylmc
ms.openlocfilehash: b8c90e3c8bea7f6471a60804f546957f5236b70b
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110536016"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>가상 머신에 복사하여 붙여넣기: Azure Bastion

이 문서는 Azure Bastion을 사용할 때 가상 머신으로 혹은 가상 머신에서 복사하여 붙여넣는 방법에 대한 설명을 제공합니다. VM을 사용하려면 먼저 다음 단계를 따라 [베스천 호스트를 만들어야](./tutorial-create-host-portal.md) 합니다. 그런 다음 [RDP](bastion-connect-vm-rdp.md) 또는 [SSH](bastion-connect-vm-ssh.md)를 사용하여 작업하려는 VM에 연결합니다.

고급 클립보드 API 액세스를 지원하는 브라우저의 경우 로컬 디바이스에서 애플리케이션 간에 복사하여 붙여넣는 것과 동일한 방식으로 로컬 디바이스와 원격 세션 간에 복사하여 붙여넣을 수 있습니다. 다른 브라우저의 경우에는 Bastion 클립보드 액세스 도구 팔레트를 사용할 수 있습니다.

>[!NOTE]
>현재는 텍스트 복사/붙여넣기만 지원됩니다.
>

   ![클립보드 허용](./media/bastion-vm-manage/allow.png)

텍스트 복사/붙여넣기만 지원됩니다. 직접 복사하여 붙여넣으려는 경우 Bastion 세션이 시작될 때 브라우저에서 클립보드 액세스를 확인할 수 있습니다. 웹 페이지에서 클립보드 액세스를 **허용** 합니다. Mac에서 작업하는 경우 붙여넣기의 바로 가기 키는 **SHIFT-CTRL-V** 입니다.

## <a name="copy-to-a-remote-session"></a><a name="to"></a>원격 세션에 복사

[Azure Portal](https://portal.azure.com)을 사용하여 가상 머신에 연결한 후 다음 단계를 완료합니다.

1. 로컬 디바이스의 텍스트/콘텐츠를 로컬 클립보드에 복사합니다.
1. 원격 세션 중에 두 화살표를 선택하여 Bastion 클립보드 액세스 도구 팔레트를 시작합니다. 화살표는 세션의 왼쪽 가운데에 있습니다.

   ![창의 왼쪽에 강조 표시된 도구 팔레트의 시작 화살표를 보여 주는 스크린샷](./media/bastion-vm-manage/left.png)

   ![스크린샷에는 Bastion에 복사된 텍스트의 클립보드가 표시됩니다.](./media/bastion-vm-manage/clipboard.png)
1. 일반적으로 복사된 텍스트는 자동으로 Bastion 복사 붙여넣기 팔레트에 표시됩니다. 텍스트가 없으면 팔레트의 텍스트 영역에 텍스트를 붙여넣습니다.
1. 텍스트가 텍스트 영역에 있으면 원격 세션에 붙여넣을 수 있습니다.

   ![강조 표시된 복사/붙여넣기 단추와 원격 세션에 복사된 샘플 텍스트 문자열을 보여 주는 스크린샷](./media/bastion-vm-manage/local.png)

## <a name="copy-from-a-remote-session"></a><a name="from"></a>원격 세션에서 복사

[Azure Portal](https://portal.azure.com)을 사용하여 가상 머신에 연결한 후 다음 단계를 완료합니다.

1. 원격 세션에서 원격 클립보드로 텍스트/콘텐츠를 복사합니다(Ctrl-C 사용).

   ![도구 팔레트](./media/bastion-vm-manage/remote.png)
1. 원격 세션 중에 두 화살표를 선택하여 Bastion 클립보드 액세스 도구 팔레트를 시작합니다. 화살표는 세션의 왼쪽 가운데에 있습니다.

   ![클립보드](./media/bastion-vm-manage/clipboard2.png)
1. 일반적으로 복사된 텍스트는 자동으로 Bastion 복사 붙여넣기 팔레트에 표시됩니다. 텍스트가 없으면 팔레트의 텍스트 영역에 텍스트를 붙여넣습니다.
1. 텍스트가 텍스트 영역에 있으면 로컬 디바이스에 붙여넣을 수 있습니다.

   ![붙여넣기](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>다음 단계

[Bastion FAQ](bastion-faq.md)를 참조하세요.
