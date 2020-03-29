---
title: Azure 직렬 콘솔 전원 옵션 | 마이크로 소프트 문서
description: Azure 직렬 콘솔 내에서 사용 가능한 VM 전원 옵션
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/6/2019
ms.author: alsin
ms.openlocfilehash: 11c2549d7282bae5654ede1ac34d63a43cd0f059
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451194"
---
# <a name="power-options-available-from-the-azure-serial-console"></a>Azure 직렬 콘솔에서 사용할 수 있는 전원 옵션

Azure 직렬 콘솔은 VM 또는 가상 시스템 규모 집합에서 전원 관리를 위한 몇 가지 강력한 도구를 제공합니다. 이러한 전원 관리 옵션은 일부 도구에 혼동될 수 있으므로 각 도구와 의도된 사용 사례에 대한 개요입니다.

시리얼 콘솔 기능 | 설명 | 사용 사례
:----------------------|:------------|:---------
VM 다시 시작 | VM 또는 가상 시스템 크기 집합 인스턴스를 정상적으로 다시 시작합니다. 이 작업은 개요 페이지에서 사용할 수 있는 다시 시작 기능을 호출하는 것과 동일합니다. | 대부분의 경우 이 옵션은 VM을 다시 시작하려고 시도하는 첫 번째 도구여야 합니다. 직렬 콘솔 연결에 잠시 중단이 발생하며 VM이 다시 시작되는 즉시 자동으로 다시 시작됩니다.
VM 다시 설정 | Azure 플랫폼에서 설정한 VM 또는 가상 시스템 규모의 강력한 전원 주기입니다. | 이 옵션은 현재 상태에 관계없이 운영 체제를 즉시 다시 시작하는 데 사용됩니다. 이 작업은 정상적이지 않으므로 데이터 손실이나 손상의 위험이 있습니다. 직렬 콘솔 연결에는 중단이 없으며 부팅 시간 초기에 명령을 보내는 데 유용할 수 있습니다(예: Windows VM의 Linux VM에서 GRUB 또는 안전 모드로 이동).
SysRq - 재부팅 (b) | 게스트를 강제로 다시 시작하도록 요청하는 시스템 요청입니다. | 이 기능은 Linux 운영 체제에만 적용 가능하며 운영 체제에서 [SysRq를 사용하도록 설정해야](./serial-console-nmi-sysrq.md#system-request-sysrq) 합니다. SysRq에 대해 운영 체제가 올바르게 구성된 경우 이 명령으로 인해 OS가 다시 시작됩니다.
NMI(마스크 불가 인터럽트) | 운영 체제로 전달되는 인터럽트 명령 | 이 작업은 [Windows](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) 및 [Linux](./serial-console-nmi-sysrq.md#non-maskable-interrupt-nmi) VM 모두에서 사용할 수 있으며 NMI를 사용하도록 설정해야 합니다. NMI를 보내면 일반적으로 운영 체제가 충돌합니다. 운영 체제를 구성하여 덤프 파일을 만든 다음 NMI를 수신할 때 다시 시작할 수 있으며, 이는 낮은 수준의 디버깅에 유용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Linux VM용 Azure 직렬 콘솔에](./serial-console-linux.md) 대해 자세히 알아보기
* [Windows VM용 Azure 직렬 콘솔에](./serial-console-windows.md) 대해 자세히 알아보기