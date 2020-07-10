---
title: Orchestrator에서 Azure Automation으로 마이그레이션(베타)
description: 이 문서에서는 Orchestrator에서 Azure Automation으로 Runbook 및 통합 팩을 마이그레이션하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 3399138ef7c14dd2db9133334a08b3984bd26448
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186007"
---
# <a name="migrate-from-orchestrator-to-azure-automation-beta"></a>Orchestrator에서 Azure Automation으로 마이그레이션(베타)

[System Center 2012 - Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12))의 Runbook은 특별히 Orchestrator용으로 작성된 통합 팩의 활동을 기반으로 하는 반면, Azure Automation의 Runbook은 Windows PowerShell을 기반으로 합니다. Azure Automation에서 [그래픽 Runbook](automation-runbook-types.md#graphical-runbooks)은 해당 활동이 PowerShell cmdlet, 자식 Runbook 및 자산을 나타내는 Orchestrator Runbook과 모양이 유사합니다. Runbook 자체를 변환하는 것 외에 Runbook에서 사용하는 활동이 포함된 통합 팩을 Windows PowerShell cmdlet이 포함된 통합 모듈로 변환해야 합니다. 

SMA([Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)))는 Orchestrator처럼 로컬 데이터 센터에서 Runbook을 저장하고 실행하며, Azure Automation과 동일한 통합 모듈을 사용합니다. Runbook Converter는 Orchestrator Runbook을 그래픽 Runbook으로 변환합니다(SMA에서는 지원되지 않음). Standard Activities Module 및 System Center Orchestrator Integration Modules를 SMA에 설치할 수 있지만 [Runbook을 수동으로 다시 작성](/system-center/sma/authoring-automation-runbooks)해야 합니다.

## <a name="download-the-orchestrator-migration-toolkit"></a>Orchestrator 마이그레이션 도구 키트 다운로드

마이그레이션의 첫 번째 단계는 [System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all)을 다운로드하는 것입니다. 이 도구 키트에는 Orchestrator의 Runbook을 Azure Automation으로 변환하는 데 도움이 되는 도구가 포함되어 있습니다.  

## <a name="import-the-standard-activities-module"></a>표준 활동 모듈 가져오기

[표준 활동 모듈](/system-center/orchestrator/standard-activities?view=sc-orch-2019)을 Azure Automation에 가져옵니다. 여기에는 변환된 그래픽 Runbook에서 사용할 수 있는 변환된 버전의 표준 Orchestrator 활동이 들어 있습니다.

## <a name="import-orchestrator-integration-modules"></a>Orchestrator 통합 모듈 가져오기

