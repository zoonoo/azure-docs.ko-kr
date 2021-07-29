---
title: Azure Automation FAQ | Microsoft Docs
description: 이 문서에서는 Azure Automation에 대한 질문과 대답을 제공합니다.
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 06/04/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9e9168f7ef3cf49f4c13fdc67807061f23b6b402
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111984017"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation 질문과 대답

이 Microsoft FAQ는 Azure Automation에 대한 일반적인 질문과 대답 목록입니다. 기능에 대한 다른 질문이 있으면 토론 포럼으로 이동하여 질문을 게시하세요. 자주 묻는 질문일 경우 빠르고 쉽게 찾을 수 있도록 이 문서에 추가합니다.

## <a name="update-management"></a>업데이트 관리

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>예기치 않은 OS 수준 업그레이드를 방지할 수 있나요?

Red Hat Enterprise Linux와 같은 일부 Linux 변형에서는 OS 수준 업그레이드가 패키지를 통해 발생할 수 있습니다. 이로 인해 OS 버전 번호가 변경되는 업데이트 관리가 실행될 수 있습니다. 업데이트 관리는 관리자가 Linux 머신에서 로컬로 사용하는 것과 동일한 방법으로 패키지를 업데이트하므로 이 동작은 의도적인 것입니다.

업데이트 관리 배포를 통해 OS 버전을 업데이트하지 않으려면 **제외** 기능을 사용합니다.

Red Hat Enterprise Linux에서 제외할 패키지 이름은 `redhat-release-server.x86_64`입니다.

### <a name="why-arent-criticalsecurity-updates-applied"></a>중요/보안 업데이트가 적용되지 않는 이유는 무엇인가요?

Linux 컴퓨터에 업데이트를 배포할 때 업데이트 분류를 선택할 수 있습니다. 이 옵션은 지정된 조건에 맞는 업데이트를 필터링합니다. 이 필터는 업데이트가 배포될 때 컴퓨터에 로컬로 적용됩니다.

업데이트 관리는 클라우드에서 업데이트 적용을 수행하므로, 로컬 머신에 해당 정보가 없더라도 업데이트 관리의 일부 업데이트에 보안에 영향을 주는 것으로 플래그를 지정할 수 있습니다. Linux 시스템에 중요 업데이트를 적용하면 해당 머신의 보안에 영향을 주는 것으로 표시되지 않은 일부 업데이트가 있으므로 업데이트가 적용되지 않습니다. 그러나 업데이트 관리는 관련 업데이트에 대한 추가 정보가 있으므로 해당 머신을 호환되지 않는 것으로 보고할 수 있습니다.

업데이트 분류에 따라 업데이트를 배포하는 것은 RTM 버전의 CentOS에서 작동하지 않습니다. CentOS에 대한 업데이트를 제대로 배포하려면 업데이트를 적용할 수 있도록 모든 분류를 선택합니다. SUSE의 경우 분류로 **기타 업데이트** 만 선택하면 zypper(패키지 관리자)와 관련이 있거나 해당 종속성이 먼저 필요한 경우 일부 다른 보안 업데이트를 설치할 수 있습니다. 이 동작은 zypper의 제한 사항입니다. 경우에 따라 업데이트 배포를 다시 실행한 후 업데이트 로그를 통해 배포를 확인해야 할 수도 있습니다.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Azure 테넌트에 업데이트를 배포할 수 있나요?

업데이트 관리에 보고하는 다른 Azure 테넌트에서 패치가 필요한 머신이 있는 경우 다음 해결 방법을 사용하여 예약해야 합니다. `ForUpdateConfiguration` 매개 변수가 지정된 [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) cmdlet을 사용하여 일정을 만들 수 있습니다. [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) cmdlet을 사용하여 다른 테넌트의 머신을 `NonAzureComputer` 매개 변수에 전달할 수 있습니다. 다음 예제에 이 작업을 수행하는 방법이 나와 있습니다.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="process-automation---python-runbooks"></a>프로세스 자동화 - Python Runbook

### <a name="which-python-3-version-is-supported-in-azure-automation"></a>Azure Automation에서 지원되는 Python 3 버전은 무엇인가요?

클라우드 작업의 경우 Python 3.8이 지원됩니다. 서로 다른 버전에서 코드가 호환되는 경우에는 3.x 버전의 스크립트 및 패키지가 작동할 수 있습니다.

Windows Hybrid Runbook Worker의 하이브리드 작업의 경우 사용하려는 모든 3.x 버전을 설치하도록 선택할 수 있습니다. Linux Hybrid Runbook Workers의 하이브리드 작업의 경우 머신에 설치된 Python 3 버전에 따라 DSC OMSConfig 및 Linux Hybrid Worker를 실행합니다. 버전 3.6을 설치할 것을 권장하지만 Python 3 버전 간에 호환성이 손상되는 메서드 서명 또는 계약 변경이 없다면 다른 버전도 사용할 수 있습니다.

