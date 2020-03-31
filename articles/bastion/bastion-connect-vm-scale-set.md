---
title: Azure 요새를 사용하여 Windows 가상 시스템 규모 집합에 연결 | 마이크로 소프트 문서
description: 이 문서에서는 Azure Bastion을 사용하여 Azure 가상 시스템 집합 집합에 연결하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 4f513aaf113ef4bd6e75e5c4b31e0f0252d45f10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988093"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>Azure 방호를 사용하여 가상 시스템 규모 집합에 연결

이 문서에서는 Azure Bastion을 사용하여 Azure 가상 네트워크에서 Windows 가상 시스템 크기 집합 인스턴스에 RDP를 안전하고 원활하게 설정하는 방법을 보여 줍니다. Azure 포털에서 직접 가상 시스템 규모 집합 인스턴스에 연결할 수 있습니다. Azure Bastion을 사용하는 경우 VM에는 클라이언트, 에이전트 또는 추가 소프트웨어가 필요하지 않습니다. Azure 요새에 대한 자세한 내용은 [개요](bastion-overview.md)를 참조하십시오.

## <a name="before-you-begin"></a>시작하기 전에

가상 시스템 확장 집합이 있는 가상 네트워크에 Azure Bastion 호스트를 설정했는지 확인합니다. 자세한 내용은 [Azure 요새 호스트 만들기를](bastion-create-host-portal.md)참조하십시오. Bastion 서비스가 프로비전되고 가상 네트워크에 배포되면 이 가상 네트워크에서 가상 시스템 규모 집합 인스턴스에 연결하는 데 사용할 수 있습니다. Bastion은 RDP를 사용하여 Windows 가상 시스템 규모 집합에 연결하고 SSH를 사용하여 Linux 가상 시스템 규모 집합에 연결하는 것으로 가정합니다. 리눅스 VM에 대한 연결에 대한 자세한 내용은 [VM - Linux에 연결](bastion-connect-vm-ssh.md)참조 .

## <a name="connect-using-rdp"></a><a name="rdp"></a>RDP를 사용하여 연결

1. Azure [포털을](https://portal.azure.com)엽니다. 연결하려는 가상 시스템 크기 집합으로 이동합니다.

   ![탐색](./media/bastion-connect-vm-scale-set/1.png)
2. 연결하려는 가상 시스템 규모 집합 인스턴스로 이동한 다음 **연결을**선택합니다. RDP 연결을 사용하는 경우 가상 시스템 규모 집합은 Windows 가상 시스템 크기 집합이어야 합니다.

   ![가상 머신 스케일 세트](./media/bastion-connect-vm-scale-set/2.png)
3. **연결을**선택하면 RDP, SSH 및 요새의 세 개의 탭이 있는 사이드 바가 나타납니다. 사이드 바에서 **배스션** 탭을 선택합니다. 가상 네트워크에 대한 바스티온을 프로비전하지 않은 경우 링크를 선택하여 요새를 구성할 수 있습니다. 구성 지침은 [대지 구성](bastion-create-host-portal.md)을 참조하십시오.

   ![요새 탭](./media/bastion-connect-vm-scale-set/3.png)
4. 방호 탭에서 가상 시스템 규모 집합의 사용자 이름과 암호를 입력한 다음 **연결을**선택합니다.

   ![연결](./media/bastion-connect-vm-scale-set/4.png)
5. Bastion을 통해 이 가상 시스템에 대한 RDP 연결은 포트 443 및 Bastion 서비스를 사용하여 Azure 포털(HTML5 이상)에서 직접 열립니다.

## <a name="next-steps"></a>다음 단계

[바스티온 자주 묻는 질문 읽기.](bastion-faq.md)