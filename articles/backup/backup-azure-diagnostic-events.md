---
title: 복구 서비스 볼트에 진단 설정 사용
description: Azure Backup에 대한 이전 및 새 진단 이벤트를 사용하는 방법을 설명하는 문서
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: e3919d120e5f741af6cd30dd27e5a1dfa2b06cf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136942"
---
# <a name="using-diagnostics-settings-for-recovery-services-vaults"></a>Recovery Services 자격 증명 모음용 진단 설정 사용

Azure Backup은 분석, 경고 및 보고를 위해 수집및 사용할 수 있는 진단 이벤트를 보냅니다. 

자격 증명 모음으로 이동하 고 **진단 설정** 메뉴 항목을 클릭 하 여 Azure 포털을 통해 복구 서비스 볼트에 대 한 진단 설정을 구성할 수 있습니다. + **진단 추가 설정을** 클릭하면 하나 이상의 진단 이벤트를 저장소 계정, 이벤트 허브 또는 로그 분석(LA) 작업 영역으로 보낼 수 있습니다.

![진단 설정 블레이드](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Azure 백업 사용자가 사용할 수 있는 진단 이벤트

Azure Backup은 다음과 같은 진단 이벤트를 제공하며, 각 진단 이벤트는 특정 백업 관련 아티팩트 집합에 대한 자세한 데이터를 제공합니다.

* 코어Azure백업
* 애드온Azure백업경고
* 애드온Azure백업보호인스턴스
* 애드온Azure백업작업
* 애드온Azure백업정책
* 애드온Azure백업스토리지

[Azure 백업 진단 이벤트에 대한 데이터 모델](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

이러한 이벤트에 대한 데이터는 저장소 계정, LA 작업 영역 또는 이벤트 허브로 보낼 수 있습니다. 이 데이터를 LA 작업 공간으로 보내는 경우 **진단 설정** 화면에서 **리소스별** 토글을 선택해야 합니다(아래 섹션에서 자세한 정보 참조).

## <a name="using-diagnostics-settings-with-log-analytics-la"></a>로그 분석(LA)을 사용하여 진단 설정 사용

Azure Log Analytics 로드맵에 맞게 Azure Backup을 사용하면 백업을 위한 전용 LA 테이블에 볼트 진단 데이터를 보낼 수 있습니다. 이를 [리소스 별 테이블이라고](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)합니다.

볼트 진단 데이터를 LA로 보내려면 다음 을 수행하십시오.

1.  볼트로 이동하여 **진단 설정을**클릭합니다. **+진단 설정 추가를**클릭합니다.
2.  진단 설정에 이름을 지정합니다.
3.  **로그 분석으로 보내기** 확인란을 선택하고 로그 분석 작업 영역을 선택합니다.
4.  토글에서 **특정 리소스를** 선택하고 다음 여섯 가지 이벤트를 확인합니다 - **CoreAzureBackup,** **AddonAzureBackupAlerts,** **AddonAzureBackupBackupJobs,** **AddonAzureBackupPolicy,** 및 **AddonAzureBackupStorage**. **AddonAzureBackupJobs**
5.  **Save**를 클릭합니다.

![리소스별 모드](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

데이터가 LA 작업 공간으로 흐르면 이러한 각 이벤트에 대한 전용 테이블이 작업 영역에 만들어집니다. 이러한 테이블을 직접 쿼리하고 필요한 경우 이러한 테이블 간에 조인 또는 공용 구조체를 수행할 수도 있습니다.

> [!IMPORTANT]
> 위의 여섯 이벤트, 즉, 코어 AzureBackupAlerts, AddonAzureBackupProtectedInstance, AddonAzureBackupJobs, AddonAzureBackupPolicy 및 AddonAzureBackupStorage는 자원 특정 **모드에서만** 지원됩니다. **Azure 진단 모드에서 이러한 6개 이벤트에 대한 데이터를 보내려고 하면 데이터가 LA 작업 영역으로 흐르지 않습니다.**

## <a name="legacy-event"></a>레거시 이벤트

일반적으로 볼트에 대한 모든 백업 관련 진단 데이터는 'AzureBackupReport'라는 단일 이벤트에 포함되어 있습니다. 위에서 설명한 여섯 가지 이벤트는 본질적으로 AzureBackupReport에 포함된 모든 데이터의 분해입니다. 

현재 사용자가 이 이벤트에 대한 기존 사용자 지정 쿼리(예: 사용자 지정 로그 경고, 사용자 지정 시각화 등)가 있는 경우 이전 버전과의 호환성을 위해 AzureBackupReport 이벤트를 계속 지원합니다. 그러나 **가능한 한 빨리 새 이벤트로 이동하는 것이 좋습니다.** **Azure 진단 모드 사용에 대 한 지원은 결국 단계적으로 중단 됩니다 및 따라서 새 이벤트를 선택 하 여 나중에 복잡 한 마이그레이션을 방지 하는 데 도움이 될 수 있습니다.**

Azure Backup의 기본 제공 정책을 사용하여 지정된 범위의 모든 볼트에 대해 6개의 새 이벤트가 있는 새 진단 [설정을](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics) 추가합니다.

새 테이블의 데이터를 사용하도록 모든 사용자 지정 쿼리를 마이그레이션할 때까지 AzureBackupReport 및 6개의 새 이벤트에 대한 별도의 진단 설정을 만들도록 선택할 수 있습니다. 아래 이미지는 두 개의 진단 설정이 있는 볼트의 예를 보여 주십니다. **세팅1이라는** 이름의 첫 번째 설정은 AzureBackupReport 이벤트의 데이터를 AzureDiagnostics 모드의 LA 작업 공간으로 보냅니다. **세팅2라는** 두 번째 설정은 6개의 새 Azure Backup 이벤트의 데이터를 리소스 별 모드의 LA 작업 공간으로 보냅니다.

![두 가지 설정](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> Azure BackupReport 이벤트는 Azure 진단 **모드에서만** 지원됩니다. **리소스 별 모드에서 이 이벤트에 대한 데이터를 보내려고 하면 데이터가 LA 작업 공간으로 흐르지 않습니다.**

> [!NOTE]
> Azure 진단/리소스 별에 대 한 토글은 사용자가 **로그 분석으로 보내기를**확인 하는 경우에 나타납니다. 저장소 계정 또는 이벤트 허브로 데이터를 보내려면 사용자는 추가 입력 없이 필요한 대상을 선택하고 원하는 이벤트를 확인할 수 있습니다. 다시 말하지만 앞으로 레거시 이벤트 AzureBackupReport를 선택하지 않는 것이 좋습니다.

## <a name="users-sending-azure-site-recovery-events-to-log-analytics-la"></a>AZURE 사이트 복구 이벤트를 로그 분석(LA)으로 보내는 사용자

Azure 백업 및 Azure 사이트 복구 이벤트는 동일한 복구 서비스 자격 증명 모음에서 전송됩니다. Azure 사이트 복구는 현재 리소스 특정 테이블에 온보덱히 지지 않고 있기 때문에 Azure 사이트 복구 이벤트를 LA로 보내려는 사용자는 Azure 진단 **모드만** 사용하도록 지시됩니다(아래 이미지 참조). **Azure 사이트 복구 이벤트에 대한 리소스별 모드를 선택하면 필요한 데이터가 LA 작업 영역으로 전송되지 않습니다.**

![사이트 복구 이벤트](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

위의 뉘앙스를 요약하려면 :

* 이미 LA 진단Azure 진단으로 설정 하 고 그 위에 사용자 지정 쿼리를 작성 한 경우 해당 설정을 **그대로**유지 합니다.
* 또한 새 테이블에 온보온하려는 경우(권장대로) **새** 진단 설정을 만들고 **리소스 특성을** 선택하고 위에 지정된 대로 6개의 새 이벤트를 선택합니다.
* 현재 LA로 Azure 사이트 복구 이벤트를 보내는 경우 이러한 이벤트에 대한 리소스 별 모드를 **선택하지 않으면** 이러한 이벤트에 대한 데이터가 LA 작업 공간으로 흐르지 않습니다. 대신 추가 **진단 설정을**만들고 Azure 진단을 선택하고 관련 Azure 사이트 복구 이벤트를 **선택합니다.**

아래 이미지는 볼트에 대한 세 가지 진단 설정을 가진 사용자의 예를 보여 주며 있습니다. **세팅1이라는** 이름의 첫 번째 설정은 AzureBackupReport 이벤트에서 AzureDiagnostics 모드의 LA 작업 공간으로 데이터를 보냅니다. **세팅2라는** 두 번째 설정은 6개의 새 Azure Backup 이벤트의 데이터를 리소스 별 모드의 LA 작업 공간으로 보냅니다. 세 번째 설정인 **Setting3은**Azure 사이트 복구 이벤트의 데이터를 Azure 진단 모드의 LA 작업 영역으로 보냅니다.

![세 가지 설정](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>다음 단계

[진단 이벤트에 대한 로그 분석 데이터 모델 알아보기](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
