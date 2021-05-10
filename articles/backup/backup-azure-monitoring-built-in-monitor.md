---
title: Azure Backup 보호된 워크로드 모니터링
description: 이 문서에서는 Azure Portal을 사용하여 Azure Backup 워크로드의 모니터링 및 알림 기능에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 83ed5af00bb61d7a8929e710b52e60c33c0f479b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559216"
---
# <a name="monitoring-azure-backup-workloads"></a>Azure Backup 워크로드 모니터링

Azure Backup은 백업 요구 사항 및 인프라 토폴로지를 기준으로 하는 여러 백업 솔루션을 제공합니다(온-프레미스 및 Azure). 모든 백업 사용자 또는 관리자는 모든 솔루션에서 발생하는 상황을 확인하고 중요한 시나리오에서 알림이 발생할 것으로 예측할 수 있습니다. 이 문서에서는 Azure Backup 서비스에서 제공하는 모니터링 및 알림 기능에 대해 자세히 설명합니다.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="backup-items-in-recovery-services-vault"></a>Recovery Services 자격 증명 모음의 백업 항목

Recovery Services 자격 증명 모음을 통해 모든 백업 항목을 모니터링할 수 있습니다. 자격 증명 모음의 **백업 항목** 섹션으로 이동하면 자격 증명 모음과 연결된 각 워크로드 유형의 백업 항목 수를 제공하는 보기가 열립니다. 행을 클릭하면 지정된 워크로드 유형의 모든 백업 항목과 각 항목에 대한 마지막 백업 상태에 대한 정보, 사용 가능한 최근 복원 지점 등을 나열하는 상세 보기가 열립니다.

![RS 자격 증명 모음 백업 항목](media/backup-azure-monitoring-laworkspace/backup-items-view.png)

> [!NOTE]
> DPM을 사용하여 Azure에 백업된 항목의 경우 목록에 DPM 서버를 사용하여 보호된 모든 데이터 원본(디스크 및 온라인)이 표시됩니다. 백업 데이터가 보존되는 데이터 원본에 대한 보호가 중지된 경우에도 데이터 원본은 포털에 계속 나열됩니다. 데이터 원본의 세부 정보로 이동하여 복구 지점이 디스크, 온라인 또는 둘 다에 있는지를 확인할 수 있습니다. 또한 온라인 보호가 중지되었지만 데이터가 유지되는 데이터 원본의 경우 데이터가 완전히 삭제될 때까지 온라인 복구 지점에 대한 비용 청구는 계속됩니다.
>
> 백업 항목이 Recovery Services 자격 증명 모음 포털에 표시되려면 DPM 버전이 DPM 1807(5.1.378.0) 또는 DPM 2019(버전 10.19.58.0 이상)이어야 합니다.

## <a name="backup-jobs-in-recovery-services-vault"></a>Recovery Services 자격 증명 모음의 백업 작업

Azure Backup은 Azure Backup으로 보호되는 워크로드에 대한 기본 제공 모니터링 및 경고 기능을 제공합니다. Recovery Services 자격 증명 모음 설정에서 **모니터링** 섹션은 기본 제공 작업 및 경고를 제공합니다.