Microsoft에서는 System Center 구성 요소 및 기타 제품을 자동화하는 Runbook을 빌드할 수 있는 [통합 팩](/previous-versions/system-center/packs/hh295851(v=technet.10)) 을 제공합니다. 이러한 통합 팩의 일부는 현재 OIT 기반이지만 현재 알려진 문제 때문에 통합 모듈로 변환될 수 없습니다. System Center에 액세스하는 Runbook에서 사용하는 통합 팩을 위해 [System Center Orchestrator Integration Modules](https://www.microsoft.com/download/details.aspx?id=49555)를 Azure Automation에 가져옵니다. 이 패키지에는 Azure Automation 및 Service Management Automation으로 가져올 수 있는 변환된 버전의 통합 팩이 포함되어 있습니다.  

## <a name="convert-integration-packs"></a>통합 팩 변환

[Integration Pack Converter](/system-center/orchestrator/orch-integration-toolkit/integration-pack-wizard?view=sc-orch-2019)를 사용하여 [OIT(Orchestrator Integration Toolkit)](/previous-versions/system-center/developer/hh855853(v=msdn.10))를 사용하여 만든 통합 팩을 Azure Automation 또는 Service Management Automation으로 가져올 수 있는 PowerShell 기반 통합 모듈로 변환합니다. Integration Pack Converter를 실행하면 통합 팩(.oip) 파일을 선택할 수 있는 마법사가 제공됩니다. 이 마법사는 해당 통합 팩에 포함된 활동을 나열하며 이를 통해 마이그레이션할 활동을 선택할 수 있습니다. 마법사를 완료하면 원래 통합 팩의 각 활동에 대한 해당 cmdlet이 포함된 통합 모듈이 만들어집니다.

> [!NOTE]
> OIT로 만들지 않은 통합 팩은 Integration Pack Converter를 사용하여 변환할 수 없습니다. 또한 이 도구로 현재 변환할 수 없는 Microsoft에서 제공한 통합 팩이 있습니다. 이러한 변환된 버전의 통합 팩은 다운로드를 위해 제공되므로 Azure Automation 또는 Service Management Automation에 설치할 수 있습니다.

### <a name="parameters"></a>매개 변수

통합 팩에 있는 활동의 모든 속성은 통합 모듈에서 해당 cmdlet의 매개 변수로 변환됩니다.  Windows PowerShell cmdlet에는 모든 cmdlet에서 사용할 수 있는 [일반 매개 변수](/powershell/module/microsoft.powershell.core/about/about_commonparameters) 집합이 있습니다. 예를 들어 -Verbose 매개 변수를 사용하면 cmdlet에서 해당 작업에 대한 자세한 정보를 출력합니다.  일반 매개 변수와 이름이 같은 매개 변수는 cmdlet에서 사용할 수 없습니다. 일반 매개 변수와 이름이 같은 속성이 활동에 있는 경우 마법사에서 다른 매개 변수 이름을 제공하라는 메시지를 표시합니다.

### <a name="monitor-activities"></a>모니터링 활동

Orchestrator의 모니터링 Runbook은 [모니터링 활동](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) 으로 시작되며, 특정 이벤트의 호출을 대기하면서 지속적으로 실행됩니다. Azure Automation은 모니터링 Runbook을 지원하지 않으므로 통합 팩의 모니터링 활동은 변환되지 않습니다. 대신 모니터링 활동에 대한 자리 표시자 cmdlet이 통합 모듈에 생성됩니다.  이 cmdlet은 이를 사용하는 변환된 Runbook을 설치할 수 있도록 해줄 뿐 아무 기능이 없습니다. 이 Runbook은 Azure Automation에서 실행할 수 없지만 수정할 수 있도록 설치할 수 있습니다.

Orchestrator에는 통합 팩에 없지만 많은 Runbook에서 사용하는 [표준 활동](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12)) 집합이 들어 있습니다.  Standard Activities Module은 이러한 각 활동에 해당하는 cmdlet을 포함하는 통합 모듈입니다. 표준 활동을 사용하는 변환된 Runbook을 가져오기 전에 이 통합 모듈을 Azure Automation에 설치해야 합니다.

Standard Activities Module의 cmdlet은 변환된 Runbook을 지원할 뿐만 아니라 Orchestrator에 친숙한 사용자가 Azure Automation에서 새 Runbook을 빌드하는 데 사용할 수 있습니다. 모든 표준 활동의 기능은 cmdlet을 사용하여 수행할 수 있지만 서로 다르게 작동할 수 있습니다. 변환된 표준 활동 모듈의 cmdlet은 해당 활동과 동일한 방식으로 작동하며 동일한 매개 변수를 사용합니다. 이를 통해 Azure Automation Runbook으로 전환할 수 있습니다.

## <a name="convert-orchestrator-runbooks"></a>Orchestrator Runbook 변환

