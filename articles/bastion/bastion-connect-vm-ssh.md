---
title: Azure 방호를 사용 하 여 Linux VM에 연결
description: 이 문서에서는 Azure 방호를 사용 하 여 Linux 가상 머신에 연결 하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/12/2021
ms.author: cherylmc
ms.openlocfilehash: a5cda5d4d447bc04f853ea4a9abd15be75e7e177
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588717"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Azure 방호를 사용 하 여 Linux 가상 머신에 SSH를 사용 하 여 연결

이 문서에서는 Azure 가상 네트워크에서 Linux Vm에 안전 하 고 원활 하 게 SSH 하는 방법을 보여 줍니다. Azure Portal에서 직접 VM에 연결할 수 있습니다. Azure 방호를 사용 하는 경우 Vm에는 클라이언트, 에이전트 또는 추가 소프트웨어가 필요 하지 않습니다. Azure 방호에 대 한 자세한 내용은 [개요](bastion-overview.md)를 참조 하세요.

Azure 방호를 사용 하 여 SSH를 통해 Linux 가상 머신에 연결할 수 있습니다. 인증에 사용자 이름/암호 및 SSH 키를 모두 사용할 수 있습니다. 다음 중 하나를 사용 하 여 SSH 키를 사용 하 여 VM에 연결할 수 있습니다.

* 수동으로 입력 하는 개인 키
* 개인 키 정보를 포함 하는 파일입니다.

SSH 개인 키는로 시작 하 고로 끝나는 형식 이어야 합니다  `"-----BEGIN RSA PRIVATE KEY-----"` `"-----END RSA PRIVATE KEY-----"` .

## <a name="prerequisites"></a>사전 요구 사항

VM이 상주 하는 가상 네트워크에 대 한 Azure 방호 호스트를 설정 했는지 확인 합니다. 자세한 내용은 [Azure 방호 호스트 만들기](./tutorial-create-host-portal.md)를 참조 하세요. 요새 서비스가 프로 비전 되 고 가상 네트워크에 배포 되 면이를 사용 하 여이 가상 네트워크의 VM에 연결할 수 있습니다. 

방호를 사용 하 여 연결 하는 경우 RDP를 사용 하 여 Windows VM에 연결 하 고 SSH를 사용 하 여 Linux Vm에 연결 하는 것으로 가정 합니다. Windows VM에 연결 하는 방법에 대 한 자세한 내용은 [vm에 연결-Windows](bastion-connect-vm-rdp.md)를 참조 하세요.

### <a name="required-roles"></a>필요한 역할

연결하려면 다음 역할이 필요합니다.

* 가상 머신에 대한 읽기 권한자 역할
* 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
* Azure Bastion 리소스에 대한 읽기 권한자 역할

### <a name="ports"></a>포트

SSH를 통해 Linux VM에 연결 하려면 VM에서 다음 포트를 열어야 합니다.