![RS 자격 증명 모음 기본 제공 모니터링](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

작업(job)은 백업 구성, 백업, 복원, 삭제 등의 작업(operation)을 수행하는 경우에 생성됩니다.

다음 Azure Backup 솔루션의 작업이 여기에 표시됩니다.

- Azure VM 백업
- Azure 파일 백업
- Azure 워크로드 백업(예: SQL 및 SAP HANA)
- MARS(Microsoft Azure Recovery Services) 에이전트

SC-DPM(System Center Data Protection Manager) 및 MABS(Microsoft Azure Backup Server)의 작업이 표시되지 않습니다.

> [!NOTE]
> Azure VM 내에서 SQL 및 SAP HANA 백업 같은 Azure 워크로드에는 상당한 수의 백업 작업이 있습니다. 예를 들어, 로그 백업은 15분마다 실행될 수 있습니다. 따라서 이러한 DB 워크로드의 경우 사용자가 트리거한 작업만 표시됩니다. 예약된 백업 작업은 표시되지 않습니다.

## <a name="backup-alerts-in-recovery-services-vault"></a>Recovery Services 자격 증명 모음의 백업 경고

> [!NOTE]
> 자격 증명 모음 간의 경고 보기는 현재 백업 센터에서 지원되지 않습니다. 해당 자격 증명 모음에 대한 경고를 보려면 개별 자격 증명 모음으로 이동해야 합니다.

경고는 사용자가 관련 작업을 수행할 수 있도록 사용자에게 알림을 제공하는 경우에 주로 발생합니다. **백업 경고** 섹션에는 Azure Backup 서비스에서 생성된 경고가 표시됩니다. 이러한 경고는 서비스에서 정의되며 사용자가 만들 수 없습니다.

### <a name="alert-scenarios"></a>경고 시나리오

다음 시나리오는 서비스에서 경고 표시 가능 시나리오로 정의됩니다.

- 백업/복원 실패
- MARS(Microsoft Azure Recovery Services) 에이전트에 대해 결과를 나타내며 백업을 성공했습니다.
- 데이터 보존을 사용하여 보호 중지/데이터 삭제를 사용하여 보호 중지

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>다음 Azure Backup 솔루션의 경고가 여기에 표시됩니다.

- Azure VM 백업
- Azure 파일 백업
- SQL, SAP HANA 같은 Azure 워크로드 백업
- MARS(Microsoft Azure Recovery Services) 에이전트

> [!NOTE]
> 여기에는 SC-DPM(System Center Data Protection Manager) 및 MABS(Microsoft Azure Backup Server)의 경고가 표시되지 않습니다.

### <a name="consolidated-alerts"></a>통합된 경고

SQL 및 SAP HANA 같은 Azure 워크로드 백업 솔루션의 경우 로그 백업은 매우 자주 생성될 수 있습니다(정책에 따라 최대 15분마다). 따라서 로그 백업 실패가 매우 자주 발생할 수도 있습니다(최대 15분마다). 이 시나리오에서는 실패할 때마다 경고가 나타나는 경우 최종 사용자가 작업하기 어려워집니다. 따라서 첫 번째 발생에 대한 경고를 보낸 후, 후속 실패가 동일한 근본 원인으로 인해 발생하는 경우 추가 경고가 생성되지 않습니다. 첫 번째 경고가 실패 횟수로 업데이트됩니다. 그러나 사용자가 경고를 비활성화한 경우 다음 번에 경고가 발생하면 다른 경고가 트리거되고 해당 발생에 대한 첫 번째 경고로 처리됩니다. 이와 같은 방식으로 Azure Backup은 SQL 및 SAP HANA 백업에 대한 경고 통합을 수행합니다.

### <a name="exceptions-when-an-alert-is-not-raised"></a>경고가 발생하지 않는 예외

실패 시 경고가 발생하지 않는 몇 가지 예외가 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

- 사용자가 실행 중인 작업을 명시적으로 취소함
- 다른 백업 작업이 진행 중이므로 작업이 실패합니다(이전 작업이 완료될 때까지 기다려야 하므로 여기에서 수행할 작업이 없음).
- 백업된 Azure VM이 더 이상 존재하지 않으므로 VM 백업 작업이 실패합니다.
- [통합된 경고](#consolidated-alerts)

위의 예외는 이러한 작업의 결과(주로 사용자가 트리거함)가 포털/PS/CLI 클라이언트에 즉시 표시된다는 사실에서 설계되었습니다. 따라서 사용자는 즉시 인식하게 되며 알림이 필요하지 않습니다.

### <a name="alert-types"></a>경고 유형

경고 심각도에 따라 다음과 같은 세 가지 유형으로 경고를 정의할 수 있습니다.

- **중요**: 원칙적으로 모든 백업 또는 복구 실패(예약되었거나 사용자가 트리거)로 인해 경고가 생성되고, 중요 알림 및 백업 삭제와 같은 파괴적 작업으로 표시됩니다.
- **경고**: 백업 작업이 성공하지만 경고가 거의 없으면 경고 알림으로 표시됩니다. 경고 알림은 현재 Azure Backup 에이전트 백업에 대해서만 사용할 수 있습니다.
- **정보**: 현재 Azure Backup 서비스에 의해 생성된 정보 알림이 없습니다.

## <a name="notification-for-backup-alerts"></a>백업 경고에 대한 알림

> [!NOTE]
> 알림 구성은 Azure Portal을 통해서만 수행할 수 있습니다. PS/CLI/REST API/Azure Resource Manager 템플릿은 지원되지 않습니다.

경고가 발생하면 사용자에게 알림이 제공됩니다. Azure Backup는 메일을 통해 기본 제공 알림 메커니즘을 제공합니다. 경고가 생성될 때 알림을 받을 개별 메일 주소 또는 메일 그룹을 지정할 수 있습니다. 각 개별 경고에 대한 알림을 받을지 또는 시간별 요약으로 그룹화한 후 알림을 받을지 선택할 수도 있습니다.

![RS 자격 증명 모음 기본 제공 메일 알림](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

알림이 구성되면 환영 또는 소개 메일을 받게 됩니다. 이렇게 하면 경고가 발생할 때 Azure Backup이 주소로 메일을 보낼 수 있습니다.<br>

빈도가 시간별 요약으로 설정되고, 경고가 발생하고 1시간 안에 해결되면 예정된 시간별 요약에 포함되지 않게 됩니다.

> [!NOTE]
>
> - **데이터 삭제로 보호 중지** 와 같은 파괴적인 작업을 수행하면, Recovery Services 자격 증명 모음에 대해 알림을 구성하지 않았더라도 경고가 발생한 후 메일이 구독 소유자, 관리자 및 공동 관리자에게 전송됩니다.
> - 성공한 작업에 대한 알림을 구성하려면 [Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace)를 사용합니다.

## <a name="inactivating-alerts"></a>경고 비활성화

활성 경고를 비활성화/해결하려면 비활성화할 경고에 해당하는 목록 항목을 선택할 수 있습니다. 이렇게 하면 경고에 대한 자세한 정보를 표시하는 화면이 열리고 맨 위에 **비활성화** 단추가 표시됩니다. 이 단추를 선택하면 경고 상태가 **비활성** 으로 변경됩니다. 해당 경고에 해당하는 목록 항목을 마우스 오른쪽 단추로 클릭하고 **비활성화** 를 선택하여 경고를 비활성화할 수도 있습니다.

![RS 자격 증명 모음 경고 비활성화](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="azure-monitor-alerts-for-azure-backup-preview"></a>Azure Backup에 대한 Azure Monitor 경고(미리 보기)

또한 Azure Backup은 Azure Monitor를 통해 경고를 제공하여 사용자가 백업을 비롯한 다양한 Azure 서비스에서 경고 관리를 위한 일관된 환경을 제공할 수 있도록 합니다. Azure Monitor 경고를 사용하여 메일, ITSM, Webhook, 논리 앱 등, Azure Backup에서 지원하는 알림 채널로 경고를 라우팅할 수 있습니다.

현재 이 기능은 Azure Database for PostgreSQL Server, Azure Blob 및 Azure Managed Disks에서 사용할 수 있습니다. 경고는 다음과 같은 시나리오에 대해 생성되며, 백업 자격 증명 모음으로 이동한 후 **경고** 메뉴 항목을 클릭하여 액세스할 수 있습니다.

- 백업 데이터 삭제
- 백업 실패(백업 실패에 대한 경고를 가져오려면 미리 보기 포털을 통해 **EnableAzureBackupJobFailureAlertsToAzureMonitor** 라는 AFEC 플래그를 등록해야 함)
- 복원 실패(복원 실패에 대한 경고를 가져오려면 미리 보기 포털을 통해 **EnableAzureBackupJobFailureAlertsToAzureMonitor** 라는 AFEC 플래그를 등록해야 함)

Azure Monitor 경고에 대한 자세한 내용은 [Azure의 경고 개요](../azure-monitor/alerts/alerts-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure Monitor를 사용하여 Azure Backup 워크로드 모니터링](backup-azure-monitoring-use-azuremonitor.md)