---
title: Azure Automation 데이터 관리
description: 이 문서는 Azure Automation 환경 관리에 대한 여러 항목을 포함합니다.  현재 Azure Automation에 데이터 보존 및 Azure Automation 재해 복구 백업이 포함되어 있습니다.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: f917e9c64a932d75fd0f6b14c9e0f35808467355
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984660"
---
# <a name="managing-azure-automation-data"></a>Azure Automation 데이터 관리

이 문서에는 Azure 자동화 환경에서 데이터를 관리하기 위한 여러 항목이 포함되어 있습니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="data-retention"></a>데이터 보존

Azure 자동화에서 리소스를 삭제하면 영구적으로 제거하기 전에 감사 목적으로 며칠 동안 유지됩니다. 이 시간 동안리소스를 보거나 사용할 수 없습니다. 이 정책은 삭제된 자동화 계정에 속한 리소스에도 적용됩니다.

다음 표에는 여러 리소스에 대한 보존 정책이 요약되어 있습니다.

| 데이터 | 정책 |
|:--- |:--- |
| 계정 |사용자가 계정을 삭제한 후 30일 후에 계정이 영구적으로 삭제됩니다. |
| 자산 |저작물은 사용자가 삭제한 후 30일 후, 또는 사용자가 자산을 보유한 계정을 삭제한 후 30일 후에 영구적으로 제거됩니다. |
| DSC 노드 |DSC 노드는 Azure 포털 또는 Windows PowerShell의 [등록 취소-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) cmdlet을 사용하여 자동화 계정에서 등록취소된 후 30일 후에 영구적으로 제거됩니다. 또한 사용자가 노드를 보유한 계정을 삭제한 후 30일 후에 노드가 영구적으로 제거됩니다. |
| 작업 |작업이 완료되거나 중지되거나 일시 중단된 경우와 같은 수정 후 30일 후에 작업이 삭제되고 영구적으로 제거됩니다. |
| 모듈 |모듈은 사용자가 모듈을 삭제한 후 30일 후 또는 사용자가 모듈을 보유한 계정을 삭제한 후 30일 후에 영구적으로 제거됩니다. |
| 노드 구성/MOF 파일 |새 노드 구성이 생성된 후 30일 후에 이전 노드 구성이 영구적으로 제거됩니다. |
| 노드 보고서 |노드 보고서는 해당 노드에 대해 새 보고서가 생성된 후 90일 후에 영구적으로 제거됩니다. |
| Runbook |Runbook은 사용자가 리소스를 삭제한 후 30일 후 또는 사용자가 리소스를 보유한 계정을 삭제한 후 30일 후에 영구적으로 제거됩니다. |

보존 정책은 모든 사용자에게 적용되며 현재 사용자 지정할 수 없습니다. 그러나 더 긴 기간 동안 데이터를 유지해야 하는 경우 [Azure Automation 작업 데이터를 Azure Monitor 로그로 전달할](automation-manage-send-joblogs-log-analytics.md)수 있습니다.

## <a name="data-backup"></a>데이터 백업

Azure에서 자동화 계정을 삭제하면 계정의 모든 개체가 삭제됩니다. 개체에는 런북, 모듈, 구성, 설정, 작업 및 자산이 포함됩니다. 계정이 삭제된 후에는 복구할 수 없습니다. 다음 정보를 사용하여 자동화 계정의 내용을 백업한 다음 정보를 삭제할 수 있습니다.

### <a name="runbooks"></a>Runbook

Azure Portal 또는 Windows PowerShell의 [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition)을 사용하여 Runbook을 스크립트 파일로 내보낼 수 있습니다. [Azure Automation의 Runbook 관리에서](manage-runbooks.md)설명한 대로 이러한 스크립트 파일을 다른 자동화 계정으로 가져올 수 있습니다.

### <a name="integration-modules"></a>통합 모듈

Azure 자동화에서 통합 모듈을 내보낼 수 없습니다. 자동화 계정 외부에서 사용할 수 있도록 해야 합니다.

### <a name="assets"></a>자산

Azure 자동화 자산(인증서, 연결, 자격 증명, 일정 및 변수)은 내보낼 수 없습니다. 대신 Azure 포털 및 Azure cmdlet을 사용하여 이러한 자산의 세부 정보를 기록할 수 있습니다. 그런 다음 이러한 세부 정보를 사용하여 다른 자동화 계정으로 가져오는 Runbook에서 사용하는 모든 자산을 만듭니다.

cmdlet을 사용하여 암호화된 변수 또는 자격 증명의 암호 필드에 대한 값을 검색할 수 없습니다. 이러한 값을 모르는 경우 Runbook에서 해당 값을 검색할 수 있습니다. 변수 값을 검색하는 경우 [Azure 자동화의 가변 자산을](shared-resources/variables.md)참조하십시오. 자격 증명 값 검색에 대한 자세한 내용은 [Azure Automation의 자격 증명 자산을](shared-resources/credentials.md)참조하십시오.

 ### <a name="dsc-configurations"></a>DSC 구성

Windows PowerShell에서 Azure 포털 또는 [내보내기-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) cmdlet을 사용하여 DSC 구성을 스크립트 파일로 내보낼 수 있습니다. 다른 자동화 계정에서 이러한 구성을 가져오고 사용할 수 있습니다.

## <a name="geo-replication-in-azure-automation"></a>Azure Automation의 지역에서 복제

Azure 자동화 계정의 지리적 복제는 표준입니다. 계정을 설정할 때 기본 지역을 선택합니다. 내부 자동화 지역 복제 서비스는 계정에 보조 지역을 자동으로 할당합니다. 그런 다음 서비스는 기본 리전에서 보조 지역으로 계정 데이터를 지속적으로 백업합니다. 기본 및 보조 지역의 전체 목록은 [비즈니스 연속성 및 재해 복구(BCDR): Azure 페어링 된 지역에서](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)찾을 수 있습니다. 

자동화 지역 복제 서비스에서 만든 백업은 자동화 자산, 구성 등의 전체 복사본입니다. 이 백업은 기본 영역이 다운되어 데이터가 손실되는 경우에 사용할 수 있습니다. 드물게 기본 지역에 대한 데이터가 손실되는 경우 Microsoft는 이를 복구하려고 시도합니다. 회사에서 기본 데이터를 복구할 수 없는 경우 자동 장애 조치(failover)를 사용하고 Azure 구독을 통해 상황을 알려줍니다. 

지역 장애가 있는 경우 자동화 지역 복제 서비스에 는 외부 고객에게 직접 액세스할 수 없습니다. 지역 적 장애 시 자동화 구성 및 runbook을 유지 관리하려면 다음을 수행하십시오.

1. 보조 지역을 선택하여 기본 자동화 계정의 지리적 영역과 페어링합니다.

2. 보조 지역에서 자동화 계정을 만듭니다.

3. 기본 계정에서 Runbook을 스크립트 파일로 내보냅니다.

4. Runbook을 보조 지역의 자동화 계정으로 가져옵니다.

## <a name="next-steps"></a>다음 단계

* Azure 자동화의 보안 자산에 대해 자세히 알아보려면 [Azure 자동화의 보안 자산 암호화를](automation-secure-asset-encryption.md)참조하십시오.

* 지역 복제에 대한 자세한 내용은 [활성 지역 복제 만들기 및 사용](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication)