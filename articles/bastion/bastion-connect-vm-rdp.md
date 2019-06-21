---
title: 배스 천 Azure를 사용 하 여 Windows VM에 연결 | Microsoft Docs
description: 이 문서에서는 Azure 배스 천을 사용 하 여 Windows를 실행 중인 Azure Virtual Machine에 연결 하는 방법에 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: c8a4b09a27325f31e548d1b345b2932c6ab6315c
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191890"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion-preview"></a>Azure 배스 천 (미리 보기)를 사용 하 여 Windows 가상 머신에 연결

이 아티클에서 네트워크 하는 방법에 안전 하 고 원활 하 게 가상 Azure에서 Windows Vm에 RDP 배스 천 Azure를 사용 하 여 합니다. Azure portal에서 직접 VM에 연결할 수 있습니다. 배스 천 Azure를 사용 하는 경우 Vm은 클라이언트, 에이전트 또는 추가 소프트웨어 필요 하지 않습니다. 배스 천 Azure에 대 한 자세한 내용은 참조는 [개요](bastion-overview.md)합니다.

> [!IMPORTANT]
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>

## <a name="before-you-begin"></a>시작하기 전에

VM이 상주 하는 가상 네트워크를 Azure 요새 호스트를 설정 해야 합니다. 자세한 내용은 [Azure 요새 호스트를 만드는](bastion-create-host-portal.md)합니다. 배스 천 서비스 프로 비전 되 면 가상 네트워크에 배포 되는 모든 VM이 가상 네트워크에 연결을 사용할 수 있습니다. 이 미리 보기에서는 배스 천 Linux Vm에 연결할 Windows VM 및 SSH로 연결 하려면 RDP를 사용 한다고 가정 합니다. Linux VM에 연결 하는 방법에 대 한 내용은 [Linux VM에 연결](bastion-connect-vm-ssh.md)합니다.

연결 하기 위해 다음 역할이 필요 합니다.

* 가상 컴퓨터에 대 한 읽기 권한자 역할
* 가상 머신의 개인 IP 사용 하 여 NIC에 대 한 읽기 권한자 역할
* 배스 천 Azure 리소스에 대 한 읽기 권한자 역할

## <a name="rdp"></a>RDP를 사용 하 여 연결

1. 에 [Azure portal](https://aka.ms/BastionHost) 배스 천 미리 보기의 경우 연결 하려는 가상 머신을 이동한 다음 클릭 **Connect**합니다. VM이 RDP 연결을 사용 하는 경우 Windows 가상 컴퓨터 여야 합니다.

    ![VM 연결](./media/bastion-connect-vm-rdp/connect.png)

1. 연결을 클릭 하면 세로 막대는 세 개의 탭 – RDP, SSH 및 배스 천은 표시 됩니다. 배스 천을 가상 네트워크에 대 한 프로 비전 하는 경우 배스 천 탭이 기본적으로 활성화 되어 있습니다. 배스 천 가상 네트워크를 프로 비전 하지 않은 경우에 배스 천 구성에 대 한 링크를 클릭 수 있습니다. 구성 지침을 참조 하세요 [배스 천 구성](bastion-create-host-portal.md)합니다. 표시 되지 않으면 **배스 천** 나열을 열지 않은 경우 미리 보기 포털입니다. 이 사용 하 여 포털을 엽니다 [미리 보기 링크](https://aka.ms/BastionHost)합니다.

    ![VM 연결](./media/bastion-connect-vm-rdp/bastion.png)

1. 배스 천 탭, 사용자 이름 및 가상 컴퓨터에 대 한 암호, 클릭 **Connect**합니다. 배스 천 통해이 가상 머신에 RDP 연결 포트 443 및 배스 천 서비스를 사용 하 여 (HTML5)를 통해 Azure 포털에서 직접 열립니다.

    ![VM 연결](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>다음 단계

읽기는 [배스 천 FAQ](bastion-faq.md)