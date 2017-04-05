---
title: "Windows Server VM에 연결 | Microsoft Docs"
description: "Azure 포털 및 리소스 관리자 배포 모델을 사용하여 Windows VM에 연결 및 로그온하는 방법을 알아봅니다."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/01/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 88431377a36d5bc36220c630f0c8d4a46ab4a434
ms.lasthandoff: 03/31/2017


---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>Windows를 실행하는 Azure 가상 컴퓨터에 연결하고 로그온하는 방법
Azure Portal의 **연결** 단추를 사용하여 Windows 데스크톱에서 RDP(원격 데스크톱) 세션을 시작합니다. 먼저 가상 컴퓨터에 연결한 다음 로그온합니다.

Mac에서 Windows VM에 연결하려는 경우 [Microsoft 원격 데스크톱](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417)과 같이 Mac용 RDP 클라이언트를 설치해야 합니다.

## <a name="connect-to-the-virtual-machine"></a>가상 컴퓨터에 연결
1. 아직 로그인하지 않은 경우 [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 허브 메뉴에서 **가상 컴퓨터**를 클릭합니다.
3. 목록에서 가상 컴퓨터를 선택합니다.
4. 가상 컴퓨터 블레이드에서 **연결**을 클릭합니다.
   
    ![VM에 연결하는 방법을 보여 주는 Azure 포털의 스크린샷입니다.](./media/connect-logon/connect.png)
   
   > [!TIP]
   > 포털에서 **연결** 단추가 회색으로 표시되고 [Express 경로](../../expressroute/expressroute-introduction.md) 또는 [사이트 간 VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) 연결을 통해 Azure에 연결되지 않는 경우 RDP를 사용하려면 먼저 공용 IP 주소를 만들고 VM에 할당해야 합니다. 자세한 내용은 [Azure의 공용 IP 주소](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)에서 확인할 수 있습니다.
   > 
   > 

## <a name="log-on-to-the-virtual-machine"></a>가상 컴퓨터에 로그온
[!INCLUDE [virtual-machines-log-on-win-server](../../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>다음 단계
연결하려고 할 때 문제가 발생할 경우 [원격 데스크톱 연결 문제 해결](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요. 이 문서에서는 일반적인 문제를 진단 및 해결하는 과정을 안내합니다.