Orchestrator Runbook Converter는 Orchestrator Runbook을 Azure Automation으로 가져올 수 있는 [그래픽 Runbook](automation-runbook-types.md#graphical-runbooks)으로 변환합니다. Runbook Converter는 변환을 수행하는 `ConvertFrom-SCORunbook` cmdlet을 사용하여 PowerShell 모듈로 구현됩니다. 변환기를 설치하면 cmdlet을 로드하는 PowerShell 세션에 바로 가기를 만듭니다.   

Runbook을 변환하고 Azure Automation으로 가져오기 위한 기본 단계는 다음과 같습니다. cmdlet 사용에 대한 자세한 내용은 이 섹션의 뒷부분에서 제공됩니다.

1. Orchestrator에서 하나 이상의 runbook을 내보냅니다.
2. Runbook의 모든 활동에 대한 통합 모듈을 가져옵니다.
3. 내보낸 파일에서 Orchestrator runbook을 변환합니다.
4. 변환의 유효성을 검사하고 필요한 수동 작업을 결정하려면 로그에서 정보를 검토합니다.
5. 변환된 runbook을 Azure Automation으로 가져옵니다.
6. Azure Automation에 필요한 자산을 만듭니다.
7. 필요한 동작을 수정하려면 Azure Automation에서 runbook을 편집합니다.

`ConvertFrom-SCORunbook`에 대한 구문은 다음과 같습니다.

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath - 변환할 runbook을 포함하는 내보내기 파일의 경로입니다.
* 모듈 - runbook의 작업을 포함하는 통합 모듈의 목록을 구분하는 쉼표입니다.
* OutputFolder - 변환된 그래픽 runbook을 만드는 폴더의 경로입니다.

다음 예제 명령은 **MyRunbooks.ois_export**라는 내보내기 파일에서 runbook을 변환합니다.  이러한 runbook은 Active Directory 및 Data Protection Manager 통합 팩을 사용합니다.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="use-runbook-converter-log-files"></a>Runbook Converter 로그 파일 사용

Runbook Converter는 동일한 위치에 있는 다음 로그 파일을 변환된 Runbook으로 만듭니다.  파일이 이미 있는 경우 마지막 변환에서 정보를 사용하여 덮어씁니다.

| 파일 | 콘텐츠 |
|:--- |:--- |
| Runbook Converter - Progress.log |성공적으로 변환된 각 작업에 대한 정보 및 변환되지 않은 각 활동에 대한 경고를 포함하는 변환의 자세한 단계입니다. |
| Runbook Converter - Summary.log |변환된 runbook에 필요한 변수를 만드는 등 수행해야 하는 경고 및 후속 작업을 포함한 마지막 변환의 요약입니다. |

### <a name="export-runbooks-from-orchestrator"></a>Orchestrator에서 Runbook 내보내기

Runbook Converter는 하나 이상의 runbook을 포함하는 Orchestrator에서 내보내기 파일을 사용합니다.  각 Orchestrator Runbook에 대해 해당하는 Azure Automation Runbook을 내보내기 파일에 만듭니다.  

Orchestrator에서 runbook을 내보내려면 Runbook Designer에서 runbook의 이름을 마우스 오른쪽 단추로 클릭하고 **내보내기**를 선택합니다.  폴더에서 모든 runbook을 내보내려면 폴더의 이름을 마우스 오른쪽 단추로 클릭하고 **내보내기**를 선택합니다.

### <a name="convert-runbook-activities"></a>Runbook 활동 변환

Runbook Converter는 Azure Automation에서 Orchestrator runbook의 각 작업을 해당하는 작업으로 변환합니다.  변환할 수 없는 해당 작업의 경우 자리 표시자 작업은 경고 텍스트를 사용하여 runbook에서 만들어집니다.  Azure Automation에 변환된 runbook을 가져온 후에 이러한 작업을 필요한 기능을 수행하는 유효한 작업으로 대체해야 합니다.

표준 활동 모듈의 모든 Orchestrator 활동이 변환됩니다. 이 모듈에 없지만 변환되지 않는 일부 표준 Orchestrator 작업이 있습니다. 예를 들어 `Send Platform Event`는 이벤트가 Orchestrator에 지정되기 때문에 Azure Automation과 동등하지 않습니다.

[작업 모니터링](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) 은 Azure Automation에서 동등한 기능이 없기 때문에 변환되지 않습니다. 예외는 자리 표시자 활동으로 변환되는 변환된 통합 팩의 활동을 모니터링합니다.

`modules` 매개 변수를 사용하여 통합 모듈에 경로를 제공하는 경우 변환된 통합 팩에서 활동이 변환됩니다. System Center 통합 팩의 경우 System Center Orchestrator 통합 모듈을 사용할 수 있습니다.

### <a name="manage-orchestrator-resources"></a>Orchestrator 리소스 관리

Runbook Converter는 카운터, 변수, 연결 등 다른 Orchestrator 리소스가 아닌 Runbook만을 변환합니다.  카운터는 Azure Automation에서 지원되지 않습니다.  변수 및 연결은 지원되지만 수동으로 만들어야 합니다. Runbook이 이러한 리소스가 필요한 경우 로그 파일은 사용자에게 알려주고 변환된 Runbook이 제대로 작동하기 위해 Azure Automation에서 만들어야 하는 해당 리소스를 지정합니다.

예를 들어 runbook은 작업에 특정 값을 채우기 위해 변수를 사용할 수 있습니다.  변환된 Runbook은 작업을 변환하고 Azure Automation에서 Orchestrator 변수와 동일한 이름을 사용하여 변수 자산을 지정합니다. 이 활동은 변환 후에 만들어진 **Runbook Converter - Summary.log** 파일에서 설명합니다. Runbook을 사용하기 전에 Azure Automation에서 이 변수 자산을 수동으로 만들어야 합니다.

### <a name="work-with-orchestrator-input-parameters"></a>Orchestrator 입력 매개 변수 작업

Orchestrator의 Runbook은 `Initialize Data` 활동을 사용하여 입력 매개 변수를 수용합니다.  변환 중인 runbook이 이 활동을 포함하는 경우 Azure Automation runbook에서 [입력 매개 변수](automation-graphical-authoring-intro.md#handle-runbook-input) 는 작업에서 각 매개 변수에 대해 만들어집니다.  [워크플로 스크립트 제어](automation-graphical-authoring-intro.md#use-activities) 활동은 각 매개 변수를 검색하고 반환하는 변환된 runbook에 만들어집니다. 입력 매개 변수를 사용하는 Runbook의 활동은 이 활동에서 출력을 참조합니다.

이 전략을 사용하는 이유는 Orchestrator runbook의 기능을 가장 잘 미러링하기 때문입니다.  새 그래픽 runbook의 작업은 Runbook 입력 데이터 원본을 사용하여 입력 매개 변수를 직접 참조해야 합니다.

### <a name="invoke-runbook-activity"></a>Runbook 작업 호출

Orchestrator에서 Runbook은 `Invoke Runbook` 활동을 사용하여 다른 Runbook을 시작합니다. 변환 중인 Runbook이 이 활동을 포함하고 `Wait for completion` 옵션이 설정된 경우 Runbook 작업은 변환된 Runbook에서 만들어집니다.  `Wait for completion` 옵션을 설정하지 않으면 워크플로 스크립트 활동은 Runbook을 시작하는 데 [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0)을 사용하도록 만들어집니다. Azure Automation에 변환된 runbook을 가져온 후에 작업에 지정한 정보를 사용하여 이 작업을 수정해야 합니다.

## <a name="create-orchestrator-assets"></a>Orchestrator 자산 만들기

Runbook Converter는 Orchestrator 자산을 변환하지 않습니다. Azure Automation에서 필요한 Orchestrator 자산을 수동으로 만들어야 합니다.

## <a name="configure-hybrid-runbook-worker"></a>Hybrid Runbook Worker 구성

Orchestrator는 Runbook을 데이터베이스 서버에 저장하고 Runbook 서버에서 실행합니다(두 서버 모두 로컬 데이터 센터에 있음). Azure Automation의 Runbook은 Azure 클라우드에 저장되며, [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)를 사용하여 로컬 데이터 센터에서 실행할 수 있습니다. Orchestrator에서 변환된 Runbook은 로컬 서버에서 실행되고 로컬 리소스에 액세스하도록 설계되므로 이를 실행하도록 작업자를 구성합니다.

## <a name="related-articles"></a>관련 문서

* Orchestrator에 대한 자세한 내용은 [System Center 2012 - Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12))를 참조하세요.
* [Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12))에서 서비스 관리를 자동화하는 방법에 대해 자세히 알아보세요.
* Orchestrator 활동의 세부 정보는 [Orchestrator 표준 활동](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12))에서 찾을 수 있습니다.
* Orchestrator 마이그레이션 도구 키트를 얻으려면 [System Center Orchestrator Migration Toolkit 다운로드](https://www.microsoft.com/download/details.aspx?id=47323)를 참조하세요.
* Azure Automation Hybrid Runbook Worker의 개요는 [Hybrid Runbook Worker 개요](automation-hybrid-runbook-worker.md)를 참조하세요.
