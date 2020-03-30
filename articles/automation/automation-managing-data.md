---
title: Azure Automation 데이터 관리
description: 이 문서는 Azure Automation 환경 관리에 대한 여러 항목을 포함합니다.  현재 Azure Automation에 데이터 보존 및 Azure Automation 재해 복구 백업이 포함되어 있습니다.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: 340fa19b6f9f07e192123900bc96b07bb016542f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132892"
---
# <a name="managing-azure-automation-data"></a>Azure Automation 데이터 관리

이 문서는 Azure Automation 환경 관리에 대한 여러 항목을 포함합니다.

## <a name="data-retention"></a>데이터 보존

Azure 자동화에서 리소스를 삭제하면 영구적으로 제거되기 전에 감사 목적으로 30일 동안 유지됩니다. 이 시간 동안리소스를 보거나 사용할 수 없습니다. 이 정책은 삭제되는 자동화 계정에 속한 리소스에도 적용됩니다.

Azure Automation은 30일이 지난 작업을 자동으로 삭제하고 영구적으로 제거합니다.

다음 표에는 여러 리소스에 대한 보존 정책이 요약되어 있습니다.

| 데이터 | 정책 |
|:--- |:--- |
| 계정 |사용자가 계정을 삭제한 후 30일 후에 영구적으로 삭제되었습니다. |
| 자산 |사용자가 저작물을 삭제한 후 30일 후 또는 자산을 보유한 계정이 사용자에 의해 삭제된 후 30일 후에 영구적으로 제거됩니다. |
| 모듈 |사용자가 모듈을 삭제한 후 30일 후 또는 모듈을 보유한 계정이 사용자에 의해 삭제된 후 30일 후에 영구적으로 제거됩니다. |
| Runbook |사용자가 리소스를 삭제한 후 30일 후 또는 리소스를 보유한 계정이 사용자에 의해 삭제된 후 30일 후에 영구적으로 제거됩니다. |
| 작업 |마지막으로 수정된 후 30일 후에 삭제되고 영구적으로 제거되었습니다. 이는 작업이 완료되거나 중지되거나 일시 중단된 이후일 수 있습니다. |
| 노드 구성/MOF 파일 |새 노드 구성이 생성된 후 30일 후에 이전 노드 구성이 영구적으로 제거됩니다. |
| DSC 노드 |Windows PowerShell에서 Azure 포털 또는 [등록 취소-AzureRMAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) cmdlet을 사용하여 노드가 자동화 계정에서 등록 취소된 후 30일 후에 영구적으로 제거되었습니다. 노드를 보유한 계정이 사용자에 의해 삭제된 후 30일 후에 노드도 영구적으로 제거됩니다. |
| 노드 보고서 |해당 노드에 대한 새 보고서가 생성된 후 90일이 지나면 영구적으로 제거됩니다. |

보존 정책은 모든 사용자에게 적용되며 지금은 사용자 지정할 수 없습니다.

그러나 더 긴 기간 동안 데이터를 유지해야 하는 경우 Runbook 작업 로그를 Azure Monitor 로그로 전달할 수 있습니다. 자세한 내용은 [Azure 자동화 작업 데이터를 Azure Monitor 로그로 전달합니다.](automation-manage-send-joblogs-log-analytics.md)

## <a name="backing-up-azure-automation"></a>Azure Automation 백업

Microsoft Azure에서 자동화 계정을 삭제하면 Runbook, 모듈, 구성, 설정, 작업, 자산 등 해당 계정의 모든 개체가 삭제됩니다. 계정을 삭제한 후에는 개체를 복구할 수 없습니다.  자동화 계정을 삭제하기 전에 다음 정보를 사용하여 자동화 계정의 내용을 백업할 수 있습니다.

### <a name="runbooks"></a>Runbook

Azure Portal 또는 Windows PowerShell의 [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition)을 사용하여 Runbook을 스크립트 파일로 내보낼 수 있습니다. [Runbook 만들기 또는 가져오기](/previous-versions/azure/dn643637(v=azure.100))에 설명된 것처럼 다른 자동화 계정으로 이러한 스크립트 파일을 가져올 수 있습니다.

### <a name="integration-modules"></a>통합 모듈

Azure Automation에서는 통합 모듈을 내보낼 수 없습니다. 자동화 계정 외부에서 사용할 수 있는지 확인해야 합니다.

### <a name="assets"></a>자산

Azure Automation에서는 [자산](/previous-versions/azure/dn939988(v=azure.100)) 을 내보낼 수 없습니다.  Azure Portal을 사용할 때 변수, 자격 증명, 인증서, 연결 및 일정에 대한 세부 정보를 기록해 두어야 합니다.  그런 다음 다른 자동화로 가져온 Runbook에서 사용하는 자산을 수동으로 만들어야 합니다.

[Azure cmdlet](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) 을 사용하여 암호화되지 않은 자산의 세부 정보를 검색하고 나중에 참조하기 위해 저장하거나 다른 자동화 계정에서 동일한 자산을 만들 수 있습니다.

자격 증명의 암호 필드 또는 암호화된 변수 값은 cmdlet을 사용하여 검색할 수 없습니다. 이러한 값을 모르는 경우 [Get-AutomationVariable](/previous-versions/azure/dn940012(v=azure.100)) 및 [Get-AutomationPSCredential](/previous-versions/azure/dn940015(v=azure.100)) 활동을 사용하여 Runbook에서 검색할 수 있습니다.

Azure Automation에서는 인증서를 내보낼 수 없습니다. Azure 외부에서 인증서를 사용할 수 있는지 확인해야 합니다.

### <a name="dsc-configurations"></a>DSC 구성

Azure Portal 또는 Windows PowerShell의 [Export-AzureRmAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) cmdlet을 사용하여 구성을 스크립트 파일로 내보낼 수 있습니다. 이러한 구성은 다른 자동화 계정에서 가져오고 사용할 수 있습니다.

## <a name="geo-replication-in-azure-automation"></a>Azure Automation의 지역에서 복제

Azure Automation 계정의 표준인 지역에서 복제는 중복성을 위해 다른 지역에 계정 데이터를 백업합니다. 사용자 계정을 설정할 때 주 지역을 선택할 수 있고 보조 지역은 자동으로 할당됩니다. 주 지역에서 복사된 보조 데이터는 데이터 손실이 발생할 경우 지속적으로 업데이트됩니다.  

다음 테이블에서는 사용 가능한 기본 및 보조 지역 쌍을 보여 줍니다.

| 주 | 보조 |
| --- | --- |
| 미국 중남부 |미국 중북부 |
| 미국 동부 2 |미국 중부 |
| 서유럽 |북유럽 |
| 동남아시아 |동아시아 |
| 일본 동부 |일본 서부 |

주 지역 데이터가 손실되는 예기치 않은 이벤트가 발생한  경우 Microsoft는 복구를 시도합니다. 기본 데이터를을 복원할 수 없는 경우 지역 장애 조치(failover)가 수행되고 영향을 받는 고객은 해당 구독을 통해 이에 대한 알림을 받게 됩니다.
