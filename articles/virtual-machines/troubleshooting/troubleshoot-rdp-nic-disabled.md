---
title: NIC가 해제되어 Azure Virtual Machines에 원격으로 연결할 수 없음 | Microsoft Docs
description: Azure VM에서 NIC를 사용하지 않기 때문에 RDP가 실패하는 문제를 해결하는 방법 알아보기 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/12/2018
ms.author: genli
ms.openlocfilehash: 742026a8ff35f318f58674ebc2fb5c03e45161a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60319006"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>네트워크 인터페이스를 사용하지 않기 때문에 원격 데스크톱을 VM에 사용할 수 없음

이 문서에서는 네트워크 인터페이스가 해제되어 Azure Windows VM(Virtual Machines)에 원격 데스크톱 연결을 사용할 수 없는 문제 해결 방법을 보여줍니다.

> [!NOTE]
> Azure에는 리소스를 만들고 사용하기 위한 [Resource Manager 및 클래식](../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 Resource Manager 배포 모델 사용에 대해 설명합니다. 이 배포 모델은 클래식 배포 모델 대신 새 배포에 사용하는 것이 좋습니다.

## <a name="symptoms"></a>증상

VM에서 네트워크 인터페이스를 사용하지 않기 때문에 Azure의 VM에 RDP 연결 또는 다른 포트에 대한 다른 형식의 연결을 수행할 수 없습니다.

## <a name="solution"></a>해결 방법

다음 단계를 수행하기 전에 영향을 받는 VM의 OS 디스크 스냅샷을 백업으로 만듭니다. 자세한 내용은 [디스크 스냅샷](../windows/snapshot-copy-managed-disk.md)을 참조하세요.

VM에 인터페이스를 사용하도록 설정하려면 VM에 직렬 컨트롤을 사용하거나 [네트워크 인터페이스를 다시 설정](##reset-network-interface)합니다.

### <a name="use-serial-control"></a>직렬 콘솔 사용

1. [직렬 콘솔에 연결하고 CMD 인스턴스를 엽니다](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). VM에서 직렬 콘솔을 사용하지 않도록 설정한 경우 [네트워크 인터페이스 다시 설정](#reset-network-interface)을 참조하세요.
2. 네트워크 인터페이스의 상태를 확인합니다.

        netsh interface show interface

    비활성화된 네트워크 인터페이스의 이름을 적어둡니다.

3. 네트워크 인터페이스를 활성화합니다.

        netsh interface set interface name="interface Name" admin=enabled

    예를 들어 상호 연동 인터페이스 이름이 "이더넷 2"인 경우 다음 명령을 실행합니다.

        netsh interface set interface name=""Ethernet 2" admin=enabled


4.  네트워크 인터페이스를 사용할 수 있도록 다시 네트워크 인터페이스의 상태를 확인합니다.

        netsh interface show interface

    이 시점에서 VM을 다시 시작할 필요가 없습니다. VM에 다시 연결할 수 있게 됩니다.

5.  VM에 연결하고 문제가 해결되었는지 확인합니다.

## <a name="reset-network-interface"></a>네트워크 인터페이스 다시 설정

네트워크 인터페이스를 다시 설정하려면 IP 주소를 서브넷에서 사용할 수 있는 다른 IP 주소로 변경합니다. 이렇게 하려면 Azure Portal 또는 Azure PowerShell을 사용합니다. 자세한 내용은 [네트워크 인터페이스 다시 설정](reset-network-interface.md)을 참조하세요.