---
title: Azure 요새를 사용하여 Windows VM에 연결
description: 이 문서에서는 Azure 방호를 사용하여 Windows를 실행하는 Azure 가상 컴퓨터에 연결하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 6ec60dc313c8a4374637adf38ea0e5a7d4ed964b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597350"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Azure 방호를 사용하여 Windows 가상 컴퓨터에 연결

Azure Bastion을 사용하면 Azure Portal에서 직접 SSL을 통해 가상 컴퓨터에 안전하고 원활하게 연결할 수 있습니다. Azure 요새를 사용하는 경우 VM에는 클라이언트, 에이전트 또는 추가 소프트웨어가 필요하지 않습니다. 이 문서에서는 Windows VM에 연결하는 방법을 보여 줍니다. Linux VM에 연결하는 것에 대한 자세한 내용은 [Azure Bastion - Linux를 사용하여 VM에 연결 참조](bastion-connect-vm-ssh.md)

Azure Bastion은 프로비전된 가상 네트워크의 모든 VM에 대한 보안 연결을 제공합니다. Azure Bastion을 사용하면 가상 머신에서 RDP/SSH를 사용하여 안전한 액세스 권한을 계속 제공하며 RDP/SSH 포트가 외부 환경에 노출되는 상황으로부터 보호합니다. 자세한 내용은 [개요](bastion-overview.md)를 참조하십시오.

## <a name="before-you-begin"></a>시작하기 전에

VM이 있는 가상 네트워크에 Azure 요새 호스트를 설정했는지 확인합니다. Bastion 서비스가 프로비전되고 가상 네트워크에 배포되면 이를 사용하여 가상 네트워크의 모든 VM에 연결할 수 있습니다. Azure 요새 호스트를 설정하려면 [Azure 요새 호스트 만들기를](bastion-create-host-portal.md)참조하십시오.

### <a name="required-roles"></a>필수 역할

연결을 만들려면 다음 역할이 필요합니다.

* 가상 머신에 대한 읽기 권한자 역할
* 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
* Azure Bastion 리소스에 대한 읽기 권한자 역할

### <a name="ports"></a>포트

Windows VM에 연결하려면 Windows VM에서 다음 포트가 열려 있어야 합니다.

* 인바운드 포트: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>연결

1. Azure [포털을](https://portal.azure.com)엽니다. 연결하려는 가상 컴퓨터로 이동한 다음 **연결을** 클릭하고 드롭다운에서 **방신을** 선택합니다.

   ![VM 연결](./media/bastion-connect-vm-rdp/connect.png)
1. 요새를 클릭하면 RDP, SSH 및 요새의 세 개의 탭이 있는 사이드 바가 나타납니다. 대호션이 가상 네트워크에 대해 프로비전된 경우 기본적으로 요새 탭이 활성화되어 있습니다. 가상 네트워크에 대한 바스티온을 프로비전하지 않은 경우 링크를 클릭하여 Bastion을 구성할 수 있습니다. 구성 지침은 [대지 구성](bastion-create-host-portal.md)을 참조하십시오.

   ![배스천 탭](./media/bastion-connect-vm-rdp/bastion.png)
1. 방호 탭에서 가상 컴퓨터의 사용자 이름과 암호를 입력한 다음 **연결을**클릭합니다. Bastion을 통해 이 가상 시스템에 대한 RDP 연결은 포트 443 및 Bastion 서비스를 사용하여 Azure 포털(HTML5 이상)에서 직접 열립니다.

   ![RDP 연결](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>다음 단계

요새 [자주 묻는 질문](bastion-faq.md) 읽기
