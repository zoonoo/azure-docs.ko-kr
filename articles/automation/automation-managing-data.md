---
title: Azure Automation 데이터 관리
description: 이 문서는 Azure Automation 환경 관리에 대한 여러 항목을 포함합니다.  현재 Azure Automation에 데이터 보존 및 Azure Automation 재해 복구 백업이 포함되어 있습니다.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: f917e9c64a932d75fd0f6b14c9e0f35808467355
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80984660"
---
# <a name="managing-azure-automation-data"></a>Azure Automation 데이터 관리

이 문서에는 Azure Automation 환경에서 데이터를 관리 하기 위한 여러 항목이 포함 되어 있습니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대한 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](automation-update-azure-modules.md)을 사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="data-retention"></a>데이터 보존

Azure Automation에서 리소스를 삭제 하는 경우 영구적으로 제거 되기 전에 감사를 위해 며칠 동안 보존 됩니다. 이 시간 동안에는 리소스를 보거나 사용할 수 없습니다. 이 정책은 삭제 된 자동화 계정에 속한 리소스에도 적용 됩니다.

다음 표에는 여러 리소스에 대한 보존 정책이 요약되어 있습니다.

| 데이터 | 정책 |
|:--- |:--- |
| 계정 |사용자가 계정을 삭제 하면 30 일 후에 계정이 영구적으로 제거 됩니다. |
| 자산 |사용자가 자산을 삭제 한 후 30 일이 지나면 자산이 영구적으로 제거 되 고, 사용자가 자산을 보유 하는 계정을 삭제 한 후 30 일이 지나면 자산이 영구적으로 제거 됩니다. |
| DSC 노드 |DSC 노드는 Azure Portal 또는 Windows PowerShell의 [AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) cmdlet을 사용 하 여 자동화 계정에서 등록이 취소 된 후 30 일 후에 영구적으로 제거 됩니다. 노드는 사용자가 노드를 보유 하는 계정을 삭제 한 후 30 일 후에도 영구적으로 제거 됩니다. |
| 작업 |작업이 완료 된 후 (예: 작업이 완료 되거나 중지 되거나 일시 중단 된 경우), 수정 후 30 일이 지나면 작업이 삭제 되 고 영구적으로 제거 됩니다. |
| 모듈 |모듈은 사용자가 계정을 삭제 한 후 30 일이 지나면 영구적으로 제거 되 고, 사용자가 모듈을 보유 하는 계정을 삭제 한 후 30 일 후에 제거 됩니다. |
| 노드 구성/MOF 파일 |이전 노드 구성은 새 노드 구성이 생성 된 후 30 일이 지나면 영구적으로 제거 됩니다. |
| 노드 보고서 |노드 보고서는 해당 노드에 대해 새 보고서를 생성 한 후 90 일이 지나면 영구적으로 제거 됩니다. |
| Runbook |Runbook은 사용자가 리소스를 삭제 한 후 30 일이 지나면 영구적으로 제거 되 고, 사용자가 리소스를 보유 하는 계정을 삭제 한 후 30 일이 지나면 영구적으로 제거 됩니다. |

보존 정책은 모든 사용자에 게 적용 되며 현재는 사용자 지정할 수 없습니다. 그러나 데이터를 더 오래 유지 해야 하는 경우 [Azure Automation 작업 데이터를 Azure Monitor 로그에 전달할](automation-manage-send-joblogs-log-analytics.md)수 있습니다.

## <a name="data-backup"></a>데이터 백업

Azure에서 Automation 계정을 삭제 하면 계정의 모든 개체가 삭제 됩니다. 개체에는 runbook, 모듈, 구성, 설정, 작업 및 자산이 포함 됩니다. 계정을 삭제 한 후에는 복구할 수 없습니다. 다음 정보를 사용 하 여 Automation 계정의 콘텐츠를 삭제 하기 전에 백업할 수 있습니다.

### <a name="runbooks"></a>Runbook

