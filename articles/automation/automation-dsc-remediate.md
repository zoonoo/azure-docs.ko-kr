---
title: 비준수 Azure 자동화 상태 구성 서버 수정
description: 구성 상태가 드리프트된 서버에 요청 시 구성을 다시 적용하는 방법
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: a6c7639cb4988eb13dfaa1c151085cda6e53b5d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68614499"
---
# <a name="remediate-non-compliant-dsc-servers"></a>비준수 DSC 서버 수정

서버가 Azure 자동화 상태 구성에 등록되면 '구성 모드'가 적용, 적용 및 모니터 또는 ApplyAndAutoCorrect로 설정됩니다.
모드가 자동 수정으로 설정되지 않은 경우 어떤 이유로든 호환 되는 상태에서 표류하는 서버는 수동으로 수정될 때까지 비호환 상태로 유지됩니다.

Azure 계산은 고객이 가상 시스템 내에서 스크립트를 실행할 수 있는 Run Command라는 기능을 제공합니다.
이 문서에서는 구성 드리프트를 수동으로 수정할 때 이 기능에 대한 예제 스크립트를 제공합니다.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>PowerShell을 사용하여 Windows 가상 시스템의 올바른 드리프트

Windows 가상 컴퓨터에서 명령 실행 기능을 사용하는 단계별 지침은 [실행 명령을 사용하여 Windows VM의 설명서](/azure/virtual-machines/windows/run-command)페이지 PowerShell 스크립트 를 참조하십시오.

Azure 자동화 상태 구성 노드가 최신 구성을 다운로드하여 `Update-DscConfiguration` 적용하도록 하려면 cmdlet을 사용합니다.
자세한 내용은 cmdlet 설명서 [를 참조하십시오Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration).

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>리눅스 가상 머신의 올바른 드리프트

Linux 서버에서도 유사한 기능을 현재 사용할 수 없습니다.
유일한 옵션은 등록 프로세스를 반복하는 것입니다.
Azure 노드의 경우 포털에서 또는 Az 자동화 cmdlet을 사용하여 드리프트 보정을 수행할 수 있습니다.
이 프로세스에 대한 자세한 내용은 [Azure 자동화 상태 구성에 의해 관리를 위한 시스템 온보딩](/azure/automation/automation-dsc-onboarding#azure-portal)페이지에 설명되어 있습니다.
하이브리드 노드의 경우 포함된 파이썬 스크립트를 사용하여 드리프트 보정을 수행할 수 있습니다.
[리눅스 리포지토리에 대한 PowerShell DSC의 설명서를](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer)참조하십시오.

## <a name="next-steps"></a>다음 단계

- PowerShell cmdlet 참조는 [Azure Automation 상태 구성 cmdlet](/powershell/module/azurerm.automation/#automation)을 참조하세요.
- 지속적인 배포 파이프라인에서 Azure Automation 상태 구성을 사용하는 예제는 [Azure Automation 상태 구성 및 Chocolatey를 사용한 지속적인 배포](automation-dsc-cd-chocolatey.md)를 참조하세요.
