---
title: Azure Stack Edge Pro GPU에서 VM 게스트 로그 수집
description: Azure Stack Edge Pro GPU 디바이스에서 VM에 대한 게스트 로그를 사용하여 지원 패키지를 만드는 방법을 설명합니다.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/03/2021
ms.author: alkohli
ms.openlocfilehash: 7ffb96cdc3fbb561009b7f545a3b9a1da9eef729
ms.sourcegitcommit: ef950cf37f65ea7a0f583e246cfbf13f1913eb12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111421007"
---
# <a name="collect-vm-guest-logs-on-an-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스에서 VM 게스트 로그 수집

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro GPU 디바이스에서 VM 프로비전 실패를 진단하려면 실패한 가상 머신에 대한 게스트 로그를 검토합니다. 이 문서에서는 지원 패키지에서 VM에 대한 게스트 로그를 수집하는 방법을 설명합니다.

## <a name="collect-vm-guest-logs-in-support-package"></a>지원 패키지에서 VM 게스트 로그 수집

Azure Stack Edge Pro GPU 디바이스에서 실패한 가상 머신에 대한 게스트 로그를 수집하려면 다음 단계를 수행합니다.

1. [디바이스의 PowerShell 인터페이스에 연결](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)합니다.

2. 다음 명령을 실행하여 실패한 VM에 대한 게스트 내 로그를 수집하고 지원 패키지에 이러한 로그를 포함합니다.

   ```powershell
   Get-VMInGuestLogs -FailedVM
   Get-HcsNodeSupportPackage -Path “\\<network path>” -Include InGuestVMLogFiles -Credential “domain_name\user”
   ```

   그러면 `hcslogs\VmGuestLogs` 폴더에서 로그를 찾을 수 있습니다.

3. VM 프로비전 기록의 세부 정보를 확인하려면 다음 로그를 검토합니다.

   **Linux VM:**
   - /var/log/cloud-init-output.log
   - /var/log/cloud-init.log
   - /var/log/waagent.log

   **Windows VM:**
   - C:\Windows\Azure\Panther\WaSetup.xml

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Pro GPU에서 VM 프로비전 문제 해결](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)