### <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>Python 2 및 Python 3 Runbook을 동일한 Automation 계정에서 실행할 수 있나요?

예. 동일한 Automation 계정에서 Python 2 및 Python 3 Runbook을 사용하는 데에는 제한이 없습니다.  

### <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>기존 Python 2 Runbook 및 패키지를 Python 3으로 마이그레이션하기 위한 계획은 무엇인가요?

Azure Automation은 Python 2 Runbook 및 패키지를 Python 3으로 마이그레이션할 계획이 없습니다. 이 마이그레이션을 직접 수행해야 합니다. 기존 및 새로운 Python 2 Runbook 및 패키지는 계속 작동합니다.

### <a name="what-are-the-packages-supported-by-default-in-python-3-environment"></a>Python 3 환경에서 기본적으로 지원되는 패키지는 무엇인가요?

Azure 패키지 4.0.0은 기본적으로 Python 3 Automation 환경에 설치됩니다. 더 높은 버전의 Azure 패키지를 수동으로 가져와서 기본 버전을 재정의할 수 있습니다.

### <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-the-other-way-around"></a>Python 2 패키지를 참조하는 Python 3 Runbook을 실행하거나 그 반대로 실행하는 경우 어떻게 되나요?

Python 2와 Python 3에는 서로 다른 실행 환경이 있습니다. Python 2 Runbook을 실행하는 동안 Python 2 패키지만 가져올 수 있으며 Python 3의 경우도 이와 유사합니다.

### <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>Python 2와 Python 3 Runbook 및 패키지를 어떻게 구분하나요?

Python 3은 Python 2와 Python 3 Runbook을 구분하는 새로운 Runbook 정의입니다. 마찬가지로 Python 3 패키지에 다른 패키지 종류가 도입되었습니다.

### <a name="how-does-a-hybrid-runbook-worker-know-which-version-of-python-to-run-when-both-python2-and-python3-are-installed"></a>Hybrid Runbook Worker는 Python 2 및 Python 3이 둘 다 설치된 경우 실행할 Python 버전을 어떻게 알 수 있나요?

Windows Runbook Worker의 경우 Python 2 Runbook을 실행할 때 먼저 환경 변수 `PYTHON_2_PATH`를 찾고 유효한 실행 파일을 가리키는지 유효성을 검사합니다. 예를 들어, 설치 폴더가 `C:\Python2`이면 `C:\Python2\python.exe`가 유효한 경로인지 확인합니다. 해당 환경 변수가 없으면 `PATH` 환경 변수를 찾아서 비슷한 확인을 수행합니다.

Python 3의 경우 먼저 `PYTHON_3_PATH` 환경 변수를 찾은 다음, `PATH` 환경 변수로 대체합니다.

Python 버전을 하나만 사용하는 경우 `PATH` 변수에 설치 경로를 추가할 수 있습니다. Runbook Worker에서 두 버전을 모두 사용하려면 `PYTHON_2_PATH` 및 `PYTHON_3_PATH`를 해당 버전의 모듈 위치로 설정합니다.

### <a name="how-does-a-hybrid-runbook-worker-locate-the-python-interpreter"></a>Hybrid Runbook Worker는 Python 인터프리터를 어떻게 찾나요?

Python 모듈 찾기는 앞에서 설명한 대로 환경 변수를 통해 제어합니다.

### <a name="is-python-3-supported-in-source-control"></a>원본 제어에서 Python 3이 지원되나요?

아니요. 원본 제어는 현재 Python 3에서 지원되지 않습니다. 기본적으로 Python Runbook은 Python 2 Runbook으로 동기화됩니다.

### <a name="how-can-a-runbook-author-know-what-python-packages-are-available-in-an-azure-sandbox"></a>Runbook 작성자는 Azure 샌드박스에서 사용할 수 있는 Python 패키지를 어떻게 알 수 있나요?

다음 코드를 사용하여 기본 설치된 모듈을 나열합니다.

```python
#!/usr/bin/env python3

import pkg_resources
installed_packages = pkg_resources.working_set
installed_packages_list = sorted(["%s==%s" % (i.key, i.version)
   for i in installed_packages])

for package in installed_packages_list:
    print(package)
```

### <a name="how-can-a-runbook-author-set-which-version-of-a-package-module-to-be-used-if-there-are-multiple-modules"></a>Runbook 작성자는 여러 모듈이 있는 경우 사용할 패키지 모듈의 버전을 어떻게 설정할 수 있나요?

Automation 계정에서 Python 패키지를 가져와 기본 버전을 재정의할 수 있습니다. Automation 계정에서 가져온 버전에 대한 기본 설정이 제공됩니다.

## <a name="next-steps"></a>다음 단계

질문에 대한 대답이 여기에 없으면 다음 출처에서 추가 질문 및 대답을 참조할 수 있습니다.

- [Azure Automation](/answers/topics/azure-automation.html)
- [피드백 포럼](https://feedback.azure.com/forums/905242-update-management)
