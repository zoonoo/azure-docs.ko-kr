---
title: Azure 요새를 사용하여 Linux VM에 연결
description: 이 문서에서는 Azure Bastion을 사용하여 Linux 가상 컴퓨터에 연결하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 381f45a51002188c72174cff83c26b829912a0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596830"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Azure 요새를 사용하여 SSH를 사용하여 Linux 가상 컴퓨터에 연결

이 문서에서는 Azure 가상 네트워크에서 Linux VM에 안전하고 원활하게 SSH를 수행하는 방법을 보여 줍니다. Azure 포털에서 직접 VM에 연결할 수 있습니다. Azure Bastion을 사용하는 경우 VM에는 클라이언트, 에이전트 또는 추가 소프트웨어가 필요하지 않습니다. Azure 요새에 대한 자세한 내용은 [개요](bastion-overview.md)를 참조하십시오.

Azure Bastion을 사용하여 SSH를 사용하여 Linux 가상 컴퓨터에 연결할 수 있습니다. 인증을 위해 사용자 이름/암호 및 SSH 키를 모두 사용할 수 있습니다. 다음 중 하나를 사용하여 SSH 키를 사용하여 VM에 연결할 수 있습니다.

* 수동으로 입력하는 개인 키
* 개인 키 정보가 포함된 파일

SSH 개인 키는 에서 시작하고 `"-----BEGIN RSA PRIVATE KEY-----"` 로 끝나는 형식이어야 `"-----END RSA PRIVATE KEY-----"`합니다.

## <a name="before-you-begin"></a>시작하기 전에

VM이 있는 가상 네트워크에 Azure 요새 호스트를 설정했는지 확인합니다. 자세한 내용은 [Azure 요새 호스트 만들기를](bastion-create-host-portal.md)참조하십시오. Bastion 서비스가 프로비전되고 가상 네트워크에 배포되면 이 가상 네트워크의 모든 VM에 연결하는 데 사용할 수 있습니다. 

Bastion을 사용하여 연결하면 RDP를 사용하여 Windows VM에 연결하고 SSH를 사용하여 Linux VM에 연결하는 것으로 가정합니다. Windows VM연결에 대한 자세한 내용은 [VM - Windows에 연결 참조](bastion-connect-vm-rdp.md)

### <a name="required-roles"></a>필수 역할

연결하려면 다음 역할이 필요합니다.

* 가상 머신에 대한 읽기 권한자 역할
* 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
* Azure Bastion 리소스에 대한 읽기 권한자 역할

### <a name="ports"></a>포트

SSH를 통해 Linux VM에 연결하려면 VM에서 다음 포트가 열려 있어야 합니다.

* 인바운드 포트: SSH (22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>연결: 사용자 이름 및 암호 사용

1. Azure [포털을](https://portal.azure.com)엽니다. 연결하려는 가상 컴퓨터로 이동한 다음 **연결을** 클릭하고 드롭다운에서 **방신을** 선택합니다.

   ![연결](./media/bastion-connect-vm-ssh/connect.png)
1. 요새를 클릭하면 RDP, SSH 및 요새의 세 개의 탭이 있는 사이드 바가 나타납니다. 대호션이 가상 네트워크에 대해 프로비전된 경우 기본적으로 요새 탭이 활성화되어 있습니다. 가상 네트워크에 대한 요새를 프로비전하지 않은 경우 [요새 구성](bastion-create-host-portal.md)을 참조하십시오.

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. 가상 컴퓨터에 SSH의 사용자 이름과 암호를 입력합니다.
1. 키를 입력한 후 **연결** 버튼을 클릭합니다.

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>연결: 개인 키를 수동으로 입력합니다.

1. Azure [포털을](https://portal.azure.com)엽니다. 연결하려는 가상 컴퓨터로 이동한 다음 **연결을** 클릭하고 드롭다운에서 **방신을** 선택합니다.

   ![연결](./media/bastion-connect-vm-ssh/connect.png)
1. 요새를 클릭하면 RDP, SSH 및 요새의 세 개의 탭이 있는 사이드 바가 나타납니다. 대호션이 가상 네트워크에 대해 프로비전된 경우 기본적으로 요새 탭이 활성화되어 있습니다. 가상 네트워크에 대한 요새를 프로비전하지 않은 경우 [요새 구성](bastion-create-host-portal.md)을 참조하십시오.

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. 사용자 이름을 입력하고 **SSH 개인 키를**선택합니다.
1. 개인 키를 텍스트 영역 **SSH 개인 키에** 입력하거나 직접 붙여 넣습니다.
1. 키를 입력한 후 **연결** 버튼을 클릭합니다.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>연결: 개인 키 파일 사용

1. Azure [포털을](https://portal.azure.com)엽니다. 연결하려는 가상 컴퓨터로 이동한 다음 **연결을** 클릭하고 드롭다운에서 **방신을** 선택합니다.

   ![연결](./media/bastion-connect-vm-ssh/connect.png)
1. 요새를 클릭하면 RDP, SSH 및 요새의 세 개의 탭이 있는 사이드 바가 나타납니다. 대호션이 가상 네트워크에 대해 프로비전된 경우 기본적으로 요새 탭이 활성화되어 있습니다. 가상 네트워크에 대한 요새를 프로비전하지 않은 경우 [요새 구성](bastion-create-host-portal.md)을 참조하십시오.

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. 사용자 이름을 입력하고 **로컬 파일에서 SSH 개인 키를**선택합니다.
1. **찾아보기** 단추(로컬 파일의 폴더 아이콘)를 클릭합니다.
1. 파일을 찾아보고 **열기를**클릭합니다.
1. **연결을** 클릭하여 VM에 연결합니다. 연결을 클릭하면 이 가상 시스템에 대한 SSH가 Azure 포털에서 직접 열립니다. 이 연결은 가상 시스템의 개인 IP를 통해 Bastion 서비스에서 포트 443을 사용하여 HTML5를 초과합니다.

## <a name="next-steps"></a>다음 단계

요새 [자주 묻는 질문](bastion-faq.md) 읽기