* 인바운드 포트: SSH (22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>연결: 사용자 이름 및 암호 사용

1. [Azure Portal](https://portal.azure.com)을 엽니다. 연결 하려는 가상 머신으로 이동한 다음 **연결** 을 클릭 하 고 드롭다운에서 **방호** 를 선택 합니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="연결이 선택 된 Azure Portal의 가상 컴퓨터에 대 한 개요를 보여 주는 스크린샷":::
1. 요새를 선택한 후에는 **요새 사용** 을 클릭 합니다. 가상 네트워크에 대 한 방호를 프로 비전 하지 않은 경우에는 [요새 구성](./quickstart-host-portal.md)을 참조 하세요.
1. **Azure 방호를 사용 하 여 연결** 페이지에서 **사용자 이름** 및 **암호** 를 입력 합니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh/password.png" alt-text="암호 인증":::
1. **연결** 을 선택 하 여 VM에 연결 합니다.

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Connect: 개인 키를 수동으로 입력 합니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다. 연결 하려는 가상 머신으로 이동한 다음 **연결** 을 클릭 하 고 드롭다운에서 **방호** 를 선택 합니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="연결이 선택 된 Azure Portal의 가상 컴퓨터에 대 한 개요를 보여 주는 스크린샷":::
1. 요새를 선택한 후에는 **요새 사용** 을 클릭 합니다. 가상 네트워크에 대 한 방호를 프로 비전 하지 않은 경우에는 [요새 구성](./quickstart-host-portal.md)을 참조 하세요.
1. **Azure 방호를 사용 하 여 연결** 페이지에서 **사용자 이름** 및 **SSH 개인 키** 를 입력 합니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh/ssh-private-key.png" alt-text="SSH 개인 키 인증":::
1. 텍스트 영역 **SSH 개인 키** 에 개인 키를 입력 하거나 직접 붙여 넣습니다.
1. **연결** 을 선택 하 여 VM에 연결 합니다.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>연결: 개인 키 파일 사용

1. [Azure Portal](https://portal.azure.com)을 엽니다. 연결 하려는 가상 머신으로 이동한 다음 **연결** 을 클릭 하 고 드롭다운에서 **방호** 를 선택 합니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="연결이 선택 된 Azure Portal의 가상 컴퓨터에 대 한 개요를 보여 주는 스크린샷":::
1. 요새를 선택한 후에는 **요새 사용** 을 클릭 합니다. 가상 네트워크에 대 한 방호를 프로 비전 하지 않은 경우에는 [요새 구성](./quickstart-host-portal.md)을 참조 하세요.
1. **Azure 방호를 사용 하 여 연결** 페이지에서 로컬 파일의 **사용자 이름** 및 **SSH 개인 키** 를 입력 합니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh/private-key-file.png" alt-text="SSH 개인 키 파일":::

1. 파일을 찾은 다음 **열기** 를 선택 합니다.
1. **연결** 을 선택 하 여 VM에 연결 합니다. 연결을 클릭 하면이 가상 컴퓨터에 대 한 SSH가 Azure Portal에서 바로 열립니다. 이 연결은 가상 머신의 개인 IP를 통해 요새 서비스에서 포트 443을 사용 하 여 HTML5를 통해 연결 됩니다.

## <a name="connect-using-a-private-key-stored-in-azure-key-vault"></a><a name="akv"></a>연결: Azure Key Vault에 저장 된 개인 키 사용

>[!NOTE]
>이 기능에 대 한 포털 업데이트는 현재 지역으로 롤아웃 중입니다.
>

1. [Azure Portal](https://portal.azure.com)을 엽니다. 연결 하려는 가상 머신으로 이동한 다음 **연결** 을 클릭 하 고 드롭다운에서 **방호** 를 선택 합니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="연결이 선택 된 Azure Portal의 가상 컴퓨터에 대 한 개요를 보여 주는 스크린샷":::
1. 요새를 선택한 후에는 **요새 사용** 을 클릭 합니다. 가상 네트워크에 대 한 방호를 프로 비전 하지 않은 경우에는 [요새 구성](./quickstart-host-portal.md)을 참조 하세요.
1. **Azure 방호를 사용 하 여 연결** 페이지에서 **사용자 이름을** 입력 하 고 **Azure Key Vault에서 SSH 개인 키** 를 선택 합니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh/ssh-key-vault.png" alt-text="Azure Key Vault의 SSH 개인 키":::
1. **Azure Key Vault** 드롭다운을 선택 하 고 SSH 개인 키를 저장 한 리소스를 선택 합니다. Azure Key Vault 리소스를 설정 하지 않은 경우 [키 자격 증명 모음 만들기](../key-vault/general/quick-create-portal.md) 를 참조 하 고 SSH 개인 키를 새 Key Vault 비밀 값으로 저장 합니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh/key-vault.png" alt-text="Azure Key Vault":::

   Key Vault 리소스에 저장 된 암호에 대 한 **목록** **및 액세스 권한이 있는지** 확인 하세요. Key Vault 리소스에 대 한 액세스 정책을 할당 하 고 수정 하려면 [Key Vault 액세스 정책 할당](../key-vault/general/assign-access-policy-portal.md)을 참조 하세요.
1. **Azure Key Vault 암호** 드롭다운을 선택 하 고 SSH 개인 키 값을 포함 하는 Key Vault 암호를 선택 합니다.
1. **연결** 을 선택 하 여 VM에 연결 합니다. **연결** 을 클릭 하면이 가상 컴퓨터에 대 한 SSH가 Azure Portal에서 바로 열립니다. 이 연결은 가상 머신의 개인 IP를 통해 요새 서비스에서 포트 443을 사용 하 여 HTML5를 통해 연결 됩니다.

## <a name="next-steps"></a>다음 단계

Azure에 대 한 자세한 내용은 Azure 방호 [FAQ](bastion-faq.md)를 참조 하세요. 
