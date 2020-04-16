---
title: 비규격 Azure 자동화 상태 구성 서버 수정
description: 구성 상태가 드리프트된 서버에 요청 시 구성을 다시 적용하는 방법
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: cc5884e1f70bdccee4e7a113e6e3ee2d6604b50a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406077"
---
# <a name="remediate-noncompliant-dsc-servers"></a>비준수 DSC 서버 수정

서버가 Azure 자동화 상태 구성에 등록되면 구성 `ApplyOnly`모드가 `ApplyAndAutoCorrect`로 설정됩니다. `ApplyandMonitor` 모드가 `ApplyAndAutoCorrect`설정되지 않은 경우 어떤 이유로든 호환 되는 상태에서 표류하는 서버는 수동으로 수정될 때까지 비규격으로 유지됩니다.

Azure 계산은 고객이 가상 시스템 내에서 스크립트를 실행할 수 있는 Run Command라는 기능을 제공합니다.
이 문서에서는 구성 드리프트를 수동으로 수정할 때 이 기능에 대한 예제 스크립트를 제공합니다.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>PowerShell을 사용하여 Windows 가상 시스템의 올바른 드리프트

Windows 가상 컴퓨터에서 명령 실행 기능을 사용하는 단계별 지침은 실행 [명령을 사용하여 Windows VM의 설명서 페이지 PowerShell 스크립트를](/azure/virtual-machines/windows/run-command)참조하십시오.

Azure 자동화 상태 구성 노드가 최신 구성을 다운로드하여 적용하도록 하려면 [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration) cmdlet을 사용합니다.

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>리눅스 가상 머신의 올바른 드리프트

Linux 서버에서도 유사한 기능을 현재 사용할 수 없습니다.
유일한 옵션은 등록 프로세스를 반복하는 것입니다.

Azure 노드의 경우 Azure 포털에서 드리프트를 수정하거나 Az 모듈 cmdlet을 사용하여 수정할 수 있습니다. 이 프로세스에 대한 자세한 내용은 [Azure 자동화 상태 구성에 의해 관리를 위해 온보딩 컴퓨터에](automation-dsc-onboarding.md#onboard-vms-by-using-the-azure-portal)설명되어 있습니다.
하이브리드 노드의 경우 포함된 Python 스크립트를 사용하여 드리프트를 수정할 수 있습니다.
[리눅스 리포지토리에 대한 PowerShell DSC를](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer)참조하십시오.

## <a name="next-steps"></a>다음 단계

- PowerShell cmdlet 참조는 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)을 참조하십시오.
- 연속 배포 파이프라인에서 Azure 자동화 상태 구성을 사용하는 예제를 보려면 [Azure 자동화 상태 구성 및 초콜릿을 사용한 지속적인 배포를](automation-dsc-cd-chocolatey.md)참조하십시오.