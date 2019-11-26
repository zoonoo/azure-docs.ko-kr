---
title: Azure Linux Virtual Machines에서 부팅 오류 문제 해결 | Microsoft Docs
description: 이 문서는 Azure Linux Virtual Machines에서 부팅 오류 문제를 해결 하는 문서에 연결 하는 데 도움이 됩니다.
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
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74408743"
---
# <a name="troubleshoot-azure-linux-virtual-machines-boot-errors"></a>Azure Linux Virtual Machines 부팅 오류 문제 해결

이 문서에는 Microsoft Azure에서 Linux VM (가상 머신)을 시작할 때 나타날 수 있는 일반적인 부팅 오류가 나열 되어 있습니다. 오류에 대한 자세한 내용은 문서의 **부팅 오류 및 해결 방법** 섹션을 참조하세요.

## <a name="boot-errors-and-solutions"></a>부팅 오류 및 해결 방법

* [GRUB 복구](troubleshoot-vm-boot-error.md)

## <a name="next-steps"></a>다음 단계

- [VM 직렬 콘솔](serial-console-linux.md)

Azure를 사용 하 여 OS 디스크를 복구 VM에 연결 함으로써 Linux VM 문제를 해결 합니다.

- [Azure VM 복구](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

 디스크 교환 – 다음 중 하나를 사용 하 여 자동화할 수 있습니다.
- [Power Shell 복구 스크립트](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
- [bash 복구 스크립트](https://github.com/sribs/azure-support-scripts)

- [CLI](troubleshoot-recovery-disks-linux.md)
- [Azure Portal](troubleshoot-recovery-disks-portal-linux.md)


## <a name="disk-swap-video"></a>디스크 교체 비디오:

GRUB에 액세스할 수 없는 경우 [이](https://youtu.be/m5t0GZ5oGAc) 비디오를 시청 하 고, 디스크 교체 절차를 쉽게 자동화 하 여 VM을 복구 하는 방법을 참조 하세요.

## <a name="unofficial-solution"></a>비공식 솔루션

지원 되지 않는 베타 스크립트 [Alar](https://github.com/malachma/azure-auto-recover) 를 사용 하 여 VM 복구를 시도할 수도 있습니다.