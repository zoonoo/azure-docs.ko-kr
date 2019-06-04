---
title: 정적 IP로 인해 Azure Virtual Machines에 대해 원격 데스크톱을 사용할 수 없음 | Microsoft Docs
description: Microsoft Azure에서 정적 IP로 인해 발생한 RDP 문제를 해결하는 방법 알아보기 | Microsoft Docs
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
ms.date: 11/08/2018
ms.author: genli
ms.openlocfilehash: 81a3064290e0aa720a4fe6b0fa0d8eb13cfe6903
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60318921"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>정적 IP로 인해 Azure Virtual Machines에 대해 원격 데스크톱을 사용할 수 없음

이 문서에서는 VM에 정적 IP가 구성된 후 Azure Windows VM(Virtual Machines)에 원격 데스크톱을 연결할 수 없는 문제에 대해 설명합니다.

> [!NOTE]
> Azure에는 리소스를 만들고 작업하기 위한 [Resource Manager 및 클래식](../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 Resource Manager 배포 모델 사용에 대해 설명합니다. 이 배포 모델은 클래식 배포 모델 대신 새 배포에 사용하는 것이 좋습니다.

## <a name="symptoms"></a>증상

Azure의 VM에 RDP를 연결하는 경우 다음 오류 메시지를 받습니다.

**다음 이유 중 하나로 인해 원격 데스크톱에서 원격 컴퓨터에 연결할 수 없습니다.**

1. **서버에 대한 원격 액세스를 사용할 수 없음**

2. **원격 컴퓨터가 꺼져 있음**

3. **네트워크에서 원격 컴퓨터를 사용할 수 없음**

**원격 컴퓨터가 켜져 있고 네트워크에 연결되어 있으며 원격 액세스가 사용하도록 설정되었는지 확인합니다.**

Azure Portal의 [부팅 진단](../troubleshooting/boot-diagnostics.md)에서 스크린샷을 확인하면 VM이 정상적으로 부팅되고 로그인 화면에서 자격 증명을 기다리는 것으로 표시됩니다.

## <a name="cause"></a>원인

Windows 내 네트워크 인터페이스에 정의된 고정 IP 주소가 VM에 있습니다. 이 IP 주소는 Azure Portal에 정의된 주소와 다릅니다.

## <a name="solution"></a>해결 방법

다음 단계를 수행하기 전에 영향을 받는 VM의 OS 디스크 스냅샷을 백업으로 만듭니다. 자세한 내용은 [디스크 스냅샷](../windows/snapshot-copy-managed-disk.md)을 참조하세요.

이 문제를 해결하려면 직렬 컨트롤을 사용하여 DHCP를 사용하도록 설정하거나 VM에 대한 [네트워크 인터페이스를 다시 설정](reset-network-interface.md)하세요.

### <a name="use-serial-control"></a>직렬 콘솔 사용

1. [직렬 콘솔에 연결하고 CMD 인스턴스를 엽니다](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). VM에서 직렬 콘솔을 사용할 수 없는 경우 [네트워크 인터페이스 다시 설정](reset-network-interface.md)을 참조하세요.
2. 네트워크 인터페이스에서 DHCP를 사용할 수 없는지 확인합니다.

        netsh interface ip show config
3. DHCP를 사용할 수 없는 경우 DHCP를 사용하도록 네트워크 인터페이스 구성을 되돌립니다.

        netsh interface ip set address name="<NIC Name>" source=dhc

    예를 들어, 네트워크 인터페이스 이름이 “이더넷 2”인 경우 다음 명령을 실행합니다.

        netsh interface ip set address name="Ethernet 2" source=dhc

4. IP 구성을 다시 쿼리하여 이제 네트워크 인터페이스가 올바르게 설정되었는지 확인합니다. 새 IP 주소는 Azure에서 제공하는 IP 주소와 일치해야 합니다.

        netsh interface ip show config

    이 시점에서 VM을 다시 시작할 필요는 없습니다. VM에 다시 연결할 수 있게 됩니다.

이후 VM에 대한 고정 IP를 구성하려면 [VM에 대한 고정 IP 주소 구성](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md)을 참조하세요.