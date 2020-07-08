---
title: Azure 방호를 사용 하 여 Windows VM에 연결
description: 이 문서에서는 Azure 방호를 사용 하 여 Windows를 실행 하는 Azure 가상 머신에 연결 하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 79eb09a005f62846fc2f7e3e7b493d5e366edabc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84744326"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Azure 방호를 사용 하 여 Windows 가상 머신에 연결

Azure 방호를 사용 하 여 Azure Portal에서 직접 SSL을 통해 가상 머신에 안전 하 고 원활 하 게 연결할 수 있습니다. Azure 방호를 사용 하는 경우 Vm에 클라이언트, 에이전트 또는 추가 소프트웨어가 필요 하지 않습니다. 이 문서에서는 Windows Vm에 연결 하는 방법을 보여 줍니다. Linux VM에 연결 하는 방법에 대 한 자세한 내용은 [Azure 방호-Linux를 사용 하 여 VM에 연결](bastion-connect-vm-ssh.md)을 참조 하세요.

Azure 방호는 프로 비전 되는 가상 네트워크의 모든 Vm에 대 한 보안 연결을 제공 합니다. Azure Bastion을 사용하면 가상 머신에서 RDP/SSH를 사용하여 안전한 액세스 권한을 계속 제공하며 RDP/SSH 포트가 외부 환경에 노출되는 상황으로부터 보호합니다. 자세한 내용은 [개요](bastion-overview.md)를 참조 하세요.

## <a name="before-you-begin"></a>시작하기 전에

VM이 있는 가상 네트워크에 대해 Azure 방호 호스트를 설정 했는지 확인 합니다. 요새 서비스가 프로 비전 되 고 가상 네트워크에 배포 되 면 가상 네트워크의 VM에 연결 하는 데 사용할 수 있습니다. Azure 방호 호스트를 설정 하려면 [Azure 방호 호스트 만들기](bastion-create-host-portal.md)를 참조 하세요.

### <a name="required-roles"></a>필요한 역할

연결을 설정 하려면 다음 역할이 필요 합니다.

* 가상 머신에 대한 읽기 권한자 역할
* 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
* Azure Bastion 리소스에 대한 읽기 권한자 역할

### <a name="ports"></a>포트

Windows VM에 연결 하려면 다음 포트가 Windows VM에서 열려 있어야 합니다.

* 인바운드 포트: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>연결

1. [Azure Portal](https://portal.azure.com)을 엽니다. 연결 하려는 가상 머신으로 이동한 다음 **연결** 을 클릭 하 고 드롭다운에서 **방호** 를 선택 합니다.

   ![VM 연결](./media/bastion-connect-vm-rdp/connect.png)
1. 요새를 클릭 하면 RDP, SSH 및 방호의 3 개 탭이 있는 사이드 막대가 나타납니다. 가상 네트워크에 대해 방호를 프로 비전 한 경우에는 기본적으로 방호 탭이 활성화 됩니다. 가상 네트워크에 대 한 방호를 프로 비전 하지 않은 경우에는 링크를 클릭 하 여 요새를 구성할 수 있습니다. 구성 지침은 [요새 구성](bastion-create-host-portal.md)을 참조 하세요.

   ![요새 탭](./media/bastion-connect-vm-rdp/bastion.png)
1. 요새 탭에서 가상 머신에 대 한 사용자 이름 및 암호를 입력 한 다음 **연결**을 클릭 합니다. 이 가상 머신에 대한 RDP 연결은 포트 443 및 Bastion 서비스를 사용하여 Azure Portal에서 직접 열립니다(HTML5를 통해).

   ![RDP 연결](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>다음 단계

[요새 FAQ](bastion-faq.md) 읽기