Azure Portal 또는 Windows PowerShell의 [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition)을 사용하여 Runbook을 스크립트 파일로 내보낼 수 있습니다. [Azure Automation에서 Runbook 관리](manage-runbooks.md)에 설명 된 대로 이러한 스크립트 파일을 다른 Automation 계정으로 가져올 수 있습니다.

### <a name="integration-modules"></a>통합 모듈

Azure Automation에서 통합 모듈을 내보낼 수 없습니다. Automation 계정 외부에서 사용할 수 있도록 설정 해야 합니다.

### <a name="assets"></a>자산

인증서, 연결, 자격 증명, 일정 및 변수 Azure Automation 자산을 내보낼 수 없습니다. 대신 Azure Portal 및 Azure cmdlet을 사용 하 여 이러한 자산에 대 한 세부 정보를 확인할 수 있습니다. 그런 다음 이러한 세부 정보를 사용 하 여 다른 Automation 계정으로 가져오는 runbook에서 사용 되는 자산을 만듭니다.

Cmdlet을 사용 하 여 암호화 된 변수나 자격 증명의 암호 필드에 대 한 값을 검색할 수 없습니다. 이러한 값을 모르는 경우 runbook에서 검색할 수 있습니다. 변수 값을 검색 하려면 [Azure Automation의 변수 자산](shared-resources/variables.md)을 참조 하세요. 자격 증명 값을 검색 하는 방법에 대 한 자세한 내용은 [Azure Automation 자격 증명 자산](shared-resources/credentials.md)을 참조 하세요.

 ### <a name="dsc-configurations"></a>DSC 구성

Azure Portal 또는 Windows PowerShell의 [AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) cmdlet 중 하나를 사용 하 여 DSC 구성을 스크립트 파일로 내보낼 수 있습니다. 이러한 구성은 다른 Automation 계정에서 가져오고 사용할 수 있습니다.

## <a name="geo-replication-in-azure-automation"></a>Azure Automation의 지역에서 복제

지역에서 복제는 Azure Automation 계정의 표준입니다. 계정을 설정할 때 주 지역을 선택 합니다. 내부 자동화 지역에서 복제 서비스는 자동으로 계정에 보조 지역을 할당 합니다. 그런 다음 서비스는 주 지역에서 보조 지역으로 계정 데이터를 지속적으로 백업 합니다. 기본 및 보조 지역의 전체 목록은 [BCDR (비즈니스 연속성 및 재해 복구): Azure 쌍을 이루는 지역](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)에서 찾을 수 있습니다. 

자동화 지역에서 복제 서비스에 의해 생성 된 백업은 자동화 자산, 구성 및 이와 같은의 완전 한 복사본입니다. 주 지역이 중단 되 고 데이터가 손실 되는 경우이 백업을 사용할 수 있습니다. 주 지역에 대 한 데이터가 손실 될 가능성이 있는 경우 Microsoft에서 복구를 시도 합니다. 회사에서 기본 데이터를 복구할 수 없는 경우 자동 장애 조치 (failover)를 사용 하 고 Azure 구독을 통해 상황을 알려 줍니다. 

지역 장애가 발생 하는 경우 자동화 지역 복제 서비스는 외부 고객에 게 직접 액세스할 수 없습니다. 지역 오류 시 자동화 구성 및 runbook을 유지 하려는 경우:

1. 주 Automation 계정의 지역에 연결할 보조 지역을 선택 합니다.

2. 보조 지역에 Automation 계정을 만듭니다.

3. 주 계정에서 runbook을 스크립트 파일로 내보냅니다.

4. Runbook을 보조 지역의 Automation 계정으로 가져옵니다.

## <a name="next-steps"></a>다음 단계

* Azure Automation의 보안 자산에 대 한 자세한 내용은 [Azure Automation 보안 자산 암호화](automation-secure-asset-encryption.md)를 참조 하세요.

* 지역에서 복제에 대 한 자세한 내용은 [활성 지역 복제 만들기 및 사용](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication)을 참조 하세요.