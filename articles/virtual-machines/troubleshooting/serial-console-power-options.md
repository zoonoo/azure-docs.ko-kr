---
title: Azure 직렬 콘솔 전원 옵션 | Microsoft Docs
description: Azure 직렬 콘솔 내에서 사용할 수 있는 VM 전원 옵션
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75451194"
---
# <a name="power-options-available-from-the-azure-serial-console"></a>Azure 직렬 콘솔에서 사용할 수 있는 전원 옵션

Azure 직렬 콘솔은 VM 또는 가상 머신 확장 집합에서 전원 관리를 위한 여러 가지 강력한 도구를 제공 합니다. 이러한 전원 관리 옵션은 일부에 대해 혼란 스 러 울 수 있으므로 각 도구 및 해당 사용 사례에 대 한 개요를 제공 합니다.

직렬 콘솔 기능 | 설명 | 사용 사례
:----------------------|:------------|:---------
VM 다시 시작 | VM 또는 가상 머신 확장 집합 인스턴스를 정상적으로 다시 시작 합니다. 이 작업은 개요 페이지에서 사용할 수 있는 다시 시작 기능을 호출 하는 것과 같습니다. | 대부분의 경우이 옵션은 VM을 다시 시작 하려는 첫 번째 도구 여야 합니다. 직렬 콘솔 연결이 잠시 중단 되 고 VM이 다시 시작 되는 즉시 자동으로 다시 시작 됩니다.
VM 다시 설정 | Azure 플랫폼에서 VM 또는 가상 머신 확장 집합의 강제 전원 주기입니다. | 이 옵션은 현재 상태와 상관 없이 운영 체제를 즉시 다시 시작 하는 데 사용 됩니다. 이 작업은 정상 상태가 아니므로 데이터가 손실 되거나 손상 될 위험이 있습니다. 직렬 콘솔 연결은 중단 되지 않습니다. 부팅 시간 (예: Linux VM에서 GRUB로 가져오기 또는 Windows VM의 안전 모드)에서 명령을 전송 하는 데 유용할 수 있습니다.
SysRq-다시 부팅 (b) | 게스트 다시 시작을 강제 하는 시스템 요청 | 이 기능은 Linux 운영 체제에만 적용 되며, 운영 체제에서 [Sysrq를 사용 하도록 설정](./serial-console-nmi-sysrq.md#system-request-sysrq) 해야 합니다. SysRq에 대해 운영 체제가 올바르게 구성 된 경우이 명령을 수행 하면 OS가 다시 시작 됩니다.
NMI (마스크 불가능 인터럽트) | 운영 체제에 전달 되는 인터럽트 명령 | 이 작업은 [Windows](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) 및 [Linux](./serial-console-nmi-sysrq.md#non-maskable-interrupt-nmi) vm 둘 다에서 사용할 수 있으며, NMI를 사용 하도록 설정 해야 합니다. 일반적으로 NMI를 전송 하면 운영 체제가 충돌 합니다. 덤프 파일을 만들도록 운영 체제를 구성 하 고, NMI를 받을 때 다시 시작 하 여 하위 수준 디버깅에 유용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Linux vm 용 Azure 직렬 콘솔](./serial-console-linux.md) 에 대 한 자세한 정보
* [Windows vm 용 Azure 직렬 콘솔](./serial-console-windows.md) 에 대 한 자세한 정보