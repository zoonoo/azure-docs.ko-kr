---
title: Azure Bastion을 사용하여 Windows VM에 연결
description: Azure Bastion을 사용하여 Windows를 실행하는 Azure VM에 연결하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: fac6d5a0b7d7c8817dadead7e2a9269027140991
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110535307"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Azure Bastion을 사용하여 Windows 가상 머신에 연결

Azure Bastion을 사용하여 Azure Portal에서 직접 SSL을 통해 가상 머신에 안전하고 원활하게 연결할 수 있습니다. Azure Bastion을 사용하는 경우 VM에는 클라이언트, 에이전트 또는 추가 소프트웨어가 필요하지 않습니다. 이 문서에서는 Windows VM에 연결하는 방법을 보여 줍니다. Linux VM에 연결에 대한 자세한 내용은 [에 연결](bastion-connect-vm-ssh.md)을 참조하세요.

Azure Bastion은 프로비저닝된 가상 네트워크의 모든 VM에 대한 안전한 연결을 제공합니다. Azure Bastion을 사용하면 가상 머신에서 RDP/SSH를 사용하여 안전한 액세스 권한을 계속 제공하며 RDP/SSH 포트가 외부 환경에 노출되는 상황으로부터 보호합니다. 자세한 내용은 [Azure Bastion이란?](bastion-overview.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 기준을 충족하는지 확인합니다.

* Bastion 호스트가 있는 VNet이 이미 설치되어 있습니다.

   VM이 있는 가상 네트워크에 대해 Azure Bastion 호스트를 설정했는지 확인합니다. Bastion 서비스가 가상 네트워크에 프로비저닝되고 배포되면 이를 사용하여 이 가상 네트워크의 모든 VM에 연결할 수 있습니다. Azure Bastion 호스트를 설정하려면 [베스천 호스트 만들기](tutorial-create-host-portal.md#createhost)를 참조하세요.
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