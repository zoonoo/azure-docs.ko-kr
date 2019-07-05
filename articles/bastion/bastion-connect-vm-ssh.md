---
title: 배스 천 Azure를 사용 하 여 Linux VM에 연결 | Microsoft Docs
description: 이 문서에서는 Azure 배스 천을 사용 하 여 Linux Virtual Machine에 연결 하는 방법에 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 69548541d16db95f633400808f72aebaf59cff08
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477773"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion-preview"></a>SSH를 사용 하 여 Azure 배스 천 (미리 보기)를 사용 하 여 Linux 가상 머신에 연결

이 문서는 방법을 안내를 안전 하 고 원활 하 게 Azure virtual network에 Linux Vm에 SSH 합니다. Azure Portal에서 직접 VM에 연결할 수 있습니다. Azure Bastion을 사용하면 VM에 클라이언트, 에이전트 또는 추가 소프트웨어가 필요하지 않습니다. 배스 천 Azure에 대 한 자세한 내용은 참조는 [개요](bastion-overview.md)합니다.

SSH를 사용 하 여 Linux 가상 컴퓨터에 연결할 Azure 배스 천을 사용할 수 있습니다. 인증에 대 한 사용자 이름/암호 및 SSH 키를 사용할 수 있습니다. 사용 하 여 SSH 키를 사용 하 여 VM에 연결할 수 있습니다.

* 개인 키를 수동으로 입력
* 개인 키 정보를 포함 하는 파일

SSH 개인 키로 시작 하는 형식 이어야 합니다 `"-----BEGIN RSA PRIVATE KEY-----"` 끝나는 `"-----END RSA PRIVATE KEY-----"`합니다.

> [!IMPORTANT]
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>

## <a name="before-you-begin"></a>시작하기 전에

VM이 상주 하는 가상 네트워크를 Azure 요새 호스트를 설정 해야 합니다. 자세한 내용은 [Azure 요새 호스트를 만드는](bastion-create-host-portal.md)합니다. 배스 천 서비스 프로 비전 되 면 가상 네트워크에 배포 되는 모든 VM이 가상 네트워크에 연결을 사용할 수 있습니다. 이 미리 보기에서 배스 천을 사용 하 여 연결할 때 가정 Linux Vm에 연결할 Windows VM 및 SSH로 연결 하려면 RDP를 사용 한다고 합니다.

연결하려면 다음 역할이 필요합니다.

* 가상 머신에 대한 읽기 권한자 역할
* 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
* Azure Bastion 리소스에 대한 읽기 권한자 역할

## <a name="username"></a>연결 합니다. 사용자 이름 및 암호를 사용 하 여


1.  사용 하 여 [이 링크](https://aka.ms/BastionHost) 를 배스 천 Azure에 대 한 미리 보기 포털 페이지를 엽니다. 연결 하려는 가상 머신을 이동한 다음 클릭 **Connect**합니다. SSH 연결을 사용 하는 경우 VM에서 Linux 가상 머신 이어야 합니다.
1. 연결을 클릭 하면 세로 막대는 세 개의 탭 – RDP, SSH 및 배스 천은 표시 됩니다. 배스 천을 가상 네트워크에 대 한 프로 비전 하는 경우 배스 천 탭이 기본적으로 활성화 되어 있습니다. 배스 천 가상 네트워크를 프로 비전 하지 않은 경우 참조 [배스 천 구성](bastion-create-host-portal.md)합니다. 표시 되지 않으면 **배스 천** 나열을 열지 않은 경우 미리 보기 포털입니다. 사용 하 여 포털을 엽니다 [이 링크](https://aka.ms/BastionHost)합니다.

      ![VM 연결](./media/bastion-connect-vm-ssh/bastion.png)

1. 가상 컴퓨터에 SSH에 대 한 사용자 이름과 암호를 입력 합니다.
1. 클릭 **Connect** 키를 입력 한 후 단추입니다.

## <a name="privatekey"></a>연결 합니다. 개인 키를 수동으로 입력

1.  사용 하 여 [이 링크](https://aka.ms/BastionHost) 를 배스 천 Azure에 대 한 미리 보기 포털 페이지를 엽니다. 연결 하려는 가상 머신을 이동한 다음 클릭 **Connect**합니다. SSH 연결을 사용 하는 경우 VM에서 Linux 가상 머신 이어야 합니다.
1. 연결을 클릭 하면 세로 막대는 세 개의 탭 – RDP, SSH 및 배스 천은 표시 됩니다. 배스 천을 가상 네트워크에 대 한 프로 비전 하는 경우 배스 천 탭이 기본적으로 활성화 되어 있습니다. 배스 천 가상 네트워크를 프로 비전 하지 않은 경우 참조 [배스 천 구성](bastion-create-host-portal.md)합니다. 표시 되지 않으면 **배스 천** 나열을 열지 않은 경우 미리 보기 포털입니다. 사용 하 여 포털을 엽니다 [이 링크](https://aka.ms/BastionHost)합니다.

      ![VM 연결](./media/bastion-connect-vm-ssh/bastion.png)

1. 사용자 이름을 입력 하 고 선택 **SSH 개인 키**합니다.
1. 텍스트 영역에 사용자의 개인 키를 입력 **SSH 개인 키** (또는 직접 붙여).
1. 클릭 **Connect** 키를 입력 한 후 단추입니다.

## <a name="ssh"></a>연결 합니다. 개인 키 파일을 사용 하 여

1.  사용 하 여 [이 링크](https://aka.ms/BastionHost) 를 배스 천 Azure에 대 한 미리 보기 포털 페이지를 엽니다. 연결 하려는 가상 머신을 이동한 다음 클릭 **Connect**합니다. SSH 연결을 사용 하는 경우 VM에서 Linux 가상 머신 이어야 합니다.

    ![VM 연결](./media/bastion-connect-vm-ssh/connect.png)

1. 연결을 클릭 하면 세로 막대는 세 개의 탭 – RDP, SSH 및 배스 천은 표시 됩니다. 배스 천을 가상 네트워크에 대 한 프로 비전 하는 경우 배스 천 탭이 기본적으로 활성화 되어 있습니다. 배스 천 가상 네트워크를 프로 비전 하지 않은 경우 참조 [배스 천 구성](bastion-create-host-portal.md)합니다. 표시 되지 않으면 **배스 천** 나열을 열지 않은 경우 미리 보기 포털입니다. 사용 하 여 포털을 엽니다 [이 링크](https://aka.ms/BastionHost)합니다.

    ![VM 연결](./media/bastion-connect-vm-ssh/bastion.png)

1. 사용자 이름을 입력 하 고 선택 **SSH 개인 키 파일을 로컬에서**합니다.
1. 클릭 합니다 **찾아보기** 단추 (로컬 파일 폴더 아이콘).
1. 파일을 찾은 다음 클릭 **열려**합니다.
1. 클릭 **Connect** VM에 연결 합니다. 클릭 하면 Connect,이 가상 머신에 SSH는 Azure portal에서 직접 열립니다 됩니다. HTML5를 통해이 연결 된 가상 머신의 개인 IP를 통해 배스 천 서비스에서 포트 443을 사용 합니다.

## <a name="next-steps"></a>다음 단계

읽기는 [배스 천 FAQ](bastion-faq.md)
