---
title: Microsoft Azure 기반 가상 머신에 연결 | Azure Marketplace
description: Azure에서 만든 새 가상 머신에 연결하는 방법을 설명합니다.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 4aea624c2127c9b0a61d72b8d14929ce6f47df24
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142484"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Azure 기반 가상 머신에 연결

> [!IMPORTANT]
> 2020 년 4 월 13 일부 터 파트너 센터에 대 한 Azure Virtual Machine 제품의 이동 관리를 시작 합니다. 마이그레이션 후 파트너 센터에서 제품을 만들고 관리 합니다. [Azure Virtual Machine 기술 자산 만들기](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) 의 지침에 따라 마이그레이션된 제안을 관리 합니다.

이 문서에서는 Azure에서 만든 VM(가상 머신)에 연결하고 로그인하는 방법에 대해 설명합니다.  성공적으로 연결 되 면 해당 호스트 서버에 로컬로 로그온 한 것 처럼 VM을 사용할 수 있습니다.

## <a name="connect-to-a-windows-based-vm"></a>Windows 기반 VM에 연결

원격 데스크톱 클라이언트를 사용 하 여 Azure에서 호스트 되는 Windows 기반 VM에 연결 합니다.  대부분의 Windows 버전에는 기본적으로 RDP(원격 데스크톱 프로토콜)에 대한 지원이 포함되어 있습니다.  다른 머신의 경우 [원격 데스크톱 클라이언트](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)에서 클라이언트에 대한 자세한 정보를 찾을 수 있습니다.  

[Windows를 실행하는 Azure 가상 머신에 연결하고 로그온하는 방법](../../../virtual-machines/windows/connect-logon.md) 문서에서는 기본 제공 Windows RDP 지원을 사용하여 VM에 연결하는 방법에 대해 자세히 설명합니다.  

>[!TIP]
> 프로세스 중에 알 수 없는 게시자의 .rdp 파일이거나 사용자 자격 증명을 확인할 수 없다는 것과 같은 보안 경고가 표시될 수 있습니다.  이러한 경고는 무시해도 됩니다.


## <a name="connect-to-a-linux-based-vm"></a>Linux 기반 VM에 연결

Linux 기반 VM을 연결 하려면 SSH (보안 셸 프로토콜) 클라이언트가 필요 합니다.  이 토론에서는 추가 비용이 들지 않는 [PuTTY](https://www.ssh.com/ssh/putty/) SHH 터미널을 사용합니다.

1. [Azure 포털](https://ms.portal.azure.com)로 이동합니다. **가상 머신**을 검색하여 선택합니다. 
2. 연결 하려는 VM을 선택 합니다.  
3. VM이 아직 실행 되 고 있지 않으면 **시작** 합니다.
4. VM의 이름을 클릭하여 **개요** 페이지를 엽니다.
5. VM의 공용 IP 주소와 DNS 이름을 적어 둡니다.  이러한 값을 설정하지 않으면 [네트워크 인터페이스를 만들어야 합니다](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface).

   ![VM 개요 설정](./media/publishvm_019.png)
 
6. PuTTY 애플리케이션을 엽니다.  
7. [PuTTY 구성] 대화 상자에서 VM의 IP 주소 또는 DNS 이름을 입력합니다. 

   ![PuTTY 터미널 설정](./media/publishvm_020.png)
 
8. **열기**를 클릭하여 PuTTY 터미널을 엽니다.  
9. 메시지가 표시 되 면 Linux VM 계정의 계정 이름 및 암호를 입력 합니다. 

연결 문제가 있는 경우 SSH 클라이언트 설명서(예: [10장: 일반적인 오류 메시지](https://www.ssh.com/ssh/putty/putty-manuals))를 참조하세요.

프로비전된 Linux VM에 데스크톱을 추가하는 방법을 포함한 자세한 내용은 [Azure에서 원격 데스크톱을 설치 및 구성하여 Linux VM에 연결](../../../virtual-machines/linux/use-remote-desktop.md)을 참조하세요.


## <a name="stop-unused-vms"></a>사용되지 않는 VM 중지
Azure는 실행 중이거나 *유휴 상태*인 VM의 호스팅에 대한 요금을 청구합니다.  따라서 현재 사용 하지 않는 Vm은 중지 하는 것이 좋습니다.  예를 들어 테스트, 백업 또는 사용 중지 VM은 종료 대상입니다. VM을 종료 하려면 다음 단계를 완료 합니다.

1. **Virtual Machines** 블레이드에서 중지하려는 VM을 선택합니다. 
2. 페이지 위쪽의 도구 모음에서 **중지** 단추를 클릭합니다.

   ![VM 중지](./media/publishvm_018.png)

Azure는 VM에서 운영 체제를 종료할 뿐만 아니라 이전에 프로비전된 하드웨어 및 네트워크 리소스를 해제하는 *할당 취소라는 * 프로세스에서 VM을 빠르게 중지합니다.

중지된 VM을 나중에 다시 활성화하려면 해당 VM을 선택하고 **시작** 단추를 클릭합니다.


## <a name="next-steps"></a>다음 단계

원격으로 연결 하 고 나면 [VM을 구성할](./cpp-configure-vm.md)준비가 된 것입니다.
