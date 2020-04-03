---
title: '가상 컴퓨터로 복사 및 붙여넣기: Azure 요새'
description: 이 문서에서는 Bastion을 사용하여 Azure VM을 복사하고 붙여넣습니다.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 0af0fdfa1b0bcbb06d9424390e7ca8c8f406bcb3
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619312"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>복사하여 가상 시스템에 붙여넣기: Azure 요새

이 문서에서는 Azure Bastion을 사용할 때 가상 컴퓨터에서 텍스트를 복사하고 붙여넣습니다. VM으로 작업하기 전에 [배스션 호스트 만들기](bastion-create-host-portal.md)단계를 수행했는지 확인합니다. 그런 다음 [RDP](bastion-connect-vm-rdp.md) 또는 [SSH를](bastion-connect-vm-ssh.md)사용하여 작업하려는 VM에 연결합니다.

고급 Clipboard API 액세스를 지원하는 브라우저의 경우 로컬 장치의 응용 프로그램 간에 복사하고 붙여넣기와 동일한 방식으로 로컬 장치와 원격 세션 간에 텍스트를 복사하고 붙여칠 수 있습니다. 다른 브라우저의 경우 배스토클립보드 액세스 도구 팔레트를 사용할 수 있습니다.

   ![클립보드 허용](./media/bastion-vm-manage/allow.png)

텍스트 복사/붙여넣기만 지원됩니다. 직접 복사 및 붙여넣기의 경우 브라우저에서 Bastion 세션이 초기화될 때 클립보드 액세스에 대한 메시지를 표시할 수 있습니다. 웹 페이지가 클립보드에 액세스할 **수 있도록 허용합니다.**

## <a name="copy-to-a-remote-session"></a><a name="to"></a>원격 세션으로 복사

[Azure 포털을 ](https://portal.azure.com)사용하여 가상 컴퓨터에 연결한 후 다음 단계를 완료합니다.

1. 로컬 장치에서 텍스트/콘텐츠를 로컬 클립보드에 복사합니다.
1. 원격 세션 중에 두 화살표를 선택하여 배스온 클립보드 액세스 도구 팔레트를 시작합니다. 화살표는 세션의 왼쪽 중앙에 있습니다.

   ![도구 팔레트](./media/bastion-vm-manage/left.png)

   ![클립보드](./media/bastion-vm-manage/clipboard.png)
1. 일반적으로 복사된 텍스트는 Bastion 복사 붙여넣기 팔레트에 자동으로 표시됩니다. 텍스트가 없는 경우 팔레트의 텍스트 영역에 텍스트를 붙여넣습니다.
1. 텍스트가 텍스트 영역에 있으면 원격 세션에 붙여넣기를 할 수 있습니다.

   ![붙여넣기](./media/bastion-vm-manage/local.png)

## <a name="copy-from-a-remote-session"></a><a name="from"></a>원격 세션에서 복사

[Azure 포털을 ](https://portal.azure.com)사용하여 가상 컴퓨터에 연결한 후 다음 단계를 완료합니다.

1. 원격 세션의 텍스트/콘텐츠를 Ctrl-C 사용으로 원격 클립보드에 복사합니다.

   ![도구 팔레트](./media/bastion-vm-manage/remote.png)
1. 원격 세션 중에 두 화살표를 선택하여 배스온 클립보드 액세스 도구 팔레트를 시작합니다. 화살표는 세션의 왼쪽 중앙에 있습니다.

   ![클립보드](./media/bastion-vm-manage/clipboard2.png)
1. 일반적으로 복사된 텍스트는 Bastion 복사 붙여넣기 팔레트에 자동으로 표시됩니다. 텍스트가 없는 경우 팔레트의 텍스트 영역에 텍스트를 붙여넣습니다.
1. 텍스트가 텍스트 영역에 있으면 로컬 장치에 붙여넣기할 수 있습니다.

   ![붙여넣기](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>다음 단계

[바스티온 자주 묻는 질문 읽기.](bastion-faq.md)