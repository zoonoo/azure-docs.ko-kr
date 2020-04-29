---
title: 비규격 Azure Automation 상태 구성 서버 재구성
description: 구성 상태가 데이터베이스가 드리프트 서버에 요청 시 구성을 다시 적용 하는 방법
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: dfe62c54bfb10d70f1dbf19daec90eec68e66431
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81406077"
---
# <a name="remediate-noncompliant-dsc-servers"></a>비규격 DSC 서버 재구성

서버를 Azure Automation 상태 구성으로 등록 하면 구성 모드가, `ApplyOnly` `ApplyandMonitor`또는 `ApplyAndAutoCorrect`로 설정 됩니다. 모드가로 `ApplyAndAutoCorrect`설정 되지 않은 경우 모든 이유로 인해 호환 상태를 드리프트 하는 서버는 수동으로 수정할 때까지 비규격 상태로 유지 됩니다.

Azure compute는 고객이 가상 머신 내에서 스크립트를 실행할 수 있도록 하는 실행 명령 이라는 기능을 제공 합니다.
이 문서에서는 구성 드리프트를 수동으로 수정할 때이 기능에 대 한 예제 스크립트를 제공 합니다.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>PowerShell을 사용 하 여 Windows 가상 머신의 정확한 드리프트

Windows 가상 머신에서 명령 실행 기능을 사용 하는 단계별 지침은 [실행 명령을 사용 하 여 WINDOWS VM에서 PowerShell 스크립트 실행](/azure/virtual-machines/windows/run-command)문서를 참조 하세요.

Azure Automation 상태 구성 노드에서 최신 구성을 다운로드 하 고 적용 하려면 [start-dscconfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration) cmdlet을 사용 합니다.

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Linux 가상 머신의 정확한 드리프트

Linux 서버에는 현재 유사한 기능을 사용할 수 없습니다.
유일한 옵션은 등록 프로세스를 반복 하는 것입니다.
Azure 노드의 경우 Azure Portal에서 또는 Az module cmdlet을 사용 하 여 드리프트를 수정할 수 있습니다. 이 프로세스에 대 한 세부 정보는 [Azure Automation 상태 구성을 통해 관리할 수 있도록 컴퓨터를 온 보 딩](automation-dsc-onboarding.md#onboard-a-vm-using-azure-portal)에 설명 되어 있습니다.
하이브리드 노드의 경우 포함 된 Python 스크립트를 사용 하 여 드리프트를 수정할 수 있습니다.
[Linux 리포지토리의 경우 POWERSHELL DSC를](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer)참조 하세요.

## <a name="next-steps"></a>다음 단계

- PowerShell cmdlet 참조는 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)을 참조하세요.
- 연속 배포 파이프라인에서 Azure Automation 상태 구성을 사용 하는 예제를 보려면 [Azure Automation State 구성 및 Chocolatey를 사용 하 여 연속 배포](automation-dsc-cd-chocolatey.md)를 참조 하세요.