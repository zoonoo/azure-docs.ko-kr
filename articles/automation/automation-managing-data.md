---
title: Azure Automation 데이터 관리
description: 이 문서에서는 데이터 보존 및 백업을 비롯하여 Azure Automation의 데이터 관리에 대한 개념을 제공합니다.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: de60ef31a39a698f9a797a5836546f9b75b67594
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835209"
---
# <a name="management-of-azure-automation-data"></a>Azure Automation 데이터 관리

이 문서는 Azure Automation 환경의 데이터 관리에 대한 여러 항목을 포함합니다.

## <a name="data-retention"></a>데이터 보존

Azure Automation에서 리소스를 삭제할 경우 영구적으로 제거하기 전에 감사를 위해 며칠 동안 보존됩니다. 이 기간 동안에는 리소스를 보거나 사용할 수 없습니다. 이 정책은 삭제되는 Automation 계정에 속한 리소스에도 적용됩니다.

다음 표에는 여러 리소스에 대한 보존 정책이 요약되어 있습니다.

| 데이터 | 정책 |
|:--- |:--- |
| 계정 |사용자가 삭제한 후 30일이 지나면 계정은 영구적으로 제거됩니다. |
| 자산 |자산은 사용자가 삭제한 후 30일이 지나거나 사용자가 자산을 보유한 계정을 삭제한 후 30일이 지나면 영구적으로 제거됩니다. |
| DSC 노드 |DSC 노드는 Azure Portal이나 Windows PowerShell에서 [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) cmdlet을 사용하여 자동화 계정에서 노드의 등록을 취소한 후 30일이 지나면 영구적으로 제거됩니다. 노드는 또한 사용자가 노드를 보유하는 계정을 삭제한 후 30일이 지나면 영구적으로 제거됩니다. |
| 작업 |작업 완료, 중지 또는 일시 중단과 같은 수정 이후 30일이 지나면 작업이 삭제되고 영구 제거됩니다. |
| 모듈 |모듈은 사용자가 삭제한 후 30일이 지나면 영구적으로 제거됩니다. 또는 사용자가 모듈을 보유한 계정을 삭제한 후 30일이 지나면 영구적으로 제거됩니다. |
| 노드 구성/MOF 파일 |이전 노드 구성은 새 노드 구성이 생성된 후 30일이 지나면 영구적으로 제거됩니다. |
| 노드 보고서 |노드 보고서는 해당 노드에 대한 새 보고서가 생성된 후 90일이 지나면 영구적으로 제거됩니다. |
| Runbook |Runbook은 사용자가 리소스를 삭제한 후 30일이 지나면 영구적으로 제거됩니다. 또는 사용자가 리소스를 보유한 계정을 삭제한 후 30일이 지나면 영구적으로 제거됩니다. |

보존 정책은 모든 사용자에게 적용되며 지금은 사용자 지정할 수 없습니다. 하지만 데이터를 더 오랜 기간 동안 유지해야 하는 경우 [Azure Automation 작업 데이터를 Azure Monitor 로그로 전달](automation-manage-send-joblogs-log-analytics.md)할 수 있습니다.

## <a name="data-backup"></a>데이터 백업

Azure에서 Automation 계정을 삭제하면 계정의 모든 개체가 삭제됩니다. 개체에는 Runbook, 모듈, 구성, 설정, 작업 및 자산이 포함됩니다. 계정을 삭제한 후에는 개체를 복구할 수 없습니다. Automation 계정을 삭제하기 전에 다음 정보를 사용하여 Automation 계정의 내용을 백업할 수 있습니다.

### <a name="runbooks"></a>Runbook

Azure Portal 또는 Windows PowerShell의 [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition)을 사용하여 Runbook을 스크립트 파일로 내보낼 수 있습니다. [Azure Automation에서 Runbook 관리](manage-runbooks.md)에서 설명하는 것과 같이 다른 Automation 계정으로 스크립트 파일을 가져올 수 있습니다.

### <a name="integration-modules"></a>통합 모듈

Azure Automation에서는 통합 모듈을 내보낼 수 없습니다. Automation 계정 외부에서 사용할 수 있도록 설정해야 합니다.

### <a name="assets"></a>자산

인증서, 연결, 자격 증명, 일정 및 변수 등 Azure Automation 자산을 내보낼 수 없습니다. 대신 Azure Portal 및 Azure cmdlet을 사용하여 이러한 자산에 대한 세부 정보를 확인할 수 있습니다. 그런 다음 이러한 세부 정보를 사용하여 다른 Automation 계정으로 가져온 Runbook에서 사용하는 자산을 만듭니다.

자격 증명의 암호 필드 또는 암호화된 변수 값은 cmdlet을 사용하여 검색할 수 없습니다. 이러한 값을 모르는 경우 Runbook에서 검색할 수 있습니다. 변수 값 검색은 [Azure Automation에서 변수 관리](shared-resources/variables.md)를 참조하세요. 자격 증명 값 검색을 자세히 알아보려면 [Azure Automation의 자격 증명 자산](shared-resources/credentials.md)을 참조하세요.

 ### <a name="dsc-configurations"></a>DSC 구성

Azure Portal 또는 Windows PowerShell의 [Export-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) cmdlet을 사용하여 DSC 구성을 스크립트 파일로 내보낼 수 있습니다. 다른 Automation 계정에서 이러한 구성을 가져오고 사용할 수 있습니다.

## <a name="geo-replication-in-azure-automation"></a>Azure Automation의 지역에서 복제

지역 복제는 Azure Automation 계정에서 표준입니다. 계정을 설정할 때 주 지역을 선택합니다. 내부 Automation 지역 복제 서비스는 자동으로 계정에 보조 지역을 할당합니다. 그런 다음 서비스는 주 지역에서 보조 지역으로 계정 데이터를 지속적으로 백업합니다. 주 지역 및 보조 지역의 전체 목록은 [BCDR(비즈니스 연속성 및 재해 복구): Azure 쌍을 이루는 지역](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)을 참조하세요. 

Automation 지역 복제 서비스에 의해 생성된 백업은 Automation 자산, 구성 등의 전체 복사본입니다. 주 지역이 중단되고 데이터가 손실되는 경우 이 백업을 사용할 수 있습니다. 주 지역 데이터가 손실되는 예기치 않은 이벤트가 발생한 경우 Microsoft는 복구를 시도합니다. 회사에서 주 데이터를 복구할 수 없는 경우 자동 장애 조치를 사용하고 Azure 구독을 통해 상황을 알립니다. 

지역 실패가 발생하는 경우 Automation 지역 복제 서비스는 외부 고객에게 직접 액세스할 수 없습니다. 지역 실패가 발생하는 경우 Automation 구성 및 Runbook을 유지하고자 하는 경우 다음을 수행합니다.

1. 주 Automation 계정의 지역에 연결할 보조 지역을 선택합니다.

2. 보조 지역에서 Automation 계정을 만듭니다.

3. 주 계정에서 Runbook을 스크립트 파일로 내보냅니다.

4. Runbook을 보조 지역의 Automation 계정으로 가져옵니다.

## <a name="next-steps"></a>다음 단계

* Azure Automation의 보안 자산에 대한 자세한 내용은 [Azure Automation의 보안 자산 암호화](automation-secure-asset-encryption.md)를 참조하세요.
* 지역 복제에 대한 자세한 내용은 [활성 지역 복제 만들기 및 사용](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication)을 참조하세요.