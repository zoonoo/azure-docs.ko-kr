---
title: NIC가 해제되어 Azure Virtual Machines에 원격으로 연결할 수 없음 | Microsoft Docs
description: Azure VM에서 NIC를 사용하지 않기 때문에 RDP가 실패하는 문제를 해결하는 방법 알아보기 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/12/2018
ms.author: genli
ms.openlocfilehash: 0e79efc9de43fc0a3044e9ae1e3959f63bb6e69f
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90090267"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>네트워크 인터페이스를 사용하지 않기 때문에 원격 데스크톱을 VM에 사용할 수 없음

이 문서에서는 네트워크 인터페이스가 해제되어 Azure Windows VM(Virtual Machines)에 원격 데스크톱 연결을 사용할 수 없는 문제 해결 방법을 보여줍니다.


## <a name="symptoms"></a>증상

VM에서 네트워크 인터페이스를 사용하지 않기 때문에 Azure의 VM에 RDP 연결 또는 다른 포트에 대한 다른 형식의 연결을 수행할 수 없습니다.

![네트워크 인터페이스의 연결이 끊어진 VM을 보여 주는 스크린샷](./media/troubleshoot-rdp-nic-disabled/disconnected.png)

![네트워크 인터페이스를 사용 하지 않도록 설정 된 VM을 보여 주는 스크린샷](./media/troubleshoot-rdp-nic-disabled/disabled.png)


## <a name="solution"></a>솔루션

다음 단계를 수행하기 전에 영향을 받는 VM의 OS 디스크 스냅샷을 백업으로 만듭니다. 자세한 내용은 [디스크 스냅샷](../windows/snapshot-copy-managed-disk.md)을 참조하세요.

VM에 인터페이스를 사용하도록 설정하려면 VM에 직렬 컨트롤을 사용하거나 [네트워크 인터페이스를 다시 설정](#reset-network-interface)합니다.

### <a name="use-serial-control"></a>직렬 콘솔 사용

1. [직렬 콘솔에 연결하고 CMD 인스턴스를 엽니다](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console). VM에서 직렬 콘솔을 사용 하도록 설정 하지 않은 경우 [네트워크 인터페이스 다시 설정](#reset-network-interface)을 참조 하세요.
2. 네트워크 인터페이스의 상태를 확인합니다.

    ```console
    netsh interface show interface
    ```

    비활성화된 네트워크 인터페이스의 이름을 적어둡니다.

3. 네트워크 인터페이스를 활성화합니다.

    ```console
    netsh interface set interface name="interface Name" admin=enabled
    ```

    예를 들어 상호 연동 인터페이스 이름이 "이더넷 2"인 경우 다음 명령을 실행합니다.

    ```console
    netsh interface set interface name="Ethernet 2" admin=enabled
    ```

4.  네트워크 인터페이스를 사용할 수 있도록 다시 네트워크 인터페이스의 상태를 확인합니다.

    ```console
    netsh interface show interface
    ```

    이 시점에서 VM을 다시 시작할 필요는 없습니다. VM에 다시 연결할 수 있게 됩니다.

5.  VM에 연결하고 문제가 해결되었는지 확인합니다.

## <a name="reset-network-interface"></a>네트워크 인터페이스 다시 설정

네트워크 인터페이스를 다시 설정하려면 IP 주소를 서브넷에서 사용할 수 있는 다른 IP 주소로 변경합니다. 이렇게 하려면 Azure Portal 또는 Azure PowerShell을 사용합니다. 자세한 내용은 [네트워크 인터페이스 다시 설정](reset-network-interface.md)을 참조하세요.
