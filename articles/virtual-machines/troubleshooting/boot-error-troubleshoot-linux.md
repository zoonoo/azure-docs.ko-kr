---
title: Azure Linux 가상 머신의 부팅 오류 해결 | 마이크로 소프트 문서
description: 이 문서에서는 Azure Linux 가상 시스템에서 부팅 오류를 해결하는 아티클에 연결하는 데 도움이 됩니다.
services: virtual-machines-linux
documentationCenter: ''
author: vilibert
manager: spogge
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2019
ms.author: vilibert
ms.openlocfilehash: 37cb201751f72918838efe5837aa0e357d483f24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74408743"
---
# <a name="troubleshoot-azure-linux-virtual-machines-boot-errors"></a>Azure Linux 가상 컴퓨터 부팅 오류 문제 해결

이 문서에서는 Microsoft Azure에서 Linux 가상 시스템(VM)을 시작할 때 받을 수 있는 일반적인 부팅 오류를 나열합니다. 오류에 대한 자세한 내용은 문서의 **부팅 오류 및 해결 방법** 섹션을 참조하세요.

## <a name="boot-errors-and-solutions"></a>부팅 오류 및 해결 방법

* [GRUB 구조](troubleshoot-vm-boot-error.md)

## <a name="next-steps"></a>다음 단계

- [VM 직렬 콘솔](serial-console-linux.md)

Azure를 사용하여 복구 VM에 OS 디스크를 연결하여 Linux VM 문제를 해결합니다.

- [Azure VM 복구](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

 디스크 스왑 - 다음 중 하나를 사용하여 자동화할 수 있습니다.
- [전원 셸 복구 스크립트](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
- [bash 복구 스크립트](https://github.com/sribs/azure-support-scripts)

- [Cli](troubleshoot-recovery-disks-linux.md)
- [Azure 포털](troubleshoot-recovery-disks-portal-linux.md)


## <a name="disk-swap-video"></a>디스크 스왑 비디오:

GRUB에 액세스할 수 없는 경우 [이](https://youtu.be/m5t0GZ5oGAc) 비디오를 시청하고 디스크 스왑 프로시저를 쉽게 자동화하여 VM을 복구하는 방법을 확인하십시오.

## <a name="unofficial-solution"></a>비공식 솔루션

지원되지 않는 BETA 스크립트 [ALAR를](https://github.com/malachma/azure-auto-recover) 사용해 VM 복구를 시도할 수도 있습니다.