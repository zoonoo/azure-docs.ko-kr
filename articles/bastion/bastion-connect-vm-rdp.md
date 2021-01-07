---
title: Azure 방호를 사용 하 여 Windows VM에 연결
description: 이 문서에서는 Azure 방호를 사용 하 여 Windows를 실행 하는 Azure 가상 머신에 연결 하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: 708bd1f61da2f3973333f8e68cabdceee0717bee
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521540"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Azure 방호를 사용 하 여 Windows 가상 머신에 연결

Azure 방호를 사용 하 여 Azure Portal에서 직접 SSL을 통해 가상 머신에 안전 하 고 원활 하 게 연결할 수 있습니다. Azure 방호를 사용 하는 경우 Vm에 클라이언트, 에이전트 또는 추가 소프트웨어가 필요 하지 않습니다. 이 문서에서는 Windows Vm에 연결 하는 방법을 보여 줍니다. Linux VM에 연결 하는 방법에 대 한 자세한 내용은 [LINUX vm에 연결](bastion-connect-vm-ssh.md)을 참조 하세요.

Azure 방호는 프로 비전 되는 가상 네트워크의 모든 Vm에 대 한 보안 연결을 제공 합니다. Azure Bastion을 사용하면 가상 머신에서 RDP/SSH를 사용하여 안전한 액세스 권한을 계속 제공하며 RDP/SSH 포트가 외부 환경에 노출되는 상황으로부터 보호합니다. 자세한 내용은 [Azure 방호 이란?](bastion-overview.md)을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작 하기 전에 다음 조건을 충족 하는지 확인 합니다.

* 요새 호스트가 있는 VNet이 이미 설치 되어 있습니다.

   VM이 있는 가상 네트워크에 대해 Azure 방호 호스트를 설정 했는지 확인 합니다. 요새 서비스가 프로 비전 되 고 가상 네트워크에 배포 되 면 가상 네트워크의 VM에 연결 하는 데 사용할 수 있습니다. Azure 방호 호스트를 설정 하려면 [요새 호스트 만들기](tutorial-create-host-portal.md#createhost)를 참조 하세요.
* 가상 네트워크의 Windows 가상 머신
* 필요한 역할은 다음과 같습니다.
  * 가상 머신에 대한 읽기 권한자 역할
  * 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
  * Azure Bastion 리소스에 대한 읽기 권한자 역할
* 포트: Windows VM에 연결하려면 Windows VM에서 다음 포트가 열려 있어야 합니다.
  * 인바운드 포트: RDP(3389)

## <a name="connect"></a><a name="rdp"></a>연결

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>다음 단계

[Bastion FAQ](bastion-faq.md)를 참조하세요.